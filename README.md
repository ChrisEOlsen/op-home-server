### Create the teleport folders
```bash
mkdir -p teleport/config teleport/data
```

### Ensure TUNNEL_TOKEN is retrieved from Cloudflare dashboard
```bash
export TUNNEL_TOKEN=your_tunnel_token_here
```

### Setup swarm mode
```bash
docker swarm init
```

### Create an overlay network (Swarm)
```bash
docker network create --driver overlay teleport-overlay-network
```

### Create a role:
```bash
docker exec -i teleport tctl create -f - <<'EOF'
kind: role
version: v4
metadata:
  name: admin
spec:
  options:
    max_session_ttl: 24h
  allow:
    logins: ['chris', 'root', 'ubuntu']
    node_labels:
      '*': '*'
    rules:
      - resources: ['*']
        verbs: ['*']
EOF
```

### Add the admin user:
```bash
docker exec -i teleport tctl users add chris --roles=admin
```

### Launch
```bash
docker stack deploy -c docker-compose.yml teleport_stack
```

### Doing this on an old linux laptop?
#### Keep awake while lid is closed:
```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```