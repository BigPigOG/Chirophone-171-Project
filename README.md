# Chirophone - Air Piano
**ICT171 Could Server Project - 2026**  
**Name:** Marwan Iqbal  
**SID:** 36054904  

**Live site:** https://cinephone.sytes.net

## Pre-Requisites
- Microsoft Azure Account: Mine was a Student Account.
- Lots of time if you're new to this.

## 1. Virtual Machine Setup
The server is hosted on **Microsoft Azure** using Infrastructure as a Service (IaaS).
 
| Property | Value |
|---|---|
| Resource group | `Rgroup` |
| Region | Japan East |
| OS image | Ubuntu Server 22.04 LTS |
| VM size | Standard B2ats v2 (2 vCPUs, 1 GiB memory) |
| Authentication | Password-based SSH login |

### Steps
1. Created a new resource group in the Azure Portal.
2. Deployed a new VM using the Ubuntu Server 22.04 LTS image, size Standard B2ats v2.
3. Configured a username and password for SSH authentication during VM creation.
4. Noted the VM's assigned public IP address, shown on the VM's **Overview** page in the Azure Portal.
   
   
## 2. Network Security Group (Firewall Rules)
 
Azure automatically provisions a Network Security Group (NSG) alongside the VM, controlling inbound and outbound traffic.
 
The following inbound rules were confirmed/added under **VM → Networking → Inbound port rules**:
 
| Priority | Name | Port | Protocol | Source | Action |
|---|---|---|---|---|---|
| 300 | SSH | 22 | TCP | Any | Allow |
| 320 | HTTP | 80 | TCP | Any | Allow |
| 340 | HTTPS | 443 | TCP | Any | Allow |
 
HTTP and HTTPS were required to serve the website publicly; SSH was required for remote administration.
 
## 3. Connecting to the VM
 
Connected from a local machine via SSH, using the VM's public IP address:
```bash
ssh <username>@<public-ip>
```
On first connection, the SSH client prompts to confirm the host fingerprint — this is expected for a new server and was accepted before entering the account password.
 
## 4. Web Server Installation
 
**Apache HTTP Server** was installed to serve the website.
 
```bash
sudo apt update
sudo apt install apache2 -y
```
 
Verified the service was running:
```bash
sudo systemctl status apache2
```
Expected output includes `Active: active (running)`.
 
Confirmed the server was publicly reachable by visiting the VM's public IP address in a browser and seeing the default Apache landing page.

 ## 5. DNS Setup
 
Rather than relying on the VM's raw IP address, DNS was configured through **[No-IP](https://www.noip.com/)**, a free dynamic DNS provider.
 
- **Domain:** `cinephone.sytes.net`
- **Points to:** the Azure VM's public IP address
### Steps
1. Created a free No-IP account and registered the hostname `cinephone.sytes.net`.
2. Pointed the hostname at the VM's public IP address through the No-IP dashboard.
3. Confirmed DNS resolution from the VM itself:
```bash
   nslookup cinephone.sytes.net
```
   The output confirmed the hostname correctly resolves to the VM's public IP address.
 
