# 🏠 Homelab Infrastructure

Infrastructure as Code - System configurations, benchmarks, and tuning for my homelab environment.

## 🖥️ System Inventory

### Machine 1: Development Workstation
- **CPU**: Intel Core i9-11900K (11th Gen)
- **RAM**: 64GB DDR4-4600 (G.Skill F4-4600C19-16GTZR x2)
- **GPU**: NVIDIA GeForce RTX 3090 (24GB)
- **Motherboard**: ASUS ROG Strix Z590-E Gaming WiFi
- **PSU**: 1200W ASUS ROG Thor
- **Role**: Development, Testing, Gaming

### Machine 2: AI/Compute Server
- **CPU**: Intel Core i9-11900K (11th Gen)
- **RAM**: 128GB DDR4-3600 (G.Skill F4-3600C18-32GTZN)
- **GPU**: NVIDIA RTX A5000 (24GB ECC)
- **Motherboard**: ASUS ROG Maximus XIII Hero
- **PSU**: 1200W ASUS ROG Thor
- **Role**: AI Training, CAD/BIM, Production Workloads

### Machine 3: Render/Simulation Server
- **CPU**: AMD Ryzen 9 5950X (16C/32T)
- **RAM**: 128GB DDR4-3600 (G.Skill F4-3600C18Q-128GTRS)
- **GPU**: NVIDIA RTX A4000 (16GB)
- **Motherboard**: ASUS ROG Crosshair VIII Formula
- **PSU**: 1600W
- **Role**: Rendering, Simulation, Virtualization

### Network Infrastructure
- **Backbone**: 10GbE Network
- **NAS**: Terramaster F8 Plus
  - Storage: 6x4TB RAID Configuration
  - Cache: 16TB NVMe
  - OS: TOS 6.0

## 📂 Repository Structure

```
homelab-infrastructure/
├── machines/
│   ├── machine1-i9-3090/
│   │   ├── hardware/
│   │   │   ├── hwinfo-report.csv
│   │   │   ├── aida64-full.txt
│   │   │   ├── cpu-z.txt
│   │   │   └── gpu-z.xml
│   │   ├── bios/
│   │   │   ├── current-settings.CMO
│   │   │   └── profiles/
│   │   ├── benchmarks/
│   │   │   ├── aida64/
│   │   │   ├── 3dmark/
│   │   │   └── ml-benchmarks/
│   │   └── docker/
│   │       ├── docker-info.json
│   │       └── compose-overrides.yml
│   │
│   ├── machine2-i9-a5000/
│   └── machine3-5950x-a4000/
│
├── network/
│   ├── topology.md
│   ├── switch-configs/
│   └── performance-tests/
│
├── storage/
│   ├── nas-configuration/
│   ├── raid-setup.md
│   └── backup-strategy.md
│
├── scripts/
│   ├── collect-system-info.ps1
│   ├── benchmark-suite.ps1
│   └── backup-configs.sh
│
├── monitoring/
│   ├── prometheus-exporters/
│   ├── grafana-dashboards/
│   └── alerts/
│
└── documentation/
    ├── setup-guides/
    ├── tuning-notes/
    └── lessons-learned.md
```

## 🔧 Usage

### Collecting System Information

```powershell
# Run from each machine
.\scripts\collect-system-info.ps1 -MachineName "machine1-i9-3090"
```

### BIOS Configuration Export

For ASUS boards:
1. Enter BIOS (DEL key during boot)
2. Press F7 for Advanced Mode
3. Navigate to Tool → ASUS User Profile
4. Save Profile to USB drive
5. Copy .CMO file to `machines/[machine-name]/bios/`

### Benchmarking

```powershell
# Full benchmark suite
.\scripts\benchmark-suite.ps1 -All

# Specific benchmarks
.\scripts\benchmark-suite.ps1 -GPU -Storage
```

## 🔗 Integration with Projects

In your project repositories (like CORTEX), reference this repo:

```yaml
# docker-compose.yml
x-machine-config:
  machine1: &machine1
    extends:
      file: ${HOMELAB_INFRA_PATH}/machines/machine1-i9-3090/docker/compose-overrides.yml
      service: base
```

```bash
# .env
HOMELAB_INFRA_PATH=../homelab-infrastructure
MACHINE_ID=machine1-i9-3090
```

## 📊 Performance Baselines

| Component | Machine 1 | Machine 2 | Machine 3 |
|-----------|-----------|-----------|-----------|
| CPU Score | TBD | TBD | TBD |
| GPU Score | TBD | TBD | TBD |
| RAM Latency | TBD | TBD | TBD |
| NVMe Read | TBD | TBD | TBD |
| Network Throughput | TBD | TBD | TBD |

## 🚀 Quick Start

1. Clone this repository on each machine:
   ```bash
   git clone https://github.com/SamuraiBuddha/homelab-infrastructure.git
   cd homelab-infrastructure
   ```

2. Run initial setup:
   ```powershell
   .\scripts\initial-setup.ps1
   ```

3. Collect system information:
   ```powershell
   .\scripts\collect-system-info.ps1
   ```

4. Commit your machine's data:
   ```bash
   git add machines/[your-machine]/
   git commit -m "Add configuration for [machine-name]"
   git push
   ```

## 📝 Notes

- All sensitive information (passwords, keys) should be stored in `.env` files (gitignored)
- BIOS profiles are machine-specific and should only be restored to identical hardware
- Benchmark results should include ambient temperature and timestamp
- Use consistent naming convention: `machine[N]-[cpu]-[gpu]`

## 🔒 Security

- This repo contains hardware configurations only
- No credentials, keys, or sensitive data
- BIOS passwords are not stored (document separately)
- Network topology excludes sensitive routing info