# Morpheus Micetro Plugin

This plugin provides an IPAM and DNS integration between [Micetro by Men&Mice](https://bluecatnetworks.com/products/micetro/) and [Morpheus](https://morpheusdata.com). It enables IPv4 and IPv6 range sync, DNS zone and record inventory, host record management, IP allocation, and IP release automation from within the Morpheus platform.

## Requirements

| Component | Minimum Version |
|-----------|----------------|
| Morpheus | 7.0.2 |

## Installation

1. Download the latest `.jar` from the [Releases](https://github.com/HewlettPackard/morpheus-micetro-plugin/releases) page, or [build it yourself](#building).
2. In Morpheus, navigate to **Administration → Integrations → Plugins**.
3. Click **Browse** and upload the `.jar` file.
4. The **Micetro** IPAM/DNS network service integration will appear after the plugin loads.

## Configuration

When adding a Micetro network service in Morpheus (**Infrastructure → Network → Services**), provide the following:

| Field | Description |
|-------|-------------|
| **API Url** | Micetro API endpoint root URL, e.g. `https://x.x.x.x/`. |
| **Credentials** | Morpheus credential containing the Micetro username and password. |
| **Username** | Micetro username used when local credentials are selected. |
| **Password** | Micetro password used when local credentials are selected. |
| **Throttle Rate** | Optional API throttle rate for Micetro requests. |
| **Disable SSL SNI Verification** | Disables SSL SNI verification when connecting to Micetro. |
| **Inventory Existing** | Syncs existing IP address records and DNS resource records from Micetro into Morpheus. |
| **Name Property** | Required Micetro custom property key used to store the Morpheus hostname on IPAM reservations. |

Credentials can also be stored as a Morpheus [Credential](https://docs.morpheusdata.com/en/latest/administration/credentials/credentials.html) and selected at network service setup time.

## Features

### IPAM Sync

The plugin implements `IPAMProvider` and keeps Morpheus network pools aligned with Micetro ranges.

- **IPv4 ranges** — synced as Micetro network pools with CIDR and range data
- **IPv6 ranges** — synced as Micetro IPv6 network pools
- **Custom titles** — Micetro `Title` custom properties are used as pool names when available
- **Range updates** — display name and CIDR changes are reflected in Morpheus

Any additions, updates, and removals in Micetro are automatically reflected in Morpheus on the next network service refresh.

### IP Address Inventory

When existing inventory sync is enabled, the plugin caches Micetro address records for synced ranges.

- **IPv4 and IPv6 addresses** — synced from Micetro IPAM records
- **Hostnames** — synced from Micetro IPAM record names when available
- **Address state** — assigned and unmanaged records are reflected in Morpheus

### IP Allocation and Release

Morpheus can allocate and release addresses from synced Micetro ranges during workload lifecycle operations. Supported operations include:

- Assign a requested IPv4 or IPv6 address when available
- Claim the next free address from a Micetro range
- Create A records for IPv4 allocations when DNS record creation is requested
- Create AAAA records for IPv6 allocations when DNS record creation is requested
- Store hostnames on IPAM reservations using the configured Name Property
- Update hostnames on existing IPAM records
- Release IPAM records when workloads are removed

### DNS Zone Sync

The plugin implements `DNSProvider` and discovers primary DNS zones from Micetro.

- **Primary zones** — synced into Morpheus as authoritative network domains
- **Zone identifiers** — retained for record creation and deletion
- **Existing record inventory** — optional sync of existing DNS resource records when enabled in configuration

### DNS Record Management

DNS records can be managed from Morpheus through the Micetro API. Supported operations include:

- Create DNS records in synced zones
- Delete DNS records
- Sync existing A records
- Sync existing AAAA records
- Sync existing PTR records
- Sync existing TXT records
- Sync existing CNAME records
- Sync existing MX records

## Building

```bash
./gradlew shadowJar
```

The plugin JAR will be written to `build/libs/`.

## License

Copyright 2022 the original author or authors. Licensed under the [Apache License, Version 2.0](LICENSE).
