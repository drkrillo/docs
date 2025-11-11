---
description: Complete API reference for all Decentraland backend services
icon: terminal
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
  metadata:
    visible: true
---

# API Reference

Welcome to the Decentraland API Reference. This section provides comprehensive documentation for all backend services that power Decentraland.

## Overview

Decentraland's backend infrastructure consists of distributed services organized into five main categories:

* **Core Services** - Essential infrastructure for service discovery, authentication, and communications
* **Content Services** - Scene and content management, including worlds, events, and places
* **Social Services** - User interactions, friends management, and notifications
* **Asset Services** - Media processing, asset optimization, and storage
* **Platform Services** - Virtual economy, rewards, badges, and gamification

All APIs documented here follow OpenAPI specifications and support RESTful conventions unless otherwise noted.

## Service Categories

### Core Services

Essential infrastructure services that form the foundation of Decentraland:

| Service | Purpose | Key Features |
|---------|---------|--------------|
| [Realm Provider](apis/realm-provider/) | Service discovery | Find available Catalyst servers and communication endpoints |
| [Auth Server](apis/auth-server/) | Authentication | Wallet-based authentication and session management |
| [Comms Gatekeeper](apis/comms-gatekeeper/) | Access control | Time-limited tokens for LiveKit media server access |
| [Archipelago Workers](apis/archipelago-workers/) | Communications | WebSocket clustering and message routing via NATS |

### Content Services

Services for managing scenes, events, and discoverable content:

| Service | Purpose | Key Features |
|---------|---------|--------------|
| [Catalyst](apis/catalyst/) | Content delivery | Decentralized content servers and deployment |
| [Worlds](apis/worlds/) | Isolated scenes | Personal worlds and custom domains |
| [Events](apis/events/) | Event management | Create, schedule, and discover in-world events |
| [Places](apis/places/) | Points of interest | Curated locations and featured experiences |
| [Atlas Server](apis/atlas-server/) | Map data | Parcel information and world map tiles |

### Social Services

User interaction and community features:

| Service | Purpose | Key Features |
|---------|---------|--------------|
| [Social Service](apis/social-service/) | Friends & communities | Friendship management, private messaging |
| [Notifications Workers](apis/notifications-workers/) | User notifications | Real-time and push notifications |
| [Events Notifier](apis/events-notifier/) | Event alerts | Notifications for upcoming events |

### Asset Services

Media processing and optimization:

| Service | Purpose | Key Features |
|---------|---------|--------------|
| [Asset Bundle Registry](apis/asset-bundle-registry/) | Asset optimization | Unity asset bundles for optimized loading |
| [Camera Reel](apis/camera-reel/) | Screenshots | User-generated screenshot management |
| [Marketplace Server](apis/marketplace-server/) | NFT marketplace | Wearables and emotes marketplace integration |

### Platform Services

Virtual economy and gamification:

| Service | Purpose | Key Features |
|---------|---------|--------------|
| [Credits Server](apis/credits-server/) | Virtual credits | Decentraland's virtual currency system |
| [Badges](apis/badges/) | Achievements | User achievement and badge system |
| [Rewards API](apis/rewards/) | NFT rewards | Campaign-based NFT reward distribution |
| [Exploration Games](apis/exploration-games/) | Onboarding | Interactive tutorials and quests |
| [Lamb2](apis/lamb2/) | Lambda utilities | Utility endpoints for various features |

## Getting Started

### Authentication

Most APIs require authentication using Decentraland's wallet-based system:

1. **Connect wallet** - Users sign authentication challenges with their Ethereum wallet
2. **Get tokens** - Auth Server validates signatures and issues JWT tokens
3. **Include tokens** - Add tokens to API requests via `Authorization` header

See the [Auth Server API](apis/auth-server/) for detailed authentication flows.

### Base URLs

APIs are deployed across the Decentraland network:

* **Production**: `https://peer.decentraland.org` (main Catalyst)
* **Development**: Various test environments
* **Custom**: Self-hosted Catalyst nodes

Most services are accessible through Catalyst nodes. Use the [Realm Provider](apis/realm-provider/) to discover available endpoints.

### Rate Limiting

APIs implement rate limiting to ensure fair usage:

* Default limits vary by service and endpoint
* Authenticated requests typically have higher limits
* Rate limit headers included in responses
* Contact maintainers for increased limits if needed

## API Conventions

All APIs follow these conventions:

* **HTTP Methods** - Standard REST verbs (GET, POST, PUT, DELETE)
* **Status Codes** - Standard HTTP status codes
* **Content Type** - `application/json` unless otherwise specified
* **Error Format** - Consistent error response structure
* **Versioning** - Version info in URL path or headers

## Architecture Context

These APIs are part of a larger distributed system. For architectural context:

* [Architecture Overview](../contributor/architecture/architecture.md) - Complete system architecture
* [Backend Services](../contributor/architecture/services.md) - Detailed service descriptions
* [Client Architecture](../contributor/architecture/client.md) - How clients interact with services

## Contributing

These API specifications are maintained in their respective repositories:

* Report issues via GitHub repositories
* Propose changes through pull requests
* Join technical discussions in the [Decentraland Discord](https://dcl.gg/discord)
* Attend monthly [AMA sessions](https://www.notion.so/decentraland/Decentraland-Foundation-Technical-AMA-230fcf1ca1ec4d49922744fe91e6995d)

## Additional Resources

* [Catalyst API Specs](https://decentraland.github.io/catalyst-api-specs/) - Complete OpenAPI specifications
* [Catalyst Monitor](https://decentraland.github.io/catalyst-monitor) - Network health and status
* [Status Page](https://status.decentraland.org/) - Service status and incident history
* [GitHub Organization](https://github.com/decentraland) - Source code repositories

## OpenAPI Documentation

Each API includes:

* **Interactive documentation** - Try endpoints directly from the docs
* **Request/response examples** - Sample payloads for all operations
* **Schema definitions** - Complete data model documentation
* **Authentication requirements** - Required credentials for each endpoint

Navigate to specific APIs using the sidebar to explore detailed endpoint documentation.
