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
 
