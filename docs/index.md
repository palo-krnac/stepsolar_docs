---
title: stepSOLAR — Solar Water Heater Controller
description: Open-source ESP32-S3 based MPPT inverter controller for solar water heating.
hide:
  - navigation
  - toc
---

<div class="hero">
  <h1 class="hero-title">stepSOLAR</h1>
  <p class="hero-sub">
    Open-source MPPT Inverter Controller pre solárny ohrev vody. 
    Postavený na ESP32-S3 s webovým rozhraním a Modbus RTU.
  </p>
  
  <div class="hero-badges" markdown="1">
[![GitHub](https://img.shields.io/badge/GitHub-palo--krnac%2FstepSOLAR-2eb129?style=flat&logo=github&logoColor=white)](https://github.com/palo-krnac/stepSOLAR)
[![Version](https://img.shields.io/badge/firmware-v2.0.0-2eb129?style=flat)](changelog/changelog.md)
[![Platform](https://img.shields.io/badge/platform-ESP32--S3-2eb129?style=flat&logo=espressif)](hardware/overview.md)
[![License](https://img.shields.io/badge/license-MIT-2eb129?style=flat)](https://github.com/palo-krnac/stepSOLAR/blob/main/LICENSE)
  </div>

  <div class="hero-actions">
    <a href="getting-started/quickstart/" class="md-button md-button--primary">
      :octicons-rocket-24: Quick Start
    </a>
    <a href="hardware/overview/" class="md-button">
      :octicons-cpu-24: Hardware Docs
    </a>
  </div>
</div>

---

## O projekte

**stepSOLAR** je pokročilý solárny regulátor, ktorý transformuje jednosmerný prúd (DC) z fotovoltických panelov na striedavý obdĺžnikový priebeh s premenlivou striedou (**MPPT**). Systém je navrhnutý pre maximálnu efektivitu ohrevu vody v bojlery pri zachovaní bezpečnosti a plnej kontroly cez WiFi alebo priemyselnú zbernicu.

---

## Hlavné funkcie

<div class="grid cards" markdown>

-   :fontawesome-solid-solar-panel: **MPPT Riadenie**
    ---
    Algoritmus **P&O (Hill-climbing)** s automatickým skenovaním VA krivky panela. Maximalizuje zisk energie za každého počasia.

-   :fontawesome-solid-globe: **Web Rozhranie**
    ---
    Responzívny dashboard s real-time telemetriou cez **WebSockets**, grafmi histórie a podporou jazykov (EN/SK/PL/CS).

-   :fontawesome-solid-network-wired: **Modbus RTU**
    ---
    Plnohodnotný RS485 Slave. Integrujte stepSOLAR do vášho SCADA systému, Loxone alebo Home Assistanta cez priemyselný štandard.

-   :fontawesome-solid-microchip: **Dual-Core FreeRTOS**
    ---
    Riadiaca slučka beží na **Core 0**, kým WiFi/Web komunikácia na **Core 1**. Sieťová aktivita nikdy neovplyvní stabilitu PWM.

-   :fontawesome-solid-home: **ESPHome / HA**
    ---
    Natívna podpora pre **Home Assistant**. Všetky senzory a prepínače sú dostupné ako entity vďaka pripravenej YAML konfigurácii.

-   :fontawesome-solid-clock: **NTP & Logovanie**
    ---
    Automatická synchronizácia času cez internet. Inteligentné logovanie chýb a prevádzkových stavov.

</div>

---

## Hardvérová špecifikácia

| Komponent | Typ / Čip | Rozhranie |
|:----------|:----------|:----------|
| **MCU** | ESP32-S3-DevKitC-1 | 240MHz Dual-Core |
| **ADC** | ADS1115 (16-bit) | I2C (0x48) |
| **EEPROM** | 24LC256 (32kB) | I2C (0x50) |
| **PWM Výstup** | 2× LEDC 50Hz | GPIO 17, 18 |
| **RS485** | MAX485 / SP3485 | UART2 |
| **Teplota** | KTY81/210 NTC | ADS1115 (Ch 2) |

---

## Rýchly štart (Quick Start)

Ak máte hardvér pripravený, môžete začať týmito krokmi:

```bash
# 1 — Klonovanie repozitára
git clone [https://github.com/palo-krnac/stepSOLAR.git](https://github.com/palo-krnac/stepSOLAR.git)
cd stepSOLAR

# 2 — Nahranie firmware (PlatformIO)
pio run -t upload

# 3 — Nahranie súborov Web rozhrania
pio run -t uploadfs

# 4 — Prvé pripojenie
# WiFi AP: stepSOLAR  |  Heslo: solar2024  
# Adresa: [http://192.168.4.1](http://192.168.4.1)