# 🍯 IoT Honeypot

> Sistem za privlačenje, beleženje i analizu napada na IoT uređaje u realnom vremenu.

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20Raspberry%20Pi-orange)

---

## 📋 Sadržaj

- [O projektu](#-o-projektu)
- [Funkcionalnosti](#-funkcionalnosti)
- [Arhitektura](#-arhitektura)
- [Instalacija](#-instalacija)
- [Konfiguracija](#-konfiguracija)
- [Pokretanje](#-pokretanje)
- [Dashboard](#-dashboard)
- [Struktura projekta](#-struktura-projekta)
- [Tehnologije](#-tehnologije)
- [Bezbednosne napomene](#-bezbednosne-napomene)
- [Licenca](#-licenca)

---

## 🎯 O projektu

**IoT Honeypot** je sistem koji simulira ranjive IoT uređaje (IP kamera, ruter, pametni termostat) kako bi privukao potencijalne napadače. Svaki pokušaj pristupa se beleži i analizira, pružajući uvid u aktuelne metode napada na IoT infrastrukturu.

### Zašto Honeypot?

Većina IoT uređaja ima:
- Podrazumevane kredencijale (`admin:admin`, `root:root`)
- Nezakrpljene ranjivosti
- Nezaštićene portove (Telnet, SSH, HTTP)

Ovaj projekat koristi te slabosti kao mamac i **beleži ko, kada i kako pokušava da napadne**.

---

## ✨ Funkcionalnosti

- 🖥️ **Simulacija uređaja** — lažna kamera, ruter i termostat
- 🔒 **Praćenje Telnet/SSH/HTTP** pokušaja prijavljivanja
- 📊 **Real-time dashboard** sa vizualizacijom napada
- 🗺️ **Geolokacija napadača** na interaktivnoj mapi
- 📧 **Alert sistem** — email/Telegram notifikacije
- 📁 **Detaljni logovi** u JSON formatu
- 🧠 **ML detekcija anomalija** — prepoznaje automatizovane botove
- 🐳 **Docker podrška** za jednostavno pokretanje

---

## 🏗️ Arhitektura

```
                  ┌─────────────────────┐
                  │     INTERNET        │
                  │   (Napadači 👾)     │
                  └────────┬────────────┘
                           │
                  ┌────────▼────────────┐
                  │   IoT Honeypot      │
                  │  ┌───────────────┐  │
                  │  │ Telnet :23    │  │
                  │  │ SSH    :22    │  │
                  │  │ HTTP   :80    │  │
                  │  │ MQTT   :1883  │  │
                  │  └───────┬───────┘  │
                  │          │          │
                  │  ┌───────▼───────┐  │
                  │  │  Logger &     │  │
                  │  │  Analyzer     │  │
                  │  └───────┬───────┘  │
                  │          │          │
                  │  ┌───────▼───────┐  │
                  │  │   Dashboard   │  │
                  │  │  + Alerts     │  │
                  │  └───────────────┘  │
                  └─────────────────────┘
```

---

## 🚀 Instalacija

### Preduslovi

- Python 3.10+
- pip
- (Opciono) Docker & Docker Compose

### 1. Kloniranje repozitorijuma

```bash
git clone https://github.com/korisnik/iot-honeypot.git
cd iot-honeypot
```

### 2. Kreiranje virtualnog okruženja

```bash
python -m venv venv
source venv/bin/activate        # Linux/macOS
# venv\Scripts\activate         # Windows
```

### 3. Instalacija zavisnosti

```bash
pip install -r requirements.txt
```

### 4. Docker (alternativa)

```bash
docker-compose up -d
```

---

## ⚙️ Konfiguracija

Kopirajte primer konfiguracije i prilagodite ga:

```bash
cp config/config.example.yaml config/config.yaml
```

### `config/config.yaml`

```yaml
honeypot:
  name: "IP-Camera-01"         # Ime lažnog uređaja
  ports:
    telnet: 23
    ssh: 22
    http: 80
    mqtt: 1883

logging:
  level: INFO
  output: logs/honeypot.json
  max_size_mb: 100

alerts:
  email:
    enabled: true
    smtp_host: smtp.gmail.com
    smtp_port: 587
    sender: vas@email.com
    recipients:
      - admin@email.com
  telegram:
    enabled: false
    bot_token: "YOUR_BOT_TOKEN"
    chat_id: "YOUR_CHAT_ID"

geolocation:
  enabled: true
  api_key: "YOUR_IPINFO_API_KEY"   # https://ipinfo.io

ml:
  anomaly_detection: true
  model_path: models/detector.pkl
```

---

## ▶️ Pokretanje

### Pokretanje Honeypot-a

```bash
python src/honeypot.py
```

### Pokretanje Dashboard-a

```bash
python src/dashboard.py
# Otvori http://localhost:5000
```

### Pokretanje svega odjednom

```bash
python main.py --all
```

### Dostupne opcije

```
usage: main.py [-h] [--honeypot] [--dashboard] [--all] [--port PORT] [--verbose]

opcije:
  -h, --help       prikaži pomoć
  --honeypot       pokreni samo honeypot
  --dashboard      pokreni samo dashboard
  --all            pokreni sve
  --port PORT      port za dashboard (default: 5000)
  --verbose        detaljan ispis logova
```

---

## 📊 Dashboard

Dashboard prikazuje u realnom vremenu:

| Sekcija | Opis |
|---|---|
| 🗺️ Mapa napada | Geolokacija napadača u realnom vremenu |
| 📈 Grafik aktivnosti | Broj pokušaja po satu/danu |
| 🔑 Top kredencijali | Najčešće korišćene lozinke |
| 🌍 Top države | Odakle dolaze napadi |
| 📋 Live log | Poslednji pokušaji pristupa |

**Primer loga:**

```json
{
  "timestamp": "2026-05-05T14:32:10Z",
  "ip": "185.220.101.45",
  "port": 23,
  "protocol": "telnet",
  "username": "admin",
  "password": "admin123",
  "country": "Romania",
  "city": "Bucharest",
  "is_bot": true,
  "session_duration_s": 2.3
}
```

---

## 📁 Struktura projekta

```
iot-honeypot/
├── src/
│   ├── honeypot.py          # Glavni honeypot modul
│   ├── dashboard.py         # Web dashboard (Flask)
│   ├── services/
│   │   ├── telnet_server.py # Simulacija Telnet servisa
│   │   ├── ssh_server.py    # Simulacija SSH servisa
│   │   ├── http_server.py   # Simulacija HTTP/kamera interfejsa
│   │   └── mqtt_server.py   # Simulacija MQTT brokera
│   ├── logger.py            # Sistem za logovanje
│   ├── alerts.py            # Email/Telegram notifikacije
│   ├── geoip.py             # Geolokacija IP adresa
│   └── ml/
│       ├── detector.py      # ML detekcija anomalija
│       └── train.py         # Trening modela
├── config/
│   ├── config.yaml          # Konfiguracija
│   └── config.example.yaml  # Primer konfiguracije
├── logs/                    # JSON logovi napada
├── models/                  # ML modeli
├── tests/                   # Unit testovi
├── docker-compose.yml
├── Dockerfile
├── requirements.txt
├── main.py
└── README.md
```

---

## 🛠️ Tehnologije

| Tehnologija | Uloga |
|---|---|
| **Python 3.10+** | Glavni jezik |
| **Twisted / asyncio** | Asinhroni mrežni serveri |
| **Paramiko** | SSH honeypot server |
| **Flask** | Web dashboard |
| **Leaflet.js** | Interaktivna mapa |
| **scikit-learn** | ML detekcija anomalija |
| **SQLite** | Lokalna baza logova |
| **Docker** | Kontejnerizacija |

---

## 🔒 Bezbednosne napomene

> ⚠️ **VAŽNO** — Pročitajte pre pokretanja!

- Pokretajte **isključivo** na izolovanoj mreži ili VM-u
- Nikada ne pokrećite na production serveru
- Koristite **firewall** da ograničite ko može da vidi dashboard
- Redovno proveravajte logove — napadači mogu pokušati bekstvo iz honeypot-a
- Preporučuje se pokretanje kao **neprivilegovan korisnik**
- Pridržavajte se zakona vaše jurisdikcije o honeypot sistemima

```bash
# Pokretanje kao neprivilegovan korisnik (preporučeno)
sudo setcap 'cap_net_bind_service=+ep' $(which python3)
python main.py --all
```

---

## 🤝 Doprinos projektu

Doprinosi su dobrodošli! Otvorite Issue ili Pull Request.

1. Fork repozitorijuma
2. Kreirajte feature granu (`git checkout -b feature/nova-funkcija`)
3. Commit izmene (`git commit -m 'Dodaj novu funkciju'`)
4. Push na granu (`git push origin feature/nova-funkcija`)
5. Otvorite Pull Request

---

## 📄 Licenca

Ovaj projekat je licenciran pod **MIT licencom** — pogledajte [LICENSE](LICENSE) fajl za detalje.

---

<div align="center">

Napravljeno sa ❤️ u svrhe istraživanja cybersecurity-ja

⭐ Ostavite zvezdu ako vam je projekat koristan!

</div>
# Honeypot-for-IoT-attacks