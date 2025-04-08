### 1. Install Cloudflare and get domain
### 2. Go to dashboard and create a tunnel. This tunnel creation will generate the CNAME record automatically. Make the target URL: https://localhost:443
### 3. Run this command on your Linux host hiding behind CGNAT 
```bash
curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && 

sudo dpkg -i cloudflared.deb && 

sudo cloudflared service install your_token_will_be_here
```
NOTE: Keep this token super safe, and ideally do not save it anywhere. It will always be there on your cloudflare dashboard.

### 4. Download Teleport locally (the docker version is no good unless you enjoy not being able to run commands on the container)
Community Version One-Liner
```bash
curl https://cdn.teleport.dev/install.sh | bash -s 17.4.2
```
### 5. Add the included teleport.yaml file to your /etc folder (make any changes necessary for your personal setup)

### Start Teleport!
```bash
sudo systemctl enable teleport
sudo systemctl start teleport
```


### Once downloaded Teleport successfully
```bash
tctl version
```
### Create admin.yaml file to define admin role and rules
```yaml
kind: role
version: v5
metadata:
  name: admin
spec:
  allow:
    logins: ['root', 'chris']  # Replace 'chris' with your Linux username
    node_labels:
      '*': '*'
    rules:
      - resources: ['*']
        verbs: ['*']
```
### Apply the role and create the user
```bash
sudo tctl create -f admin-role.yaml
sudo tctl users add chris --roles=admin --logins=root,chris
```
Follow the link and then create your credentials with the OTP device
VOILA! Happy Teleporting
