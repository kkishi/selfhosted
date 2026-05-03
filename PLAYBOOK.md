# Self-Hosted Services Playbook

This playbook outlines the steps to safely maintain and update Traefik,
Vaultwarden, and PhotoPrism in your environment.

## 1. General Principles

* **Backup First:** Always ensure you have a recent backup of your data.
* **One by One:** Update services one at a time and verify their functionality
  before moving to the next.
* **Check Logs:** Use `docker compose logs -f` to monitor the update process and
  catch any errors early.
* **Use Docker Compose V2:** The commands below use `docker compose` (V2).

---

## 2. Preparation: Backups (Recommended)

Since your data resides on ZFS, you should perform a consistent remote backup
after stopping the services.

1. **Stop all services** to ensure database consistency:

    ```bash
    # Stop services (Vaultwarden, PhotoPrism instances, etc.)
    cd ~/projects/selfhosted/vaultwarden && docker compose stop
    cd ~/projects/selfhosted/photoprism/https/keisuke && docker compose stop
    cd ~/projects/selfhosted/photoprism/https/ayumi && docker compose stop
    cd ~/projects/selfhosted/photoprism/https/satoshi && docker compose stop
    cd ~/projects/selfhosted/photoprism/https/stable-diffusion && \
    docker compose stop
    ```

2. **Sync the dataset** to the backup server:

    ```bash
    dataset=selfhosted
    zync snapshot --dataset=tank/$dataset && \
    zync sync --src=tank/$dataset --dst=proxmox.lan:tank-mirror/$dataset
    ```

3. **Restart services** (or proceed to updates):

    ```bash
    # You can start them now or let the 'docker compose up' in the
    # update steps start them.
    ```

---

## 3. Routine Updates (Patch Releases)

These commands pull the latest images for your current version tags.
(e.g., latest `v3` patch, latest `mariadb:10.6` patch).

### Traefik

Traefik is currently pinned to `v3`. This command will pull the latest
minor/patch release for the `v3` branch.

1. `cd ~/projects/selfhosted/traefik`
2. `docker compose pull && docker compose up -d`

### Vaultwarden

1. `cd ~/projects/selfhosted/vaultwarden`
2. `docker compose pull && docker compose up -d`

### PhotoPrism

Repeat for **ayumi**, **keisuke**, **satoshi**, and **stable-diffusion**.

1. `cd ~/projects/selfhosted/photoprism/https/ayumi`
2. `docker compose pull && docker compose up -d`
3. Monitor logs: `docker compose logs -f photoprism`

---

## 4. Checking Current Versions

To see exactly what versions you are currently running:

* **Traefik**: Check the dashboard at `localhost:8080` or run:
    `docker exec traefik-reverse-proxy-1 traefik version`
* **Vaultwarden**: Check the logs or the admin panel:
    `docker logs vaultwarden 2>&1 | grep "Version"`
* **PhotoPrism**: Run the internal version command:
    `docker exec keisuke-photoprism-1 photoprism version`

---

## 5. Updating Version Tags (Major/Minor Upgrades)

When you want to move to a new major version (e.g., Traefik `v3` to `v4`) or a
new MariaDB version:

1. Open the `docker-compose.yml` for the service.
2. Update the `image:` tag (e.g., `traefik:v3` -> `traefik:v4`).
3. Run `docker compose up -d`.
4. **Note for MariaDB**: Major version upgrades (e.g., `10.6` to `11.x`) require
   a database export and import. **Do not** simply change the tag for MariaDB
   without following the official migration guide.

---

## 6. Post-Update Cleanup

After verifying that all services are running correctly, clean up old, unused
Docker images:

```bash
docker image prune -f
```

## 7. Troubleshooting

* **Rollback**: Change the image tag back to the previous version in
  `docker-compose.yml` and run `docker compose up -d`.
* **Logs**: Use `docker compose logs -f [service_name]` to see real-time errors.
* **Database**: If MariaDB fails, check
  `/tank/selfhosted/photoprism/[user]/database` logs.
