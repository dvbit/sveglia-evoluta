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

## Codice card
# Pop-up Configurazione Sveglia Evoluta
# Accesso tramite hash URL: #configsveglia

type: vertical-stack
cards:
  # Container pop-up principale
  - type: custom:bubble-card
    card_type: pop-up
    hash: "#configsveglia"
  
  # Interfaccia a schede per configurazione
  - type: custom:tabbed-card
    options: {}
    tabs:
      # ==========================================
      # SCHEDA 1: STATO
      # ==========================================
      - attributes:
          label: Stato
          icon: mdi:list-status
        card:
          type: entities
          show_header_toggle: false
          entities:
            # Timer diretti per monitoraggio
            - timer.sveglia_principale
            - timer.sveglia_snooze
            
            # Status principale con template personalizzato
            - type: custom:template-entity-row
              entity: sensor.sveglia_status
              name: Status Sveglia
              state: |
                {% if is_state('timer.sveglia_principale', 'active') %}
                  🔔 Attiva - {{ state_attr('timer.sveglia_principale', 'remaining') }}
                {% elif is_state('timer.sveglia_snooze', 'active') %}
                  😴 Snooze - {{ state_attr('timer.sveglia_snooze', 'remaining') }}
                {% elif is_state('input_boolean.sveglia_attivazione', 'on') %}
                  ⚙️ Preparata - Pronta per l'avvio
                {% else %}
                  ⏹️ Inattiva - Non configurata
                {% endif %}
              secondary: |
                {% if is_state('timer.sveglia_principale', 'active') %}
                  {{ states('input_select.sveglia_tipo') }} | Progresso: {{ states('sensor.sveglia_progresso') }}%
                {% elif is_state('timer.sveglia_snooze', 'active') %}
                  Snooze {{ states('input_number.sveglia_snooze_contatore') }}/{{ states('input_number.snooze_numero_massimo') }} | {{ state_attr('sensor.sveglia_snooze_prossima_durata', 'snooze_rimanenti') }} rimanenti
                {% elif is_state('input_boolean.sveglia_attivazione', 'on') %}
                  {{ states('input_select.sveglia_tipo') }} | Durata: {{ state_attr('sensor.sveglia_durata_totale', 'durata_formattata') }} | Vol: {{ states('input_number.volume_iniziale') }}-{{ states('input_number.volume_massimo') }}%
                {% else %}
                  Configurare parametri e attivare preparazione
                {% endif %}
              icon: |
                {% if is_state('timer.sveglia_principale', 'active') %}
                  mdi:alarm-bell
                {% elif is_state('timer.sveglia_snooze', 'active') %}
                  mdi:sleep
                {% elif is_state('input_boolean.sveglia_attivazione', 'on') %}
                  mdi:alarm-check
                {% else %}
                  mdi:alarm-plus
                {% endif %}
              state_color: |
                {% if is_state('timer.sveglia_principale', 'active') %}
                  true
                {% elif is_state('timer.sveglia_snooze', 'active') %}
                  true
                {% elif is_state('input_boolean.sveglia_attivazione', 'on') %}
                  true
                {% else %}
                  false
                {% endif %}
            
            # Metriche operative
            - type: custom:template-entity-row
              entity: sensor.sveglia_progresso
              name: Progresso
              state: >
                {% set current = states('input_number.sveglia_aggiornamenti_contatore') | int %}
                {% set total = states('input_number.numero_aggiornamenti') | int %}
                {{ current }}/{{ total }} ({{ states('sensor.sveglia_progresso') }}%)
              icon: mdi:progress-clock
            
            - type: custom:template-entity-row
              entity: sensor.sveglia_progresso
              name: Volume Corrente
              state: |
                {{ state_attr('sensor.sveglia_progresso', 'volume_corrente') }}%
              icon: mdi:volume-high
            
            - type: custom:template-entity-row
              entity: sensor.sveglia_progresso
              name: Luminosità Corrente
              state: >
                {{ state_attr('sensor.sveglia_progresso', 'luminosita_corrente') }}/255 
                ({{ (state_attr('sensor.sveglia_progresso', 'luminosita_corrente') | int / 255 * 100) | round(1) }}%)
              icon: mdi:brightness-6
            
            - type: custom:template-entity-row
              entity: binary_sensor.snooze_disponibile
              name: Snooze Rimanenti
              state: >
                {{ state_attr('binary_sensor.snooze_disponibile', 'snooze_rimanenti') }}/{{
                state_attr('binary_sensor.snooze_disponibile', 'snooze_massimi') }}
              icon: mdi:sleep
            
            - type: custom:template-entity-row
              entity: sensor.sveglia_valori_pre_sveglia
              name: Valori Pre-Sveglia
              state: >
                {% if states('sensor.sveglia_valori_pre_sveglia') == 'Memorizzati' %}
                  📁 Vol: {{ state_attr('sensor.sveglia_valori_pre_sveglia', 'volume_memorizzato') }} | Lum: {{ state_attr('sensor.sveglia_valori_pre_sveglia', 'luminosita_memorizzata_percentuale') }}
                {% else %}
                  📭 Non Memorizzati
                {% endif %}
              icon: mdi:content-save

      # ==========================================
      # SCHEDA 2: GENERALE
      # ==========================================
      - attributes:
          label: Generale
          icon: mdi:cog
        card:
          type: entities
          show_header_toggle: false
          entities:
            - entity: input_select.sveglia_tipo
              name: Tipo di Sveglia
              icon: mdi:alarm
            - type: divider
              style: partial
            - entity: input_number.sveglia_durata_minuti
              name: Durata (Minuti)
              icon: mdi:timer
            - entity: input_number.sveglia_durata_secondi
              name: Durata (Secondi)
              icon: mdi:timer
            - entity: input_number.numero_aggiornamenti
              name: N° Aggiornamenti Volume/Luce
              icon: mdi:update

      # ==========================================
      # SCHEDA 3: DISPOSITIVI
      # ==========================================
      - attributes:
          label: Dispositivi
          icon: mdi:devices
        card:
          type: entities
          show_header_toggle: false
          entities:
            - entity: input_text.sveglia_entita_switch
              name: Entità Switch/Interruttore
              icon: mdi:light-switch
            - entity: input_text.sveglia_entita_luce
              name: Entità Lampadina Smart
              icon: mdi:lightbulb
            - entity: input_text.sveglia_entita_media_player
              name: Entità Media Player Spotify
              icon: mdi:spotify
            - entity: input_text.sveglia_spotify_device_name
              name: Nome Device Spotify
              icon: mdi:speaker
            - type: divider
            - entity: input_text.sveglia_uri_media
              name: URI Spotify (Playlist/Brano)
              icon: mdi:spotify

      # ==========================================
      # SCHEDA 4: SNOOZE
      # ==========================================
      - attributes:
          label: Snooze
          icon: mdi:alarm-snooze
        card:
          type: entities
          show_header_toggle: false
          entities:
            - entity: input_number.snooze_durata
              name: Durata Snooze (sec)
              icon: mdi:sleep
            - entity: input_number.snooze_numero_massimo
              name: N° Massimo Snooze
              icon: mdi:counter
            - type: divider
              style: partial
            - entity: input_boolean.sveglia_moltiplicativo_attivo
              name: Attiva Fattore Moltiplicativo
              icon: mdi:multiplication
            - entity: input_number.snooze_fattore_moltiplicativo
              name: Fattore Moltiplicativo
              icon: mdi:multiplication
            - type: divider
              style: partial
            - entity: input_boolean.sveglia_luci_durante_snooze
              name: Mantieni Luci Durante Snooze
              icon: mdi:lightbulb
            - entity: input_boolean.sveglia_musica_durante_snooze
              name: Mantieni Musica Durante Snooze
              icon: mdi:music

      # ==========================================
      # SCHEDA 5: AUDIO
      # ==========================================
      - attributes:
          label: Audio
          icon: mdi:music
        card:
          type: entities
          show_header_toggle: false
          entities:
            - entity: input_number.volume_iniziale
              name: Volume Iniziale
              icon: mdi:volume-low
            - entity: input_number.volume_massimo
              name: Volume Massimo
              icon: mdi:volume-high
            - type: divider
              style: partial
            - entity: input_number.sveglia_volume_pre_sveglia
              name: Volume Pre-Sveglia Memorizzato
              icon: mdi:content-save
            - type: divider
              style: partial
            - entity: input_boolean.sveglia_musica_fine_manuale
              name: Mantieni Musica a Fine Manuale
              icon: mdi:music-off

      # ==========================================
      # SCHEDA 6: LUCI
      # ==========================================
      - attributes:
          label: Luci
          icon: mdi:lightbulb
        card:
          type: entities
          show_header_toggle: false
          entities:
            - entity: input_number.luminosita_iniziale
              name: Luminosità Iniziale
              icon: mdi:brightness-1
            - entity: input_number.luminosita_massima
              name: Luminosità Massima
              icon: mdi:brightness-6
            - type: divider
              style: partial
            - entity: input_number.sveglia_luminosita_pre_sveglia
              name: Luminosità Pre-Sveglia Memorizzata
              icon: mdi:content-save
            - type: divider
              style: partial
            - entity: input_boolean.sveglia_luci_fine_manuale
              name: Mantieni Luci a Fine Manuale
              icon: mdi:lightbulb-off

      # ==========================================
      # SCHEDA 7: SCRIPTS
      # ==========================================
      - attributes:
          label: Scripts
          icon: mdi:script
        card:
          type: entities
          show_header_toggle: false
          entities:
            - entity: input_boolean.sveglia_script_inizio_attivo
              name: Attiva Script Inizio
              icon: mdi:script-text-play
            - entity: input_text.sveglia_script_inizio
              name: Script di Inizio
              icon: mdi:script-text-play
            - type: divider
              style: partial
            - entity: input_boolean.sveglia_script_fine_naturale_attivo
              name: Attiva Script Fine Naturale
              icon: mdi:script-text
            - entity: input_text.sveglia_script_fine_naturale
              name: Script Fine Naturale
              icon: mdi:script-text
            - type: divider
              style: partial
            - entity: input_boolean.sveglia_script_fine_interrotta_attivo
              name: Attiva Script Fine Interrotta
              icon: mdi:script-text-outline
            - entity: input_text.sveglia_script_fine_interrotta
              name: Script Fine Interrotta
              icon: mdi:script-text-outline

      # ==========================================
      # SCHEDA 8: UTILITIES
      # ==========================================
      - attributes:
          label: Utilities
          icon: mdi:wrench
        card:
          type: entities
          show_header_toggle: false
          entities:
            # Pulsanti di utilità
            - type: button
              entity: script.sveglia_reset_completo
              name: 🔄 Reset Completo Sistema
              icon: mdi:restore
              tap_action:
                action: call-service
                service: script.sveglia_reset_completo
              hold_action:
                action: more-info
            
            - type: button
              entity: script.sveglia_pulizia_automatica
              name: 🧹 Pulizia Soft
              icon: mdi:broom
              tap_action:
                action: call-service
                service: script.sveglia_pulizia_automatica
              hold_action:
                action: more-info
            
            - type: divider
            
            # Stato validazione
            - entity: binary_sensor.sveglia_configurazione_valida
              name: ✅ Configurazione Valida
              icon: mdi:check-circle
            
            - type: attribute
              entity: binary_sensor.sveglia_configurazione_valida
              attribute: errori_configurazione
              name: Errori Configurazione
              icon: mdi:alert-circle

    # Layout grid per organizzazione
    grid_options:
      columns: 12
      rows: 4

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

