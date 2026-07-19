# Runbook — SIP Lab Operations

## Starting the lab

```bash
docker compose up -d
docker compose ps
```

## Verifying SIP registration

```bash
docker exec -it asterisk asterisk -rx "pjsip show endpoints"
docker exec -it kamailio kamctl ul show
```

## Common troubleshooting

| Symptom | Likely cause | Check |
|---|---|---|
| Extension won't register | Firewall blocking UDP 5060/5061 | `sudo tcpdump -i any port 5060` |
| One-way audio | rtpengine not anchoring media correctly | `docker logs rtpengine`, check port-min/port-max range is open |
| Calls drop after ~30s | NAT keepalive / RTP timeout | Verify `advertised-address` in rtpengine.conf if behind NAT |
| REGISTER accepted but INVITE fails | Kamailio routing logic issue | Check `request_route` in kamailio.cfg, confirm `lookup("location")` succeeds |

## Packet capture for debugging

```bash
sudo tcpdump -i any -w /tmp/sip-capture.pcap port 5060 or portrange 30000-40000
```

Pull the `.pcap` into Wireshark and filter on `sip` to inspect the SIP ladder, or `rtp` to inspect media flow.

## Stopping the lab

```bash
docker compose down
```
