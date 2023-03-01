# Qblockchain-Testnet-Nodee

System Requirements
4 CPU
8 GB RAM
250 GB Disk Alanı

System Update
sudo apt update
sudo apt upgrade
apt install docker-compose

Docker Setup
sudo apt-get update && sudo apt install jq && sudo apt install apt-transport-https ca-certificates curl software-properties-common -y && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" && sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin && sudo apt-get install docker-compose-plugin

1. Download and Installation of Q Blockchain Files
git clone https://gitlab.com/q-dev/testnet-public-tools
Creating keystore Folder and pwd.txt File
With the following command, we create a keystore folder in the testnet-validator file and a pwd.txt file in which we will write our password for our wallet to be given to us and enter this file. We write our password and save it with ctrl x y enter and exit.

YOURPASSWORD Type your password in the field.

cd testnet-public-tools/testnet-validator/
mkdir keystore
echo YOURPASSWORD > keystore/pwd.txt

Wallet Creation
Make sure you are in the testnet-public-tools/testnet-validator/ directory.


docker-compose run --rm --entrypoint "geth account new --datadir=/data --password=/data/keystore/pwd.txt" testnet-validator-node

After entering the above code, you should get an output like below. If you get such an output, everything is fine.

Your new key was generated

Public address of the key:   0xb3FF24F818b0ff6Cc50de951bcB8f86b522aa 
Path of the secret key file: /data/keystore/UTC--2021-01-18T11-36-28.705754426Z--b3ff24f818b0ff6cc50de951bcb8f86b52287dac

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!

installation configuration // .env
Make sure you are in the testnet-public-tools/testnet-validator/ directory.


cp .env.example .env
nano .env
We fill in the following places in the file.

ADDRESS in this section you write the wallet address given to you above without 0x at the beginning.
IP you enter the ip address of your server.
Finally, save the file by pressing ctrl x y enter.



Editing config.json File // .config.json
We enter the file.

nano config.json
We organize the following places;

ADRESS in this section you write the wallet address given to you above without 0x at the beginning.
password type your password
Finally, save the file by pressing ctrl x y enter.
 {
      "address": "ADRESS",
      "password": "YOURPASSWPRD",<br>
      "keystoreDirectory": "/data",
      "rpc": "https://rpc.qtestnet.org"
    }



Metamask wallet import
Save the file starting with UTC in the keystore folder to your computer. Then open your Matemask wallet and import this file.
You can save your Utc file to your computer using Flezilla, winspc or Mobaxtrem.






ITN registration
Register with the address given to you at the link below. The form will then give you an ITN number. Example: ITN-Hercules -12537 . Add this number to your docker- compose.yaml file.


image

Reward registration link
Adding Our Validator to https://stats.qtestnet.org
nano docker-compose.yaml
We edit the following section in the file;

YOUR VALIDATOR_ID in this section we write our validator name.
Copy the code below completely and replace it with the one in the docker-compose.yaml file where only the part that says YOUR VALIDATOR-NAME is changed and save. ctrl + x Yes and save

version: "3"

services:
  testnet-validator-node:
    image: $QCLIENT_IMAGE
    entrypoint: [
      "geth",
      "--testnet",
      "--datadir=/data",
      "--syncmode=full",
      "--ethstats=VALIDATOR_STATS_ID:qstats-testnet@stats.qtestnet.org",
      "--whitelist=3699041=0xabbe19ba455511260381aaa7aa606b2fec2de762b9591433bbb379894aba55c1",
      "--bootnodes=$BOOTNODE1_ADDR,$BOOTNODE2_ADDR,$BOOTNODE3_ADDR",
      "--verbosity=3",
      "--nat=extip:$IP",
      "--port=$EXT_PORT",
      "--unlock=$ADDRESS",
      "--password=/data/keystore/pwd.txt",
      "--mine",
      "--miner.threads=1",
      "--miner.gasprice=1",
      "--rpc.allow-unprotected-txs"
    ]
    volumes:
      - ./keystore:/data/keystore
      - ./additional:/data/additional
      - testnet-validator-node-data:/data
    ports:
      - $EXT_PORT:$EXT_PORT
      - $EXT_PORT:$EXT_PORT/udp
    restart: unless-stopped

volumes:
  testnet-validator-node-data:

Staking Validatore
Remember to ask FAUCET for a token before doing this.

docker run --rm -v $PWD:/data -v $PWD/config.json:/build/config.json qblockchain/js-interface:testnet validators.js

Run your node.
docker-compose up -d

Looking at Logs
docker-compose logs -f --tail "100"
