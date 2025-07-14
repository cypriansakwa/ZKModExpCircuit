# ZKModExpCircuit

This project demonstrates a simple **Zero-Knowledge Proof (ZKP)** circuit using **Noir** to prove knowledge of a modular exponentiation computation. The circuit allows you to prove that you know the values for `base`, `exponent`, and `modulus` such that `expected_result = base^exponent mod modulus`, **without revealing** the base or the exponent.

---

## 📝 Circuit Description

The circuit computes:

expected_result $= \mathrm{base^exponent} \bmod \mathrm{modulus}$

All values (`base`, `exponent`, `modulus`) are private inputs except `modulus` (which can be made public) and the `expected_result` (which is always public).

### Circuit Code

```rust
fn mod_exp(base: u32, exponent: u32, modulus: u32) -> u32 {
    let mut result: u32 = 1;
    let mut base_power: u32 = base % modulus;
    let mut exp: u32 = exponent;

    for _ in 0..32 { // Supports up to 32-bit exponent
        if exp & 1 == 1 {
            result = (result * base_power) % modulus;
        }
        base_power = (base_power * base_power) % modulus;
        exp = exp >> 1;
    }

    result
}

fn main(base: u32, exponent: u32, modulus: pub u32, expected_result: pub Field) {
    assert(modulus != 0);
    let result: u32 = mod_exp(base, exponent, modulus);
    let result_field: Field = result as Field;
    assert(result_field == expected_result);
}
```

**Inputs:**
- `base` (`u32`): The private base.
- `exponent` (`u32`): The private exponent.
- `modulus` (`pub u32`): The (optionally) public modulus.
- `expected_result` (`pub Field`): The public output, must be equal to `base^exponent mod modulus`.

The main constraint enforced by the circuit is:

$$
\mathrm{expected_result} = \mathrm{base^exponent} \bmod \mathrm{modulus}
$$

---


## 📁 Project Structure

- `/circuits` — Contains the Noir circuit and build scripts.
- `/contract` — Foundry project with the Solidity verifier and integration test contract.
- `/js` — JavaScript code to generate proof and save as a file.

**Tested with:**  
- Noir >= 1.0.0-beta.6  
- bb >= 0.84.0

---

## 🚀 Installation / Setup

```bash
# Update Foundry submodules
git submodule update

# Build circuits and generate the verifier contract
(cd circuits && ./build.sh)

# Install JS dependencies
(cd js && yarn)
```

---

## 🛠️ Proof Generation in JavaScript

```bash
# Use bb.js to generate proof and save to a file
(cd js && yarn generate-proof)

# Run Foundry test to verify the generated proof
(cd contract && forge test --optimize --optimizer-runs 5000 --gas-report -vvv)
```

---

## 🛠️ Proof Generation with bb CLI

```bash
# Generate witness
nargo execute

# Generate proof with keccak hash
bb prove -b ./target/ZKModExpCircuit.json -w target/ZKModExpCircuit.gz -o ./target --oracle_hash keccak

# Run Foundry test to verify proof
(cd contract && forge test --optimize --optimizer-runs 5000 --gas-report -vvv)
```

---
### 🧭 Ecosystem Attribution

This project is indexed in the [Electric Capital Crypto Ecosystems Map](https://github.com/electric-capital/crypto-ecosystems).

**Source**: Electric Capital Crypto Ecosystems  
**Link**: [https://github.com/electric-capital/crypto-ecosystems](https://github.com/electric-capital/crypto-ecosystems)  
**Logo**: ![Electric Capital Logo](https://avatars.githubusercontent.com/u/44590959?s=200&v=4)

💡 _If you’re working in open source crypto, [submit your repository here](https://github.com/electric-capital/crypto-ecosystems) to be counted._

Thank you for contributing and for reading the contribution guide! ❤️
