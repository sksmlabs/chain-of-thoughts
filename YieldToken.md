# Yield Bearing Token Architectures

## Introductions

The emergence of yield-bearing stablecoins has opened a new frontier in DeFi, but it also introduces a fundamental design dilemma: how can a token remain perfectly pegged to $1 while still passing yield seamlessly across wallets, protocols, and time? Traditional stablecoins like USDC provide stability but no native yield. Yield vaults and rebasing tokens offer yield but often break composability, distort user experience, or create opportunities for exploitation. To bridge these gaps, we need architectures that can **securely preserve the peg, transparently track yield, and remain frictionlessly usable across the DeFi stack**.

This paper explores three candidate models—**Two-Token Systems, Rebasing Tokens, and ERC-4626 Vaults**—each approaching the trade-offs of stability, composability, and yield attribution in unique ways. Together, they map the design space for building the next generation of yield-integrated stable assets.

## Core Challenges

1. **Stable Peg Preservation**
    
    Ensure that `xUSDC` remains strictly pegged **1:1 to USDC**, without rebasing or fluctuating value, enabling seamless use across DeFi protocols.
    
2. **Composability with Yield Exposure**
    
    Users must maintain full **exposure to protocol yield** while retaining the **liquidity** of `xUSDC`.
    
3. **Transferable Yield Attribution**
    
    Yield entitlement must **move with xUSDC transfers**. Attribution should be **time-weighted and wallet-specific**, avoiding yield farming exploits or disincentivizing composability.
    

## Solution 1 : Two Token Architecture

A **two-token architecture** for building a yield-bearing stablecoin system that preserves a **1:1 dollar peg**, allows **unrestricted liquidity and composability**, and ensures **fair and transferable yield attribution**. The model introduces two distinct tokens:

- **xUSDC** – a principal token pegged to USDC
- **yUSDC** – a yield token that represents claimable interest

### xUSDC – The Principal Token

- **Type:** ERC-20  
- **Peg:** 1:1 to USDC  
- **Function:** Represents deposited capital (non-yielding by itself)  
- **Accessible:** Available in wallet with **24/7/365 Minting & Redemption**  
- **Usage:** Freely used in DeFi protocols  
- **Behavior:** Minted per deposit with no rebasing or balance changes  

### yUSDC – The Yield Token

- **Type:** ERC-20 or virtual balance  
- **Peg:** 1 yUSDC = 1 USDC of accrued yield  
- **Function:** Tracks the amount of yield a user has earned  
- **Accessible:** Not accessible; only locked in protocol and redeemable at epochs  
- **Usage:** Can be burned to mint `xUSDC`, effectively realizing the earned interest  
- **Behavior:** Minted per epoch based on yield attribution  

| Token | Exchanged for | Purpose | Accessibility | Transferable | Pegged to $1 | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| xUSDC | USDC | Claim on principal | Available to users in wallet | ✅ Yes | ✅ Yes | Used across DeFi |
| yUSDC | xUSDC | Claim on yield | Locked in protocol on behalf of users | 🚫 restricted | ❌ No | Tracks who earned what yield |

### **User Flow**

The protocol operates through two vaults:

- `usdc_deposit_vault`: holds deposited principal (backing `xUSDC`)  
- `usdc_yield_vault`: holds protocol-earned yield (backing `yUSDC`)  

Below is the complete lifecycle from deposit to redemption and yield realization:

*USDC Deposits Flow*

1. **User deposits USDC** into the `usdc_deposit_vault`.  
2. The protocol **mints `xUSDC` 1:1** to the user:  
   `TVL_xUSDC` = `TVL_usdc_deposit_vault`  
3. Separately, the protocol **generates yield** and deposits it into the `usdc_yield_vault`.  
4. The protocol **mints `yUSDC`** equivalent to the locked yield:  
   `TVL_yUSDC` = `TVL_usdc_yield_vault`  

*Principal Redemption Flow*

1. User initiates a `principal_claim` by submitting `xUSDC`.  
2. The protocol **burns `xUSDC`** and **releases the corresponding `USDC`** from the `usdc_deposit_vault` to the user's wallet.  

*Yield Claim Flow*

1. User initiates a `yield_claim` to realize earned yield by granting access to user’s locked `yUSDC`.  
2. The protocol performs the following:  
   - **Burns `yUSDC`**  
   - **Transfers equivalent `USDC`** from `usdc_yield_vault` → `usdc_deposit_vault`  
   - **Mints `xUSDC`** to the user for the claimed amount  
3. The user may optionally **redeem the received `xUSDC`** via the principal redemption flow to withdraw yield in `USDC`.  

### Yield Attribution via Off-Chain Indexing