## Card Popup 
# Pop-up Configurazione Sveglia Evoluta

Interfaccia completa per la gestione e configurazione del sistema di sveglia intelligente, organizzata in un pop-up multi-tab con accesso tramite hash URL `#configsveglia`.

## Struttura Generale

La scheda utilizza un **vertical-stack** che combina:
- **Bubble Card Pop-up**: Container principale con routing via hash
- **Tabbed Card**: Interfaccia a schede per organizzare le funzionalità

## Schede Disponibili

### 1. Stato (mdi:list-status)

**Monitoraggio in tempo reale** dello stato operativo della sveglia:

- **Timer attivi**: Visualizzazione diretta dei timer principale e snooze
- **Status dettagliato**: Template row personalizzata con:
  - Stati: Attiva, Snooze, Preparata, Inattiva
  - Tempo rimanente per stati attivi
  - Informazioni contestuali (tipo sveglia, progresso, configurazione)
- **Metriche operative**:
  - Progresso sveglia con contatori e percentuale
  - Volume e luminosità correnti durante l'esecuzione
  - Snooze rimanenti disponibili
  - Valori pre-sveglia memorizzati

### 2. Generale (mdi:cog)

**Configurazione base** del comportamento della sveglia:

- **Tipo sveglia**: Selezione tra Solo Luce, Solo Musica, Luce e Musica
- **Durata**: Configurazione minuti e secondi separati
- **Aggiornamenti**: Numero di step per fade-in progressivo

