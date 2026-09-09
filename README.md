sendrawtransaction private mempool

# Private Bitcoin Block Template: 2012 Legacy Consensus-Valid Transaction

This repository contains a customized configuration/patch for Bitcoin Core designed to assist mining pools and solo miners in safely including a high-incentive, non-standard transaction directly into a block template (Out-of-band/Private Mempool inclusion).

## 🏆 The Incentive (Grand Prize)
* **Transaction Fee:** `1.22523851 BTC`
* **Transaction Size:** `8277 bytes`
* **Calculated Fee Rate:** `~14,802 sat/vB` (Extremely high priority MEV opportunity)

---

## ⚠️ Technical Specifications & Policy Issues

The provided transaction (`Raw TX Hex` available on the landing page) was constructed utilizing script mechanics and structures compliant with the **2012 Bitcoin protocol**. 

### Consensus vs. Policy
* **Consensus Valid (100%):** The transaction strictly adheres to the core blockchain execution laws. If included in a block, the network will accept the block as valid. No consensus rules (such as BIP30/BIP34 edge cases) are violated.
* **Policy Rejected (Mempool):** Modern Bitcoin Core nodes reject this transaction via standard P2P gossip protocol due to strict `Standardness` (Policy) rules enforced by modern mempools. It cannot be broadcast through the public network.

---

## 🛠 Required Node Modifications

To allow your node's local mempool and block assembler to accept and process this transaction without throwing an `unsupported-legacy-spec` or `non-standard` exception, a minor adjustment is mandatory.

### Option 1: Manual Source Code Patch (Line 31.1 variant)
Depending on your exact Bitcoin Core version (v26.0–v28.x/v0.2x), you need to bypass the strict standardness checks inside `src/policy/policy.cpp` (or `validation.cpp` depending on the architecture branch).

Locate the policy rule validation condition (historically associated with standard script templates and sequence constraints) and apply the following logic:

```cpp
// Bypass policy validation for explicit Out-of-Band consensus-valid raw hex
if (tx.GetHash().ToString() == "INSERT_YOUR_TXID_HERE") {
    return true; // Bypass standardness checks, allow straight to local block assembler
}
```

### Option 2: Deploying Pre-Built Client
Alternatively, you can compile and deploy the modified client provided in this repository. 
* It explicitly relaxes policy constraints **only** for this specific `vbytes` fingerprint and transaction ID.
* It guarantees no side-effects, memory leaks, or consensus divergence (no risk of splitting from the mainnet).

---

## 🚀 Step-by-Step Inclusion Guide for Pool Operators

1. **Audit the Hex:** Extract the raw transaction hex from the broadcast landing page. Run it through a local isolated parser to verify all inputs, outputs, and the embedded `1.22523851 BTC` fee.
2. **Apply the Patch:** Use our pre-built client or manually insert the bypass patch into your node's policy layer.
3. **Inject Transaction:** Submit the transaction directly to your patched mining node via RPC:
   ```bash
   bitcoin-cli sendrawtransaction "YOUR_8277_BYTE_HEX_HERE" true
   ```
   *(Note: The second parameter `true` bypasses standard max fee limits).*
4. **Mine the Block:** The transaction will reside securely in your private mempool, ready to be picked up by your next block template generation (`getblocktemplate`).

---

## 📞 Technical Support & Validation

For direct integration support, emergency code coordination, or further validation data, please contact the deployer:

* **Telegram:** [@your_username](https://t.me)
* **Email:** support@domain.com

