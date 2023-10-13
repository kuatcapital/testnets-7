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

> **NOTE: Participation in the public testnet is currently in closed beta but will eventually be opened. Some of the steps below will not work for participants with no beta access.**

> Based on material from:
> 
> - https://docs.cosmos.network/main/tooling/cosmovisor
> - https://docs.osmosis.zone/networks/join-mainnet/#set-up-cosmovisor

### From Scratch Using Cosmovisor

Download binary and genesis:

- Binary from: https://github.com/entrypoint-zone/entrypoint/releases/tag/v1.0.0.
- Genesis from: https://github.com/entrypoint-zone/testnets/blob/main/entrypoint-pubtest-1/genesis.json.

For convenience, copy the downloaded binary to `$GOPATH/bin/` with the name `entrypointd` for easy execution:

- `cp <downloaded-binary> $GOPATH/bin/entrypointd` (make sure `$GOPATH` variable exists)

Initialise node config files:

- `entrypointd init <node-name> --chain-id entrypoint-pubtest-1`.

Install and configure Cosmovisor:

- Install Cosmovisor: `go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest`.
- If you need a specific version of go you can run e.g. `go install golang.org/dl/go1.20@latest`. The executable will be `go1.20` in this example.
- Set environment variables:
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
- Initialise Cosmovisor directories: `cosmovisor init <path-to-entrypointd-binary>`.

Configure node:

- Configure `minimum-gas-prices` in `nano ~/.entrypoint/config/app.toml`.
  - Recommended value: `0.01ibc/E774302AE43D5FA03522C42B14823288E7DD1B2F54F85DFD3D6FC3E5FCF54645`.
- Configure `persistent_peers` in `nano ~/.entrypoint/config/config.toml`.
  - Recommended value: `127c2509947c4bc61c977e92a1457eb88f3964c3@185.107.68.148:26656,19a93a626fd1c20b276176d80b85e6c3db5eca0d@80.64.208.169:26656`
- Move the downloaded genesis file to `~/.entrypoint/config/genesis.json`.

At this point `cosmovisor run` will be the equivalent of running `entrypointd`. In fact, to run the node you can use `cosmovisor run start`. **It is highly recommended to run the EntryPoint as a service**, so that it can run in the background. You will need to replicate the environment variables defined above.

Here's an example service file with some placeholder (`<...>`) values:

```bash
[Unit]
Description=EntryPoint Node
After=network.target

[Service]
Type=simple
User=<USER>
ExecStart=<HOME>/go/bin/cosmovisor run start --home <NODE-HOME>
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

Environment="DAEMON_NAME=entrypointd"
Environment="DAEMON_HOME=/home/entrypointd/.entrypoint"  # Double-check this!
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_LOG_BUFFER_SIZE=512"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"

[Install]
WantedBy=multi-user.target
```

### Handling Upgrades Using Cosmovisor

- Download new binary from https://github.com/entrypoint-zone/entrypoint/releases or obtain it from a reputable source.
- Apply environment variables: `source ~/.profile`.
- Register the upgrade: `cosmovisor add-upgrade <upgrade-name> <path-to-new-entrypointd-binary>`.
- Wait for the upgrade height and monitor your node's logs to ensure everything goes well.
