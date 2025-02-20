Lightweight Blockchain Node
This document provides instructions for installing and running a node of the Lightweight Blockchain application. The node includes support for cryptocurrency transactions, smart contracts, Proof‑of‑Work (with dynamic difficulty adjustment), and P2P communication. In addition, it features:

Hybrid Consensus: A combination of PoW for block proposal and a PoS-inspired validator voting mechanism for block finalization.
Dynamic Difficulty Adjustment: The mining difficulty adjusts automatically based on the time taken to mine recent blocks.
Sharding: A basic beacon chain architecture partitions the blockchain into shards to improve scalability.
Auto-Mining: Nodes automatically mine new blocks when there are pending transactions.
Dynamic Contract Registry and Execution Environment: Developers can deploy and execute smart contracts dynamically (using, for example, a WebAssembly runtime), without needing direct access to the codebase.
Edge Device Optimizations: Pruning, archiving, and light client modes help keep the local storage footprint low, making it ideal for resource-constrained devices.
The node is designed to run on edge devices with limited resources while providing a full suite of features for decentralized application development.

Table of Contents
Prerequisites
Installation
Local Build
Docker Build
Configuration
Command-Line Flags
Environment Variables
Running the Node
Running as a Full Node
Running as a Light Client
P2P Networking
Pruning and Archiving
Interacting with the Node
Troubleshooting
Additional Resources
Prerequisites
Go 1.20 or higher:
Make sure you have Go installed. You can download it from golang.org.

Git:
Used for cloning the repository.

Docker (Optional):
To run the node in a containerized environment, install Docker.

Installation
Local Build
Clone the Repository:

