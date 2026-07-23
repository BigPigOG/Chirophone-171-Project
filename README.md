# Cinephone: Air Piano
Cinephone is an interactive browser-based air piano that uses real-time webcam hand tracking to turn finger movements into musical notes. Users hold their hands in front of the camera and curl individual fingers to play notes from C4 to C5.

## Project Information

| Project detail | Information |
|---|---|
| Student | Marwan Iqbal |
| Student ID | 36054904 |
| Unit | ICT171 Introduction to Server Environments and Architectures |
| Assignment | Cloud Server Project and Video Explainer — 2026 |
| Live website | [https://cinephone.sytes.net](https://cinephone.sytes.net) |
| Public IP address | `20.222.194.146` |
| GitHub repository | `a` |
| Video explainer | `a` |
| Cloud platform | Microsoft Azure |
| Deployment model | Infrastructure as a Service |
| Operating system | Ubuntu Server 22.04 LTS |
| Web server | Apache2 |
| DNS provider | No-IP |
| TLS certificate | Let's Encrypt through Certbot |

## Project Overview

Cinephone uses the user's webcam to detect hand landmarks in real time. The application tracks four fingers on each hand and plays a musical note when a finger moves from an extended position to a curled position.

The application uses MediaPipe Hands to detect hand landmarks and Tone.js to generate audio in the browser. An HTML canvas is placed over the webcam feed to display tracking dots on the fingertips.

The website is hosted on a manually configured Ubuntu virtual machine in Microsoft Azure. Apache2 serves the website, No-IP provides the public hostname, and Let's Encrypt manages the HTTPS certificate.

## Key Features

- Real-time webcam hand tracking
- Support for two hands
- Eight playable notes from C4 to C5
- Custom finger-curl detection
- Fingertip tracking indicators
- On-screen piano key feedback
- Musical note notifications
- Camera-position onboarding prompt
- Responsive browser interface
- HTTPS-secured camera access
- No client-side installation required

## How the Application Works

MediaPipe Hands provides 21 landmark coordinates for every detected hand.

Cinephone monitors the following fingers:

- Index
- Middle
- Ring
- Pinky

For each finger, the application compares the distance between the fingertip and the wrist with the distance between the finger's middle joint and the wrist.

When the fingertip moves sufficiently close to the wrist, the finger is classified as curled. A note is played only when the finger changes from extended to curled, preventing the note from repeatedly playing while the finger remains bent.

## Note Mapping

| Hand | Finger | Note |
|---|---|---|
| Left | Pinky | C4 |
| Left | Ring | D4 |
| Left | Middle | E4 |
| Left | Index | F4 |
| Right | Index | G4 |
| Right | Middle | A4 |
| Right | Ring | B4 |
| Right | Pinky | C5 |

Note that some users might experience the opposite, as the webcam feed might be mirrored depending on their device.

## System Architecture

```text
User webcam
     |
     v
MediaPipe Hands
     |
     v
Hand landmark coordinates
     |
     v
Custom JavaScript gesture detection
     |
     +------------------------+
     |                        |
     v                        v
Tone.js audio            Canvas overlay
     |                        |
     v                        v
Musical note             Fingertip dots
```

# Cloud Server Deployment

## Prerequisites

The following were required before beginning the deployment:

- Microsoft Azure account
- Azure student subscription
- Local terminal with SSH support
- Cinephone website files
- No-IP account
- Email address for Let's Encrypt
- Basic familiarity with Linux commands

## 1. Azure Virtual Machine Configuration

The Cinephone server was deployed using Microsoft Azure Infrastructure as a Service.

| Setting | Configuration |
|---|---|
| Resource group | `Rgroup` |
| Region | Japan East |
| Image | Ubuntu Server 22.04 LTS |
| VM size | Standard B2ats v2 |
| Virtual CPUs | 2 |
| Memory | 1 GiB |
| Authentication | Password-based SSH |
| Inbound ports | 22, 80 and 443 |

### VM Creation Process

1. Signed in to the Microsoft Azure Portal.
2. Created a resource group named `Rgroup`.
3. Created a new Azure virtual machine.
4. Selected Ubuntu Server 22.04 LTS.
5. Selected Standard B2ats v2 as the VM size.
6. Created an administrator username and password.
7. Enabled password-based SSH authentication.
8. Completed the virtual machine deployment.
9. Recorded the public IP address from the VM Overview page.

The server was administered entirely through SSH and command-line tools. No graphical desktop environment was installed on the virtual machine.

## 2. Network Security Group Rules

Azure created a Network Security Group for the virtual machine.

The following inbound rules were confirmed or added:

| Priority | Name | Port | Protocol | Source | Action |
|---|---|---:|---|---|---|
| 300 | SSH | 22 | TCP | Any | Allow |
| 320 | HTTP | 80 | TCP | Any | Allow |
| 340 | HTTPS | 443 | TCP | Any | Allow |

### Port Purposes

- Port 22 allows remote administration through SSH.
- Port 80 allows standard HTTP traffic and Let's Encrypt validation.
- Port 443 allows encrypted HTTPS traffic.

## 3. Connecting to the Server

The server was accessed from a local terminal using SSH:

```bash
ssh <username>@<public-ip>
ex: marwanuser@20.222.194.146
```

## 4. Web Server Installation

Apache2 was installed to serve the Cinephone website over HTTP and, later, HTTPS.

```bash
sudo apt update
sudo apt install apache2 -y
```

### Verification

```bash
sudo systemctl status apache2
```

Expected output includes `Active: active (running)`. The default Apache landing page was confirmed by visiting the VM's public IP address in a browser before any custom content was deployed.

## 5. DNS Configuration

Cinephone uses **No-IP**, a free dynamic DNS provider, to map a human-readable hostname to the VM's public IP address rather than relying on the raw IP.

| Setting | Configuration |
|---|---|
| Provider | No-IP |
| Hostname | `cinephone.sytes.net` |
| Record type | A record (hostname → public IP) |

### Steps

1. Created a free account at [noip.com](https://www.noip.com/).
2. Registered the hostname `cinephone.sytes.net`.
3. Pointed the hostname at the virtual machine's public IP address through the No-IP dashboard.
4. Confirmed DNS resolution from the server itself:

```bash
nslookup cinephone.sytes.net
```

The output confirmed the hostname correctly resolves to the VM's public IP address.

> **Note:** No-IP's free hostnames are a static mapping rather than an auto-updating client. If the VM's public IP address changes (for example, after a VM restart, since this deployment does not use a static IP), the No-IP hostname record must be manually updated to point at the new address.

## 6. SSL/TLS Configuration

HTTPS is provided using a free certificate from **Let's Encrypt**, issued and managed through the Certbot client.

```bash
sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d cinephone.sytes.net
```

Certbot requested a certificate scoped to `cinephone.sytes.net`, updated the Apache virtual host configuration to serve traffic over HTTPS, and configured an automatic redirect from HTTP to HTTPS.

### Verification

```bash
sudo apache2ctl configtest      # confirms the Apache configuration syntax is valid
sudo systemctl reload apache2   # applies the updated configuration
```

Visiting `https://cinephone.sytes.net` shows a valid certificate with no browser warnings.

### Renewal

Let's Encrypt certificates are valid for 90 days. Certbot installs a systemd timer that renews certificates automatically before expiry. This was tested with:

```bash
sudo certbot renew --dry-run
```

## 7. Website Deployment

The Cinephone front end — a single HTML file containing the interface, hand-tracking logic, and audio engine — was deployed directly to Apache's web root.

```bash
sudo nano /var/www/html/index.html
```

The existing contents were replaced with the Cinephone application code, then saved. No build step or server-side framework is required, since the application is a static file that loads MediaPipe Hands and Tone.js from a CDN at runtime and performs all processing client-side in the browser.

### Verification

Visited `https://cinephone.sytes.net` directly and confirmed:
- The page loads over HTTPS with no certificate warnings
- The camera permission prompt appears correctly
- Hand landmarks are detected and fingertip indicators render on screen
- Curling a finger triggers the correct musical note

## 8. Script Component

The core script for this project is the **finger-curl detection algorithm**, written in JavaScript and embedded directly in the Cinephone front end.

```js
// Detects whether a finger is curled by comparing how far the fingertip
// sits from the wrist versus how far the middle knuckle (pip joint) sits
// from the wrist. A straight finger has its tip as the farthest point
// from the wrist; a curled finger's tip moves closer to the wrist than
// the knuckle beneath it. Comparing distances (rather than raw screen
// coordinates) keeps this working regardless of hand rotation or tilt.
function isFingerCurled(landmarks, finger) {
  const wrist = landmarks[0];
  const { tip, pip } = FINGER_LANDMARKS[finger];
  return dist(landmarks[tip], wrist) < dist(landmarks[pip], wrist) * 0.92;
}
```

This function is called once per tracked finger, per hand, on every processed video frame. Combined with an edge-detection check (comparing the current curl state against the previous frame's state), it ensures a note triggers exactly once per finger bend, rather than repeatedly firing while the finger is held curled.

### Verifiable output

The output of this script can be verified independently by visiting **[https://cinephone.sytes.net](https://cinephone.sytes.net)**, allowing camera access, and curling any finger. The corresponding note name appears immediately as an on-screen toast, and the matching key in the on-screen piano strip highlights — providing direct visual confirmation that the detection logic executed correctly and produced the expected mapped output.

## 9. Final Verification Checklist

| Check | Status |
|---|---|
| VM accessible via SSH | ✅ |
| Apache2 running | ✅ |
| Site resolves via `cinephone.sytes.net` | ✅ |
| HTTPS certificate valid, no warnings | ✅ |
| Camera permission prompt functions correctly | ✅ |
| Hand tracking detects landmarks in real time | ✅ |
| Finger curl correctly triggers mapped note | ✅ |
