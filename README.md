# Sveglia Evoluta per Home Assistant

Un sistema completo e configurabile per gestire una sveglia intelligente con controllo di luci, musica e funzionalità avanzate.

## Caratteristiche Principali

### 🌅 Modalità Sveglia
- **Solo Luce**: Aumento graduale della luminosità
- **Solo Musica**: Aumento graduale del volume  
- **Luce e Musica**: Combinazione di entrambi

### 🔄 Funzionalità Avanzate
- **Snooze intelligente** con contatore e durata progressiva
- **Fade-in progressivo** per volume e luminosità
- **Script personalizzabili** per eventi specifici
- **Ripristino automatico** dei valori originali
- **Notifiche di stato** in tempo reale

### 📊 Monitoraggio Completo
- Sensori per stato sveglia e progresso
- Binary sensors per controllo disponibilità
- Template sensors per calcoli dinamici
- Validazione automatica configurazione

## Installazione

### Prerequisiti
- Home Assistant con supporto packages
- SpotifyPlus integration (per controllo musica)
- Dispositivi compatibili (luci smart, media player)

### Setup
1. Salva il file come `packages/sveglia_evoluta.yaml`
2. Aggiungi nel `configuration.yaml`:
```yaml
homeassistant:
  packages: !include_dir_named packages
```
3. Riavvia Home Assistant
4. Configura le entità nei helper input

## Configurazione

### Helper Obbligatori da Configurare

#### Entità Dispositivi
- `input_text.sveglia_entita_switch`: Interruttore luce (es: `switch.interruttore_luce_cucina`)
- `input_text.sveglia_entita_luce`: Lampadina smart (es: `light.luce_cucina`)
- `input_text.sveglia_entita_media_player`: Media player Spotify (es: `media_player.spotifyplus_user`)
- `input_text.sveglia_spotify_device_name`: Nome device Spotify (es: `"Alexa Soggiorno"`)
- `input_text.sveglia_uri_media`: URI brano/playlist Spotify

#### Parametri Temporali
- `input_number.sveglia_durata_minuti/secondi`: Durata totale sveglia
- `input_number.snooze_durata`: Durata snooze in secondi (10-600)
- `input_number.snooze_numero_massimo`: Numero massimo snooze (1-100)

#### Parametri Audio/Video
- `input_number.volume_iniziale`: 1-50% (volume di partenza)
- `input_number.volume_massimo`: 50-100% (volume finale)
- `input_number.luminosita_iniziale`: 0.01-0.30 (1-30% luminosità iniziale)
- `input_number.luminosita_massima`: 0.5-1.0 (50-100% luminosità finale)

### Configurazioni Avanzate

#### Snooze Progressivo
```yaml
input_boolean.sveglia_moltiplicativo_attivo: true
input_number.snooze_fattore_moltiplicativo: 1.2  # +20% ogni snooze
```

#### Comportamenti Personalizzati
- `sveglia_luci_durante_snooze`: Mantieni luci accese durante snooze
- `sveglia_musica_durante_snooze`: Mantieni musica durante snooze
- `sveglia_luci_fine_manuale`: Non spegnere luci a fine sveglia
- `sveglia_musica_fine_manuale`: Non fermare musica a fine sveglia

## Utilizzo

### Avvio Sveglia
1. Configura tutti i parametri negli helper
2. Attiva `input_boolean.sveglia_attivazione` (prepara e testa la sveglia)
3. Attiva `input_boolean.sveglia_trigger_inizio` per iniziare

### Durante la Sveglia
- **Snooze**: Attiva `input_boolean.sveglia_trigger_snooze`
- **Stop**: Attiva `input_boolean.sveglia_trigger_fine`
- **Controllo Intelligente**: Usa `script.sveglia_controllo_intelligente`

### Script di Automazione
```yaml
# Esempio chiamata da altra automazione
- service: input_boolean.turn_on
  target:
    entity_id: input_boolean.sveglia_trigger_inizio
```

## Sensori e Monitoraggio

### Sensori Principali
- `sensor.sveglia_status`: Stato corrente (Attiva/Snooze/Inattiva)
- `sensor.sveglia_progresso`: Percentuale completamento (0-100%)
- `binary_sensor.sveglia_attiva`: True se sveglia in corso
- `binary_sensor.snooze_disponibile`: True se snooze ancora possibili

### Attributi Utili
```yaml
# Tempo rimanente
{{ state_attr('sensor.sveglia_status', 'tempo_rimanente') }}

# Volume/luminosità correnti
{{ state_attr('sensor.sveglia_progresso', 'volume_corrente') }}
{{ state_attr('sensor.sveglia_progresso', 'luminosita_corrente') }}

# Snooze rimanenti
{{ state_attr('binary_sensor.snooze_disponibile', 'snooze_rimanenti') }}
```

