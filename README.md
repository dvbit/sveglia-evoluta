# sveglia-evoluta
Sistema completo di sveglia intelligente che combina controllo progressivo di luci e musica Spotify con funzionalità avanzate di snooze, memorizzazione stati e ripristino automatico.

Pacchetto Sveglia Evoluta per Home Assistant
Descrizione
Sistema completo di sveglia intelligente che combina controllo progressivo di luci e musica Spotify con funzionalità avanzate di snooze, memorizzazione stati e ripristino automatico.
Prerequisiti
Software Richiesti

Home Assistant con supporto per packages
Integrazione SpotifyPlus (non Spotify standard)
Dispositivi compatibili: Lampadine smart controllabili e media player Spotify

Configurazione Necessaria
yaml# In configuration.yaml
homeassistant:
  packages: !include_dir_named packages
Hardware Supportato

Switch/interruttori per controllo alimentazione luci
Lampadine smart con controllo luminosità
Speaker/dispositivi compatibili con Spotify Connect

Componenti del Package
Helper (38 entità)

5 Input Boolean: Trigger di controllo e configurazioni comportamento
12 Input Number: Parametri temporali, volume, luminosità e contatori
1 Input Select: Tipo di sveglia (Solo Luce/Solo Musica/Entrambi)
8 Input Text: Configurazione entità dispositivi e script personalizzati
3 Timer: Gestione temporizzazioni principale, progresso e snooze
9 Sensor/Binary Sensor: Monitoraggio stato e validazione configurazione

Automazioni (4)

Automazione Principale: Gestione completa ciclo sveglia
Preparazione: Test dispositivi e memorizzazione stati pre-sveglia
Reset Trigger: Pulizia automatica stati temporanei
Notifiche: Feedback visivo cambiamenti stato

Script (3)

Reset Completo: Ripristino totale sistema
Pulizia Automatica: Manutenzione soft al restart
Script Personalizzabili: Azioni custom per ogni fase sveglia

Funzionalità Principali
Preparazione Smart

Test automatico dispositivi (5 secondi)
Memorizzazione volume e luminosità originali
Validazione configurazione prima dell'uso

Controllo Progressivo

Aumento graduale volume (1%-100%)
Incremento progressivo luminosità (1%-100%)
Configurabile numero aggiornamenti e intervalli

Sistema Snooze Avanzato

Numero massimo configurabile (1-100)
Durata fissa o moltiplicativa progressiva
Controllo selettivo luci/musica durante snooze

Ripristino Intelligente

Ripristino automatico valori pre-sveglia a fine ciclo
Controllo separato dispositivi (mantieni acceso/spegni)
Transizioni fluide per evitare shock sensoriali

Monitoraggio Real-time

Progresso percentuale sveglia
Countdown timer visibili
Stati dispositivi in tempo reale
Validazione configurazione continua

Modalità Operative
Solo Luce
Controllo esclusivo illuminazione con progressione luminosità
Solo Musica
Gestione volume Spotify con selezione automatica device
Luce e Musica
Coordinamento sincronizzato entrambi i sistemi
Configurazione Dispositivi
Entità Richieste

input_text.sveglia_entita_switch: Interruttore alimentazione
input_text.sveglia_entita_luce: Lampadina smart
input_text.sveglia_entita_media_player: Entity SpotifyPlus
input_text.sveglia_spotify_device_name: Nome device Spotify Connect

Parametri Critici

URI Spotify (formato: spotify:track:ID o spotify:playlist:ID)
Durata totale (minuti + secondi)
Range volume (iniziale 1-50%, massimo 50-100%)
Range luminosità (iniziale 1-30%, massima 50-100%)

Sicurezza e Affidabilità
Gestione Errori

continue_on_error: true per azioni critiche
Timeout configurabili per operazioni di rete
Validazione parametri prima dell'esecuzione

Persistenza Dati

Timer sopravvivono ai restart di Home Assistant
Configurazioni mantengono stati tra sessioni
Auto-pulizia stati temporanei al riavvio

Controlli di Sicurezza

Validazione range parametri
Verifiche disponibilità dispositivi
Fallback su valori predefiniti

Utilizzo Tipo

Configurazione iniziale: Impostare entità dispositivi e parametri
Preparazione: Attivare input_boolean.sveglia_attivazione
Avvio: Trigger input_boolean.sveglia_trigger_inizio
Gestione: Usare snooze o stop secondo necessità
Ripristino: Automatico a fine ciclo o manuale tramite script

Il sistema garantisce un risveglio graduale e personalizzabile con ripristino completo delle condizioni originali dell'ambiente.
