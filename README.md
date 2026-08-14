# Morpheus Micetro Plugin

The Morpheus Micetro Plugin integrates Morpheus with Men&Mice Micetro (formerly BlueCat DNS/DHCP) to provide IP address management (IPAM) and DNS record automation. The plugin communicates with the Micetro Web Services API (MMWS) to allocate and release IP addresses and manage DNS records.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Repository structure](#repository-structure)
- [Building the plugin](#building-the-plugin)
- [License](#license)
- [Installing](#installing)
- [Detailed Usage Steps](#detailed-usage-steps)
- [API Endpoints](#api-endpoints)

---

## Features

### IP Address Management

Allocate and release IP addresses from Micetro IP ranges within Morpheus. Supports automatic next-available IP selection and existing inventory import.

### DNS Record Management

Create and delete DNS records in Micetro zones when instances are provisioned or decommissioned.

### Cloud Sync

Morpheus synchronises the following Micetro resources for inventory:

- DNS zones and records
- IP ranges and address allocations

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Morpheus | 7.0.2 or later |
| Java | 11 or later |
| Gradle | Use the included Gradle wrapper (`./gradlew`) |

Additional prerequisites:

- A running Micetro server with MMWS (Web Services API) enabled and accessible from the Morpheus appliance
- A Micetro user account with API access and sufficient permissions to manage IP addresses and DNS records
- Network access from the Morpheus appliance to the Micetro host on the configured port

---

## Repository structure

```
src/main/groovy/com/morpheusdata/micetro/
├── MicetroPlugin.groovy    - Plugin entry point; registers MicetroProvider
└── MicetroProvider.groovy  - IPAMProvider implementation; IPAM and DNS operations, sync, OptionTypes
build.gradle, gradle.properties - Build configuration and plugin metadata
```

---

## Building the plugin

Run the following command to compile and package the plugin jar:

```bash
./gradlew clean build
```

The packaged jar will be written to `build/libs/`.

To execute tests, use the following command:

```bash
./gradlew test
```

---

## License

This project is licensed under the Apache License 2.0.

See the [LICENSE](LICENSE) file for details.

---

## Installing

1. Build the plugin (see [Building the plugin](#building-the-plugin)) or download a released jar.
2. In Morpheus, navigate to **Administration > Integrations > Plugins**.
3. Click **Add** and upload the `morpheus-micetro-plugin-<version>.jar` from `build/libs/`.
4. Navigate to **Infrastructure > Networks > IP Pools > Add** and select **Micetro** to configure the integration.

---

## Detailed Usage Steps

### Adding a Micetro IPAM Integration

1. Go to **Infrastructure > Networks > IP Pools > Add**.
2. Select **Micetro** as the pool server type.
3. Enter the **Service URL** (e.g. `https://micetro.example.com`), **Username**, and **Password**.
4. Save. Morpheus connects to the Micetro MMWS API and syncs available IP ranges and DNS zones.

### Allocating an IP Address

When provisioning an instance on a network backed by a Micetro range, Morpheus calls the MMWS API to reserve the next available IP. DNS records are created if DNS is configured on the network.

### Releasing an IP Address

When an instance is decommissioned, Morpheus calls the Micetro API to release the IP and delete associated DNS records.

---

## API Endpoints

This plugin communicates with the **Micetro MMWS API** at `{serviceUrl}/mmws/api/v2/`. Authentication uses HTTP Basic credentials.

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `mmws/api/v2/dnsZones` | GET | List DNS zones |
| `mmws/api/v2/dnsZones/{id}/dnsRecords` | GET | List DNS records in a zone |
| `mmws/api/v2/dnsZones/{id}/dnsRecords` | POST | Create a DNS record |
| `mmws/api/v2/dnsRecords/{id}` | DELETE | Delete a DNS record |
| `mmws/api/v2/ranges/{id}/nextFreeAddress` | GET | Get the next available IP in a range |
| `mmws/api/v2/ranges` | GET | List IP ranges |
