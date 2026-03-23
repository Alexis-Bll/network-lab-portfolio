# Core Layer Setup (HQ)

## Objective
Configure VLANs, SVIs, HSRP, and EtherChannel for redundancy.

## VLANs
- VLAN 10: Admin
- VLAN 20: Servers

## Configuration

### CSW1
(config here)

### CSW2
(config here)

## Verification
- show vlan brief
- show standby brief
- show etherchannel summary

## Testing
- Ping between VLANs
- Shut CSW1 → verify failover to CSW2

## Key Learning
- HSRP provides gateway redundancy
- STP root affects traffic flow
