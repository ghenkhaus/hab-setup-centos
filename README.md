# Installing Chef Habitat as a service on CentOS7 and configuring Automate data collector with an Effortless package

## Install Habitat Supervisor

1. Download and run the Habitat installation script
```bash
$ curl https://raw.githubusercontent.com/habitat-sh/habitat/master/components/hab/install.sh | bash
```
2. Accept the Habitat license
```bash
$ hab license accept
```
3. Add hab as a user and add to group habitat
```bash
$ groupadd hab
$ adduser hab -g hab
```
4. Create systemd service definition by creating the file `/etc/systemd/system/hab-sup.service` with the following contents and replace the values:
  - `<HAB_AUTH_TOKEN>` # define: auth to bldr to bring down pkgs
  - `<NAME_OF_APP>` # define 
  - `<NAME_OF_ENV>` # define
  - `<NAME_OF_SITE>` # define
  - `<AUTOMATE_REPORTER_HOSTNAME>` # sends app data to applications tab in automate
  - `<AUTOMATE_REPORTER_TOKEN>`

  ```
[Unit]
Description=Habitat Supervisor
[Service]
Environment=HAB_AUTH_TOKEN=<HAB_AUTH_TOKEN>
ExecStart=/bin/hab run --auto-update --listen-gossip 0.0.0.0:9638 --listen-http 0.0.0.0:9631 --event-stream-application=<NAME_OF_APP> --event-stream-environment=<NAME_OF_ENV> --event-stream-site=<NAME_OF_SITE> --event-stream-url=<AUTOMATE_REPORTER_HOSTNAME>:4222 --event-stream-token=<AUTOMATE_REPORTER_TOKEN>
[Install]
WantedBy=default.target
```
5. Reload daemon, enable, then start service.
```bash
$ systemctl daemon-reload
$ systemctl enable hab-sup
$ systemctl start hab-sup
```
6. Now the Habitat Supervisor should be running as a service. You can validate this by running the following command
```bash
$ systemctl status hab-sup
```
You can access the logs
```bash
$ journalctl -fu hab-sup
```
## Load a Habitat Service

1. Now that the Habitat Supervisor is running in the background, we can now load a service.
## TODO: Add explination here to set context
- namespace (origin) / pkg name
```bash
$ hab svc load chef/effortless-audit-baseline
```

```toml
interval = 1800
splay = 1800
splay_first_run = 0
log_level = 'warn'

[chef_license]
acceptance = "undefined"

[automate]
enable = false
server_url = 'https://<automate_url>'
token = '<automate_token>'
user = '<automate_user>'
```

```toml
interval = 300
report_to_stdout = false

chef_license.acceptance = "accept-no-persist"

# Uncomment and replace values to report to Automate.
# # # This can also be applied at runtime via `hab config apply`

[automate]
enable = "true"
server_url = "https://automate.example.com"
token = "x9Gfdw34O534d54ytgdfQlKq4TApDE="
```

## TODO: Add automate screenshots


