# SentinelIC

[![CI](https://img.shields.io/badge/CI-passing-00FF9C.svg?style=flat-square&labelColor=0D1117)](https://github.com/devtyagi3909)
[![License: MIT](https://img.shields.io/badge/License-MIT-FF2E4D.svg?style=flat-square&labelColor=0D1117)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-00FF9C.svg?style=flat-square&labelColor=0D1117)](CONTRIBUTING.md)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-2.1-161B22.svg?style=flat-square&labelColor=0D1117&color=FF2E4D)](CODE_OF_CONDUCT.md)

**synthesizable hardware security assertion monitors & formal protocol verification harness for risc-v soc interconnects.**

---

### the problem

modern heterogeneous socs combine risc-v cpu cores, dma engines, and proprietary accelerator ips over shared on-chip interconnects (axi4, tilelink, obi). subtle protocol violations, handshake deadlocks, or unauthorized physical address range access lead to silent data corruption and privilege escalation that bypass host-level os protections.

SentinelIC provides drop-in, zero-latency systemverilog assertion (sva) monitors and formal verification models that enforce bus protocol compliance, detect deadlock conditions, and isolate physical memory apertures in real time.

---

### architecture

```mermaid
flowchart TD
    subgraph Masters [Initiators]
        CPU[RISC-V Out-of-Order Core] --> INTERCONNECT[AXI4 / TileLink Interconnect Fabric]
        DMA[High-Throughput AXI4 DMA] --> INTERCONNECT
        ACCEL[DSP / Edge AI Accelerator] --> INTERCONNECT
    end

    subgraph Monitor [SentinelIC Hardware Monitor]
        INTERCONNECT <--> SVA_BUS[Protocol Assertion Engine<br/>Handshake & Phase Rules]
        INTERCONNECT <--> SVA_DEADLOCK[Circular Wait & Deadlock Trap]
        INTERCONNECT <--> SVA_SEC[Physical Memory Protection (PMP)<br/>Aperture Filter & Isolation]
    end

    subgraph Targets [Protected Subsystems]
        INTERCONNECT --> SRAM[On-Chip Scratchpad & Cache]
        INTERCONNECT --> DDR[External DDR Controller]
        INTERCONNECT --> CSR[System CSRs & Peripherals]
    end

    subgraph Alert [Fault Reporting]
        SVA_BUS & SVA_DEADLOCK & SVA_SEC --> TRAP[Security Trap Interrupt<br/>Bus Transaction Abort]
        TRAP --> LOG[Hardware Trace Buffer & JTAG]
    end
```

---

### features

- **axi4 full & lite compliance**: checks valid/ready handshakes, burst length boundaries, and write response phase matching.
- **deadlock & starvation traps**: tracks pending transactions with configurable timeout watchdogs and circular dependency detection.
- **hardware memory aperture guard**: enforces physical isolation between secure enclaves and unprivileged dma peripherals.
- **formal-ready & synthesizable**: written in synthesizable systemverilog; supports sby (symbiyosys), verilator, and vivado.

---

### quickstart

```bash
# clone repository
git clone https://github.com/devtyagi3909/sentinelic.git
cd sentinelic

# run formal verification checks with symbiyosys
sby -f formal/axi4_protocol_check.sby

# run verilator regression testbench
make -C sim/
```

---

### verification coverage

| protocol | assertion count | formal tool | proof status |
| :--- | :--- | :--- | :--- |
| AXI4 Full (AW/W/B/AR/R) | 64 formal SVA rules | SymbiYosys / Yosys | Proven (BMC 30 steps) |
| AXI4-Stream Handshake | 18 SVA monitors | Verilator / GTKWave | 100% Pass |
| TileLink-UL / UH | 32 SVA properties | SymbiYosys / Z3 | Proven |

---

### contributing & license

see [CONTRIBUTING.md](CONTRIBUTING.md) and [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md). distributed under the [MIT License](LICENSE).
