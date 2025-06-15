# Integration Guide

This guide shows how to integrate the homelab-infrastructure repository with your other projects, using the Three Wise Men (Magi) naming scheme.

## Quick Integration

### 1. Clone Infrastructure Repo

```bash
# In your projects directory
git clone https://github.com/SamuraiBuddha/homelab-infrastructure.git
```

### 2. Environment Variables

In your project's `.env` file:

```bash
# Infrastructure paths
HOMELAB_INFRA_PATH=../homelab-infrastructure
MACHINE_ID=melchior-cad  # or balthazar-ai, caspar-code

# Load machine-specific configs
MACHINE_CONFIG_PATH=${HOMELAB_INFRA_PATH}/machines/${MACHINE_ID}
```

### 3. Docker Compose Integration

#### Option A: Extend Configurations

```yaml
# docker-compose.yml in your project
version: '3.8'

services:
  my-service:
    extends:
      file: ${MACHINE_CONFIG_PATH}/docker/compose-overrides.yml
      service: melchior-cad-compute  # or balthazar-ai-inference, caspar-data-processing
    image: myapp:latest
    # ... rest of your config
```

#### Option B: Include Files

```yaml
# docker-compose.yml
version: '3.8'

# Include machine-specific settings
include:
  - path: ${MACHINE_CONFIG_PATH}/docker/compose-overrides.yml
    env_file: ${MACHINE_CONFIG_PATH}/docker/.env

services:
  my-service:
    # Your service config
```

### 4. Accessing Hardware Info

```python
# Python example
import json
import os

machine_id = os.environ.get('MACHINE_ID', 'melchior-cad')
config_path = f"../homelab-infrastructure/machines/{machine_id}/hardware"

# Load GPU info
with open(f"{config_path}/gpu-info.json") as f:
    gpu_info = json.load(f)
    
# Adjust batch sizes based on GPU memory
gpu_memory = gpu_info[0]['AdapterRAM']  # In bytes
batch_size = calculate_optimal_batch_size(gpu_memory)
```

### 5. Network Configuration

```yaml
# Use machine-specific network settings
networks:
  default:
    external:
      name: homelab_10gbe  # Defined in infrastructure repo
  
  magi-network:
    external:
      name: magi-network  # Overlay network for all three machines
```

## Project Examples

### CORTEX Integration

```yaml
# CORTEX docker-compose.yml
version: '3.8'

x-machine-profiles:
  # Melchior - CAD/3D Processing
  melchior: &melchior
    file: ${HOMELAB_INFRA_PATH}/machines/melchior-cad/docker/compose-overrides.yml
  
  # Balthazar - AI Model Host
  balthazar: &balthazar
    file: ${HOMELAB_INFRA_PATH}/machines/balthazar-ai/docker/compose-overrides.yml
  
  # Caspar - Code/Data Processing
  caspar: &caspar
    file: ${HOMELAB_INFRA_PATH}/machines/caspar-code/docker/compose-overrides.yml

services:
  n8n:
    extends:
      <<: *${DEPLOYMENT_PROFILE:-melchior}
      service: base
    # ... rest of n8n config

  ai-model:
    extends:
      <<: *balthazar
      service: balthazar-ai-inference
    # ... AI model config
  
  data-processor:
    extends:
      <<: *caspar
      service: caspar-data-processing
    # ... data processing config
```

### CI/CD Integration

```yaml
# .github/workflows/deploy.yml
name: Deploy to Homelab Magi Systems

env:
  MELCHIOR_HOST: 192.168.1.100  # CAD/3D workstation
  BALTHAZAR_HOST: 192.168.1.101  # AI host
  CASPAR_HOST: 192.168.1.102  # Code/Data processor

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Checkout Infrastructure
        uses: actions/checkout@v3
        with:
          repository: SamuraiBuddha/homelab-infrastructure
          path: infrastructure
      
      - name: Deploy to Balthazar (AI Production)
        env:
          MACHINE_ID: balthazar-ai
        run: |
          # Use machine-specific configs
          source infrastructure/machines/$MACHINE_ID/deploy-config.sh
          docker-compose up -d
```

### Monitoring Integration

```yaml
# Prometheus config using Magi infrastructure data
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'melchior'
    static_configs:
      - targets: ['192.168.1.100:9100']
        labels:
          machine: 'melchior-cad'
          gpu: 'rtx3090'
          role: 'cad_processing'
          gift: 'gold'
          
  - job_name: 'balthazar'
    static_configs:
      - targets: ['192.168.1.101:9100']
        labels:
          machine: 'balthazar-ai'
          gpu: 'rtx-a5000'
          role: 'ai_inference'
          gift: 'frankincense'
          
  - job_name: 'caspar'
    static_configs:
      - targets: ['192.168.1.102:9100']
        labels:
          machine: 'caspar-code'
          gpu: 'rtx-a4000'
          role: 'data_processing'
          gift: 'myrrh'
```

### MCP Services Integration

Since the Terramaster NAS hosts all MCP services:

```yaml
# MCP client configuration
mcp_services:
  postgres:
    host: terramaster.local
    port: 5432
  
  redis:
    host: terramaster.local
    port: 6379
  
  neo4j:
    host: terramaster.local
    port: 7687
  
  influxdb:
    host: terramaster.local
    port: 8086
```

## Best Practices

1. **Never commit machine-specific values** - Always use environment variables
2. **Use relative paths** - Makes projects portable between machines
3. **Document dependencies** - Note which infrastructure components are required
4. **Version compatibility** - Tag infrastructure versions for stability
5. **Respect the Magi roles** - Deploy workloads to the appropriate machine based on their gifts

## The Three Gifts in Practice

- **Melchior (Gold)**: Deploy CAD, 3D, and precision workloads
- **Balthazar (Frankincense)**: Deploy AI models, inference, and transformation workloads  
- **Caspar (Myrrh)**: Deploy code generation, data processing, and preservation workloads

## Troubleshooting

### Path Issues

```bash
# Debug paths
echo $HOMELAB_INFRA_PATH
ls -la ${HOMELAB_INFRA_PATH}/machines/

# Fix common issues
export HOMELAB_INFRA_PATH=$(realpath ../homelab-infrastructure)
```

### Permission Issues

```bash
# Fix Docker volume permissions
sudo chown -R $USER:$USER ${HOMELAB_INFRA_PATH}
```

### Network Issues

```bash
# Test 10GbE connectivity between Magi machines
iperf3 -c <other-magi-ip> -p 5201
```

### MCP Connection Issues

```bash
# Test connection to Terramaster MCP services
curl http://terramaster.local:8086/health  # InfluxDB
redis-cli -h terramaster.local ping  # Redis
```