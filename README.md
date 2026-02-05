# Infrastruttura di Messaging (RabbitMQ & Mosquitto)

Questa directory contiene la configurazione Docker per avviare i servizi di messagging necessari all'ecosistema FastGo. Il sistema utilizza un approccio ibrido: RabbitMQ per la comunicazione interna tra microservizi e Mosquitto (MQTT) per la comunicazione con i dispositivi IoT e il frontend web.

## File nel Repository

.
├── docker-compose.yml   # Definizione dei container RabbitMQ e Mosquitto
└── mosquitto.conf       # Configurazione specifica per il broker MQTT

## Servizi Avviati

### 1. RabbitMQ (Message Broker AMQP)
Utilizzato per la gestione delle code di messaggi tra i microservizi backend.
* Immagine: rabbitmq:3-management
* Porta AMQP: 5672 (Per connessione servizi Java/Go/Python)
* Porta Dashboard: 15672 (Interfaccia Web di gestione)
* Credenziali Default:
  * User: guest
  * Pass: guest

### 2. Eclipse Mosquitto (MQTT Broker)
Utilizzato per la ricezione dati dai sensori IoT e per l'invio dati in tempo reale al frontend.
* Immagine: eclipse-mosquitto:2
* Porta TCP: 1883 (Per backend e dispositivi IoT standard)
* Porta WebSockets: 9001 (Per connessione diretta da browser/React)
* Configurazione: Anonima (senza autenticazione) abilitata per sviluppo.

## Prerequisiti

* Docker
* Docker Compose

## Avvio e Utilizzo

1. Avviare i container in background:
   docker-compose up -d

2. Verificare lo stato dei container:
   docker-compose ps

3. Arrestare i servizi:
   docker-compose down

## Verifica Funzionamento

RabbitMQ Management
Aprire il browser all'indirizzo http://localhost:15672 e accedere con guest/guest. Se l'interfaccia carica correttamente, il broker è attivo.

Mosquitto
* Per testare la porta TCP (1883), utilizzare un client come MQTT Explorer o CLI (mosquitto_sub).
* Per testare i WebSockets (9001), verificare che il frontend React riesca a connettersi senza errori di handshake.

## Dettagli Configurazione (mosquitto.conf)

Il file di configurazione definisce due listener:
* Listener 1883: Protocollo standard MQTT su TCP.
* Listener 9001: Protocollo WebSockets, necessario perché i browser non supportano MQTT su TCP diretto.
* allow_anonymous true: Permette la connessione senza username/password (da disabilitare in produzione).
* persistence true: I messaggi vengono salvati su disco nel volume Docker dedicato per sopravvivere ai riavvii.
