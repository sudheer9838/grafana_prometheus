# prometheus

Install prometheus 
Let’s start by creating the Prometheus system user and group.
$ sudo groupadd --system prometheus 

create Prometheus system user and assign primary group created. using command
$ sudo useradd -s /sbin/nologin --system -g prometheus prometheus

$ sudo mkdir /var/lib/prometheus
$ for i in rules rules.d files_sd; do sudo mkdir -p /etc/prometheus/${i}; done

now update the server using command 
$ sudo apt-get update 
$ sudo apt -y install wget curl vim

download the latest binary archive for Prometheus.using coomand
$ mkdir -p /tmp/prometheus && cd /tmp/prometheus
$curl -s https://api.github.com/repos/prometheus/prometheus/releases/latest | grep browser_download_url | grep linux-amd64 | cut -d '"' -f 4 | wget -qi -

extract the file
$ tar xvf prometheus*.tar.gz
$ cd prometheus*/

Move binary files to /usr/local/bin/ directory.
$ sudo mv prometheus promtool /usr/local/bin/
$ prometheus --version

$ sudo mv prometheus.yml /etc/prometheus/prometheus.yml
$ sudo mv consoles/ console_libraries/ /etc/prometheus/
$ cd $HOME

$ sudo vim /etc/prometheus/prometheus.yml

$ sudo vi /etc/systemd/system/prometheus.service
[Unit]
Description=Prometheus
Documentation=https://prometheus.io/docs/introduction/overview/
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus

Group=prometheus
ExecReload=/bin/kill -HUP \$MAINPID
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.external-url=

SyslogIdentifier=prometheus
Restart=always

[Install]
WantedBy=multi-user.target
EOF 
---------------------------wq
$ for i in rules rules.d files_sd; do sudo chown -R prometheus:prometheus /etc/prometheus/${i}; done
$ for i in rules rules.d files_sd; do sudo chmod -R 775 /etc/prometheus/${i}; done
$ sudo chown -R prometheus:prometheus /var/lib/prometheus/

$ sudo systemctl daemon-reload
$ sudo systemctl start prometheus
$ sudo systemctl enable prometheus
$ Sudo systemctl status prometheus



# Grafana
Install required system packages.
$ sudo apt-get install -y gnupg2 curl software-properties-common

grafana gpg key
$ curl https://packages.grafana.com/gpg.key | sudo apt-key add -

install grafana repository 
$ sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
$ sudo apt-get update 

Install Grafana
$ sudo apt -y install grafana
$ sudo systemctl start grafana-server
$ sudo systemctl enable grafana-server
$ sudo systemctl status grafana-server
