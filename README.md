# Lite-Blockchain
A lightweight blockchain for edge deviecs

# Lightweight Blockchain Node

This document provides instructions for installing and running a node of the Lightweight Blockchain application. The node includes support for cryptocurrency transactions, smart contracts, Proof-of-Work (with dynamic difficulty adjustment), and P2P communication. In addition, it features:

- **Hybrid Consensus:** A combination of PoW for block proposal and a PoS-inspired validator voting mechanism for block finalization.
- **Dynamic Difficulty Adjustment:** The mining difficulty adjusts automatically based on the time taken to mine recent blocks.
- **Sharding:** A basic beacon chain architecture partitions the blockchain into shards to improve scalability.
- **Auto-Mining:** Nodes automatically mine new blocks when there are pending transactions.
- **Dynamic Contract Registry and Execution Environment:** Developers can deploy and execute smart contracts dynamically (using, for example, a WebAssembly runtime), without needing direct access to the codebase.
- **Edge Device Optimizations:** Pruning, archiving, and light client modes help keep the local storage footprint low, making it ideal for resource-constrained devices.

The node is designed to run on edge devices with limited resources while providing a full suite of features for decentralized application development.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Local Build](#local-build)
  - [Docker Build](#docker-build)
- [Configuration](#configuration)
  - [Command-Line Flags](#command-line-flags)
  - [Environment Variables](#environment-variables)
- [Running the Node](#running-the-node)
  - [Running as a Full Node](#running-as-a-full-node)
  - [Running as a Light Client](#running-as-a-light-client)
- [P2P Networking](#p2p-networking)
- [Pruning and Archiving](#pruning-and-archiving)
- [Interacting with the Node](#interacting-with-the-node)
- [Troubleshooting](#troubleshooting)
- [Additional Resources](#additional-resources)

## Prerequisites

- **Go 1.20 or higher:**
  - Make sure you have Go installed. You can download it from [golang.org](https://golang.org).
- **Git:**
  - Used for cloning the repository.
- **Docker (Optional):**
  - To run the node in a containerized environment, install Docker.

## Installation

### Local Build

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/sumeetingenuity/Blockchain.git
   cd cryptocypher
   ```
2. **Download Dependencies:**
   ```bash
   go mod tidy
   ```
3. **Build the Binary:**
   ```bash
   go build -o cryptocypher ./cmd
   ```
   This produces an executable named `cryptocypher`.

### Docker Build

1. **Build the Docker Image:**
   ```bash
   docker build -t cryptocypher:latest .
   ```
2. **(Optional) Use Docker Compose:**
   ```bash
   docker-compose up --build
   ```

## Configuration

The node can be configured using command-line flags. The key flags are:

- `-listenAddress`: The address and port the node listens on (default: `localhost:8000`).
- `-peerAddresses`: A comma-separated list of peer addresses (default: `localhost:8001`).
- `-light`: Optional flag to run the node in light client mode (loads only block headers).

### Example

To run a full node on port 8000 and connect to a peer on port 8001:

```bash
./cryptocypher -listenAddress="localhost:8000" -peerAddresses="localhost:8001"
```

To run in light client mode:

```bash
./cryptocypher -light -listenAddress="localhost:8000" -peerAddresses="localhost:8001"
```

## Running the Node

### Running as a Full Node

```bash
./cryptocypher -listenAddress="localhost:8000" -peerAddresses="localhost:8001"
```

The node will:
- Initialize a blockchain with genesis and subsequent blocks.
- Process transactions (including coinbase rewards).
- Mine blocks using Proof-of-Work.
- Connect with peers via the P2P network.
- Periodically prune old blocks to conserve storage.

### Running as a Light Client

A light client loads only block headers to reduce storage requirements:

```bash
./cryptocypher -light -listenAddress="localhost:8000" -peerAddresses="localhost:8001"
```

In light client mode, the node will operate with minimal local storage and may request full block details from full nodes as needed.

## P2P Networking

Each node starts a P2P server that:
- Listens on the address specified by `-listenAddress`.
- Connects to known peers listed in `-peerAddresses`.
- Exchanges messages for chain synchronization, block broadcasting, and heartbeats.

Peers communicate using a JSON-based protocol with commands such as:
- `GET_CHAIN`
- `CHAIN_UPDATE`
- `NEW_BLOCK`
- `HEARTBEAT`

## Pruning and Archiving

To reduce local storage:
- The node automatically prunes older blocks when the blockchain grows beyond a certain threshold (e.g., more than 100 blocks).
- Pruned blocks are archived to a JSON file (named with a timestamp), so historical data can be retrieved if needed.
- The pruning process is automatically triggered (every 10 seconds in the sample configuration) for full nodes.

## Interacting with the Node

### Smart Contract Execution

The node supports smart contracts (e.g., an AdditionContract). You can invoke contract methods via transactions or direct API calls (if you expose a REST interface).

### Ledger and Wallet Functions

The node maintains an account-based ledger for token balances. Users can send transactions (once signing and key management are implemented) to transfer tokens.

### P2P Communication

Nodes exchange blockchain data with peers to ensure consensus. You can monitor logs to see chain updates and block broadcasts.

## Troubleshooting

- **Node Not Starting:** Ensure that the ports specified in `-listenAddress` and `-peerAddresses` are not in use.
- **Blockchain Out-of-Sync:** Check your P2P connectivity and verify that your nodes are correctly exchanging chain updates.
- **Storage Issues:** For edge devices with limited memory, ensure that pruning is active and that only necessary data is retained locally.
- **Logging:** Increase verbosity in logs (if needed) by modifying your node’s logging settings to troubleshoot network or consensus issues.

## Additional Resources

- [Go Documentation](https://golang.org/doc/)
- [BoltDB Documentation](https://github.com/boltdb/bolt)
- [Go Mobile](https://golang.org/x/mobile) – for building on edge devices (Android, iOS)
- [Cryptocypher](https://github.com/sumeetingenuity/Blockchain)