One of the most critical technical components of this system is the ability to accurately attribute yield to `xUSDC` holders **based on the duration and amount of their holdings**, even as `xUSDC` is **freely transferred across wallets**.  

To solve this, we need to introduce a high-resolution, event-driven **off-chain indexing system**, which monitors user balances over fixed epochs and computes fair yield shares.

**Goal:**  

- Ensure **accurate yield distribution** based on `xUSDC` holding duration and amount.  

**Epoch Design**

- **Epoch Duration:** 8 hours (configurable)  
- Each epoch is a discrete yield calculation window.  
- At the end of every epoch, the system computes and distributes yield via `yUSDC` minting.  

**Event-Driven Indexer**

- The off-chain indexer listens to the blockchain for all `xUSDC`-related activity:  
  - `Transfer(address from, address to, uint256 amount)`  
  - `Mint(address to, uint256 amount)`  
  - `Burn(address from, uint256 amount)`  
- Each event updates a **time-series ledger** of balance state per wallet.  

**Yield Attribution Algorithm**

The core of the indexer computes **time-weighted balances** for each wallet during the epoch.  

- Let:  
  - `T` = total epoch time (in seconds)  
  - `Bᵢ(t)` = balance of wallet `i` at time `t`  
  - `Y` = total protocol USDC yield generated during the epoch  
- Then the yield share for wallet `i` is calculated as:  

**Yield Distribution**

At the end of each epoch:  

- The engine mints `yUSDC` equal to the `USDC` yield added in `usdc_yield_vault` during the epoch.  
- Yield is attributed per user and `yUSDC` is either:  
  - Locked into protocol (original suggestion)  
  - **Distributed directly** to wallets (`mint(address, amount)`)  
  - **Lazily**, using Merkle proofs for users to claim via a `claim(yUSDC)` function  

Merkle-based claims enhance scalability and reduce gas costs by batching the on-chain state changes.

---

## Solution 2: Rebase Token Architecture

### What is rebase token?

A **rebase token** is a token whose **total supply adjusts automatically** at regular intervals (or via manual triggers) to maintain a **target price or peg** (e.g., $1). Instead of changing the token’s price on the market directly, it changes the **supply held by each wallet proportionally**.

### Rebasing Properties

- The token price stays fixed (e.g., ~$1), while users’ balances increase over time to reflect accrued yield.  
- Each user holds a fixed amount of **internal shares**, and their **visible token balance** is calculated as:  

  `visible balance = shares × index`  

  where the **index** represents the cumulative yield, and index increases over time as yield is distributed.  

- When tokens are transferred, what actually moves are the **underlying shares**, ensuring rebases remain accurate across wallets.  
- Balance reflects yields: Users’ token balances **automatically increase** to reflect underlying asset growth or protocol yield.  

### Rebasing Workflow

1. An **oracle tracks the total value locked (TVL) in USDC**, which includes both user deposits and any yield accrued. This TVL is compared against the total supply of rUSDC to determine its **fair market price**.  
2. Based on the computed price:  
   - If **price > $1.00** → the protocol **increases the supply of rUSDC** to bring the price down.  
   - If **price < $1.00** → the protocol **reduces the supply of rUSDC** to push the price up.  
3. Impact on price and holding:  
   - This dynamic adjustment keeps rUSDC price aligned with its USDC backing.  
   - Each user's balance scales up/down, but ownership share remains the same.  
4. Redeeming: To **atomically burn rUSDC and transfer USDC** in a single, secure transaction, we need to create a **`redeem()` function** that performs both actions in sequence.  
   - ✅ User can’t burn rUSDC without getting USDC  
   - ✅ User can’t receive USDC without giving up rUSDC  
   - ✅ Everything happens in **one transaction**, or nothing happens (via Solidity’s revert behavior)  

### rUSDC - The rebase Token

- **Type:** ERC20Detailed based upon Ampleforth rebase token architecture  
- **Peg:** 1 rUSDC = 1 USDC (includes principal + yield)  
- **Function:** Tracks both principal and accrued yield through user shares  
- **Accessible:** Available in wallet with **24/7/365 Minting & Redemption**  

| Token Type | Balance Changes? | Price Changes? | Pegged to $1? |
| --- | --- | --- | --- |
| Rebasing Token | ✅ Yes | ❌ No | ✅ Yes |
| Wrapped Rebasing | ❌ No | ✅ Yes | ❌ No |

`Total_rUSDC_assets` = `Total_USDC_deposit` + `Total_USDC_yield`  

`Value_per_share_rUSDC` = `Total_rUSDC_Share` / `Total_rUSDC_assets`  

`User_rUSDC_assets` = `User_rUSDC_share` * `Total_rUSDC_assets`  / `Total_rUSDC_Share`  

