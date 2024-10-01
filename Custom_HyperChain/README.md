# Project - Building Blockchain Using HyperSDK

## Project Challenge
The goal of this project is to create a custom virtual machine (VM) that allows users to mint and transfer tokens. HyperSDK offers an excellent solution, enabling the development of a blockchain tailored to specific needs. Using HyperSDK, you can define the rules and functionalities of your chain, including token minting, transferring, and managing order books for trading assets.

## What is TokenEVM?
The MetaCrafters team developed TokenEVM to showcase the use of HyperSDK in familiar applications such as token minting and trading. TokenEVM allows users to create any asset, mint more of it, modify its metadata, and burn the asset. Additionally, TokenEVM includes an on-chain exchange that enables order creation and partial order fulfillment. For ease of use, it bundles a powerful CLI tool and serves RPC requests for trades via an in-memory order book that syncs blocks.

## Key Features of TokenEVM

### Arbitrary Token Minting
TokenEVM simplifies the process of creating, minting, and transferring user-generated tokens. Asset owners receive admin control, allowing them to mint additional tokens, update metadata, or transfer/revoke ownership. Assets are natively supported in TokenEVM, and the storage engine is optimized for efficiency. The balance of each asset only requires 72 bytes of state, making it possible to parallelize transfers without conflicting account touches.

### Trade Any Two Tokens
TokenEVM supports fully on-chain trading. Users can create offers with specific rates/tokens and others can fill these offers. An in-memory order book manages trades over RPC, using a 152-byte state format optimized for trading efficiency. The parallelism within trading is enabled, allowing for simultaneous execution without conflicts, unlike pool-based trading mechanisms like AMMs.

### In-Memory Order Book
To make trading interactions easier, TokenEVM includes an in-memory order book that listens for on-chain orders across asset pairs. This feature uses HyperSDK’s transaction feed to keep the order state up-to-date in-memory, arranging assets by the best rate.

## Steps to Reproduce

1. **Clone the Repository**
   - Start by cloning the project repository.

2. **Normalize Dependencies**
   - Run `go mod tidy` to ensure all dependencies are installed correctly.

3. **Configure Project Constants**
   - Update constants in `consts/consts.go` with any missing details.

4. **Register Actions**
   - Register `Create_Asset` and `Mint_Asset` actions in `registry/registry.go`.

5. **Run the VM Locally**
   - Ensure Go is installed and set up in your terminal. If Go is not on your path, run:
     ```bash
     export PATH=$PATH:$(go env GOPATH)/bin
     ```
     Alternatively, use:
     ```bash
     export PATH=$PATH:/usr/local/go/bin
     ```

6. **Execute Scripts**
   - Run the following commands:
     ```bash
     MODE="run-single" ./scripts/run.sh
     ./scripts/build.sh
     ```

   If you encounter a "Permission Denied" error, try running the scripts with the `bash` command:
   ```bash
   bash ./scripts/run.sh
   ```

7. **Load the Demo Private Key**
   - Import the demo private key:
     ```bash
     ./build/token-cli key import demo.pk
     ./build/token-cli chain import-anr
     ```

## Creating & Minting Assets

### Step 1: Create an Asset
To create an asset, run the following command:
```bash
./build/token-cli action create-asset
```
The output will display information like:
```bash
database: .token-cli
address: token1rvzhmceq997zntgvravfagsks6w0ryud3rylh4cdvayry0dl97nsjzf3yp
chainID: Em2pZtHr7rDCzii43an2bBi1M2mTFyLN33QP1Xfjy7BcWtaH9
metadata: MarioCoin
txID: 27grFs9vE2YP9kwLM5hQJGLDvqEY9ii71zzdoRHNGC4Appavug
```

### Step 2: Mint an Asset
After creating the asset, mint it by running:
```bash
./build/token-cli action mint-asset
```
The output will look like:
```bash
database: .token-cli
address: token1rvzhmceq997zntgvravfagsks6w0ryud3rylh4cdvayry0dl97nsjzf3yp
chainID: Em2pZtHr7rDCzii43an2bBi1M2mTFyLN33QP1Xfjy7BcWtaH9
assetID: 27grFs9vE2YP9kwLM5hQJGLDvqEY9ii71zzdoRHNGC4Appavug
amount: 10000
```

### Step 3: Check Your Balance
Verify that minting was successful by checking the balance:
```bash
./build/token-cli key balance
```
The output will display:
```bash
balance: 10000 27grFs9vE2YP9kwLM5hQJGLDvqEY9ii71zzdoRHNGC4Appavug
```

## Transfer Assets

To transfer tokens between subnets, use:
```bash
./build/token-cli action export
```
The output will display transfer details and allow for execution on the destination chain.
