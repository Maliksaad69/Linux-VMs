# Ubuntu CIS Assessment Targets

9 Ubuntu containers configured as SSH-accessible VM-like targets for CIS benchmark assessment and security auditing.

## Quick Start

```bash
# Build and start all containers
docker compose up -d --build

# Stop and remove all containers. This is important. If you donot remove containers with their volumes. It will consume a lot of space. 
docker compose down -v

# Rebuild a specific container
docker compose build ubuntu20-optim
```

## SSH Access

Each container maps SSH (port 22) to a unique host port. All use the same credentials.

| Container        | Host | Port | SSH Command                     |
|------------------|------|------|---------------------------------|
| ubuntu20-base    | localhost | 2226 | `ssh dev@localhost -p 2226` |
| ubuntu20-optim   | localhost | 2220 | `ssh dev@localhost -p 2220` |
| ubuntu20-mix     | localhost | 2221 | `ssh dev@localhost -p 2221` |
| ubuntu22-base    | localhost | 2227 | `ssh dev@localhost -p 2227` |
| ubuntu22-optim   | localhost | 2222 | `ssh dev@localhost -p 2222` |
| ubuntu22-mix     | localhost | 2223 | `ssh dev@localhost -p 2223` |
| ubuntu24-base    | localhost | 2228 | `ssh dev@localhost -p 2228` |
| ubuntu24-optim   | localhost | 2224 | `ssh dev@localhost -p 2224` |
| ubuntu24-mix     | localhost | 2225 | `ssh dev@localhost -p 2225` |

## Credentials

| User  | Password | Notes                 |
|-------|----------|-----------------------|
| dev   | dev      | sudo NOPASSWD access  |
| root  | ubuntu   | direct root login     |

## Variants

| Variant | Description |
|---------|-------------|
| **base**   | Minimal Ubuntu with SSH and basic tools |
| **optim**  | Optimized/hardened configuration |
| **mix**    | Mixed configuration (some hardened, some default) |

## Network

- All containers share a `vm-net` bridge network (172.20.0.0/16)
- Static IPs assigned per container (172.20.0.20–172.20.0.28)
- Containers can reach each other by internal IP or hostname

## Volume Mounts

Each container has a named volume mounted at `/home/dev` to persist the home directory across container restarts.
