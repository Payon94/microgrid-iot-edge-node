# microgrid-iot-edge-node
Embedded IoT Edge Node for Smart Microgrid – STM32 + ESP32 + TinyML
# Overview

Questo progetto implementa un nodo embedded IoT intelligente per il
monitoraggio e l'analisi predittiva di parametri energetici e ambientali
in una micro-rete.

Il sistema: - Acquisisce grandezze elettriche e ambientali - Esegue
pre-elaborazione locale deterministica - Comunica tramite protocollo IoT
(MQTT) - Integra Edge AI (TinyML) - Supporta visualizzazione su
dashboard

L'architettura riproduce il comportamento di una RTU/IED edge in un
contesto Smart Grid digitalizzato.

------------------------------------------------------------------------

# 1. Architettura di Sistema

## 1.1 Filosofia Architetturale

Il sistema è stato progettato secondo i seguenti principi: - Separazione
dei livelli funzionali - Determinismo temporale - Scalabilità -
Modularità - Coerenza con architetture industriali Smart Grid

------------------------------------------------------------------------

## 1.2 Architettura a Livelli

Field Layer → Edge Controller → IoT Gateway → Cloud/Dashboard

### Field Layer (Sensori I2C)

-   INA219 → tensione, corrente, potenza DC
-   BMP280 → temperatura
-   BH1750 → luminosità
-   OLED → feedback locale

### Edge Controller (STM32 F401RE)

Responsabilità: - Master I2C unico - Acquisizione sensori - Filtraggio
dati - Scheduler deterministico - Comunicazione UART verso gateway

Motivazione: separazione netta tra controllo deterministico del campo e
comunicazione di rete.

### IoT Gateway (ESP32)

Responsabilità: - Connessione WiFi - Parsing dati UART - Inferenzia
TinyML - Pubblicazione MQTT

Motivazione: ESP32 è più adatto a stack di rete e Machine Learning
rispetto allo STM32.

------------------------------------------------------------------------

# 2. Architettura Temporale

## 2.1 Modello Adottato

Sistema Time-Triggered Multi-Rate con Scheduler Cooperativo Minimo in
ambiente Bare-Metal.

Non viene utilizzato RTOS.

------------------------------------------------------------------------

## 2.2 Timer Base

Periferica: TIM2\
Frequenza: 100 Hz (tick ogni 10 ms)

ISR minimale: - Incremento contatore tick - Set flag scheduler

------------------------------------------------------------------------

## 2.3 Scheduler Cooperativo Multi-Rate

-   Task 10 Hz → filtraggio leggero
-   Task 2 Hz → aggiornamento OLED
-   Task 1 Hz → lettura sensori + UART
-   Task 0.5 Hz → diagnostica

------------------------------------------------------------------------

# Flusso Dati

Sensori I2C → STM32 → UART → ESP32 → WiFi → MQTT → Dashboard

------------------------------------------------------------------------

# Sintesi Tecnica

Il nodo è stato progettato come sistema multi-rate time-triggered con
scheduler cooperativo minimale, garantendo determinismo temporale,
separazione funzionale e scalabilità senza introdurre la complessità di
un RTOS.
