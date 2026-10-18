# Contributing to SentinelIC

we welcome contributions from digital design engineers, formal verification specialists, and security researchers.

---

### engineering guidelines

1. **waveform or formal trace required**: every pull request adding or fixing an assertion must provide a vcd/gtkwave trace or an sby formal proof artifact.
2. **zero-latency overhead**: monitoring assertions must be synthesizable and non-intrusive to the critical path.
3. **spec compliance**: refer to arm ihi 0022e (axi4) or sifive tilelink 1.8.0 specifications directly in comment headers.
4. **one concern per pr**: keep pull requests atomic and reviewable.

---

### development workflow

```bash
# clone repository
git clone https://github.com/devtyagi3909/sentinelic.git
cd sentinelic

# run formal verification
sby -f formal/axi4_protocol_check.sby

# run verilator regression
make -C sim/
```

---

### submitting changes

1. fork the repository and create a feature branch (`git checkout -b feat/axi-id-ordering-check`).
2. sign off your commits (`git commit -s`).
3. push to your fork and submit a pull request with formal proof logs.
