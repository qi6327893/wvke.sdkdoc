# Cloud API

## Overview

Cloud APIs are used for open-platform operation capabilities and cloud data exchange. They are suitable for driver clients, web apps, or business backends that need to connect accounts, devices, configuration sync, operational content, and related services.

This category currently acts as the entry point for cloud capabilities. Specific APIs can be added here by business module later.

## Scope

- Cloud queries for device and product information.
- Cloud sync for driver-client settings, profiles, or user data.
- Reading operational content such as campaigns, assets, and notices.
- Data APIs related to the open-platform business backend.

## Recommendations

TIP

- Cloud APIs usually depend on service endpoints, authentication, and business permissions. Confirm the available scope before integration.
- Clients should only store necessary session data. Sensitive secrets should be handled on the server side.
- For real-time hardware communication, start with the keyboard, mouse, and firmware-upgrade API categories.
