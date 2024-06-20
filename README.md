# Allora Network

> - Create a new wallet in Keplr
>
> - Connect to the on-chain Point Program [Dashboard](https://app.allora.network?ref=eyJyZWZlcnJlcl9pZCI6IjVlNmEwMjc5LTcxNjEtNDhmYS04NGM3LWEzYzM0MGM4MGIzNyJ9)
>
> - In Campaigns tab you see 2 tasks, Check them
> 
> - In the tutorial we run a `Price Prediction Worker` with `topic 1` (Predicting `ETH` price every 24h)
>
> - Check the campaigns tasks steps to see what `topic` means
>
> - We get points by running a worker
>
> - The points are 0 for everyone right now and we are not sure that we are 100% fine
>
> - I will update reguarly here, so we make sure that we will gain points when it is fixed

#

> Make sure to join [off-chain community tasks](https://zealy.io/cw/alloranetwork/invite/IU2cqqMstYG1pEtHTenpn) as they are as important as onchain tasks
>
> Team will add new tasks in it this week

#

# Price Prediction Worker Node

## Install dependecies
```console
sudo apt update & sudo apt upgrade -y

sudo apt install curl git wget make jq build-essential pkg-config libssl-dev gcc screen unzip lz4 -y
```

## Install Go
```console
cd $HOME && \
ver="1.21.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

## Install Allorad: Wallet
```console
git clone https://github.com/allora-network/allora-chain.git

cd allora-chain && make all

allorad version
```

## Add Wallet
* You can use your keplr seed-phrase to recover your wallet or create a new one
```console
# Recover your wallet with seed-phrase
allorad keys add testkey --recover

# Create a new wallet
allorad keys add testkey
```

## Get Faucet
> Connect to Allora [dashboard](https://app.allora.network?ref=eyJyZWZlcnJlcl9pZCI6IjVlNmEwMjc5LTcxNjEtNDhmYS04NGM3LWEzYzM0MGM4MGIzNyJ9) to find your Allora address
>
> You can add Allora network to Keplr [here](https://explorer.edgenet.allora.network/wallet/suggest)
> 
> Get uAllo faucet [here](https://faucet.edgenet.allora.network/)

![Screenshot_77](https://github.com/0xmoei/allora-testnet/assets/90371338/9e1d6236-ff51-48a1-a9f6-1149c842a4d0)

![Screenshot_76](https://github.com/0xmoei/allora-testnet/assets/90371338/ff27b97d-d04f-42c4-aa1b-3fb666874098)


## Install Worker
```console
git clone https://github.com/allora-network/basic-coin-prediction-node

cd basic-coin-prediction-node

mkdir worker-data
mkdir head-data

# Give certain permissions
sudo chmod -R 777 worker-data
sudo chmod -R 777 whead-data

# Create head keys
docker run -it --entrypoint=bash -v ./head-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"

# Create worker keys
docker run -it --entrypoint=bash -v ./worker-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"

# Copy the key
cat head-data/keys/identity

# Replace head-id with your key
nano docker-compose.yml

# Run worker
docker compose build
docker compose up -d
```


## Check your node status
* Worker node
```console
curl --location 'http://localhost:6000/api/v1/functions/execute' \
--header 'Content-Type: application/json' \
--data '{
    "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm",
    "method": "allora-inference-function.wasm",
    "parameters": null,
    "topic": "1",
    "config": {
        "env_vars": [
            {
                "name": "BLS_REQUEST_PATH",
                "value": "/api"
            },
            {
                "name": "ALLORA_ARG_PARAMS",
                "value": "ETH"
            }
        ],
        "number_of_nodes": -1,
        "timeout": 2
    }
}
```
Response:
```
{
  "code": "200",
  "request_id": "03001a39-4387-467c-aba1-c0e1d0d44f59",
  "results": [
    {
      "result": {
        "stdout": "{\"value\":\"2564.021586281073\"}",
        "stderr": "",
        "exit_code": 0
      },
      "peers": [
        "12D3KooWG8dHctRt6ctakJfG5masTnLaKM6xkudoR5BxLDRSrgVt"
      ],
      "frequency": 100
    }
  ],
  "cluster": {
    "peers": [
      "12D3KooWG8dHctRt6ctakJfG5masTnLaKM6xkudoR5BxLDRSrgVt"
    ]
  }
}
```

* Updater node
```console
curl http://localhost:8000/update
```
Response:
```
0
```

Inference node
```console
curl http://localhost:8000/inference/ETH
```
Response:
```
{"value":"2564.021586281073"}
```
