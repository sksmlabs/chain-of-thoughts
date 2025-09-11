# Solana Notes
---
#### Solana Frameworks
- Anchor
- Native
- Seahorse

#### Solana Concepts
- Account
- Rent

#### Solana Programs
- system_program
- token_program

---
#### Anchor Project directory
- Program folder
- Tests folder
- Target folder
- Anchor.toml file
- .anchor folder
- App folder

#### Anchor Logic
- Programs
- Instruction Handler
- Accounts
- Context

#### Anchor Program Macros
- declare_id: Specifies the program's on-chain address
- #[program]: Specifies the module containing the program’s instruction logic
- #[derive(Accounts)]: Applied to structs to indicate a list of accounts required by an instruction
- #[account]: Applied to structs to create custom account types for the program


#### Anchor Context
- ctx.accounts: The accounts required for the instruction
- ctx.program_id: The program's public key (address)
- ctx.remaining_accounts: Additional accounts not specified in the Accounts struct.
- ctx.bumps: Bump seeds for any Program Derived Address (PDA) accounts specified in the Accounts struct


#### Anchor Account Constraints

#### [Anchor Account Types](https://www.anchor-lang.com/docs/references/account-types)
- Account
    - New Account
    - Mint
- Signer
- Program
    - System
    - Token
- Sysvar
    - Rent

#### Why does Anchor or Anza use Agave?
- Anchor v0.30+ and the Anza CLI stack (Anza Solana CLI + SBF SDK) now use Agave under the hood to:
- Replace the old cargo-build-bpf flow.
- Support a more future-proof SBF build pipeline.
- Enable developer customization and shared tooling for new features.

#### Anchor Notes
- Anchor run does not spin up the validator unless your script does it

---

[*TOML*](https://toml.io/) (*Tom’s Obvious, Minimal Language*) 
- We can create a project using `cargo new`.
- We can build a project using `cargo build`.
- We can build and run a project in one step using `cargo run`.
- We can build a project without producing a binary to check for errors using `cargo check`.
- Instead of saving the result of the build in the same directory as our code, Cargo stores it in the *target/debug* directory.
- When your project is finally ready for release, you can use `cargo build --release`. This command will create an executable in *target/release* instead of *target/debug*.

---
#### Rust Commands
```
// Installing Rust

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y

. "$HOME/.cargo/env"

rustc --version
```

```
// Variables

let x = 5;
let mut x = 5;
```
---
#### Node Commands
```
// Install yarn
npm install -g yarn

// Version
yarn --version

// Install ts-mocha
npm install -g ts-mocha

// Version
ts-mocha --version

// Install ts-node and other dependencies
npm install --save-dev ts-node typescript ts-mocha mocha chai @types/mocha @types/chai
```
---
#### Solana Commands
```
// Installing Solana

sh -c "$(curl -sSfL https://release.anza.xyz/stable/install)"

echo 'export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"' >> ~/.zshrc

source ~/.zshrc

solana --version
```

```
// Remove Solana
rm -rf ~/.local/share/solana
rm -rf ~/.cache/solana
rm -rf ~/.config/solana

// Check Environment
env | grep solana
```

```

// Check config
solana config get

// Set Config
solan config set --url https://127.0.0.1:8899

// if you are running local validator with the following command: Point your Solana CLI utility to it with
solana config set --url localhost
```

```
// Generate keypair local
solana-keygen new

// Check address
solana address
```

```
// Run validator explicily in local
solan-test-validator --reset

// Update the Solana CLI config to localhost
solana config set -ul
```

```
// Airdrop 
solana airdrop [amount]

// Check balance
solana balance
```

```
// Confirm signature
solana confirm [signature_hash]
```

```
// Check owner of deployed program
solana account [PROGRAM_ID]
```
```
// Check authority of deployed program
solana program show [PROGRAM_ID]
```
---
#### Anchor Commands
```
// Install Anchor
cargo install --git https://github.com/coral-xyz/anchor anchor-cli --locked

// Is anchor binary present in cargo
ls ~/.cargo/bin/anchor

// Check that AVM was installed successfully
avm --version
avm install latest
avm use latest

//
export PATH="$HOME/.avm/bin:$PATH"
source ~/.zshrc

// Check anchor cli version
anchor --version
```

```
// Project
anchor init [project_name]

// Build
anchor build

// Deploy
anchor deploy
anchor deploy --program-name [PROGRAM_NAME]

// Test
anchor test
anchor test --skip-local-validator
anchor run [SCRIPT_TEST_NAME_IN_ANCHOR_TOML]
```

```
// Create new program
cargo new programs/[PROGRAM_NAME] --lib
```
---
```
// This account must be an SPL token mint account.
// Anchor will deserialize it into a typed Mint struct so we can safely use its fields.

pub mint: Account<'info, Mint>,

```
```
// We want access to the Rent sysvar account in this instruction
// We need to include rent if you're creating a new account that must be rent-exempt

pub rent: Sysvar<'info, Rent>,
```