### Technical

Refer original UFragments contract by Ampleforth team: [Ampleforth Contracts](https://github.com/ampleforth/ampleforth-contracts/blob/master/contracts/UFragments.sol)  

| `_totalSupply` | `Total_rUSDC_assets` | Notes |
| --- | --- | --- |
| `_totalSupply` | `Total_rUSDC_assets` | 1. Represents the total circulating supply of the token before the rebase operation is applied.<br>2. It changes after each rebase, depending on the rebase mechanism. |
| `TOTAL_GONS` | `Total_rUSDC_Share` |  |
| `_gonsPerFragment` | `Value_per_share_rUSDC` |  |
| `_gonBalances` |  | Tracks balances in **gons**, a smaller precision unit to avoid rounding errors. |
| `balanceOf()` |  | Converts gon balance → token units by dividing by the scaling factor. |
| `rebase()` |  | Adjusts supply up or down based on `supplyDelta`. |

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract RebaseToken is IERC20, Ownable {
    string public name = "Rebase USD";
    string public symbol = "rUSD";
    uint8 public decimals = 18;

    AggregatorV3Interface public priceOracle;

    uint256 public targetPrice = 1e8; // $1.00 with 8 decimals (Chainlink style)
    uint256 public rebaseInterval = 1 hours;
    uint256 public lastRebase;

    uint256 private constant INITIAL_SUPPLY = 1_000_000 ether;
    uint256 private constant TOTAL_GONS = type(uint256).max - (type(uint256).max % INITIAL_SUPPLY);

    uint256 private _totalSupply = INITIAL_SUPPLY;
    uint256 private _gonsPerFragment = TOTAL_GONS / INITIAL_SUPPLY;

    mapping(address => uint256) private _gonBalances;
    mapping(address => mapping(address => uint256)) private _allowances;

    event Rebased(uint256 newTotalSupply, int256 supplyDelta);

    constructor(address _oracle) {
        priceOracle = AggregatorV3Interface(_oracle);
        _gonBalances[msg.sender] = TOTAL_GONS;
        emit Transfer(address(0), msg.sender, _totalSupply);
    }

    function totalSupply() public view override returns (uint256) {
        return _totalSupply;
    }

    function balanceOf(address account) public view override returns (uint256) {
        return _gonBalances[account] / _gonsPerFragment;
    }

    function transfer(address recipient, uint256 amount) public override returns (bool) {
        uint256 gonValue = amount * _gonsPerFragment;
        _gonBalances[msg.sender] -= gonValue;
        _gonBalances[recipient] += gonValue;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function allowance(address owner, address spender) public view override returns (uint256) {
        return _allowances[owner][spender];
    }

    function approve(address spender, uint256 amount) public override returns (bool) {
        _allowances[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(address sender, address recipient, uint256 amount) public override returns (bool) {
        uint256 gonValue = amount * _gonsPerFragment;
        _allowances[sender][msg.sender] -= amount;
        _gonBalances[sender] -= gonValue;
        _gonBalances[recipient] += gonValue;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    // 🔵 MINT
    function _mint(address account, uint256 amount) internal {
        require(account != address(0), "Mint to zero address");

        _totalSupply += amount;
        _gonsPerFragment = TOTAL_GONS / _totalSupply;

        uint256 gonValue = amount * _gonsPerFragment;
        _gonBalances[account] += gonValue;

        emit Transfer(address(0), account, amount);
    }

    // 🔴 BURN
    function _burn(address account, uint256 amount) internal {
        require(account != address(0), "Burn from zero address");

        uint256 gonValue = amount * _gonsPerFragment;
        _gonBalances[account] -= gonValue;
        _totalSupply -= amount;

        _gonsPerFragment = TOTAL_GONS / _totalSupply;

        emit Transfer(account, address(0), amount);
    }

    function rebase() external {
        require(block.timestamp >= lastRebase + rebaseInterval, "Wait more time");
        lastRebase = block.timestamp;

        uint256 currentPrice = getPrice();
        if (currentPrice == targetPrice) return;

        int256 supplyDelta = int256((_totalSupply * (currentPrice - targetPrice)) / targetPrice);

        if (currentPrice < targetPrice) {
            _totalSupply += uint256(supplyDelta);
        } else {
            _totalSupply -= uint256(supplyDelta);
        }

        _gonsPerFragment = TOTAL_GONS / _totalSupply;
        emit Rebased(_totalSupply, supplyDelta);
    }

    function getPrice() public view returns (uint256) {
        (, int price,,,) = priceOracle.latestRoundData();
        require(price > 0, "Invalid price");
        return uint256(price);
    }
}

