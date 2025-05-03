### Install OhmGraphite exporter `https://github.com/nickbabcock/OhmGraphite`
- Create directory `C:\Apps`
- Download `https://github.com/nickbabcock/OhmGraphite/releases/download/v0.35.0/OhmGraphite-0.35.0.zip` to `C:\Apps` and Extract all
- Notepad > Run as administrator > Open `C:\Apps\OhmGraphite-0.35.0\OhmGraphite.exe.config`

      <?xml version="1.0" encoding="utf-8" ?>
      <configuration>
      <appSettings>
        <add key="type" value="prometheus" />
        <add key="prometheus_port" value="4445" />
        <add key="prometheus_host" value="*" />
        <add key="prometheus_path" value="metrics/" /> 
      </appSettings>
      </configuration>

- Open in Terminal at `C:\Apps\OhmGraphite-0.35.0`
- Run
    .\OhmGraphite.exe install
- Run
    .\OhmGraphite.exe start

### Install Grafana Alloy `https://github.com/grafana/alloy`
- Download `https://github.com/grafana/alloy/releases/download/v1.8.2/alloy-installer-windows-amd64.exe.zip` > Extract all
- Run `alloy-installer-windows-amd64.exe`
- Notepad > Run as administrator > Open `C:\Program Files\GrafanaLabs\Alloy\config.alloy`

      logging {
        level = "info"
      }

      prometheus.remote_write "default" {
        endpoint {
          url = "http://10.0.0.1:9090/api/v1/write"
        }
        external_labels = {
          hostname = "iamd",
        }
      }

      prometheus.exporter.windows "windows" { }

      prometheus.scrape "windows" {
        targets    = prometheus.exporter.windows.windows.targets
        forward_to = [prometheus.remote_write.default.receiver]
        scrape_interval = "10s"
      }

      prometheus.scrape "OhmGraphite" {
        targets = [{
            __address__ = "127.0.0.1:4445",
        }]

        forward_to = [prometheus.remote_write.default.receiver]
        scrape_interval = "10s"
      }

- Services > Alloy > Restart

### Install WSL
- Powershell

      wsl --install

- Reboot
- Microsoft Store > Ubuntu 24.04.1 LTS > Get
- Run Ubuntu 24.04.1 LTS

      sudo apt update
      sudo apt upgrade
      sudo reboot

### Install Docker Desktop for Windows
- Download from `https://docs.docker.com/get-started/introduction/get-docker-desktop/`

      [*] Use WSL 2 instead of Hyper-V (recommended)

- Close and restart

- Docker Desktop > Settings > General > 

      [*] Use the WSL 2 based engine 
        [*] Add the *.docker.internal names...
      [ ] Send usage statistics

- Apply & restart

### Start Prometheus and Grafana
- Open Ubuntu Terminal and clone this repo

      mkdir ~/git
      cd ~/git
      git clone git@github.com:dmlesc/0bs3rv3.git

- Start containers

      cd 0bs3rv3/grafana
      mkdir -p data/grafana
      sudo chown 472:472 data/grafana
      mkdir -p data/prometheus
      sudo chown nobody:nogroup data/prometheus
      docker compose up -d

### Open Grafana
    host: http://localhost:3000
    user: admin
    pass: admin
