# Packet Tracer PTBuilder Scripts (Experiments 1, 3, 5, 7)

## How to use (every experiment)

1. Open a **blank** Packet Tracer project.
2. Go to **Extensions > Builder Code Editor**.
3. Paste ONE experiment's script, then click **Run**.
4. Wait about 30 to 60 seconds for the link lights to turn green.
5. Save with **File > Save As** (for example `Experiment1.pkt`).

Important rules:
- Do NOT add `//` comments inside the scripts. PTBuilder removes line breaks before running, so a `//` comment would disable all the code after it.
- Use a fresh blank project for each experiment so device names do not clash.
- Zoom out if the devices look missing, because they are spread across the canvas.

---

## Experiment 1: Router, Switch and Hub

Builds three small networks side by side: Router group (PC0, PC1, Switch0, Router0), Switch group (PC2, PC3, PC4, Switch2) and Hub group (PC5 to PC8, Hub0).

Notes:
- PC0 IP is `192.168.10.1` (the sheet's `198.168.10.1` is a typo).
- Switch to router uses a straight-through cable.
- Add the green labels ("Router", "Switch", "Hub") by hand with the Note tool.
- Simulation: Edit Filters, tick ICMP and DNS, then send PDUs PC0 to PC1, PC2 to PC4, PC5 to PC8.

```js
addDevice("PC0", "PC-PT", 100, 100);
addDevice("PC1", "PC-PT", 300, 100);
addDevice("Switch0", "2960-24TT", 200, 230);
addDevice("Router0", "1941", 200, 360);
addLink("PC0", "FastEthernet0", "Switch0", "FastEthernet0/1", "straight");
addLink("PC1", "FastEthernet0", "Switch0", "FastEthernet0/2", "straight");
addLink("Switch0", "FastEthernet0/3", "Router0", "GigabitEthernet0/0", "straight");
configurePcIp("PC0", false, "192.168.10.1", "255.255.255.0", "192.168.10.10");
configurePcIp("PC1", false, "192.168.10.2", "255.255.255.0", "192.168.10.10");
configureIosDevice("Router0", "enable\nconfigure terminal\ninterface gig0/0\nip address 192.168.10.10 255.255.255.0\nno shutdown\nend");
addDevice("PC2", "PC-PT", 500, 100);
addDevice("PC3", "PC-PT", 700, 100);
addDevice("PC4", "PC-PT", 600, 360);
addDevice("Switch2", "2950-24", 600, 230);
addLink("PC2", "FastEthernet0", "Switch2", "FastEthernet0/1", "straight");
addLink("PC3", "FastEthernet0", "Switch2", "FastEthernet0/2", "straight");
addLink("PC4", "FastEthernet0", "Switch2", "FastEthernet0/3", "straight");
configurePcIp("PC2", false, "10.10.10.1", "255.255.255.0");
configurePcIp("PC3", false, "10.10.10.2", "255.255.255.0");
configurePcIp("PC4", false, "10.10.10.3", "255.255.255.0");
addDevice("PC5", "PC-PT", 900, 100);
addDevice("PC6", "PC-PT", 1100, 100);
addDevice("Hub0", "Hub-PT", 1000, 230);
addDevice("PC7", "PC-PT", 900, 360);
addDevice("PC8", "PC-PT", 1100, 360);
addLink("PC5", "FastEthernet0", "Hub0", "Port 0", "straight");
addLink("PC6", "FastEthernet0", "Hub0", "Port 1", "straight");
addLink("PC7", "FastEthernet0", "Hub0", "Port 2", "straight");
addLink("PC8", "FastEthernet0", "Hub0", "Port 3", "straight");
configurePcIp("PC5", false, "192.168.10.5", "255.255.255.0");
configurePcIp("PC6", false, "192.168.10.6", "255.255.255.0");
configurePcIp("PC7", false, "192.168.10.7", "255.255.255.0");
configurePcIp("PC8", false, "192.168.10.8", "255.255.255.0");
```

If `1941` or `2950-24` is not accepted in your version, swap the model name for one that is available.

---

## Experiment 3: LAN Topologies (Star, Bus, Ring, Tree)

Builds all four topologies on one canvas: Star (top-left), Bus (top-right), Ring (bottom-left), Tree (bottom-right).

Notes:
- Subnets: Star `192.168.1.x`, Bus `192.168.2.x`, Ring `192.168.3.x`, Tree `192.168.4.x`.
- Tree switch-to-switch links use cross-over cables.
- In the Ring one link may show an orange light (STP blocking). This is normal.
- Add the labels ("Star Topology", and so on) by hand with the Note tool.
- Simulation: tick only ICMP in Edit Filters, then send PDUs between PCs.

```js
addDevice("Star_SW", "2960-24TT", 300, 100);
for (var i = 0; i < 6; i++) { addDevice("Star_PC" + i, "PC-PT", 50 + i * 100, 250); addLink("Star_PC" + i, "FastEthernet0", "Star_SW", "FastEthernet0/" + (i + 1), "straight"); configurePcIp("Star_PC" + i, false, "192.168.1." + (i + 1), "255.255.255.0"); }
addDevice("Bus_Hub", "Hub-PT", 900, 100);
for (var i = 0; i < 4; i++) { addDevice("Bus_PC" + i, "PC-PT", 750 + i * 100, 250); addLink("Bus_PC" + i, "FastEthernet0", "Bus_Hub", "Port " + i, "straight"); configurePcIp("Bus_PC" + i, false, "192.168.2." + (i + 1), "255.255.255.0"); }
for (var i = 0; i < 4; i++) { addDevice("Ring_SW" + i, "2960-24TT", 50 + i * 150, 450); addDevice("Ring_PC" + i, "PC-PT", 50 + i * 150, 600); addLink("Ring_PC" + i, "FastEthernet0", "Ring_SW" + i, "FastEthernet0/1", "straight"); configurePcIp("Ring_PC" + i, false, "192.168.3." + (i + 1), "255.255.255.0"); }
addLink("Ring_SW0", "FastEthernet0/2", "Ring_SW1", "FastEthernet0/3", "straight");
addLink("Ring_SW1", "FastEthernet0/2", "Ring_SW2", "FastEthernet0/3", "straight");
addLink("Ring_SW2", "FastEthernet0/2", "Ring_SW3", "FastEthernet0/3", "straight");
addLink("Ring_SW3", "FastEthernet0/2", "Ring_SW0", "FastEthernet0/3", "straight");
addDevice("Tree_SW2", "2960-24TT", 900, 450);
addDevice("Tree_SW0", "2960-24TT", 800, 550);
addDevice("Tree_SW1", "2960-24TT", 1000, 550);
addLink("Tree_SW2", "FastEthernet0/1", "Tree_SW0", "FastEthernet0/1", "cross");
addLink("Tree_SW2", "FastEthernet0/2", "Tree_SW1", "FastEthernet0/1", "cross");
for (var i = 0; i < 4; i++) { addDevice("Tree_PC" + i, "PC-PT", 750 + i * 100, 700); configurePcIp("Tree_PC" + i, false, "192.168.4." + (i + 1), "255.255.255.0"); }
addLink("Tree_PC0", "FastEthernet0", "Tree_SW0", "FastEthernet0/2", "straight");
addLink("Tree_PC1", "FastEthernet0", "Tree_SW0", "FastEthernet0/3", "straight");
addLink("Tree_PC2", "FastEthernet0", "Tree_SW1", "FastEthernet0/2", "straight");
addLink("Tree_PC3", "FastEthernet0", "Tree_SW1", "FastEthernet0/3", "straight");
```

---

## Experiment 5: Static NAT

Builds Router0, Router1, Switch0, Switch1, PC0, PC1, PC2, Server0 and configures the routers, including static NAT on Router1.

Notes:
- Gateways use `.254` (the sheet's steps say `.0`, but its table says `.254`).
- Serial link uses `30.30.30.1` and `30.30.30.2` with mask `255.255.255.252`.
- Only the server is statically mapped (`10.10.10.2` to `30.30.30.1`). The sheet maps two hosts to the same address, which IOS rejects.
- Uses `Router-PT`, which has built-in Fa0/0 and Serial2/0.

```js
addDevice("Router0", "Router-PT", 250, 100);
addDevice("Router1", "Router-PT", 550, 100);
addDevice("Switch0", "2960-24TT", 250, 250);
addDevice("Switch1", "2960-24TT", 550, 250);
addDevice("PC0", "PC-PT", 150, 400);
addDevice("PC1", "PC-PT", 300, 400);
addDevice("PC2", "PC-PT", 500, 400);
addDevice("Server0", "Server-PT", 650, 400);
addLink("Router0", "Serial2/0", "Router1", "Serial2/0", "serial");
addLink("Router0", "FastEthernet0/0", "Switch0", "FastEthernet0/1", "straight");
addLink("Router1", "FastEthernet0/0", "Switch1", "FastEthernet0/1", "straight");
addLink("PC0", "FastEthernet0", "Switch0", "FastEthernet0/2", "straight");
addLink("PC1", "FastEthernet0", "Switch0", "FastEthernet0/3", "straight");
addLink("PC2", "FastEthernet0", "Switch1", "FastEthernet0/2", "straight");
addLink("Server0", "FastEthernet0", "Switch1", "FastEthernet0/3", "straight");
configurePcIp("PC0", false, "20.20.20.2", "255.0.0.0", "20.20.20.254");
configurePcIp("PC1", false, "20.20.20.1", "255.0.0.0", "20.20.20.254");
configurePcIp("PC2", false, "10.10.10.1", "255.0.0.0", "10.10.10.254");
configurePcIp("Server0", false, "10.10.10.2", "255.0.0.0", "10.10.10.254");
configureIosDevice("Router0", "enable\nconfigure terminal\ninterface fa0/0\nip address 20.20.20.254 255.0.0.0\nno shutdown\nexit\ninterface s2/0\nip address 30.30.30.2 255.255.255.252\nclock rate 64000\nno shutdown\nend");
configureIosDevice("Router1", "enable\nconfigure terminal\ninterface fa0/0\nip address 10.10.10.254 255.0.0.0\nip nat inside\nno shutdown\nexit\ninterface s2/0\nip address 30.30.30.1 255.255.255.252\nip nat outside\nno shutdown\nexit\nip nat inside source static 10.10.10.2 30.30.30.1\nip route 20.0.0.0 255.0.0.0 30.30.30.2\nend");
```

Verification:
- Router1 CLI: `show ip nat translations`
- PC1 browser to `http://10.10.10.2` should fail; `http://30.30.30.1` should show the server page.
- PC1 `ping 10.10.10.1` should be unreachable; `ping 30.30.30.1` should reply.

Optional, if PC2 to `20.20.20.1` ping fails (paste into Router1 CLI):

```
enable
configure terminal
access-list 1 permit 10.0.0.0 0.255.255.255
ip nat inside source list 1 interface serial2/0 overload
end
```

---

## Experiment 7: RIP Routing

Builds three routers, three switches and three PCs, then configures interfaces and RIP on all routers.

Notes:
- Uses `Router-PT`, so the sheet's `serial 0/0/0` and `0/0/1` become `s2/0` and `s3/0`. IPs, masks and RIP networks match the sheet.
- `clock rate 64000` is added on the Router0 and Router1 ends of the serial links.

```js
addDevice("Router0", "Router-PT", 150, 100);
addDevice("Router1", "Router-PT", 450, 100);
addDevice("Router2", "Router-PT", 750, 100);
addDevice("Switch0", "2960-24TT", 150, 250);
addDevice("Switch1", "2960-24TT", 450, 250);
addDevice("Switch2", "2960-24TT", 750, 250);
addDevice("PC0", "PC-PT", 150, 400);
addDevice("PC1", "PC-PT", 450, 400);
addDevice("PC2", "PC-PT", 750, 400);
addLink("Router0", "Serial2/0", "Router1", "Serial2/0", "serial");
addLink("Router1", "Serial3/0", "Router2", "Serial2/0", "serial");
addLink("Router0", "FastEthernet0/0", "Switch0", "FastEthernet0/1", "straight");
addLink("Router1", "FastEthernet0/0", "Switch1", "FastEthernet0/1", "straight");
addLink("Router2", "FastEthernet0/0", "Switch2", "FastEthernet0/1", "straight");
addLink("PC0", "FastEthernet0", "Switch0", "FastEthernet0/2", "straight");
addLink("PC1", "FastEthernet0", "Switch1", "FastEthernet0/2", "straight");
addLink("PC2", "FastEthernet0", "Switch2", "FastEthernet0/2", "straight");
configurePcIp("PC0", false, "192.168.1.2", "255.255.255.0", "192.168.1.1");
configurePcIp("PC1", false, "192.168.2.2", "255.255.255.0", "192.168.2.1");
configurePcIp("PC2", false, "192.168.3.2", "255.255.255.0", "192.168.3.1");
configureIosDevice("Router0", "enable\nconfigure terminal\ninterface fa0/0\nip address 192.168.1.1 255.255.255.0\nno shutdown\nexit\ninterface s2/0\nip address 10.0.0.1 255.0.0.0\nclock rate 64000\nno shutdown\nexit\nrouter rip\nnetwork 192.168.1.0\nnetwork 10.0.0.0\nend");
configureIosDevice("Router1", "enable\nconfigure terminal\ninterface fa0/0\nip address 192.168.2.1 255.255.255.0\nno shutdown\nexit\ninterface s2/0\nip address 10.0.0.2 255.0.0.0\nno shutdown\nexit\ninterface s3/0\nip address 20.0.0.1 255.0.0.0\nclock rate 64000\nno shutdown\nexit\nrouter rip\nnetwork 10.0.0.0\nnetwork 192.168.2.0\nnetwork 20.0.0.0\nend");
configureIosDevice("Router2", "enable\nconfigure terminal\ninterface fa0/0\nip address 192.168.3.1 255.255.255.0\nno shutdown\nexit\ninterface s2/0\nip address 20.0.0.2 255.0.0.0\nno shutdown\nexit\nrouter rip\nnetwork 20.0.0.0\nnetwork 192.168.3.0\nend");
```

Verification:
- `show ip route` on each router (RIP routes show as `R`)
- `show ip protocols` on any router
- Ping PC0 to `192.168.2.2` and `192.168.3.2`, and PC2 to `192.168.1.2` (the first ping may time out; ping again)
- Simulation mode: Simple PDU from PC0 to PC2

If a serial link stays red, also run `interface s2/0` then `clock rate 64000` on Router1 and Router2.
