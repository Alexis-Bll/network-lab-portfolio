## Known Behaviour / Platform Limitation (IOSvL2)

During testing, an issue was observed where Layer 2 switches were unable to reach remote subnets using only the `ip default-gateway` command.

Although this behaviour would normally be sufficient on real hardware, the IOSvL2 images used in EVE-NG did not consistently forward management traffic to remote networks.

---

### Workaround Implemented

To ensure reliable management connectivity, the following configuration was applied:

```cisco
ip routing
ip route 0.0.0.0 0.0.0.0 <gateway>
```

### example:

```cisco
ip routing
ip route 0.0.0.0 0.0.0.0 192.168.30.1
```

---

### Justification

- Enables the switch to reach remote subnets for SSH and ICMP
- Does not impact Layer 2 switching behaviour for user traffic
- Applied only to support lab environment limitations

---

### Note

In production environments, this behaviour would typically not be required, as Layer 2 switches correctly utilise the ip default-gateway for management traffic.

---
