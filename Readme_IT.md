# 🌬️ Gestione Clima Smart 🌬️

<table width="100%">
    <tr>
        <td valign="top">
            <b>Versione:</b> 1.5.0<br>
            <b>Ultimo aggiornamento:</b> 18 Agosto 2025
        </td>
        <td style="text-align: right; vertical-align: top;">
            <a target="_blank" href="https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://github.com/tax987/ha-smart-management-hvac/blob/v1.4.1/ha-smart-management-hvac.yaml" rel="noreferrer noopener">
            <img src="https://my.home-assistant.io/badges/blueprint_import.svg" alt="Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled." /></a>
        </td>
    </tr>
</table>

**[ReadMe EN clicca qui](https://github.com/tax987/ha-smart-management-hvac/blob/main/Readme_EN.md)**

Una blueprint per Home Assistant per una gestione ottimale e automatizzata del tuo climatizzatore, focalizzata su comfort e risparmio energetico.

NOTA: Questa blueprint supporta ora notifiche in più lingue. È stata sviluppata e testata principalmente con l'integrazione Daikin Onecta (tramite HACS), ma potrebbe funzionare anche con altre marche di climatizzatori che hanno un'entità climate supportata da Home Assistant. In caso di problemi o suggerimenti, non esitare a contattare l'autore.

---

## 🎯 Il Principio "Temperatura Comfort & Risparmio Energetico"

> Goditi un clima perfetto in ogni momento, sapendo che il tuo climatizzatore si adatta automaticamente per massimizzare il comfort e l'efficienza energetica.

Questa blueprint si basa su un principio semplice ma efficace:
* **Priorità al Comfort:** Mantiene la tua temperatura ideale.
* **Efficienza Energetica:** Rileva quando il comfort è raggiunto e passa a una modalità a basso consumo o spegne il climatizzatore.
* **Adattabilità:** Si riattiva automaticamente se la temperatura risale, garantendo un comfort continuo.

---

## 🧠 Come Funziona

Questa blueprint combina il monitoraggio costante della temperatura ambiente con la logica di controllo del climatizzatore, creando un sistema di gestione che:

1.  **Attiva il climatizzatore** nella modalità desiderata quando la temperatura è fuori range o all'inizio del periodo orario.
2.  **Mantiene il comfort** fino al raggiungimento della temperatura target impostata.
3.  **Passa a modalità "eco"** (ventola o spento) una volta raggiunta la temperatura target.
4.  **Si riattiva automaticamente** se la temperatura ambiente supera una soglia definita sopra il target.
5.  **Permette la schedulazione** oraria per l'attivazione/disattivazione del monitoraggio.
6.  Include un **interruttore master dedicato (un `input_boolean`)** per abilitare/disabilitare rapidamente tutta la logica.

### Scenari Principali

#### 😴 All'Inizio del Periodo Orario o Temperatura Alta

* **All'orario di inizio** (se configurato) o **all'attivazione dell'automazione/interruttore master** (es. riavvio HA, master switch ON) e la temperatura è già sopra la soglia di riattivazione → Il climatizzatore si accende nella `Modalità Iniziale Clima` e imposta la `Temperatura Target`.
* Le notifiche ti avvisano dell'attivazione.

#### 🌙 Durante il Periodo Orario e Temperatura Ideale Raggiunta

* **Temperatura ambiente scende sotto la `Temperatura Target` - `Offset di Spegnimento/Ventola`** → Il climatizzatore passa alla `Modalità a Basso Consumo` (es. `fan_only` o `off`).
* Questo evita cicli di accensione/spegnimento troppo frequenti e riduce i consumi.
* Le notifiche ti avvisano del cambio di modalità.

#### 📈 Durante il Periodo Orario e Temperatura che Risale

* **Temperatura ambiente sale sopra la `Temperatura Target` + `Offset di Riattivazione`** → Il climatizzatore si riattiva nella `Modalità Iniziale Clima` e imposta la `Temperatura Target`.
* Questo assicura che il comfort venga mantenuto senza che tu debba intervenire.
* Le notifiche ti avvisano della riattivazione.

---

## 🚀 Prerequisiti

Prima di importare e utilizzare questa blueprint, assicurati di avere:

* Un **sensore di temperatura** nel tuo ambiente.
* Un'**entità `climate`** che controlla il tuo climatizzatore.
* **FONDAMENTALE:** Un **Aiutante (Helper) di tipo `Toggle (interruttore)`** creato in Home Assistant, che userai come interruttore master per abilitare/disabilitare l'automazione. Puoi crearlo da: `Impostazioni` ⚙️ > `Dispositivi e Servizi` > `Aiutanti` > `Crea aiutante` > `Toggle (interruttore)`. Dagli un nome riconoscibile, es. `Interruttore Master Clima`.

| Entità | Stato |
|:---|:---|
| Daikin Onecta | ✔️ (Testato) |
| Climate Generico | ❓ (Potrebbe funzionare) |

---

## ⚙️ Opzioni di Configurazione

| Parametro | Descrizione | Tipo | Obbligatorio | Default |
|:---|:---|:---|:---|:---|
| `master_enable` | **L'interruttore master.** Seleziona l'`input_boolean` (l'aiutante interruttore) creato nei prerequisiti. Controlla tutta la logica della blueprint. | `entity` (`input_boolean`) | Sì | |
| `climate_entity` | L'entità `climate` del tuo climatizzatore. | `entity` | Sì | |
| `temperature_sensor` | Il sensore che rileva la temperatura della stanza (es. `sensor.studio_room_temperature`). | `entity` | Sì | |
| `target_temperature` | La temperatura desiderata che il climatizzatore dovrà mantenere. | `number` | Sì | |
| `turn_off_offset` | Gradi di cui la temp. deve SCENDERE sotto il target prima del cambio modalità (es. `1.0`°C). | `number` | Sì | `1.0` |
| `turn_on_offset` | Gradi di cui la temp. deve SALIRE sopra il target prima della riattivazione (es. `1.5`°C). | `number` | Sì | `1.5` |
| `initial_climate_mode` | La modalità operativa quando il climatizzatore si attiva (es. `cool`, `heat_cool`, `heat`). | `select` | Sì | |
| `low_power_mode` | La modalità in cui il climatizzatore passa al raggiungimento del target (es. `fan_only`, `off`). | `select` | Sì | |
| `turn_off_timer_eco` | Timer in minuti per spegnere l'unità dopo essere entrata in modalità a basso consumo. Imposta a `0` per disabilitare. | `number` | No | `0` |
| `start_time` | L'ora a cui la gestione deve iniziare (es. `23:00`). Lascia vuoto per attivazione manuale tramite l'interruttore master. | `time` | No | `null` |
| `end_time` | L'ora a cui la gestione deve terminare (es. `07:00`). Lascia vuoto per disattivazione manuale tramite l'interruttore master. | `time` | No | `null` |
| `end_of_time_action` | Scegli l'azione da eseguire al termine dell'orario (es. spegnere il clima, passare alla modalità ventola). | `select` | No | `off` |
| `turn_off_timer_end` | Timer in minuti per spegnere l'unità alla fine del periodo. Imposta a `0` per disabilitare. | `number` | No | `0` |
| `enable_notifications` | Attiva o disattiva l'invio di notifiche. | `boolean` | No | `true` |
| `notification_service` | Servizio a cui inviare messaggi di stato (es. `notify.mobile_app_tuo_telefono`). | `text` | No | `notify.persistent_notification` |
| `language` | La lingua delle notifiche (`it` o `en`). | `select` | No | `it` |

---

## 🛠️ Installazione & Configurazione

1. In Home Assistant, vai su **Impostazioni > Automazioni e Scene > Blueprints**.

2. Clicca su **"Importa Blueprint"** in alto a destra.

3. Nel campo "URL della Blueprint", incolla il link **RAW** del tuo file YAML: `https://raw.githubusercontent.com/tax987/ha-smart-management-hvac/v1.4.1/ha-smart-management-hvac.yaml`.

4. Clicca su "PREVIEW BLUEPRINT" e poi su "IMPORT BLUEPRINT".

5. Una volta importata, clicca su **"Crea automazione"** accanto al nome della blueprint.

6. **Compila attentamente tutti i campi, in particolare "Abilita Gestione" selezionando l'Aiutante `input_boolean` che hai creato.**

7. Dai un nome alla tua nuova automazione e salvala.

8. **Testa l'automazione** accendendo/spegnendo l'interruttore master, simulando i cambiamenti di temperatura o forzando gli orari.

---

## 🛡️ Gestione dei Casi Limite

Questa automazione è stata pensata per gestire fluidamente i cambi di stato:
* **Attivazione Intelligente:** Il climatizzatore si attiva immediatamente all'inizio del periodo orario, al riavvio di Home Assistant, o all'attivazione dell'interruttore master se la temperatura è già fuori dal range comfort.
* **Evita continui on/off:** Grazie agli offset configurabili, il climatizzatore non "cicla" continuamente quando la temperatura si avvicina al target.
* **Riavvio di Home Assistant:** La logica si riavvia correttamente dopo un riavvio del sistema, verificando lo stato attuale e agendo di conseguenza.
* **Flessibilità Oraria:** Puoi scegliere di avere una gestione completamente automatica basata sugli orari o controllarla manualmente tramite l'interruttore master.
* **Controllo Master:** L'interruttore master `input_boolean` consente di disabilitare/abilitare completamente la logica dell'automazione in qualsiasi momento, anche se gli orari sono configurati, agendo come un vero kill switch.

---

## 💡 TIPS

Questa blueprint è perfetta da utilizzare di notte, per chi non vuole svegliarsi sentendo freddo o al mattino infreddolito e dolorante!

---

## 🔗 Links & Credits

* **Home Assistant Community Discussion:** []
* **GitHub Repository:** [https://github.com/tax987/ha-smart-management-hvac]

Creato con ❤️ da [Massimo Querzolo](https://massimoquerzolo.it)