<div align="center">

<img src="https://img.shields.io/badge/Smart%20India%20Hackathon-2025-orange?style=for-the-badge&logo=india" />
<img src="https://img.shields.io/badge/Team-HackOps-blue?style=for-the-badge" />
<img src="https://img.shields.io/badge/Problem%20ID-SIH25002-green?style=for-the-badge" />

# 🏛️ Smart Tourism Safety System

### AI-powered tourist safety with Geo-Fencing, Real-time Alerts & Blockchain Digital Identity

*Smart India Hackathon 2025 · Theme: Travel & Tourism · Category: Software*

---

[🚀 Live Demo](#) · [📱 Download APK](#) · [📖 Docs](docs/) · [🎯 Problem Statement](#problem-statement)

</div>

---

## 🎯 Problem Statement

> **SIH25002 — Smart Tourism**
>
> Tourists in India—especially solo travelers, women, and foreign visitors—face safety challenges in unfamiliar places with limited access to emergency services, language barriers, and no real-time incident awareness.

---

## 💡 Our Solution

A unified safety platform that combines:

| 🤖 AI Monitoring | 📍 Geo-Fencing | 🔗 Blockchain ID |
|---|---|---|
| TensorFlow Lite anomaly detection + OpenCV CCTV analysis | Real-time zone alerts + safe route suggestions | Tamper-proof DID via Polygon ID / Hyperledger Aries |

**Unique Edge:** The only system that integrates AI + Geo-Fencing + Blockchain in one proactive, scalable tourist safety stack.

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────┐
│               Tourist Mobile App (Flutter)                │
│         Real-time location  •  SOS  •  Safe Map          │
└─────────────────────┬────────────────────────────────────┘
                      │ WebSocket (GPS every 10m)
                      ▼
┌──────────────────────────────────────────────────────────┐
│               FastAPI Backend (Python)                    │
│   REST API  •  WebSocket server  •  Geofence engine       │
└────┬──────────────────┬──────────────────┬───────────────┘
     │                  │                  │
     ▼                  ▼                  ▼
 PostgreSQL           Kafka          Polygon ID /
 + PostGIS         Event Bus        Aries Agent
     │                  │            (Blockchain DID)
     │           ┌──────┘
     │           ▼
     │    Alert Consumer
     │    FCM Push → Responders
     ▼
 ML Service
 TFLite + OpenCV
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| 📱 Mobile | Flutter 3.x (Android + iOS) |
| ⚙️ Backend | FastAPI (Python 3.11) |
| 🗄️ Database | PostgreSQL 15 + PostGIS |
| 📡 Streaming | Apache Kafka |
| 🧠 AI / ML | TensorFlow Lite · OpenCV |
| 🔗 Blockchain | Hyperledger Aries · Polygon ID (W3C DID) |
| 🗺️ Maps | OpenStreetMap + flutter_map |
| 🚀 Deploy | Docker Compose · Nginx |

---

## 📁 Project Structure

```
smart-tourism/
├── 📂 backend/
│   ├── main.py                   # FastAPI app + WebSocket server
│   ├── models.py                 # DB models (Tourist, Alert, Zone, Responder)
│   ├── requirements.txt
│   ├── Dockerfile
│   ├── 📂 routes/
│   │   ├── tourists.py           # Registration + DID issuance
│   │   ├── sos.py                # SOS trigger + nearest responders
│   │   ├── alerts.py             # Alert management
│   │   ├── geofence.py           # Zone CRUD
│   │   └── responders.py        # Responder management
│   ├── 📂 services/
│   │   ├── geofence_service.py   # Shapely polygon intersection
│   │   ├── did_service.py        # Blockchain DID create / verify
│   │   ├── kafka_producer.py     # Event publisher
│   │   └── routing_service.py   # Safe route via GraphHopper
│   └── 📂 consumers/
│       └── alert_consumer.py    # Kafka → push notifications
│
├── 📂 frontend/
│   ├── pubspec.yaml
│   └── 📂 lib/
│       ├── main.dart
│       ├── 📂 screens/
│       │   ├── home_screen.dart
│       │   ├── map_screen.dart   # Live safety map with zone overlays
│       │   ├── sos_screen.dart   # One-touch SOS
│       │   └── profile_screen.dart
│       ├── 📂 services/
│       │   ├── websocket_service.dart
│       │   ├── geofence_service.dart
│       │   └── sos_service.dart
│       └── 📂 providers/         # Riverpod state management
│
├── 📂 ml/
│   └── anomaly_detection.py      # TFLite + OpenCV analysis
│
├── 📂 docs/
│   ├── api.md                    # API reference
│   └── architecture.md          # System design deep-dive
│
├── docker-compose.yml
└── README.md
```

---

## ⚡ Quick Start

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Flutter SDK ≥ 3.2](https://flutter.dev/docs/get-started/install)
- Python 3.11+ (for local dev without Docker)

### 1️⃣ Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/smart-tourism.git
cd smart-tourism
```

### 2️⃣ Configure environment

```bash
cp backend/.env.example backend/.env
# Edit backend/.env with your settings
```

### 3️⃣ Start all backend services

```bash
docker-compose up -d
```

| Service | URL |
|---|---|
| API | http://localhost:8000 |
| Swagger Docs | http://localhost:8000/docs |
| Kafka UI | http://localhost:8080 |
| Aries Agent | http://localhost:8021 |

### 4️⃣ Run the Flutter app

```bash
cd frontend
flutter pub get
flutter run
```

---

## 🔑 Key Flows

### Tourist Registration + DID Issuance

```
POST /api/tourists/register
  → Hashes passport + email → did:polygon:<hash>
  → Stores W3C DID Document in PostgreSQL
  → Returns { tourist_id, did }
```

### Real-time Geo-Fencing

```
Flutter sends GPS every 10m via WebSocket
  → Backend checks point against PostGIS zone polygons
  → Danger zone hit? → WebSocket alert to tourist
                     → Kafka event → responder push notification
                     → Safe route suggestion returned
```

### SOS Trigger

```
POST /api/sos/trigger  { tourist_id, lat, lon, audio_url }
  → Verify DID on-chain
  → Find 3 nearest responders (PostGIS distance sort)
  → Publish to Kafka sos-alerts topic
  → Consumer sends FCM push + SMS to police / hospital
```

### AI Anomaly Detection

```
AnomalyDetectionService tracks last 10 GPS points per tourist
  → TFLite model scores movement pattern (0–1)
  → Rule fallback: stationary > 30 min OR speed > 200 km/h
  → Score > 0.75 → trigger welfare check alert
```

---

## 📡 API Reference

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/tourists/register` | Register tourist + generate DID |
| `GET` | `/api/tourists/{id}` | Get tourist profile |
| `GET` | `/api/tourists/{id}/did` | Get DID + DID Document |
| `POST` | `/api/sos/trigger` | Trigger SOS alert |
| `GET` | `/api/alerts` | List all alerts |
| `POST` | `/api/geofence/zones` | Create a geofence zone |
| `GET` | `/api/geofence/zones` | List active zones |
| `WS` | `/ws/location/{tourist_id}` | Real-time GPS stream |

Full API docs → [docs/api.md](docs/api.md)

---

## 🌍 Impact & Benefits

- 🛡️ **Improved Safety** — Real-time alerts reduce risk for tourists
- ⚡ **Faster Response** — AI + geo-fencing enables sub-minute incident detection
- 🔒 **Secure Identity** — Blockchain DID prevents identity fraud
- 📈 **Tourism Growth** — Safer destinations attract more visitors
- 📊 **Data Insights** — Crowd analytics for infrastructure planning

---

## 🚧 Challenges & How We Solved Them

| Challenge | Solution |
|---|---|
| Connectivity in remote areas | Offline GPS buffering + auto-sync on reconnect |
| Data privacy | End-to-end encryption + DID (user owns their identity) |
| User adoption | Multilingual UI, minimal onboarding (just scan passport) |
| Authority coordination | MoU integration templates + REST webhook support |

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

## 👥 Team HackOps

*Smart India Hackathon 2025 · Problem ID: SIH25002*

---

<div align="center">
Made with ❤️ for safer tourism in India
</div>
