# stack-homepage

[Homepage](https://gethomepage.dev) deployment for k8s.

One replica runs on each node labelled `homepage_core_homepage` (set in iac-homelab), it's stateless. It's reached through nginx as `https://homepage.$DOMAIN`, requests for any other host are refused (`HOMEPAGE_ALLOWED_HOSTS`).

The config is in `configs/`. `deploy` renders the `*.tmpl` files with every variable, including the widgets' credentials from `secrets.env` (e.g. `PROXMOX_API_PASS`, `PIHOLE_API_KEY`), into a temp dir and puts everything in the `homepage-config` Secret. Changing the config restarts the pods one at a time.

`$DOMAIN` names don't resolve inside the pods, so the links (`href`) use them but the widgets (`url`) go straight to the upstreams, the same ones as `NGINX_SERVICES` in stack-nginx. If an upstream moves, update both.

Homepage listens on `HOMEPAGE_PORT`. nginx's upstream for `homepage` (stack-nginx `NGINX_SERVICES`) has to use the same port.
