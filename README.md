# OpenLayer-AVS-Setup
![image](https://github.com/user-attachments/assets/fa1f1701-e638-443f-a828-bcb10fbec16c)

1. Install Dependecies
# Update & Install Packages
`sudo apt update & sudo apt upgrade -y
sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make jq build-essential pkg-config lsb-release libssl-dev libreadline-dev libffi-dev gcc screen unzip lz4 -y`

# Install Docker
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
docker version

# Install Docker-Compose
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)

curl -L "https://github.com/docker/compose/releases/download/"$VER"/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
docker-compose --version

# Docker Permission to user
sudo groupadd docker
sudo usermod -aG docker $USER

# Install Go
`sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.22.4.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> $HOME/.bash_profile
source .bash_profile
go version`

2. Install EigenLayer CLI
`curl -sSfL https://raw.githubusercontent.com/layr-labs/eigenlayer-cli/master/scripts/install.sh | sh -s

export PATH=$PATH:~/bin

eigenlayer --version`

3. Clone the OpenOracle Repository

`git clone https://github.com/0xJomo/openoracle-configs.git
cd openoracle-configs`

4. Generate Keys

**You’ll need both BLS and ECDSA keys.**

#Generate BLS Key

`make generate-bls-key`

**The keys will be stored under ./keys/bls_key/.**

#Generate ECDSA Key
`make generate-ecdsa-key`


5. Configure Environment Variables

#Copy the example .env file

`cp operator-configs/holesky/.env.example operator-configs/holesky/.env`

#Fill in the required fields in .env

`OPERATOR_ADDRESS: Find this in the ECDSA key at keys/ecdsa_key/1.ecdsa.key.json.
BLS_PRIVATE_KEY_PATH: Path to the BLS key.
ECDSA_PRIVATE_KEY_PATH: Path to the ECDSA key.
HTTP_RPC_URL & WS_RPC_URL: Your node RPC URLs.`


6) Fund Your Operator Address
#Send at least 0.1 Holesky ETH to the operator address to cover gas costs
#https://docs.eigenlayer.xyz/restaking-guides/restaking-user-guide/stage-2-testnet/obtaining-testnet-eth-and-liquid-staking-tokens-lsts

7. Start Operator

#Register the operator with EigenLayer
`make holesky-register-operator-el`

#Register with OpenOracle (Reach out to the team first for whitelisting)
`make holesky-register-operator-avs`

#Start the operator
`make holesky-start-operator`





Monitoring and Metrics Guide


1) To begin monitoring your OpenLayer AVS operator, run:
`make start-monitoring`


2) Connect Operator to Monitoring Network
`docker network connect openoracle-operator prometheus`


3) Access Grafana for Metrics

Log in to Grafana to check your metrics:

#Go to: http://<IP_OF_NODE>:3000
Username: admin
Password: admin


4) Check Raw Metrics in Prometheus

#To view raw metrics

http://<IP_OF_NODE>:9090



AVS Upgrade Guide

1) Stop your current Operator
`make holesky-stop-operator`


#If you started the operator using the all-in-one command, use:

`make holesky-stop-operator-all`


2) Pull the latest repository
`git pull`


3) Restart the operator

`make holesky-start-operator`



Other Available Commands 
`
make generate-bls-key               # Generates BLS key
make generate-ecdsa-key             # Generates ECDSA key
make holesky-start-operator         # Starts running the operator (Holesky)
make holesky-stop-operator          # Stops the operator
make holesky-register-operator-el   # Registers operator with EigenLayer
make holesky-register-operator-avs  # Registers operator with OpenOracle
make holesky-update-operator        # Updates operator stake with OpenOracle`







