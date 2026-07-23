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


