# celo-node

# System Requirements

- Atleast 12 GB RAM, 16 GB Recommended
- Atleast 512 GB SSD Storage (future-proofed for atleast 1 year, before CEL2)

## Snapshot Download Links

- Mainnet: `https://storage.googleapis.com/celo-chain-backup/mainnet/chaindata-latest.tar.zst` (Contains a missing block, may require manual rewinding)
- Alfajores Tesnet: `https://storage.googleapis.com/celo-chain-backup/alfajores/chaindata-latest.tar.zst`
- Baklava Testnet: `https://storage.googleapis.com/celo-chain-backup/baklava/chaindata-latest.tar.zst`

## Firewall

Allow:

- 30303/udp
- 30303/tcp
- 443/tcp
- 80/tcp
 
## Setup

* This step assumes the setup is done on an Ubuntu/Debian based distro

After setting up the server:

```bash
apt update && apt upgrade --yes

# Install required pkg deps
apt install curl zstd aria2 chrony git

# Install Docker
curl -fsSL https://get.docker.com | bash

# Download using the snapshot below
aria2c -x 16 -s 16 $DOWNLOAD_LINK

# Clone this repo
git clone https://github.com/grassrootseconomics/celo-node.git

# Prepare directories
cd celo
docker network create celo
docker compose up
# After a few seconds, cancel the operation with CTRL+C

# Extract and restore snapshot
tar --use-compress-program=unzstd -xvf chaindata-latest.tar.zst
# The data folder was created in the previous step when brining up the container
sudo rm -rf data/celo/chaindata
sudo mv chaindata data/celo

# Run the node
docker compose up -d
# It may take a few minutes to sync up

# Check if the sync has completed
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}' -H "Content-Type: application/json" http://localhost:8545

# Update your domain in caddy/Caddyfile
cd ..
cd caddy
docker compose up -d
# Your node should now be available at the endpoints configured
```

## License

[MIT](LICENSE).

