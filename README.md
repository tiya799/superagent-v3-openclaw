# SUPERAGENT v3 OPENCLAW — Production Crypto Agent

**Status:** Active | **Deployment:** Base Testnet + Arbitrum | **Wallets:** 50 | **Uptime:** 5 days

---

## Overview

Autonomous agent untuk Web3 operations: NFT minting, token swaps, cross-chain bridges, airdrop farming, mempool monitoring, dan smart money tracking.

**Deployed on:**
- Akash Network (GPU: free trial)
- VPS: agent-prod-01.gon-crypto.dev
- Runtime: Node.js 20 + Python 3.11

**Token Usage:** ~5M tokens/day (skill routing + reasoning + execution)

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    SUPERAGENT v3 BRAIN                      │
│  (AGENTS.md + SOUL.md + IDENTITY.md + HEARTBEAT.md)         │
└─────────────────────────────────────────────────────────────┘
                              ↓
        ┌─────────────────────┼─────────────────────┐
        ↓                     ↓                     ↓
   ┌─────────┐          ┌──────────┐          ┌──────────┐
   │ m0-m13  │          │ x1-x3    │          │ hermes   │
   │ (skills)│          │ (meta)   │          │ (crypto) │
   └─────────┘          └──────────┘          └──────────┘
        ↓                     ↓                     ↓
   ┌─────────────────────────────────────────────────────┐
   │         Keyword-Weighted Skill Router               │
   │  (on-demand loading, token-efficient)               │
   └─────────────────────────────────────────────────────┘
        ↓
   ┌─────────────────────────────────────────────────────┐
   │    Execution Layer (Web3 + Batch + Security)        │
   │  • Multi-wallet orchestration (50 wallets)          │
   │  • Parallel execution (batch ops)                   │
   │  • Honeypot detection + drainer prevention          │
   │  • Simulate before broadcast (eth_call)             │
   └─────────────────────────────────────────────────────┘
        ↓
   ┌─────────────────────────────────────────────────────┐
   │         On-Chain Operations (EVM + Solana)          │
   │  • Swap (1inch/Jupiter)                             │
   │  • Bridge (LayerZero/Stargate/LI.FI)                │
   │  • Mint (OpenSea/Manifold/Zora)                     │
   │  • DeFi (Aave/Lido/GMX/Pendle)                      │
   │  • Sniping (honeypot gate)                          │
   │  • Monitoring (mempool/whale/smart money)           │
   └─────────────────────────────────────────────────────┘
```

---

## Deployment

### Akash (Free Trial GPU)

```bash
# Deploy manifest
akash tx deployment create deployment.yaml --from wallet

# Monitor
akash query deployment get <deployment-id>

# Logs
akash logs <deployment-id> <pod-id>
```

**Provider:** akash-provider-us-1.mainnet  
**GPU:** Free trial (CPU fallback available)  
**Cost:** $0 (free trial) → $50/month (production)

### VPS (agent-prod-01.gon-crypto.dev)

```bash
# SSH
ssh root@agent-prod-01.gon-crypto.dev

# Process manager
pm2 list
pm2 logs superagent-v3

# Restart
pm2 restart superagent-v3
```

---

## Operations

### NFT Minting (m13)

```
Input: "mint NFT https://opensea.io/assets/base/0xABC.../1 pakai 50 wallet"

Output:
[BATCH MINT] 50 wallets × NFT 0xABC... on base
est total cost: 0.0070 ETH
concurrency: 5
[SENDING]
[3/50]   0xAbC...d4F1   ✅ 0xtx1...
[7/50]   0xDeF...c2A3   ✅ 0xtx2...
[12/50]  0x123...8F9    ❌ AlreadyMinted
...
[DONE]   47/50 ok, 3 failed → mint-1748150400.json
```

### Token Swap (H1)

```
Input: "swap 0.5 ETH ke USDC di base"

Output:
[PLAN]
chain:    base
wallet:   0x0eff602f7f35bef67578a47c8670e24be2e87ca3
action:   swap 0.5 ETH → USDC (via 1inch)
slippage: 1%
est out:  ~1,847 USDC
gas:      ~$0.02

Lanjut? (y/n) → y
[SENT]   0xtxhash...
[OK]     block 12345678  gasUsed 142,330
```

### Airdrop Farming (m10 + m12)

```
Input: "layerzero farming, 50 wallet, 5 chain selama 7 hari"

Output:
[FARMING PLAN]
wallets:   50
chains:    eth, arb, base, op, polygon
tx/wallet: 14-21 (random)
duration:  7 days
total tx:  ~875
est cost:  ~$45 total gas

[STARTED] background runner: pm2 lz-farming
[CHECK]   pm2 logs lz-farming
[STOP]    "stop lz-farming"
```

---

## Skills Loaded

| Skill | Domain | Tokens | Status |
|-------|--------|--------|--------|
| m0 | Registry | 0.5k | always-on |
| m10 | Web3 ops | 2.5k | on-demand |
| m12 | Batch ops | 1.5k | on-demand |
| m13 | NFT minter | 2.0k | on-demand |
| hermes/swap | DEX aggregator | 1.5k | on-demand |
| hermes/bridge | Cross-chain | 1.2k | on-demand |
| hermes/sniping | Launch sniper | 1.0k | on-demand |
| hermes/monitoring | Mempool watch | 1.8k | on-demand |

**Total per session:** ~3.5k always-on + 2-5k per skill load = **5M tokens/day** (avg 1000 operations/day)

---

## Security Rails

✅ **Always On:**
- Secret hygiene (never log priv key)
- User-funds-only (refuse 3rd-party seed)
- No drainer/scam code
- Simulate before broadcast (eth_call)
- Honeypot detection (GoPlus + honeypot.is)

✅ **Configurable:**
- `auto_confirm=True` → skip per-tx gate
- `--skip-sim` → skip simulation (fast mode)
- `dry-run=True` → simulate only, no broadcast

---

## Wallets

**Primary:** `0x0eff602f7f35bef67578a47c8670e24be2e87ca3` (Base testnet)

**Multi-wallet pool:** 50 wallets (auto-generated, tracked in `wallets/pool.json`)

**Vault:** `~/.hermes/wallets/superagent-v3.json` (encrypted)

---

## Monitoring

### Logs

```bash
pm2 logs superagent-v3 -f          # follow real-time
pm2 logs superagent-v3 --lines 100 # last 100 lines
```

### Metrics

- **Uptime:** 5 days (2026-05-20 → 2026-05-25)
- **Operations:** 5,000+ (mint, swap, bridge, farm)
- **Success rate:** 94.2%
- **Avg response time:** 2.3s
- **Token usage:** 5M/day

### Alerts

- Mempool drainer detected → TG alert
- Whale buy >$50k → TG alert
- Tx failed (retry) → log + continue
- Session error → auto-restart

---

## GitHub

Repository: `github.com/gon-crypto/superagent-v3-openclaw`

**Branches:**
- `main` — production (stable)
- `dev` — development (testing)
- `feature/hermes-deep-crypto` — new crypto layer

**Commits:** 47 commits (5 days)

---

## License

MIT
