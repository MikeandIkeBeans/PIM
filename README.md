

# STEMSight PIM – Posture Involuntary Movement detector

Camera-based AI system for detecting abnormal postures and involuntary movements in real time.

![TypeScript](https://img.shields.io/badge/typescript-%23007ACC.svg?style=for-the-badge\&logo=typescript\&logoColor=white)
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge\&logo=python\&logoColor=ffdd54)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge\&logo=amazon-aws\&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge\&logo=fastapi)
![Next JS](https://img.shields.io/badge/Next-black?style=for-the-badge\&logo=next.js\&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge\&logo=supabase\&logoColor=white)
![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge\&logo=react\&logoColor=%2361DAFB)
![OpenCV](https://img.shields.io/badge/opencv-%23white.svg?style=for-the-badge\&logo=opencv\&logoColor=white)

<div align="center">
  <img src="https://github.com/user-attachments/assets/f02eda1f-1769-4865-ad06-d2790e286197" width="350">
</div>

---

# 📌 Table of Contents

* [Overview](#overview)
* [Project Purpose](#project-purpose)
* [Screenshots](#screenshots)
* [System Architecture](#system-architecture)
* [Data Flow](#data-flow)
* [Install & Run](#install--run)
* [Raspberry Pi Setup](#raspberry-pi-setup)
* [Testing Commands](#testing-commands)
* [Deployment (Azure)](#deployment-azure)
* [Project Structure](#project-structure)
* [Troubleshooting](#troubleshooting)
* [Documentation](#documentation)
* [Contributors](#contributors)
* [Licenses](#licenses)

---

# 🧠 **Overview**

STEMSight PIM is a **real-time camera AI system** that detects abnormal postures and involuntary movements.
It uses:

* **Raspberry Pi 4** edge devices
* **MediaPipe Pose** + **custom PyTorch models**
* **FastAPI backend**
* **Next.js monitoring dashboard**
* **Supabase/PostgreSQL database**

The system streams video from ambulances to medical providers and assists with **early detection of neurological distress**.

---

# 🎯 **Project Purpose**

Ambulances often lack continuous neurological monitoring. PIM aims to:

* Detect abnormal postures or involuntary movements in real time
* Provide early warning signs of neurological distress
* Support healthcare providers during patient transport
* Build a synthetic dataset of neurological posture/movement patterns
* Serve as an upstream AI module for NeuroSpring’s Virtual Neurologist

---

# 📸 **Screenshots**

| Feature                 | Preview                                                                                                    |
| ----------------------- | ---------------------------------------------------------------------------------------------------------- |
| **Main Dashboard**      | ![Main Dashboard](https://github.com/user-attachments/assets/77e40c0a-d051-4c48-95ba-0617de66d790)         |
| **Streaming Interface** | ![Streaming Dashboard](https://github.com/user-attachments/assets/14205763-a4fa-4da0-8c4f-c238371a118e)    |
| **Playback**            | ![Playback Page](https://github.com/user-attachments/assets/cfad5bf1-a721-40b5-930b-3903b2cf1ea5)          |
| **Recent Sessions**     | ![Recent Sessions](https://github.com/user-attachments/assets/2af99f77-51ff-4940-b728-af7431655068)        |
| **Supabase ERD**        | ![Edge Stream Simulation](https://github.com/user-attachments/assets/19edfff5-49a9-484f-8240-d7aaad03d763) |
| **Edge Device (RPi4)**  | ![AI Output](https://github.com/user-attachments/assets/3cda8c3a-1f19-468b-acfb-10ee6441a871)              |

---

# 🏗️ **System Architecture**

```
┌─────────────────┐    ┌──────────────────────┐    ┌──────────────────┐
│ Raspberry Pi 4  │    │ FastAPI Backend      │    │ Next.js Frontend │
│ • MediaPipe AI  │───▶│ • ML inference       │───▶│ • AI Dashboard   │
│ • WebRTC Stream │    │ • Supabase DB        │    │ • Live Alerts    │
└─────────────────┘    └──────────────────────┘    └──────────────────┘
```

---

# 🔄 **Data Flow**

1. **RPi 4** captures live video
2. MediaPipe extracts pose → AI model classifies movement
3. Results + stream sent to **FastAPI** (WebRTC/REST)
4. Dashboard displays **real-time detection & analytics**

---

# ⚙️ **Install & Run**

## Backend

```bash
cd Back-End
pip install -r requirements.txt
cp .env.example .env  # add Supabase keys
uvicorn main:app --reload
```

API: [http://localhost:8000/docs](http://localhost:8000/docs)

## Frontend

```bash
cd Front-End
npm install
echo "NEXT_PUBLIC_API_BASE_URL=http://localhost:8000" > .env.local
npm run dev
```

Dashboard: [http://localhost:3000](http://localhost:3000)

---

# 🧲 **Raspberry Pi Setup**

```bash
cd Raspberry-Pi
pip install -r requirements-rpi.txt
python3 pose_model_capture.py --device camera_module
```

Simulated camera streaming:

```bash
python Back-End/Testing_files/broadcaster.py --room test_room --video_device 0
```

---

# 🧪 **Testing Commands**

### Backend Tests

| Test                  | Command                                           |
| --------------------- | ------------------------------------------------- |
| Unit tests            | `pytest`                                          |
| Broadcaster test      | `pytest tests/test_broadcaster.py`                |
| PoseTCN (single view) | `python test_pose_tcn_single_view.py`             |
| Live inference        | `python test_live.py --ckpt <model-path> --T 240` |

### Frontend Tests

```bash
npm run test
npm test -- src/__tests__/Dashboard.test.tsx
```

### Annotated Video Generation

```bash
python annotated_video_generator.py <input> --output out.mp4 --model model.pt
```

---

# ☁️ **Deployment (Azure)**

### Requirements

* Azure CLI
* Python 3.11
* Node 18+

### Backend Deployment

```bash
az group create -n pim-rg -l westus3
az appservice plan create -g pim-rg -n pim-plan --sku B1 --is-linux
az webapp create -g pim-rg -p pim-plan -n fastapibackend --runtime "PYTHON:3.11"
```

Set environment variables:

```bash
az webapp config appsettings set ... SUPABASE_URL=...
```

Deploy:

```bash
az webapp deploy --resource-group pim-rg --name fastapibackend --src-path Back-End --type zip
```

### Frontend Deployment

```bash
az webapp create -g pim-rg -p pim-plan -n nextjsfrontend --runtime "NODE:18LTS"
```

---

# 📁 **Project Structure**

```
PIM/
├── Back-End/        # FastAPI app + models
├── Front-End/       # Next.js dashboard
├── Raspberry-Pi/    # Edge device AI + streaming
└── .github/         # Dev guidelines
```

---

# 🛠️ **Troubleshooting**

| Issue                 | Fix                               |
| --------------------- | --------------------------------- |
| CORS errors           | Add frontend URL to backend CORS  |
| WebRTC not connecting | Enable WebSockets, use HTTPS      |
| FFmpeg missing        | Install or bundle a static binary |
| Supabase auth fails   | Re-check `.env` variables         |

---

# 📚 **Documentation**

* Backend: `Back-End/README.md`
* Frontend: `Front-End/README.md`
* Raspberry Pi: `Raspberry-Pi/README.md`
* Developer guidelines: `.github/instructions/`

---

# 👥 **Contributors**


| Name             | Contact                                                     |
| ---------------- | ----------------------------------------------------------- |
| Mike Feschenko   | [mikefeschenko@csus.edu](mailto:mikefeschenko@csus.edu)     |
| Ian Anderson     | [ima34@csus.edu](mailto:ima34@csus.edu)                     |
| Nguyen Phuc Tran | [nguyenphuctran@csus.edu](mailto:nguyenphuctran@csus.edu)   |
| Xeng Feng        | [xiangfeng@csus.edu](mailto:xiangfeng@csus.edu)             |
| Faith Montemayor | [faithmontemayor@csus.edu](mailto:faithmontemayor@csus.edu) |
| Antonio Graci    | [agraci@csus.edu](mailto:agraci@csus.edu)                   |
| Corbin West      | [corbinwest@csus.edu](mailto:corbinwest@csus.edu)           |
| Pablo Hernandez  | [phernandez4@csus.edu](mailto:phernandez4@csus.edu)         |

---

# 📜 **Licenses**

* MediaPipe – Apache 2.0
* Supabase – Apache 2.0
* OpenCV – Apache 2.0
* PyTorch – BSD

---
