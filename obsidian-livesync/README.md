# Obsidian LiveSync

Self-hosted Obsidian LiveSync server for Home Assistant. Syncs multiple Obsidian vaults with CouchDB in real-time without running Obsidian.

## Installation

1. Navigate to the Home Assistant App Store
2. Click the three dots → "Repositories"
3. Add: `https://github.com/grunjol/hassio-repository`
4. Find "Obsidian LiveSync" and click "Install"

[![Add Repository](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fgrunjol%2Fhassio-repository)

## Requirements

- A CouchDB instance accessible from your Home Assistant host
- The [Self-hosted LiveSync](https://obsidian.md/plugins?id=obsidian-livesync) plugin in Obsidian on your devices

## Configuration

### couchdb_uri

Full URL to your CouchDB instance.

Example: `http://192.168.1.100:5984`

### couchdb_user / couchdb_password

CouchDB admin credentials.

### polling_interval

Seconds between sync polls. `0` (default) uses the CouchDB `_changes` feed for near-real-time sync. Use `60` or higher for polling mode.

### vaults

List of vaults to sync. Each entry:

| Field | Description |
|-------|-------------|
| `name` | Unique vault ID. Files at `/share/obsidian-livesync/{name}/` |
| `dbname` | CouchDB database name for this vault |
| `passphrase` | Encryption passphrase (must match Obsidian plugin) |

### Example

```yaml
couchdb_uri: "http://192.168.1.100:5984"
couchdb_user: "admin"
couchdb_password: "your-password"
polling_interval: 0
vaults:
  - name: "personal"
    dbname: "obsidian-personal"
    passphrase: "your-encryption-passphrase"
```

## Connecting Obsidian

1. Install the [Self-hosted LiveSync](https://obsidian.md/plugins?id=obsidian-livesync) plugin
2. Configure with the same CouchDB URI, database name, credentials, and passphrase
3. Enable end-to-end encryption
4. Use the plugin's Setup URI feature to replicate config across devices

## Data locations

| Data | Path |
|------|------|
| Vault files | `/share/obsidian-livesync/{name}/` |
| PouchDB database | `/config/data/{name}/` |

## Support

- [CLI Documentation](https://github.com/vrtmrz/obsidian-livesync/tree/main/src/apps/cli)
- [App Repository](https://github.com/grunjol/addon-obsidian-livesync)
