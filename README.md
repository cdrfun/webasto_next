# Webasto Next Home Assistant Integration 🚗⚡

This integration allows you to control and monitor your Webasto Next charging station through Home Assistant using Modbus TCP.

## Prerequisites 📋

- Home Assistant installed and running
- Webasto Next charging station on the same network
- Webasto Next configured for Modbus TCP connection
- The IP address of your Webasto Next charger

## Installation 🔧

1. Copy these files to your Home Assistant configuration directory:
   - `secrets.yaml` (or add the contents to your existing secrets file)
   - `packages/modbus_webasto.yaml`

2. Update your `secrets.yaml` with your Webasto Next IP address and port:
   ```yaml
   webasto_modbus_host_ip: 192.168.178.xxx
   webasto_modbus_port: 502
   ```

3. Add this to your `configuration.yaml` if not already present:
   ```yaml
   homeassistant:
     packages:
       webasto_next: !include packages/modbus_webasto.yaml
   ```

4. Restart Home Assistant

## Features ✨

- Monitor charging state and status
- View real-time power usage and energy consumption
- Control charging current (5A-16A; 4A = Off)
- Start/Stop charging sessions
- Automatic failsafe mode
- Human-readable status translations
- Useable with [PV Excess Control](https://github.com/InventoCasa/ha-advanced-blueprints/tree/main/PV_Excess_Control) ([Forum](https://community.home-assistant.io/t/pv-solar-excess-optimizer-auto-control-appliances-wallbox-dish-washer-heatpump-based-on-excess-solar-power/))

## Controls 🎮

- **Current Control Slider**: Adjust charging current from 4A to 16A
- **On/Off Toggle**: Quick switch between off (4A) and charging (>=5A)
- **FastCharge Control**: Add a specific amount of kWh to charge (5-30 kWh in 1 kWh steps)
- **FullCharge Mode**: Charge at maximum current until the vehicle is fully charged
- **Start/Stop Scripts**: Control charging sessions manually

## Charging Modes ⚡

### FastCharge Mode

Allows you to add a specific amount of energy (kWh) to your vehicle's battery:

- Use the "FastCharge Increment" slider to set the desired amount (0-30 kWh)
- The system will automatically:
  - Calculate the target based on current meter reading
  - Set charging current to maximum (16A)
  - Stop charging when target is reached
  - Reset settings after completion or when power drops to 0
- Perfect for "I need X more kWh" scenarios

### FullCharge Mode

Designed for complete charging sessions:

- Toggle "FullCharge" to start charging at maximum current (16A)
- System will automatically:
  - Clear any FastCharge settings
  - Set maximum charging current (16A)
  - Monitor charging power
  - Detect when vehicle is fully charged (power drops to 0 for 1 minute)
  - Stop charging and reset when complete
- Ideal for "charge until full" scenarios

### PV Excess Control

- Setup [PV Excess Control](https://github.com/InventoCasa/ha-advanced-blueprints/tree/main/PV_Excess_Control) as described in the corresponding repo
  - From this package, use the following sensors in the Blueprint:
    - Appliance Entity: `input_boolean.webasto_next_on`
    - Appliance SetCurrent entity: `input_number.webasto_next_current_value_slider`
    - Appliance actual power sensor: `sensor.webasto_next_active_power_total`

#### Managing Automation Conflicts

The PV Excess Control must be deactivated when using FastCharge or FullCharge modes. Add this automation to your configuration:

```yaml
automation:
  - alias: Manage PV Excess Control for Webasto Next
    description: Disable PV Excess Control during FastCharge or FullCharge modes
    trigger:
      - platform: state
        entity_id: input_boolean.webasto_next_full_charge
      - platform: state
        entity_id: input_number.webasto_next_fastcharge_increment
    action:
      - choose:
          - conditions:
              - condition: or
                conditions:
                  - condition: state
                    entity_id: input_boolean.webasto_next_full_charge
                    state: 'on'
                  - condition: numeric_state
                    entity_id: input_number.webasto_next_fastcharge_increment
                    above: 0
            sequence:
              - service: automation.turn_off
                entity_id: automation.pv_excess_control
          - conditions:
              - condition: and
                conditions:
                  - condition: state
                    entity_id: input_boolean.webasto_next_full_charge
                    state: 'off'
                  - condition: numeric_state
                    entity_id: input_number.webasto_next_fastcharge_increment
                    below: 1
            sequence:
              - service: automation.turn_on
                entity_id: automation.pv_excess_control
```

This automation will:
- Turn off PV Excess Control when FastCharge or FullCharge modes are active
- Automatically restore PV Excess Control when both modes are inactive

## Sensors 📊

- Charge point state
- Charging current per phase
- Active power (total and per phase)
- Session information
- Equipment status
- Cable state
- Fault codes
- Energy metering

## Credits 🙏

- Ground work and inspiration: lemubo with his tread about the [Webasto (now Ampure) Unite Wallbox Modbus Integration](https://community.home-assistant.io/t/webasto-now-ampure-unite-wallbox-modbus-integration/720172/)
- Inspiration to create a package: MKaiser with his [Sungrow SH Integration](https://github.com/mkaiser/Sungrow-SHx-Inverter-Modbus-Home-Assistant)

## License 📄

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing 🤝

Feel free to submit issues and enhancement requests!