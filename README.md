# 🏠 Homelab Infrastructure

Infrastructure as Code - System configurations, benchmarks, and tuning for my homelab environment following the Three Wise Men (Magi) naming scheme.

## 🖥️ System Inventory

### 🏛️ Melchior (Gold) - CAD/3D Processing Station
- **CPU**: Intel Core i9-11900K (11th Gen)
- **RAM**: 64GB DDR4-4600 (G.Skill F4-4600C19-16GTZR x2)
- **GPU**: NVIDIA GeForce RTX 3090 (24GB)
- **Motherboard**: ASUS ROG Strix Z590-E Gaming WiFi
- **PSU**: 1200W ASUS ROG Thor
- **Role**: Digital craftsmanship and precision engineering
- **Gift**: Gold (enduring technical excellence)
- **Focus**: Autodesk Suite, SolidWorks, Point Cloud Processing

### 🔮 Balthazar (Frankincense) - AI Model Host
- **CPU**: Intel Core i9-11900K (11th Gen)
- **RAM**: 128GB DDR4-3600 (G.Skill F4-3600C18-32GTZN)
- **GPU**: NVIDIA RTX A5000 (24GB ECC)
- **Motherboard**: ASUS ROG Maximus XIII Hero
- **PSU**: 1200W ASUS ROG Thor
- **Role**: Intelligence elevation and model serving
- **Gift**: Frankincense (transforming data into insights)
- **Focus**: AI Training, Model Hosting, CUDA Workloads

### ⚗️ Caspar (Myrrh) - Code Generation & Data Processing
- **CPU**: AMD Ryzen 9 5950X (16C/32T)
- **RAM**: 128GB DDR4-3600 (G.Skill F4-3600C18Q-128GTRS)
- **GPU**: NVIDIA RTX A4000 (16GB)
- **Motherboard**: ASUS ROG Crosshair VIII Formula
- **PSU**: 1600W
- **Role**: Systematic transformation and automation
- **Gift**: Myrrh (preserving and transforming processes)
- **Focus**: Development, Data Processing, Virtualization

### 🗄️ Network Infrastructure
- **Backbone**: 10GbE Network
- **NAS**: Terramaster F8 Plus
  - Storage: 6x4TB RAID Configuration
  - Cache: 16TB NVMe
  - OS: TOS 6.0
  - **Special Role**: Hosts all MCP (Model Context Protocol) services for unified Claude memory across all machines

## 📂 Repository Structure

```
homelab-infrastructure/
├── machines/
│   ├── melchior-cad/
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
│   │   │   └── cad-benchmarks/
│   │   └── docker/
│   │       ├── docker-info.json
│   │       └── compose-overrides.yml
│   │
│   ├── balthazar-ai/
│   │   ├── hardware/
│   │   ├── bios/
│   │   ├── benchmarks/
│   │   │   └── ml-benchmarks/
│   │   └── docker/
│   │
│   └── caspar-code/
│       ├── hardware/
│       ├── bios/
│       ├── benchmarks/
│       └── docker/
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
.\scripts\collect-system-info.ps1 -MachineName "melchior-cad"
.\scripts\collect-system-info.ps1 -MachineName "balthazar-ai"
.\scripts\collect-system-info.ps1 -MachineName "caspar-code"
```

### BIOS Configuration Export

For ASUS boards:
1. Enter BIOS (DEL key during boot)
2. Press F7 for Advanced Mode
3. Navigate to Tool → ASUS User Profile
4. Save Profile to USB drive
5. Copy .CMO file to `machines/[magi-name]/bios/`

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
  melchior: &melchior
    extends:
      file: ${HOMELAB_INFRA_PATH}/machines/melchior-cad/docker/compose-overrides.yml
      service: gpu-compute
  
  balthazar: &balthazar
    extends:
      file: ${HOMELAB_INFRA_PATH}/machines/balthazar-ai/docker/compose-overrides.yml
      service: ai-inference
  
  caspar: &caspar
    extends:
      file: ${HOMELAB_INFRA_PATH}/machines/caspar-code/docker/compose-overrides.yml
      service: cpu-intensive
```

```bash
# .env
HOMELAB_INFRA_PATH=../homelab-infrastructure
MACHINE_ID=melchior-cad  # or balthazar-ai, caspar-code
```

## 📊 Performance Baselines

| Component | Melchior (CAD) | Balthazar (AI) | Caspar (Code) |
|-----------|----------------|----------------|---------------|
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
   # Replace with your machine's Magi name
   .\scripts\collect-system-info.ps1 -MachineName "melchior-cad"
   ```

4. Commit your machine's data:
   ```bash
   git add machines/[magi-name]/
   git commit -m "Add configuration for [magi-name]"
   git push
   ```

## 📝 Notes

- All sensitive information (passwords, keys) should be stored in `.env` files (gitignored)
- BIOS profiles are machine-specific and should only be restored to identical hardware
- Benchmark results should include ambient temperature and timestamp
- The Magi naming convention honors the Three Wise Men's gifts of expertise

## 🔒 Security

- This repo contains hardware configurations only
- No credentials, keys, or sensitive data
- BIOS passwords are not stored (document separately)
- Network topology excludes sensitive routing info

## 🎁 The Three Gifts

Each machine brings its unique computational gift to the homelab, just as the Magi brought their gifts:
- **Melchior's Gold**: Enduring precision in CAD/3D work
- **Balthazar's Frankincense**: Transforming raw data into AI insights
- **Caspar's Myrrh**: Preserving and transforming code and processes