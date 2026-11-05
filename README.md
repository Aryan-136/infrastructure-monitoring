# :chart_with_upwards_trend: Enterprise Full-Stack Observability & SRE Telemetry Suite

[![Prometheus](https://img.shields.io/badge/Metrics%20TSDB-Prometheus%20v2.50-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)](https://prometheus.io)
[![Grafana](https://img.shields.io/badge/Visual%20Analytics-Grafana%20v10-F46800?style=for-the-badge&logo=grafana&logoColor=white)](https://grafana.com)
[![cAdvisor](https://img.shields.io/badge/Container%20Telemetry-cAdvisor-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://github.com/google/cadvisor)
[![Alertmanager](https://img.shields.io/badge/Incident%20Routing-Alertmanager-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)](https://prometheus.io/docs/alerting/latest/alertmanager/)

A high-performance observability ecosystem modeled after enterprise Site Reliability Engineering (SRE) architectures. Provides end-to-end telemetry across physical bare-metal hardware, Proxmox hypervisors, containerized microservices, and network interfaces with automated sub-minute incident alert routing.

---

## :triangular_ruler: Telemetry Architecture

```mermaid
flowchart TD
    subgraph Host Infrastructure & Runtimes
        Host[Bare-Metal Server / Proxmox VE]
        Containers[Docker Microservices Fleet]
    end

    subgraph Telemetry Collectors
        NE[Node Exporter :9100<br/>CPU / RAM / Disk / Network]
        CA[cAdvisor :8080<br/>Container CPU, Mem, I/O]
    end

    subgraph Core Observability Engine
        Prom[Prometheus TSDB :9090<br/>30-Day Time-Series Retention]
        AM[Alertmanager :9093<br/>Incident Deduplication & Routing]
    end

    subgraph Visualization & Incident Response
        Graf[Grafana Enterprise :3000<br/>Unified SRE Dashboards]
        OnCall[Slack / Discord / Webhook Escalation]
    end

    Host --> NE
    Containers --> CA
    NE -->|15s Pull Scrape| Prom
    CA -->|15s Pull Scrape| Prom
    Prom -->|Threshold Violations| AM
    AM -->|Trigger Notifications| OnCall
    Prom -->|PromQL Queries| Graf
```

---

## :bar_chart: SRE Metrics & SLI/SLO Coverage

| Metric Domain | Collector | Key PromQL Expression | Alert Threshold |
| :--- | :--- | :--- | :--- |
| **Node Availability** | `node-exporter` | `up{job="node-exporter"} == 0` | Down > 1m (Critical) |
| **CPU Saturation** | `node-exporter` | `100 - (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)` | Load > 85% for 5m |
| **Memory Pressure** | `node-exporter` | `(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100` | Usage > 90% for 3m |
| **Disk Exhaustion** | `node-exporter` | `((node_filesystem_avail_bytes * 100) / node_filesystem_size_bytes)` | Free Space < 15% |
| **Container OOM** | `cadvisor` | `container_oom_events_total > 0` | Immediate Trigger |

---

## :rocket: Deployment & Provisioning

### 1. Quick Boot
```bash
git clone https://github.com/Aryan-136/infrastructure-monitoring.git
cd infrastructure-monitoring

# Start the full observability stack
docker compose up -d
```

### 2. Verify Services
- **Prometheus TSDB**: `http://localhost:9090`
- **Grafana Dashboards**: `http://localhost:3000` (Default credentials: `admin` / `admin`)
- **Alertmanager**: `http://localhost:9093`
- **Node Exporter**: `http://localhost:9100/metrics`
- **cAdvisor**: `http://localhost:8080`

---

## :file_folder: Repository Layout
```text
.
â”œâ”€â”€ config/
â”‚   â”œâ”€â”€ alertmanager/
â”‚   â”‚   â””â”€â”€ alertmanager.yml      # Alert routing and notification channels
â”‚   â””â”€â”€ prometheus/
â”‚       â”œâ”€â”€ alert.rules.yml       # Production alerting thresholds (CPU, RAM, Disk)
â”‚       â””â”€â”€ prometheus.yml        # Scrape intervals and target service definitions
â”œâ”€â”€ docker-compose.yml            # Multi-service container orchestration
â””â”€â”€ README.md                     # Architectural documentation & PromQL reference
```