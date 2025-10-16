# 🏦 VaultLayer Protocol

**Unlock Bitcoin’s dormant value through verifiable collateral vaults.**
Borrow against your BTC holdings without surrendering ownership or missing potential upside — powered by **Stacks' Bitcoin settlement finality**.

---

## 📘 Overview

**VaultLayer** is a decentralized collateralization protocol that enables Bitcoin holders to access instant liquidity while maintaining full exposure to BTC’s price appreciation.
By depositing Bitcoin (and supported assets) into overcollateralized vaults, users can generate synthetic stablecoin loans secured on-chain.

All operations are transparently executed through smart contracts on **Stacks**, inheriting Bitcoin’s immutability and final settlement security.

---

## 🚀 Key Features

* **Non-custodial Collateral Vaults**
  Users deposit BTC or STX as collateral without relinquishing ownership.

* **Synthetic Stablecoin Borrowing**
  Instantly generate loan positions against collateralized Bitcoin value.

* **Dynamic Collateral Management**
  Automated liquidation triggers protect system solvency when collateral ratios fall below safe thresholds.

* **Bitcoin Settlement Finality**
  All protocol actions settle securely to Bitcoin’s base layer via Stacks’ proof-of-transfer (PoX) consensus.

* **Transparent On-Chain Governance**
  Platform parameters (collateral ratio, fees, liquidation thresholds) are adjustable by the contract owner through auditable public functions.

---

## ⚙️ System Overview

VaultLayer functions as an on-chain **collateralized debt position (CDP)** system:

1. **Collateral Deposit**
   Users lock BTC or other supported assets as vault collateral.

2. **Loan Request**
   Users generate a synthetic stablecoin loan based on an overcollateralized ratio.

3. **Interest Accrual**
   Loan interest is computed per block and recorded in the vault’s state.

4. **Repayment or Liquidation**

   * When repaid in full, collateral is released to the user.
   * If the collateral ratio falls below the liquidation threshold, the vault is liquidated automatically.

---

## 🧩 Contract Architecture

### Core Components

| Component            | Type         | Description                                                                             |
| -------------------- | ------------ | --------------------------------------------------------------------------------------- |
| **Constants**        | Config       | Defines immutable values such as owner, valid assets, and standard error codes.         |
| **Data Vars**        | State        | Track global protocol settings, metrics, and initialization flags.                      |
| **Data Maps**        | Storage      | Store loan registry, user-loan mappings, and collateral price oracles.                  |
| **Read-Onlys**       | Views        | Provide transparent access to vault state, system metrics, and loan data.               |
| **Private Helpers**  | Logic        | Compute collateral ratios, validate assets, and enforce loan integrity.                 |
| **Public Functions** | Entry Points | Enable interactions such as deposits, borrowing, repayment, and administrative updates. |

---

### Contract Flow Summary

```text
 ┌───────────────────────┐
 │   initialize-platform │
 └──────────┬────────────┘
            │
            ▼
 ┌───────────────────────┐
 │   deposit-collateral  │
 └──────────┬────────────┘
            │
            ▼
 ┌───────────────────────┐
 │     request-loan      │
 └──────────┬────────────┘
            │
            ▼
 ┌───────────────────────┐
 │     check-liquidation │
 └──────────┬────────────┘
            │
            ▼
 ┌───────────────────────┐
 │       repay-loan      │
 └───────────────────────┘
```

Each operation updates protocol-wide state variables (total BTC locked, total loans issued) and enforces collateralization rules through deterministic checks.

---

## 📊 State & Storage Overview

| Variable                   | Type   | Purpose                                                                        |
| -------------------------- | ------ | ------------------------------------------------------------------------------ |
| `platform-initialized`     | `bool` | Indicates if the platform has been bootstrapped.                               |
| `minimum-collateral-ratio` | `uint` | Defines the required overcollateralization ratio (default: 150%).              |
| `liquidation-threshold`    | `uint` | Minimum ratio before forced liquidation (default: 120%).                       |
| `total-btc-locked`         | `uint` | Total amount of BTC collateralized in the system.                              |
| `total-loans-issued`       | `uint` | Cumulative count of loans created.                                             |
| `loans`                    | `map`  | Stores loan metadata (borrower, collateral, loan size, interest rate, status). |
| `user-loans`               | `map`  | Tracks active loan IDs per user.                                               |
| `collateral-prices`        | `map`  | Holds real-time oracle-fed price data for supported assets.                    |

---

## 🧠 Key Computations

### Collateral Ratio

```clarity
(calculate-collateral-ratio collateral loan btc-price)
```

Computes real-time collateral ratio =
[
\frac{(collateral \times btc-price)}{loan} \times 100
]

### Interest Accrual

```clarity
(calculate-interest principal rate blocks)
```

Accumulates interest linearly per block:
[
interest = principal \times \frac{rate}{100 \times 144} \times blocks
]
(assuming 144 blocks per day)

### Liquidation Trigger

```clarity
(check-liquidation loan-id)
```

If ratio ≤ `liquidation-threshold`, the position is liquidated, collateral forfeited, and loan status updated to `"liquidated"`.

---

## 🛠️ Administrative Controls

| Function                       | Access | Description                           |
| ------------------------------ | ------ | ------------------------------------- |
| `initialize-platform`          | Owner  | One-time protocol bootstrap.          |
| `update-collateral-ratio`      | Owner  | Adjusts minimum collateral ratio.     |
| `update-liquidation-threshold` | Owner  | Updates liquidation safety margin.    |
| `update-price-feed`            | Owner  | Updates on-chain asset oracle prices. |

All admin functions are permissioned by the **contract owner**, ensuring controlled upgrades and parameter tuning.

---

## 💬 Public Interactions

| Function                                 | Description                                      |
| ---------------------------------------- | ------------------------------------------------ |
| `deposit-collateral (amount)`            | Locks collateral and increases protocol reserve. |
| `request-loan (collateral, loan-amount)` | Opens a new collateralized loan position.        |
| `repay-loan (loan-id, amount)`           | Repays outstanding loan and releases collateral. |

---

## 🔐 Security Considerations

* **Overcollateralization Enforcement** ensures solvency under all conditions.
* **Oracle Validation** prevents invalid or stale price updates.
* **Access Control Assertions** restrict administrative actions to the protocol owner.
* **Deterministic State Updates** maintain on-chain consistency through Clarity’s predictable execution model.

---

## 🧭 Future Extensions

* Integration with decentralized oracles (e.g., **Stacks Nakamoto Oracle**).
* Support for multi-asset collateral pools (sBTC, xBTC, USDA).
* Automated fee routing to protocol treasury for sustainability.
* Smart contract upgrade paths through DAO governance.

---

## 📄 License

This protocol is released under the **MIT License**.
Feel free to fork, extend, and build on top of VaultLayer for your Bitcoin-DeFi innovations.

---

## 🧱 Stack

* **Language:** [Clarity](https://docs.stacks.co/write-smart-contracts/clarity-overview)
* **Layer:** Stacks (Bitcoin L2)
* **Network Compatibility:** Testnet & Mainnet Ready
* **Security Model:** Deterministic, Non-Turing Complete Logic

---

**VaultLayer Protocol** — *Bridging Bitcoin’s sound money with programmable liquidity.*
