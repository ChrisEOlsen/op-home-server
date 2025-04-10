#### 1. Install Cloudflare and get domain
#### 2. Go to Zero Trust > Network > Tunnels and create a tunnel. 
This tunnel creation will generate the CNAME record automatically. 
IMPORTANT SETTINGS:
  Make the target URL: https://localhost:3080 and enable TLS No Verify (Important)
  For Teleport proxy to work, you must set the HTTP HOST HEADER setting to whatever that domain is.

#### 3. Run this command on your Linux host hiding behind CGNAT
This whole command can be found in your Zero Trust dashboard after creating the tunnel, and will include the token.
```bash
curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && 

sudo dpkg -i cloudflared.deb && 

sudo cloudflared service install your_token_will_be_here
```
NOTE: Keep this token super safe, and ideally do not save it anywhere. It will always be there on your cloudflare dashboard.

#### 4. Download Teleport locally (the docker version is no good unless you enjoy not being able to run commands on the container)
Community Version One-Liner
```bash
curl https://cdn.teleport.dev/install.sh | bash -s 17.4.2
```
#### 5. Add the included teleport.yaml file to your /etc folder 
Ensure your make any changes necessary for your personal setup

#### 6. Start Teleport!
```bash
sudo systemctl enable teleport
sudo systemctl start teleport
```

#### Once downloaded Teleport successfully
Double check that you have tctl and tsh installed.
```bash
tsh version
tctl version
```
#### Create admin.yaml file to define admin role and rules
```yaml
kind: role
version: v5
metadata:
  name: admin
spec:
  allow:
    logins: ['root', 'chris']
    node_labels:
      '*': '*'
    app_labels:
      '*': '*'
    rules:
      - resources: ['*']
        verbs: ['*']
```
#### Apply the role and create the user
In the same directory where you created the admin.yaml file, run these commands.
```bash
sudo tctl create -f admin-role.yaml
sudo tctl users add your_name --roles=admin --logins=root,your_name
```
Follow the link and then create your credentials with your OTP device/app and
VOILA! 
\n
Happy Teleporting
