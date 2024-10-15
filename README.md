# Symbiotic

## Overview

Setting up a Stubchain validator for Symbiotic requires node configuration, environment setup, and validator transaction creation. This technical process demands a solid understanding of blockchain operations and command-line interfaces.

## 1. Set Up RPC URLs

Configure your environment with the necessary RPC URLs:

```
export BEACON_API_URLS=<your_beacon_rpc_url>
export ETH_API_URLS=<your_eth_rpc_url>
```

## 2. Start a Node

Follow these steps to initialize and run your node:

Set the network middleware address:
```
export MIDDLEWARE_ADDRESS=0x43574e3AeF4E3B29041AA34f09a4b1C8a2F4C1E6
```
Clone the repository or download the binary:
```
git clone https://github.com/symbioticfi/cosmos-sdk
```
Build the node:
```
make build-sym
```
## Navigate to the build directory or add the symd binary to your $PATH

Initialize the node:
```
symd init <your_moniker> --chain-id chain-D6yfsZ
```
Copy the genesis.json file to the~/.symapp/config/directory from thestubchaindirectory in the cosmos-sdk repository:
```
cp stubchain/genesis.json ~/.symapp/config/
```
Add our peers to the ~/.symapp/config/config.toml file. In the [p2p] section, update both the seeds and persistent_peers parameters.
```
"716f3d6c6b6cdb2c3e980641c8a2f2e2a5089cbb@node-01.stubchain.symbiotic.fi:26656,30a07da2029074bd48d5bf53423f8f4ebe706167@node-04.stubchain.symbiotic.fi:26656,f3e411655b1af5f83f31ecd8acc2567f358ac18f@node-06.stubchain.symbiotic.fi:26656"
```

Create operator keys (learn more):
```
# Choose a keyring backend from the URL above
symd keys add <key_name> --keyring-backend <keyring_backend>

Start the node

symd start
```
Allow the node to fully synchronize with the network. This process may take some time, depending on network conditions and the current blockchain height. Once synced, your node will be up-to-date with the latest blocks and ready for validator creation. Verify synchronization status with this command: `jsx

this should return "false"
```
curl -s 127.0.0.1:26657/status | jq '.result.sync_info.catching_up' `
```
## 3. Create Validator
After your node has synchronized and our test network administrators have registered your operator in the middleware contract, you can create your validator:

Create and populate validator.json with your information
```
{
    "pubkey": {
        "@type": "/cosmos.crypto.ed25519.PubKey",
        "key": "<your pub_key from priv_validator_key.json>"
    },
    "moniker": "<your node moniker from step 2.5>",
    "commission-rate": "0.1",
    "commission-max-rate": "0.1",
    "commission-max-change-rate": "0.1"
}
```
Broadcast the create-validator transaction:
```
symd tx symStaking create-validator validator.json \
  --chain-id=chain-D6yfsZ \
  --from=<key_name> \
  --keyring-backend=<keyring_backend>
```
Verify your validator status by querying the network. Information about your node should appear, though it may take some time to be added as a validator since synchronization with Symbiotic occurs every 10th block height:
```
symd query symStaking validators
```

## 4. Monitor Your Node

Set up monitoring for your validator node to ensure optimal performance and uptime. Remember to configure automatic restarts in case of Symbiotic update errors.

By following these steps, you’ll successfully set up and run a Cosmos SDK validator node for the Symbiotic network. Remember to stay updated with the latest network developments and maintain your node regularly.

⚡ Validator activation
To complete the integration process, you need to send your operator data to our test network administrators. This allows us to register your operator in the network middleware contract, which maintains the active operator set data.

Please provide the following information:

Your Ethereum operator address

Validator consensus address in hexadecimal format from the~/.symbiotic/config/priv_validator_key.jsonfile (this file will be created after executing the Cosmos SDK setup instructions)

Example of the validator consensus address:
```
{
  "address": "970B82F2927D94D29DCA6CF0F21DC164154C28DA"
}
```
You can submit your operator address and pubkey by creating an issue in our GitHub repository - see template.

Once we receive your information, our network administrators will register your operator, allowing you to participate in the network.