bash
Copy
(https://github.com/sumeetingenuity/Blockchain.git)
cd cryptocypher
Download Dependencies:

bash
Copy
go mod tidy
Build the Binary:

From the project root, run:

bash
Copy
go build -o cryptocypher ./cmd
This produces an executable named cryptocypher.

Docker Build
Build the Docker Image:

Ensure your Dockerfile is in the root directory, then run:

bash
Copy
docker build -t cryptocypher:latest .
(Optional) Use Docker Compose:

If you have a docker-compose.yml for multi-node testing, run:

bash
Copy
docker-compose up --build
Configuration
The node can be configured using command-line flags. The key flags are:

-listenAddress:
The address and port the node listens on (default: localhost:8000).

-peerAddresses:
A comma-separated list of peer addresses (default: localhost:8001).

-light:
Optional flag to run the node in light client mode (loads only block headers).

Example
To run a full node on port 8000 and connect to a peer on port 8001:

bash
Copy
./cryptocypher -listenAddress="localhost:8000" -peerAddresses="localhost:8001"
To run in light client mode:

bash
Copy
./cryptocypher -light -listenAddress="localhost:8000" -peerAddresses="localhost:8001"
Running the Node
Running as a Full Node
A full node stores the complete blockchain and participates fully in consensus and transaction validation.

bash
Copy
./cryptocypher -listenAddress="localhost:8000" -peerAddresses="localhost:8001"
The node will:

Initialize a blockchain with genesis and subsequent blocks.
Process transactions (including coinbase rewards).
Mine blocks using Proof‑of‑Work.
Connect with peers via the P2P network.
Periodically prune old blocks to conserve storage.
Running as a Light Client
A light client loads only block headers to reduce storage requirements:

bash
Copy
./cryptocypher -light -listenAddress="localhost:8000" -peerAddresses="localhost:8001"
In light client mode, the node will operate with minimal local storage and may request full block details from full nodes as needed.

P2P Networking
Each node starts a P2P server that:

Listens on the address specified by -listenAddress.
Connects to known peers listed in -peerAddresses.
Exchanges messages for chain synchronization, block broadcasting, and heartbeats.
Peers communicate using a JSON-based protocol with commands such as:

GET_CHAIN
CHAIN_UPDATE
NEW_BLOCK
HEARTBEAT
Pruning and Archiving
To reduce local storage:

The node automatically prunes older blocks when the blockchain grows beyond a certain threshold (e.g., more than 100 blocks).
Pruned blocks are archived to a JSON file (named with a timestamp), so historical data can be retrieved if needed.
The pruning process is automatically triggered (every 10 seconds in the sample configuration) for full nodes.

Interacting with the Node
Smart Contract Execution:
The node supports smart contracts (e.g., an AdditionContract). You can invoke contract methods via transactions or direct API calls (if you expose a REST interface).

Ledger and Wallet Functions:
The node maintains an account-based ledger for token balances. Users can send transactions (once signing and key management are implemented) to transfer tokens.

P2P Communication:
Nodes exchange blockchain data with peers to ensure consensus. You can monitor logs to see chain updates and block broadcasts.

Troubleshooting
Node Not Starting:
Ensure that the ports specified in -listenAddress and -peerAddresses are not in use.

Blockchain Out-of-Sync:
Check your P2P connectivity and verify that your nodes are correctly exchanging chain updates.

Storage Issues:
For edge devices with limited memory, ensure that pruning is active and that only necessary data is retained locally.

Logging:
Increase verbosity in logs (if needed) by modifying your node’s logging settings to troubleshoot network or consensus issues.

Additional Resources
Go Documentation
BoltDB Documentation
Go Mobile – for building on edge devices (Android, iOS)
Cryptocypher

API Documentation for Cryptocypher Blockchain Node
This document describes the REST API endpoints exposed by a Cryptocypher blockchain node. These endpoints allow external clients, wallet applications, and developers to interact with the blockchain network.

Note:
The node exposes these endpoints via its API server (default port 8080). All responses are in JSON format.

Table of Contents
Base URL
Endpoints
Chain and Block Retrieval
Balance Query
Transaction Submission
Smart Contract Execution
Contract Deployment
Peer Management
Status and Metrics
Manual Pruning
Examples
Error Handling
Additional Notes
Base URL
Assuming your node is running on port 8080, the base URL for all endpoints is:

cpp
Copy
http://<node_public_ip>:8080
Endpoints
1. Chain and Block Retrieval
GET /chain
Description: Returns the full blockchain.
Response: JSON array of blocks.
Example Response:

json
Copy
[
  {
    "index": 0,
    "timestamp": 1740069581,
    "prev_hash": "",
    "hash": "000159c1b69df05410de6e271d7be24f...",
    "nonce": 1234,
    "relationship_type": "one-to-one",
    "receivers": ["ReceiverA"],
    "text_data": "EncryptedTextDataXYZ",
    "audio_data": "EncryptedAudioDataABC",
    "video_data": "EncryptedVideoData123",
    "transactions": [...],
    "sub_blocks": [...],
    "difficulty": 3,
    "category": "main"
  },
  { ... }
]
GET /headers
Description: Returns only the block headers (for light clients).
Response: JSON array of block headers.
GET /block?hash={blockHash}
Description: Returns a specific block identified by its hash.
Query Parameter:
hash: The hash of the block.
Response: JSON object representing the block.
GET /latestBlock
Description: Returns the most recent (latest) block.
Response: JSON object representing the latest block.
GET /subblocks?hash={parentBlockHash}
Description: Returns the sub-blocks of a specific parent block.
Query Parameter:
hash: The hash of the parent block.
Response: JSON array of sub-block objects.
2. Balance Query
GET /balance?address={walletAddress}
Description: Returns the current balance for the specified wallet address.
Query Parameter:
address: The wallet address (public key in hex or a derived address).
Response: JSON object with address and balance.
Example Response:

json
Copy
{
  "address": "abcdef123456...",
  "balance": 100.0
}
3. Transaction Submission
POST /transaction
Description: Submits a new transaction to the node.
Request Body: JSON object representing a transaction.
Example:
json
Copy
{
  "sender": "abcdef123456...",  // Hex-encoded public key
  "recipient": "123456abcdef...",
  "amount": 25.0,
  "timestamp": 0, // Optionally, the node can override this with current time.
  "nonce": 1,
  "signature": "deadbeef..." // Hex-encoded digital signature
}
Response: HTTP 202 Accepted on success.
Note:
The node will verify the transaction signature before processing.
4. Smart Contract Execution
POST /contract
Description: Executes a smart contract call.
Request Body: JSON object containing:
contract_name: The name of the contract (e.g., "AdditionContract").
method: The method to call on the contract.
params: A JSON object with parameters.
Example:
json
Copy
{
  "contract_name": "AdditionContract",
  "method": "add",
  "params": {
    "a": 10.0,
    "b": 15.5
  }
}
Response: JSON object with the result, e.g.:
json
Copy
{
  "result": 25.5
}
5. Contract Deployment
POST /deployContract
Description: Deploys a new smart contract dynamically.
Request Body: JSON object containing:
contract_name: The unique name for the contract.
code: The contract code (e.g., WASM bytecode) as a hex-encoded string.
Example:
json
Copy
{
  "contract_name": "MyNewContract",
  "code": "deadbeef1234..."  // Hex-encoded contract code
}
Response: HTTP 200 OK with a success message.
6. Peer Management
GET /peers
Description: Returns the current list of known peers.
Response: JSON array of peer addresses.
POST /addPeer
Description: Adds a new peer to the node's peer list.
Request Body: JSON object containing:
peer: The address of the peer to add.
Example:
json
Copy
{
  "peer": "node3.example.com:8000"
}
Response: HTTP 202 Accepted on success.
GET /removePeer?peer={peerAddress}
Description: Removes a peer from the node's peer list.
Query Parameter:
peer: The address of the peer to remove.
Response: HTTP 200 OK on success.
7. Status and Metrics
GET /status
Description: Returns basic node status information.
Response: JSON object containing:
uptime: How long the node has been running.
block_height: Number of blocks in the blockchain.
peer_count: Number of connected peers.
ledger_entries: Number of ledger entries.
Example:
json
Copy
{
  "uptime": "1h23m45s",
  "block_height": 10,
  "peer_count": 3,
  "ledger_entries": 3
}
GET /metrics
Description: Returns metrics for the node (e.g., transactions per second, blocks per minute).
Response: JSON object with various metrics (dummy values for now).
Example:

json
Copy
{
  "transactions_per_second": 5.0,
  "blocks_per_minute": 2.0,
  "cpu_usage_percent": 15.0
}
8. Manual Pruning
GET /prune
Description: Manually triggers blockchain pruning and archiving.
Response: HTTP 200 OK with a message confirming that pruning was triggered.
Examples
Submitting a Transaction
bash
Copy
curl -X POST http://<node_ip>:8080/transaction \
  -H "Content-Type: application/json" \
  -d '{
    "sender": "abcdef123456...", 
    "recipient": "123456abcdef...",
    "amount": 25.0,
    "nonce": 1,
    "signature": "deadbeef..."
  }'
Deploying a Contract
bash
Copy
curl -X POST http://<node_ip>:8080/deployContract \
  -H "Content-Type: application/json" \
  -d '{
    "contract_name": "MyNewContract",
    "code": "deadbeef1234..."
  }'
Checking Node Status
bash
Copy
curl http://<node_ip>:8080/status
Error Handling
If an endpoint encounters an error, it will typically return an HTTP error status (e.g., 400 or 500) along with an error message in the response body.