### 3. Dispositivi (mdi:devices)

**Mappatura delle entità** Home Assistant utilizzate:

- **Controlli fisici**: Switch interruttore e lampadina smart
- **Media**: Entity media player e device Spotify target
- **Contenuti**: URI playlist/brano Spotify

### 4. Snooze (mdi:alarm-snooze)

**Configurazione comportamento snooze**:

- **Durata e limiti**: Secondi per snooze e numero massimo
- **Moltiplicatore progressivo**: Fattore di aumento durata ad ogni snooze
- **Comportamenti**: Mantenimento luci/musica durante pausa

### 5. Audio (mdi:music)

**Gestione parametri sonori**:

- **Range volume**: Valori iniziale e massimo per fade-in
- **Memorizzazione**: Volume pre-sveglia salvato automaticamente
- **Fine sveglia**: Opzione mantenimento musica dopo completamento

### 6. Luci (mdi:lightbulb)

**Controllo illuminazione**:

- **Range luminosità**: Valori iniziale e massimo (0.01-1.0)
- **Memorizzazione**: Luminosità pre-sveglia salvata
- **Fine sveglia**: Opzione mantenimento luci dopo completamento

### 7. Scripts (mdi:script)

**Automazioni personalizzate**:

- **Script inizio**: Eseguito all'avvio sveglia
- **Script fine naturale**: Eseguito al completamento normale
- **Script fine interrotta**: Eseguito per interruzione manuale
- **Abilitazione**: Toggle individuali per ogni script