## Script di Utilità

### Reset Completo
```yaml
service: script.sveglia_reset_completo
```
Ferma tutto e resetta lo stato del sistema.

### Controllo Intelligente
```yaml
service: script.sveglia_controllo_intelligente
```
- Se sveglia attiva → Snooze
- Se sveglia preparata → Avvia
- Altrimenti → Errore

## Script Personalizzabili

### Configurazione Script Custom
```yaml
# Abilita script personalizzati
input_boolean.sveglia_script_inizio_attivo: true
input_boolean.sveglia_script_fine_naturale_attivo: true
input_boolean.sveglia_script_fine_interrotta_attivo: true

# Specifica gli script da eseguire
input_text.sveglia_script_inizio: "script.mio_script_buongiorno"
input_text.sveglia_script_fine_naturale: "script.mio_script_fine_naturale"
input_text.sveglia_script_fine_interrotta: "script.mio_script_interrotto"
```

### Esempi Script Custom
```yaml
script:
  mio_script_buongiorno:
    sequence:
      - service: notify.mobile_app
        data:
          message: "Buongiorno! La sveglia è iniziata."
      - service: climate.set_temperature
        target:
          entity_id: climate.termostato
        data:
          temperature: 22
```

## Validazione Configurazione

Il sistema include validazione automatica:
- `binary_sensor.sveglia_configurazione_valida`: True se configurazione corretta
- Attributo `errori_configurazione`: Lista errori da correggere

### Errori Comuni
- Volume iniziale ≥ volume massimo
- Luminosità iniziale ≥ luminosità massima
- Durata sveglia = 0
- Numero aggiornamenti = 0
- Sveglia non attivata prima dell'avvio

## Integrazione con Dashboard

### Card Lovelace Bubblecard Esempio
# Bubble Card - Controllo Sveglia Evoluta

Configurazione per una bubble card avanzata che controlla il sistema di sveglia intelligente con interfaccia grafica dinamica e feedback visivo.

## Configurazione Completa

```yaml
type: custom:bubble-card
card_type: button
button_type: switch
entity: media_player.lg_webos_tv_oled65b9sla_5

# Layout e dimensioni
card_layout: large
grid_options:
  columns: 5
  rows: 3

# Azioni principali
tap_action:
  action: call-service
  perform_action: script.sveglia_controllo_intelligente
  target: {}

double_tap_action:
  action: perform-action
  perform_action: input_boolean.turn_on
  target:
    entity_id: input_boolean.sveglia_trigger_snooze

hold_action:
  action: perform-action
  perform_action: input_boolean.turn_on
  target:
    entity_id: input_boolean.sveglia_trigger_fine

button_action:
  tap_action:
    action: none

# Moduli attivi
modules:
  - visual_styling_center
  - conditional_icon
  - icon_border_progress
  - icon_animations
  - subbutton_colors
  - timer_progress
  - subbutton_below

# Icona condizionale basata sullo stato
conditional_icon:
  advanced_settings:
    color_true: dark-grey
    color_false: green
  conditions:
    - condition: or
      conditions:
        - condition: state
          entity_id: input_boolean.sveglia_attivazione
          state: "on"
        - condition: state
          entity_id: input_boolean.sveglia_trigger_inizio
          state: "on"
        - condition: state
          entity_id: input_boolean.sveglia_trigger_snooze
          state: "on"
  icon_true: mdi:alarm
  icon_false: mdi:alarm-off

# Progresso bordo icona per timer
icon_border_progress:
  # Timer principale
  - source: timer.sveglia_principale
    button: main
    invert: false
    condition:
      - condition: state
        entity_id: input_boolean.sveglia_trigger_snooze
        state: "off"
    border_radius: 50%
    remaining_color: accent
    background_color: primary
  
  # Timer snooze
  - button: main
    source: timer.sveglia_snooze
    invert: true
    remaining_color: accent
    border_radius: 20px
    condition:
      - condition: state
        entity_id: input_boolean.sveglia_trigger_snooze
        state: "on"
    interpolate_colors: false

# Animazioni icona
icon_animations:
  animation_type: pulse
  animate_when:
    - condition: state
      entity_id: timer.sveglia_snooze
      state: active

# Progresso timer dedicato
timer_progress:
  timer_entity: timer.sveglia_snooze
  startColor: red
  endColor: green
  inverted: true

# Styling visivo centralizzato
visual_styling_center:
  icon_size_multiplier: 1.5
  subicon_size_multiplier: 1.2
  subbutton_background_ratio: 1.5
  height_multiplier: 1
  icon_background_ratio: 1.5
  subbutton_row_spacing: 13
  subbutton_column_spacing: -4
  icon_vertical_margin: -10
  inner_padding: 20
  subbutton_horizontal_margin: -10

# Sub-button per configurazione
sub_button:
  - icon: mdi:cog
    tap_action:
      action: navigate
      url_path: "#configsveglia"
      navigation_path: "#configsveglia"

# Colori sub-button condizionali
subbutton_colors:
  subbutton1:
    color: green
    condition:
      - condition: state
        entity_id: input_boolean.sveglia_script_inizio_attivo
        state: "on"

# Opzioni di visualizzazione
force_icon: false
show_state: false
show_name: false
scrolling_effect: false
```

