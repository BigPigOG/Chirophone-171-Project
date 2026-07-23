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