### 8. Utilities (mdi:wrench)

**Strumenti di manutenzione**:

- **Reset completo**: Ferma tutto e resetta stati (con conferma hold)
- **Pulizia soft**: Reset parziale mantenendo configurazione
- **Validazione**: Controllo configurazione con dettagli errori
- **Diagnostica**: Visualizzazione errori di configurazione

## Caratteristiche Tecniche

### Template Entity Rows Personalizzate

Utilizzate per visualizzazioni dinamiche con:
- **Stato condizionale**: Logic template per stati diversi
- **Icone dinamiche**: Cambiano in base al contesto
- **Informazioni secondarie**: Dettagli aggiuntivi contestuali
- **Colorazione**: State color attivato per stati operativi

### Validazione Integrata

- **Controllo real-time**: Binary sensor per validazione configurazione
- **Feedback errori**: Attributo con lista errori specifici
- **Prevenzione problemi**: Blocco avvio con configurazione non valida

### Interfaccia Responsive

- **Grid layout**: 12 colonne x 4 righe per organizzazione ottimale
- **Divider styling**: Separatori parziali per raggruppamento logico
- **Icon consistency**: Set di icone coerente per categorie

## Flusso di Utilizzo

1. **Configurazione**: Imposta parametri nelle varie schede
2. **Validazione**: Verifica stato "Configurazione Valida"
3. **Test**: Usa "Preparazione" per testare dispositivi
4. **Monitoraggio**: Segui progresso nella scheda "Stato"
5. **Manutenzione**: Usa "Utilities" per reset o pulizia

## Integrazione Sistema

Il pop-up si integra perfettamente con:
- **Bubble card controllo**: Accesso via sub-button configurazione
- **Automazioni sistema**: Lettura real-time di tutti gli stati
- **Package sveglia**: Utilizza tutti gli helper e sensori disponibili
- **Hash routing**: URL diretto `#configsveglia` per accesso rapido

La struttura modulare permette configurazione completa mantenendo semplicità d'uso attraverso l'organizzazione logica in schede tematiche.
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
