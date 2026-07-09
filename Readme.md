
docker compose up -d
All three VMs are reachable via SSH — the daemons are responding on
    their ports (2220/2222/2224), just requiring the password login as
    configured.
    
    Here's the summary:
    
    3 Ubuntu VMs — all running and healthy
    
    VM: Ubuntu 20.04
    Image: ubuntu-vm:20.04
    SSH Port: localhost:2220
    User/Pass: dev/dev
    Root/Pass: root/ubuntu
    ────────────────────────────────────────
    VM: Ubuntu 22.04
    Image: ubuntu-vm:22.04
    SSH Port: localhost:2222
    User/Pass: dev/dev
    Root/Pass: root/ubuntu
    ────────────────────────────────────────
    VM: Ubuntu 24.04
    Image: ubuntu-vm:24.04
    SSH Port: localhost:2224
    User/Pass: dev/dev
    Root/Pass: root/ubuntu
    
    The original error was Docker trying to pull ubuntu-vm from Docker
    Hub instead of building locally. The compose file has build:
    sections pointing to local Dockerfiles, so docker compose build
    was needed first. Everything's working now.
    
    To SSH in:
    
    ssh dev@localhost -p 2220   # Ubuntu 20.04
    ssh dev@localhost -p 2222   # Ubuntu 22.04
    ssh dev@localhost -p 2224   # Ubuntu 24.04
    
    (password: dev)
    
    To tear it all down:
    
    sudo docker compose down
