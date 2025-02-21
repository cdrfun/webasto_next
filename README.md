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
- **Start/Stop Scripts**: Control charging sessions

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