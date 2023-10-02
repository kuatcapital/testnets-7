# entrypoint-pubtest-1

- [Public Endpoints](#public-endpoints)
- [Explorers](#explorers)
- [Listings](#listings)
- [Run a Full Node](#run-a-full-node)
  - [From Scratch Using Cosmovisor](#from-scratch-using-cosmovisor)
  - [Handling Upgrades Using Cosmovisor](#handling-upgrades-using-cosmovisor)

## Public Endpoints

- RPC: https://testnet-rpc.entrypoint.zone/
- REST: https://testnet-rest.entrypoint.zone/swagger/

## Explorers

- https://explorer.entrypoint.zone/entrypoint
- https://testnet.ping.pub/entrypoint

## Listings

- https://github.com/ping-pub/ping.pub/blob/main/chains/testnet/entrypoint.json
- https://github.com/cosmos/chain-registry/tree/master/testnets/entrypointtestnet

## Faucet

Here are details about our running faucet:

- Faucet channel on EntryPoint Discord: https://discord.com/channels/1047819102514855976/1148230965928411236
- Discord faucet source code: https://github.com/hyphacoop/cosmos-discord-faucet

## Run a Full Node

> **NOTE: Participation in the public testnet is currently in closed beta but will eventually be opened. Some of the steps below will not work for non-participants.**

> Based on material from:
> 
> - https://docs.cosmos.network/main/tooling/cosmovisor
> - https://docs.osmosis.zone/networks/join-mainnet/#set-up-cosmovisor

### From Scratch Using Cosmovisor

Download binary and genesis:

- Binary from: https://github.com/entrypoint-zone/entrypoint/releases/tag/v1.0.0.
- Genesis from: https://github.com/entrypoint-zone/testnets/blob/main/entrypoint-pubtest-1/genesis.json.

Initialise node config files (this assumes you renamed the binary to `entrypointd`):

- `entrypointd init <node-name> --chain-id entrypoint-pubtest-1`.

Install and configure Cosmovisor:

- Install Cosmovisor: `go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest`.
- If you need a specific version of go you can run e.g. `go install golang.org/dl/go1.20@latest`.
- Set environment variables (you can use `:
    ```bash
    echo "# Setup Cosmovisor" >> ~/.profile
    echo "export DAEMON_NAME=entrypointd" >> ~/.profile
    echo "export DAEMON_HOME=$HOME/.entrypoint" >> ~/.profile
    echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=false" >> ~/.profile
    echo "export DAEMON_LOG_BUFFER_SIZE=512" >> ~/.profile
    echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.profile
  
    # Check https://docs.cosmos.network/main/tooling/cosmovisor for more configuration options.
    ```
- Apply environment variables: `source ~/.profile`.
- Initialise Cosmovisor directories: `cosmovisor init <path-to-entrypointd-binary>` (hint: `whereis entrypointd` for the path).

Configure node:

- Configure `minimum-gas-prices` in `nano ~/.entrypoint/config/app.toml`.
- Configure `persistent_peers`/`seeds` in `nano ~/.entrypoint/config/config.toml`.
- Move the downloaded genesis file to `~/.entrypoint/config/genesis.json`.

Start node: `cosmovisor run start`.

> It is highly recommended to run the above as a service, so that it can run in the background.

### Handling Upgrades Using Cosmovisor

- Apply environment variables: `source ~/.profile`.
- Download new binary from https://github.com/entrypoint-zone/entrypoint/releases.
- Register the upgrade: `cosmovisor add-upgrade <upgrade-name> <new-entrypointd-binary>`.
- Sit back and relax.
