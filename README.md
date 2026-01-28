# ZK Noir ecrecover

This is a simple Noir demo that shows how to use dependencies in a Noir project.

This circuit verifies a signature without revealing the signing address.

## Usage

### 1. Clone this repo

```bash
git clone https://github.com/f2hard3/zk-ecdsa
```

### 2. Create the Prover.toml file

```bash
nargo check
```

Then fill in the inputs in the `Prover.toml` file.

### Generating the inputs

#### Generate the message

```bash
cast keccak "hello"
```

#### Sign the message

```bash
cast wallet sign --no-hash --account <name-of-your-keystore> <bytes-output>
```

#### Get your public key

```bash
cast wallet public-key --account <name-of-your-keystore>
```

The first 32 bytes are the x value and the second 32 bytes are the y value.

### Paste into inputs.txt as bytes

Add the values in the file:

```txt
expectedAddress = "0x52d64ED1fd0877797e2030fc914259e052F2bD67"
hashed_message = "0x1c8aff950685c2ed4bc3174f3472287b56d9517b9c948127319a09a7a36deac8"
pub_key_x = "533367042c3e9456fec155940165c28c01fe6e28601337df50562ddc4f36bfb9"
pub_key_y = "7dcafe27cadbe10861bb67e0599382f79dc29c1d39d93b10f716c8ceff1743ed"
signature = "0xdd935f778351217ec02e6f857e47f0cea837380f30eccf63742b9a97cf1872b4316700b028504591f6b31203b86ccb3363353eedd8f9cf4f344a769c689525b31b"
```

### Populate the Prover.toml

```bash
chmod +x generate_inputs.sh
./generate_inputs.sh
```

### 3. (optional) Compile the circuit

Do this step if you want to create a verifier smart contract without creating a proof (if you want users to create witnesses and proofs later).

```bash
nargo compile
```

### 4. Execute the circuit and generate a witness

This step will also compile the circuit.

```bash
nargo execute
```

### 5. Create the proof

```bash
bb prove -b ./target/zk-ecdsa.json -w ./target/zk-ecdsa.gz -o ./target
```

```bash
bb prove --oracle_hash keccak -b ./target/zk-ecdsa.json -w ./target/zk-ecdsa.gz -o ./target
```

### 6. Create the verification key

#### 6.(a) For off-chain verification

```bash
bb write_vk -b ./target/zk-ecdsa.json -o ./target
```

#### 6.(b) For an on-chain verifier contract

```bash
bb write_vk --oracle_hash keccak -b ./target/zk-ecdsa.json -o ./target
```

### 7. Verification

#### 7.(a) Off-chain verification

```bash
bb verify -k ./target/vk -p ./target/proof
```

#### 7.(b) Generate an on-chain verifier contract

```bash
bb write_solidity_verifier -k ./target/vk -o ./target/Verifier.sol
```
