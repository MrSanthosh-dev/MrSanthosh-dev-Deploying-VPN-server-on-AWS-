# MrSanthosh-dev-Deploying-VPN-server-on-AWS-

# Procedure :
```
Step 1 — Sign in to AWS
1. Go to AWS Management Console
2. Log in with your AWS account
3. Search EC2 in the search bar
4. Open EC2 Dashboard

Step 2 — Launch a new EC2 instance
1. Click Launch Instance
2. Enter Instance Name (example: vpn-server)
3. Choose OS image:
○ Select Ubuntu Server 22.04 LTS (Free tier eligible)

Step 3 — Choose instance type
1. Select t2.micro (Free tier eligible)
2. Click Next / Configure instance details (or continue in new UI)

Step 4 — Create Key Pair (Very Important)
1. Click Create new key pair
2. Name it (example: vpn-key)
3. Key pair type → RSA
4. File format:
○ .pem if using Linux/Mac
○ .ppk if using Windows + PuTTY
5. Download and save safely
If you lose the key pair, you cannot log in later.

Step 5 — Configure Network / Security Group
1. Allow SSH (port 22) from:
○ Your IP (recommended)
○ Or Anywhere (0.0.0.0/0) — less secure but works for testing
2. Add rule for VPN depending on protocol:
○ IKEv2 → UDP 500, 4500
3. Optionally add:
○ ICMP (Ping) for testing

Step 6 — Storage settings
Default 8 GB is fine
Increase only if needed

Step 7 — Launch the instance
Click Launch Instance

Step 8 — Get Public IP
1. Go to EC2 → Instances
2. Select your instance
3. Copy Public IPv4 address
Since you said No Elastic IP used, public IP will change if
rebooted.

Step 9 — Connect to instance and update server
sudo apt update && sudo apt upgrade -y

Step 10 — Proceed with VPN setup
From here you executed your VPN commands such as:
● installing strongSwan / ipsec tools
● certificate creation
● configuration
● testing via Android
Commands :
1. Update packages
sudo apt update
Checks for latest package lists from repositories.
2. Install strongSwan (IKEv2 VPN)
sudo apt install strongswan strongswan-pki -y
Installs:
4 VPN service strongSwan
5 PKI tools for certificates (even though you used PSK)
3. Open and edit ipsec.con
sudo nano /etc/ipsec.conf
Used to configure VPN server settings, including:
5 IKE version
6 encryption
7 tunnel mode
8 subnets
9 split/full tunnel decision
You added this working config:
config setup
charondebug="ike 1, knl 1, cfg 0"
conn ikev2-vpn
auto=add
compress=no
type=tunnel
keyexchange=ikev2
ike=aes256-sha256-modp2048!
esp=aes256-sha256!
left=%any
leftid=YOUR_PUBLIC_IP
authby=psk
leftsubnet=0.0.0.0/0 (full tunnel earlier)
right=%any
rightauth=psk
rightsourceip=10.10.10.0/24
dpdaction=clear
dpddelay=300s
rekey=no
Later you changed leftsubnet to split-tunnel — also correct.
4. Edit VPN shared secret file
sudo nano /etc/ipsec.secrets
File that stores:
2 PSK key
3 RSA key (if used)
4 user credentials (for EAP mode)
5. Verify contents of the secrets file
sudo cat /etc/ipsec.secrets
You confirmed it looked like:
YOUR_PUBLIC_IP : PSK "Private shared key"
6. Restart strongSwan service

sudo systemctl restart strongswan-starter
Applies new configuration.
(You did this several times — good troubleshooting.)
7. Check strongSwan service status
sudo systemctl status strongswan-starter
Used to verify:
● service running
● no syntax errors
● daemon started successfully
8. Live view of VPN logs (very important)
sudo journalctl -fu strongswan-starter
This is the command that showed:
received proposals unacceptable
Which led to correcting proposals in ipsec.conf

```
# Output:

<img width="1920" height="883" alt="image" src="https://github.com/user-attachments/assets/e6a482a7-feca-4587-a36d-a9ca0cc93f5b" />


<img width="1920" height="879" alt="image" src="https://github.com/user-attachments/assets/38d031ee-8bc6-40d3-ab80-9ec80aed9d00" />



<img width="1920" height="886" alt="image" src="https://github.com/user-attachments/assets/d1811f3c-1a4d-4b29-9c78-b57cacacc6b4" />


<img width="1300" height="260" alt="image" src="https://github.com/user-attachments/assets/c3889163-e42f-46ff-9ab9-d8facfbdb9d8" />


<img width="1920" height="704" alt="image" src="https://github.com/user-attachments/assets/5210a780-6f1b-4cb9-82f8-381e9a9b2eef" />



<img width="1920" height="771" alt="image" src="https://github.com/user-attachments/assets/a18a6e2b-2dcf-4d9c-9870-68eb40be4fc6" />


<img width="270" height="493" alt="image" src="https://github.com/user-attachments/assets/400f1c2d-5b6b-4fa8-a5f1-eef85a0b1676" />


<img width="488" height="515" alt="image" src="https://github.com/user-attachments/assets/5922d001-682e-410d-8e8c-bca6e290277d" />


