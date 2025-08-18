# 🌬️ Smart Climate Management 🌬️

<table width="100%">
    <tr>
        <td valign="top">
            <b>Version:</b> 1.5.3<br>
            <b>Last updated:</b> 18 August 2025
        </td>
        <td style="text-align: right; vertical-align: top;">
            <a target="_blank" href="https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://github.com/tax987/ha-smart-management-hvac/blob/v1.5.3/ha-smart-management-hvac.yaml" rel="noreferrer noopener">
            <img src="https://my.home-assistant.io/badges/blueprint_import.svg" alt="Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled." /></a>
        </td>
    </tr>
</table>

**[ReadMe IT clicca qui](https://github.com/tax987/ha-smart-management-hvac/blob/v1.5.3/Readme_IT.md)**

A Home Assistant blueprint for optimal and automated management of your air conditioner, focused on comfort and energy savings.

NOTE: This blueprint now supports multi-language notifications. It was mainly developed and tested with the Daikin Onecta integration (via HACS), but it may also work with other brands of air conditioners that have a Home Assistant supported climate entity. For any issues or suggestions, please do not hesitate to contact the author.

---

## 🎯 The "Comfort Temperature & Energy Saving" Principle

> Enjoy a perfect climate at all times, knowing that your air conditioner automatically adapts to maximize comfort and energy efficiency.

This blueprint is based on a simple yet effective principle:
* **Prioritise Comfort:** Maintains your ideal temperature.
* **Energy Efficiency:** Detects when comfort is reached and switches to a low-power mode or turns the air conditioner off.
* **Adaptability:** Automatically reactivates if the temperature rises again, ensuring continuous comfort.

---

## 🧠 How It Works

This blueprint combines constant ambient temperature monitoring with air conditioner control logic, creating a management system that:

1.  **Activates the air conditioner** in the desired mode when the temperature is out of range or at the start of the time window.
2.  **Maintains comfort** until the set target temperature is reached.
3.  **Switches to "eco" mode** (fan or off) once the target temperature is reached.
4.  **Automatically reactivates** if the ambient temperature rises above a defined threshold over the target.
5.  **Allows for hourly scheduling** for activation/deactivation of monitoring.
6.  Includes a **dedicated master switch (an `input_boolean`)** to quickly enable/disable the entire logic.

### Main Scenarios

#### 😴 At the Start of the Time Window or High Temperature

* **At the start time** (if configured) or **upon activation of the automation/master switch** (e.g., HA restart, master switch ON) and the temperature is already above the reactivation threshold → The air conditioner turns on in the `Initial Climate Mode` and sets the `Target Temperature`.
* Notifications inform you of the activation.

#### 🌙 During the Time Window and Ideal Temperature is Reached

* **Ambient temperature drops below the `Target Temperature` - `Turn Off/Fan Offset`** → The air conditioner switches to `Low Power Mode` (e.g., `fan_only` or `off`).
* This prevents frequent on/off cycles and reduces energy consumption.
* Notifications inform you of the mode change.

#### 📈 During the Time Window and Temperature Rises

* **Ambient temperature rises above the `Target Temperature` + `Turn On Offset`** → The air conditioner reactivates in the `Initial Climate Mode` and sets the `Target Temperature`.
* This ensures comfort is maintained without you having to intervene.
* Notifications inform you of the reactivation.

---

## 🚀 Prerequisites

Before importing and using this blueprint, make sure you have:

* An **ambient temperature sensor** in your room.
* A **`climate` entity** that controls your air conditioner.
* **ESSENTIAL:** A **Helper of type `Toggle`** created in Home Assistant, which you will use as a master switch to enable/disable the automation. You can create it from: `Settings` ⚙️ > `Devices & Services` > `Helpers` > `Create Helper` > `Toggle`. Give it a recognizable name, e.g., `Climate Master Switch`.

| Entity | Status |
|:---|:---|
| Daikin Onecta | ✔️ (Tested) |
| Generic Climate | ❓ (May work) |

---

## ⚙️ Configuration Options

| Parameter | Description | Type | Required | Default |
|:---|:---|:---|:---|:---|
| `master_enable` | **The master switch.** Select the `input_boolean` (the toggle helper) created in the prerequisites. It controls all the blueprint's logic. | `entity` (`input_boolean`) | Yes | |
| `climate_entity` | The `climate` entity of your air conditioner. | `entity` | Yes | |
| `temperature_sensor` | The sensor that detects the room temperature (e.g., `sensor.studio_room_temperature`). | `entity` | Yes | |
| `target_temperature` | The desired temperature that the air conditioner should maintain. | `number` | Yes | |
| `turn_off_offset` | Degrees the temp. must DROP below the target before mode change (e.g., `1.0`°C). | `number` | Yes | `1.0` |
| `turn_on_offset` | Degrees the temp. must RISE above the target before reactivation (e.g., `1.5`°C). | `number` | Yes | `1.5` |
| `initial_climate_mode` | The operating mode when the air conditioner turns on (e.g., `cool`, `heat_cool`, `heat`). | `select` | Yes | |
| `low_power_mode` | The mode the air conditioner switches to when the target is reached (e.g., `fan_only`, `off`). | `select` | Yes | |
| `turn_off_timer_eco` | Timer in minutes to turn off the unit after entering low-power mode. Set to `0` to disable. | `number` | No | `0` |
| `start_time` | The time at which management should start (e.g., `23:00`). Leave blank for manual activation via the master switch. | `time` | No | `null` |
| `end_time` | The time at which management should end (e.g., `07:00`). Leave blank for manual deactivation via the master switch. | `time` | No | `null` |
| `end_of_time_action` | Choose the action to perform at the end of the time window (e.g., turn off the climate, switch to fan mode). | `select` | No | `off` |
| `turn_off_timer_end` | Timer in minutes to turn off the unit at the end of the time window. Set to `0` to disable. | `number` | No | `0` |
| `enable_notifications` | Enable or disable the sending of notifications. | `boolean` | No | `true` |
| `notification_service` | Service to send status messages to (e.g., `notify.mobile_app_your_phone`). | `text` | No | `notify.persistent_notification` |
| `language` | The language for notifications (`it` or `en`). | `select` | No | `en` |

---

## 🛠️ Installation & Configuration

1. In Home Assistant, go to **Settings > Automations & Scenes > Blueprints**.

2. Click on **"Import Blueprint"** in the top right corner.

3. In the "Blueprint URL" field, paste the **RAW** link to your YAML file: `https://raw.githubusercontent.com/tax987/ha-smart-management-hvac/v1.5.3/ha-smart-management-hvac.yaml`.

4. Click on "PREVIEW BLUEPRINT" and then on "IMPORT BLUEPRINT".

5. Once imported, click on **"Create automation"** next to the blueprint's name.

6. **Carefully fill in all the fields, especially "Enable Management" by selecting the `input_boolean` Helper you created.**

7. Give your new automation a name and save it.

8. **Test the automation** by turning the master switch on/off, simulating temperature changes, or forcing the times.

---

## 🛡️ Handling Edge Cases

This automation has been designed to fluidly manage state changes:
* **Smart Activation:** The air conditioner activates immediately at the start of the time window, on Home Assistant restart, or upon activation of the master switch if the temperature is already outside the comfort range.
* **Prevents continuous on/off cycling:** Thanks to the configurable offsets, the air conditioner does not "cycle" continuously when the temperature approaches the target.
* **Home Assistant Restart:** The logic restarts correctly after a system reboot, checking the current state and acting accordingly.
* **Time Flexibility:** You can choose to have fully automated management based on times or control it manually via the master switch.
* **Master Control:** The `input_boolean` master switch allows you to completely disable/enable the automation's logic at any time, even if the times are configured, acting as a true kill switch.
* **External Turn Off:** The automation automatically turns off the Smart Management switch if the air conditioner is turned off externally (e.g., with the remote control), to prevent a "ghost" state where the blueprint thinks the climate is still active.

---

## 💡 TIPS

This blueprint is perfect for use at night, for those who don't want to wake up feeling cold or sore in the morning!

---

## 🔗 Links & Credits

* **Home Assistant Community Discussion:** [https://community.home-assistant.io/t/smart-climate-management/921658]
* **GitHub Repository:** [https://github.com/tax987/ha-smart-management-hvac]

Created with ❤️ by [Massimo Querzolo](https://massimoquerzolo.it)