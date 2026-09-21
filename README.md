# SIP Proxy & Media Anchoring Lab — Kamailio + rtpengine + FreeSWITCH

A containerized home lab that replicates an **access-SBC-in-front-of-core-PBX** topology, built on a Raspberry Pi 5 running Docker. This project separates SIP signaling (proxy/registrar) from RTP media handling (anchoring/relay) — the same architectural pattern used by production Session Border Controllers.

## Architecture

```
                 ┌──────────────────────────┐
   SIP clients   │        Kamailio          │
   ────────────► │  SIP Proxy / Registrar   │
                 └────────────┬─────────────┘
                              │ SIP (signaling only)
                              ▼
                 ┌──────────────────────────┐
                 │       FreeSWITCH         │
                 │   Softswitch / Core PBX  │
                 └────────────┬─────────────┘
                              │
                              ▼
                 ┌──────────────────────────┐
   RTP media     │        rtpengine         │
   ◄───────────► │   Media Anchor / Relay   │
                 └──────────────────────────┘
```

- **Kamailio** — SIP proxy and registrar. Handles registration, routing logic, and sits at the network edge, similar to the signaling role of a production SBC.
- **rtpengine** — Anchors and relays RTP media between endpoints, separating media handling from signaling.
- **FreeSWITCH** — Softswitch core providing PBX functionality, extension registration, and call routing.

## Build stages

This lab was built incrementally to isolate what each component actually does:

1. **Stage 1 — FreeSWITCH core:** Stood up a working FreeSWITCH softswitch with registered SIP extensions and functioning call routing between endpoints, with no proxy in front of it.
2. **Stage 2 — Kamailio + rtpengine:** Introduced Kamailio as a dedicated SIP proxy/registrar and rtpengine for RTP media anchoring, placing both in front of the FreeSWITCH core to mirror how an access-SBC sits in front of a production PBX.

## Repo structure

```
.
├── docker-compose.yml
├── configs/
│   ├── kamailio/
│   │   └── kamailio.cfg
│   ├── rtpengine/
│   │   └── rtpengine.conf
│   └── freeswitch/
│       ├── 1001.xml
│       └── 1002.xml
└── docs/
    └── runbook.md
```

> **Note:** Config files in this repo are sanitized templates — replace placeholder IP addresses, credentials, and domain values with your own before deploying. Never commit real credentials or public IP addresses.

## What this demonstrates

- Hands-on SIP registration flows, proxy routing, and RTP media relay/anchoring at the protocol level
- Practical understanding of why production SBCs separate signaling from media
- A sandbox for testing SIP trunk configurations and packet-level troubleshooting (Wireshark/tcpdump) outside of production systems

## Running it

```bash
git clone https://github.com/amanchikalapudi/sip-lab-kamailio-rtpengine.git
cd sip-lab-kamailio-rtpengine
cp configs/kamailio/kamailio.cfg.example configs/kamailio/kamailio.cfg   # edit with your values
cp configs/rtpengine/rtpengine.conf.example configs/rtpengine/rtpengine.conf
cp configs/freeswitch/1001.xml.example configs/freeswitch/1001.xml       # set extension passwords
cp configs/freeswitch/1002.xml.example configs/freeswitch/1002.xml
docker compose up -d
```

## License

MIT
