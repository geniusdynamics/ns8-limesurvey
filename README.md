# ns8-limesurvey

LimeSurvey is the simple, quick and anonymous online survey tool that's bursting with juicy insights. Calling students, professionals and enterprises: design a survey and get the best insights, it’s free and as easy as squeezing a lime. Make a free online survey now!

Module id: `limesurvey`. Images are pinned in `build-images.sh` and updated through reviewed changes.

## Install

```bash
add-module ghcr.io/geniusdynamics/limesurvey:latest 1
```

The command returns the instance id, for example `limesurvey1`. Use the id NS8 returns.

## Configure

```bash
api-cli run configure-module --agent module/limesurvey1 --data - <<'EOF'
{
  "host": "limesurvey.example.org",
  "http2https": true,
  "lets_encrypt": false
}
EOF
```

| Parameter | Required | Type | Description |
|---|---|---|---|
| `host` | yes | string | Host name for the application, like 'limesurvey.domain.org' |
| `lets_encrypt` | yes | boolean | Request a valid Let's Encrypt certificate. |
| `http2https` | yes | boolean | Redirect all the HTTP requests to HTTPS |

## Configuration output

`get-configuration` returns: `host`, `lets_encrypt`, `http2https`.

## Images

| Image | Version |
|---|---|
| `docker.io/mariadb` | `13.0.2` |
| `docker.io/martialblog/limesurvey` | `7.9.1-261102-apache` |

## Network and access

- Reserves 1 TCP port(s) on the node. The web application is published through Traefik on the configured host name.
- Runs rootless.
- Declares these NS8 authorizations: `traefik@node:routeadm`.

## Backup

The following paths are included in the NS8 backup (`imageroot/etc/state-include.conf`):

- `state/limesurvey.sql`
- `state/database.env`
- `state/limesurvey-db.env`
- `state/limesurvey.env`
- `volumes/limesurvey-app`

## Services

- `limesurvey-app.service`
- `limesurvey.service`
- `mariadb-app.service`

## Get the configuration

```bash
api-cli run get-configuration --agent module/limesurvey1
```

## Update and uninstall

```bash
api-cli run update-module --data '{"module_url":"ghcr.io/geniusdynamics/limesurvey:latest","instances":["limesurvey1"],"force":true}'
remove-module --no-preserve limesurvey1
```

## Testing

Continuous integration:

- `Publish images` builds the module image on every push.
- `Test module` installs the image on a QEMU NS8 node and runs the Robot Framework suite in `tests/`. It runs after `Publish images` succeeds on `dev` and on `main`.
- `Build API JSON Schema docs` and the security scan run alongside.

The suite installs the module, configures a host name, checks that the web application answers through Traefik, takes UI screenshots and removes the module.

Run it locally with the NS8 standard infrastructure described in the [ns8-github-actions README](https://github.com/NethServer/ns8-github-actions/blob/v1/README.md#running-tests-locally).

## Open items

Found by the NS8 module standards check. These must be resolved before a stable release:

- tests/limesurvey.robot generated from template: set real configure payload and URL check
- mariadb-app.service: uses %S/state, kickstart uses %E/state
- limesurvey.service: uses %S/state, kickstart uses %E/state
- limesurvey-app.service: uses %S/state, kickstart uses %E/state
- discover-smarthost still present: remove if the app does not use SMTP
- README still has the template boilerplate
- mariadb-app.service: %S/state changed to %E/state (review the bind mounts that remain)
- limesurvey.service: %S/state changed to %E/state (review the bind mounts that remain)
- limesurvey-app.service: %S/state changed to %E/state (review the bind mounts that remain)
- imageroot/actions/restore-module/80start_services: smarthost lines removed, check the unit still starts
- imageroot/actions/configure-module/80start_services: smarthost lines removed, check the unit still starts
- imageroot/systemd/user/limesurvey-app.service: smarthost lines removed, check the unit still starts
- LIMESURVEY_IMAGE is used by the module but no image in the label provides it: leftover from the template?

## Upstream

Application documentation: https://www.limesurvey.org/

## License

GPL-3.0-or-later. See [LICENSE](LICENSE).
