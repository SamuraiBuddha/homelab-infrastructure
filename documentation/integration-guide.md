# Integration Guide

This guide shows how to integrate the homelab-infrastructure repository with your other projects.

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
MACHINE_ID=machine1-i9-3090  # or machine2-i9-a5000, machine3-5950x-a4000

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
      service: gpu-compute  # or cpu-intensive, memory-intensive, etc.
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

machine_id = os.environ.get('MACHINE_ID', 'machine1-i9-3090')
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
```

## Project Examples

### CORTEX Integration

```yaml
# CORTEX docker-compose.yml
version: '3.8'

x-machine-profiles:
  development: &development
    file: ${HOMELAB_INFRA_PATH}/machines/machine1-i9-3090/docker/compose-overrides.yml
  
  production: &production
    file: ${HOMELAB_INFRA_PATH}/machines/machine2-i9-a5000/docker/compose-overrides.yml
  
  compute: &compute
    file: ${HOMELAB_INFRA_PATH}/machines/machine3-5950x-a4000/docker/compose-overrides.yml

services:
  n8n:
    extends:
      <<: *${DEPLOYMENT_PROFILE:-development}
      service: base
    # ... rest of n8n config

  ai-model:
    extends:
      <<: *${DEPLOYMENT_PROFILE:-production}
      service: gpu-compute
    # ... AI model config
```

### CI/CD Integration

```yaml
# .github/workflows/deploy.yml
name: Deploy to Homelab

env:
  MACHINE_1_HOST: 192.168.1.100  # From infrastructure repo
  MACHINE_2_HOST: 192.168.1.101
  MACHINE_3_HOST: 192.168.1.102

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
      
      - name: Deploy to Production
        env:
          MACHINE_ID: machine2-i9-a5000
        run: |
          # Use machine-specific configs
          source infrastructure/machines/$MACHINE_ID/deploy-config.sh
          docker-compose up -d
```

### Monitoring Integration

```yaml
# Prometheus config using infrastructure data
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'machine1'
    static_configs:
      - targets: ['192.168.1.100:9100']
        labels:
          machine: 'machine1-i9-3090'
          gpu: 'rtx3090'
          
  - job_name: 'machine2'
    static_configs:
      - targets: ['192.168.1.101:9100']
        labels:
          machine: 'machine2-i9-a5000'
          gpu: 'rtx-a5000'
```

## Best Practices

1. **Never commit machine-specific values** - Always use environment variables
2. **Use relative paths** - Makes projects portable between machines
3. **Document dependencies** - Note which infrastructure components are required
4. **Version compatibility** - Tag infrastructure versions for stability

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
# Test 10GbE connectivity
iperf3 -c <other-machine-ip> -p 5201
```