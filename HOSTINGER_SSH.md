# Tune – Hostinger SSH setup

Every push to `main` (or a manual run from the **Actions** tab) deploys this
repository to your Hostinger server over SSH using
`.github/workflows/deploy-hostinger.yml`.

## 1. Enable SSH on Hostinger

hPanel → **Advanced → SSH Access** → enable SSH. Note the:

- **IP / host** (e.g. `123.45.67.89`)
- **Port** (Hostinger shared hosting uses `65002`)
- **Username** (e.g. `u123456789`)

## 2. Create a deploy key

On your own computer:

```bash
ssh-keygen -t ed25519 -C "tune-deploy" -f ~/.ssh/tune_hostinger
```

- Copy the **public** key (`~/.ssh/tune_hostinger.pub`) into
  hPanel → **SSH Access → SSH keys → Add SSH key**.
- Test it:
  ```bash
  ssh -i ~/.ssh/tune_hostinger -p 65002 u123456789@123.45.67.89
  ```

## 3. Add GitHub secrets

GitHub → `vinaybmedia/Tune` → **Settings → Secrets and variables → Actions →
New repository secret**:

| Secret                  | Value                                                        |
|-------------------------|--------------------------------------------------------------|
| `HOSTINGER_SSH_HOST`    | Server IP from hPanel                                        |
| `HOSTINGER_SSH_PORT`    | `65002`                                                      |
| `HOSTINGER_SSH_USER`    | SSH username (e.g. `u123456789`)                             |
| `HOSTINGER_SSH_KEY`     | Full contents of the **private** key `~/.ssh/tune_hostinger` |
| `HOSTINGER_REMOTE_PATH` | e.g. `/home/u123456789/domains/yourdomain.com/public_html`   |

Never commit the private key to the repository.

## 4. Deploy

Push to `main`, or open **Actions → Deploy to Hostinger → Run workflow**.

> Note: the workflow uses `rsync --delete`, so files in `HOSTINGER_REMOTE_PATH`
> that are not in this repo will be removed. Point it at a dedicated folder.
