# Prometheus + Blackbox Exporter Monitoring + grafana

```helm repo add prometheus-community https://prometheus-community.github.io/helm-charts```

```helm repo update```

```helm install prometheus-blackbox prometheus-community/prometheus-blackbox-exporter --namespace mon```

```k8s kubectl port-forward service/prometheus-blackbox-prometheus-blackbox-exporter 9115:9115 --namespace mon &```
---

# prometheus github
```bash
curl -LO https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
tar -xzf prometheus-2.52.0.linux-amd64.tar.gz
mv prometheus-2.52.0.linux-amd64 prometheus
cd prometheus
```
---
nano prometheus.yml
```bash
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]  # Use whatever modules you've defined in blackbox.yml

    static_configs:
      - targets:
          - https://example.com
          - https://google.com
          - https://yourdomain1.com
          - https://yourdomain2.net
          - https://api.yourcompany.org
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: localhost:9115  # Adjust if blackbox runs elsewhere
```

./prometheus --config.file=prometheus.yml &

---

wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
sudo apt update
sudo apt install grafana -y
sudo systemctl enable --now grafana-server
sudo systemctl start --now grafana-server