## Funzionalità della Card

### Azioni Gestuali

- **Tap singolo**: Attiva controllo intelligente sveglia (prepara → avvia → snooze)
- **Double tap**: Attiva snooze direttamente
- **Hold (pressione lunga)**: Ferma sveglia completamente

### Feedback Visivo

#### Icone Dinamiche
- **mdi:alarm**: Quando sveglia è preparata, avviata o in snooze
- **mdi:alarm-off**: Quando sveglia è inattiva

#### Colori Contestuali
- **Dark-grey**: Stati attivi (preparata, avviata, snooze)
- **Green**: Stato inattivo

#### Progress Indicators
- **Border circolare**: Mostra progresso timer principale (durante sveglia attiva)
- **Border rettangolare**: Mostra progresso timer snooze (durante snooze)
- **Timer progress bar**: Barra di progresso dedicata per snooze

### Animazioni
- **Pulse**: L'icona pulsa quando il timer snooze è attivo
- **Border progress**: Animazione del bordo che segue il timer

### Sub-Button
- **Icona ingranaggio (mdi:cog)**: Navigazione alla pagina di configurazione
- **Colore verde condizionale**: Si colora quando gli script personalizzati sono attivi

## Stati Gestiti

La card rileva automaticamente e reagisce a questi stati:

1. **Inattiva**: Icona spenta, nessun progress, colore verde
2. **Preparata**: Icona accesa, colore grigio scuro
3. **Attiva**: Icona accesa, border progress circolare, possibili animazioni
4. **Snooze**: Icona pulsante, border progress rettangolare, timer dedicato

## Requisiti

- **Bubble Card**: Componente custom per Home Assistant
- **Entità sveglia**: Tutti gli input_boolean e timer del package sveglia evoluta
- **Pagina configurazione**: Vista con anchor "#configsveglia"

## Personalizzazioni Possibili

### Dimensioni
```yaml
visual_styling_center:
  icon_size_multiplier: 2.0        # Icona più grande
  height_multiplier: 1.2           # Card più alta
```

### Colori
```yaml
conditional_icon:
  advanced_settings:
    color_true: blue               # Cambia colore stato attivo
    color_false: red               # Cambia colore stato inattivo
```

### Posizionamento
```yaml
grid_options:
  columns: 3                       # Meno colonne
  rows: 2                          # Meno righe
```

Questa configurazione crea un'interfaccia touch moderna e intuitiva per il controllo completo del sistema sveglia evoluta.
## Automazioni Ausiliarie Incluse

- **Reset trigger automatico**: Pulizia stati dopo uso
- **Notifiche stato**: Messaggi informativi durante l'uso  
- **Pulizia al restart**: Reset automatico all'avvio di HA
- **Controllo intelligente**: Logica smart start/snooze
- **Preparazione sveglia**: Test dispositivi alla preparazione

## Risoluzione Problemi

### Spotify Non Funziona
1. Verifica SpotifyPlus integration installata
2. Controlla nome device in `sveglia_spotify_device_name`
3. URI media deve iniziare con `spotify:` 

### Luci Non Rispondono
1. Verifica entità switch e light esistenti
2. Controlla permessi controllo dispositivi
3. Valori luminosità: 0.01-0.30 iniziale, 0.5-1.0 massima

### Timer Non Si Avviano
1. Controlla configurazione validazione
2. Attiva prima `sveglia_attivazione`
3. Verifica durata > 0 secondi

### Reset Manuale
```yaml
service: script.sveglia_reset_completo
```

## Note Tecniche

- **Modalità automazione**: `restart` per gestione stati consistenti
- **Continue on error**: Previene blocchi su dispositivi non disponibili  
- **Template avanzati**: Calcoli dinamici real-time
- **Restore timer**: Persistenza attraverso restart HA
- **Validazione input**: Controlli limiti e coerenza valori

## Supporto

Per problemi o miglioramenti, controlla:
1. Log Home Assistant per errori specifici
2. Stato `binary_sensor.sveglia_configurazione_valida`
3. Attributo `errori_configurazione` per dettagli

Il sistema è progettato per massima flessibilità mantenendo semplicità d'uso.
