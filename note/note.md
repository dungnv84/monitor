2HX6JQEecqihB9q

# web
http://52.77.218.209:8088/
User/ pass: admin / A123456b8899

# add node to pmm server
pmm-admin config --server-insecure-tls --server-url=https://admin:A123456b8899@10.20.1.117:8443 10.20.30.230 generic mongodb-t1-node02 
# 10.20.30.230 generic mongodb-t1-node02  IP and name of host dk add
# install pmm-client
wget https://repo.percona.com/apt/percona-release_latest.generic_all.deb
dpkg -i percona-release_latest.generic_all.deb
apt update -y
apt install -y pmm2-client

# ssh server pmm server
ssh -i "new-mykiot-grafana.pem" ubuntu@ec2-52-77-218-209.ap-southeast-1.compute.amazonaws.com   con prometheus


pmm-admin config --server-insecure-tls --server-url=https://admin:mykiot-admin@10.15.132.31:8443 10.15.132.14  generic redis-04


# practice
sudo pmm-admin --version
sudo systemctl status node_exporter

sudo wget https://repo.percona.com/apt/percona-release_latest.generic_all.deb
sudo dpkg -i percona-release_latest.generic_all.deb
sudo apt update -y
sudo apt install -y pmm2-client
sudo pmm-admin --version

sudo pmm-admin config --server-insecure-tls --server-url=https://admin:A123456b8899@10.20.1.117:8443 10.20.35.11 generic  kafka-t1-node03


wget https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz
tar xvfz node_exporter-1.0.1.linux-amd64.tar.gz
sudo mv node_exporter-1.0.1.linux-amd64/node_exporter /usr/local/bin/
sudo useradd -rs /bin/false node_exporter

sudo tee /etc/systemd/system/node_exporter.service<<EOF
[Unit]
Description=Node Exporter
After=network.target
 
[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter
 
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
sudo systemctl status node_exporter


# redis exporter
sudo wget https://github.com/oliver006/redis_exporter/releases/download/v1.45.0/redis_exporter-v1.45.0.linux-amd64.tar.gz
sudo tar xvf redis_exporter-v1.45.0.linux-amd64.tar.gz
sudo mv redis_exporter-v1.45.0.linux-amd64/redis_exporter /usr/local/bin/
sudo chmod +x /usr/local/bin/redis_exporter

sudo groupadd --system prometheus
sudo useradd -s /sbin/nologin --system -g prometheus prometheus


sudo tee /etc/systemd/system/redis_exporter.service<<EOF
[Unit]
Description=Prometheus
Documentation=https://github.com/oliver006/redis_exporter
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus
ExecReload=/bin/kill -HUP $MAINPID
ExecStart=/usr/local/bin/redis_exporter \
  --log-format=txt \
  --namespace=redis \
  --web.listen-address=:9121 \
  --redis.addr="redis://127.0.0.1:6379" \
  --redis.password="2befb66c7ce30abd636c580df6199979"
  --web.telemetry-path=/metrics

SyslogIdentifier=redis_exporter
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl restart redis_exporter
sudo systemctl status redis_exporter


cd /usr/share/java/kafka
wget https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.17.1/jmx_prometheus_javaagent-0.17.1.jar

cd /etc/kafka
nano jmx_exporter.yml
https://github.com/oded-dd/prometheus-jmx-kafka/blob/master/kafka-jmx-metric.yaml


nano /usr/bin/kafka-server-start
export KAFKA_OPTS=' -javaagent:/usr/share/java/kafka/jmx_prometheus_javaagent-0.17.1.jar=7071:/etc/kafka/jmx_exporter.yml'

nano /etc/systemd/system/confluent-kafka.service
Environment="KAFKA_OPTS=-javaagent:/usr/share/java/kafka/jmx_prometheus_javaagent-0.17.1.jar=7071:/etc/kafka/jmx_exporter.yml"

export KAFKA_OPTS=' -javaagent:/usr/share/java/kafka/jmx_prometheus_javaagent-0.17.1.jar=7071:/etc/kafka/jmx_exporter.yml'