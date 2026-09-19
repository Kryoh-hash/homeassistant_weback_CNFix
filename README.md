# WeBack component for Home Assistant

[![](https://img.shields.io/github/release/insidethepixel/homeassistant_weback/all.svg?style=for-the-badge)](https://github.com/insidethepixel/homeassistant_weback)
[![hacs\_badge](https://img.shields.io/badge/HACS-Default-41BDF5.svg?style=for-the-badge)](https://github.com/hacs/integration)
![Maintenance](https://img.shields.io/maintenance/yes/2026?style=flat-square)

> **This fork includes a fix for WeBack accounts using the CN (`cn-north-1`) region.**
>
> The WeBack authentication service may return an API endpoint under `elapp.grit-cloud.cn` that is currently not resolvable via public DNS. This fork uses the working `user.grit-cloud.cn` API endpoint for CN-region accounts, restoring robot discovery and integration setup.

Home Assistant component for controlling robots from brands such as **Neatsvor, Tesvor, Orfeld, Abir**, and others.

This component controls robots through the **WeBack** cloud platform.

> **Please note:** Several of these robot brands also use the Tuya/Smart Life platform in parallel, depending on the model. If your robot works with Tuya/Smart Life, this integration will not be compatible.

> This integration is not compatible with Tesvor robot models that use the **TESVOR HOME** application.

## Installation with HACS

1. Open **HACS**.
2. Go to **Integrations**.
3. Select **EXPLORE & DOWNLOAD REPOSITORIES**.
4. Search for **WeBack**.
5. Download this repository.

## Manual installation

1. Download the latest release.
2. Extract the `weback_vacuum` folder into your Home Assistant `custom_components` directory.
3. Restart Home Assistant.

## Configuration

Edit your Home Assistant `configuration.yaml` and add:

```yaml
weback_vacuum:
  username: <your WeBack email, required>
  password: <your WeBack password, required>
  region: <your country phone code, e.g. France is 33, required>
  application: <configuration app, optional>
  client_id: <API client, optional>
  api_version: <API version used, optional>
  language: <language code, 2 characters, optional>
```

**username**: Login used to set up your robot in the WeBack application.
**password**: Your WeBack password.
**region**: Your country calling code. See [List of country calling codes](https://en.wikipedia.org/wiki/List_of_country_calling_codes). **Use digits only; do not include the leading `+`.**
**application**: If you use `WeBack`, do not change this value.
**client_id**, **api_version**, **language**: These parameters appear to have no effect and normally do not need to be configured.

### Configuration example

```yaml
weback_vacuum:
  username: mymail@contactme.com
  password: mysupersecuredpassword
  region: 33
```

> Do not use leading or trailing characters such as `< > " ' +` in configuration values.

Once the configuration is set, restart Home Assistant.

After the restart, a new vacuum entity will be created using the robot name defined in the WeBack application.

## Maps and Rooms

> **Maps are supported for LIDAR-equipped vacuum robots only.**

Tested on:

* Electriq "Helga" iQlean-LR01

Integration with [lovelace-xiaomi-vacuum-map-card](https://github.com/PiotrMachowski/lovelace-xiaomi-vacuum-map-card) supports automatic map calibration and room boundaries.

The vacuum entity has been modified to accept `send_command` calls for room/segment cleaning.

### Example `lovelace-xiaomi-vacuum-map-card` configuration

To support automatic room boundaries, the Lovelace card needs to be templated. An example using [config-template-card](https://github.com/iantrich/config-template-card) is shown below.

*Please replace the vacuum and camera entities with the appropriate entities from your Home Assistant installation. In this example they are `camera.robot_map` and `vacuum.robot`.*

```yaml
type: custom:config-template-card
variables:
  ROOMS: states['camera.robot_map'].attributes.rooms
entities:
  - camera.robot_map
card:
  type: custom:xiaomi-vacuum-map-card
  map_source:
    camera: camera.robot_map
  calibration_source:
    camera: true
  entity: vacuum.robot
  vacuum_platform: send_command
  title: Vacuum
  preset_name: Live map
  map_modes:
    - template: vacuum_clean_zone
    - template: vacuum_clean_segment
      name: Rooms
      icon: mdi:floor-plan
      predefined_selections: ${ROOMS}
```

## Issues and troubleshooting

If you find a bug or experience any problems, please enable **debug logging** for this integration before opening an issue and provide the relevant log output.

Add the following to your `configuration.yaml`:

```yaml
logger:
  default: error
  logs:
    custom_components.weback_vacuum: debug
```

After changing the logging configuration, restart Home Assistant and reproduce the problem before collecting the logs.

> **Security:** Please remove or redact sensitive information such as passwords, authentication tokens, email addresses, or other credentials before posting logs publicly.
