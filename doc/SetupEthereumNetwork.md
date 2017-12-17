#### brew install azure client
```sh
brew install azure-cli
```
#### login to azure
```sh
az login
```
#### create a group in azure
```sh
az group create --name blockchain --location eastus
```
#### save output
```json
{
  "id": "/subscriptions/a19f1335-8f39-4ee7-9d88-e431f3f95584/resourceGroups/blockchain",
  "location": "eastus",
  "managedBy": null,
  "name": "blockchain",
  "properties": {
     "provisioningState": "Succeeded"
  },
   "tags": null
}
```

#### create a vm
```sh
az vm create --resource-group blockchain --name ethnode1 --image UbuntuLTS --size Standard_A0 --generate-ssh-keys
```
#### save output
```json
{
  "fqdns": "",
  "id": "/subscriptions/a19f1335-8f39-4ee7-9d88-e431f3f95584/resourceGroups/blockchain/providers/Microsoft.Compute/virtualMachines/ethnode1",
  "location": "eastus",
  "macAddress": "00-0D-3A-15-F3-B8",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.224.181.80",
  "resourceGroup": "blockchain",
  "zones": ""
}
```
#### move over .profiles
```sh
mkdir ~/code/blockchain
cd ~/code/blockchain
git clone git@github.com:msktenn/kidcoin.git
scp ./kidcoin/res/.profile 52.224.181.80:.profile
```
#### ssh to Machine
```sh
ssh 52.224.181.80
```
#### setup ethereum
```sh
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install geth
```

#### setup your network
```sh
mkdir childblock
vi genesis.json
```

*create file with following data*
```json
{
  "coinbase"   : "0x0000000000000000000000000000000000000001",
  "difficulty" : "0x20000",
  "extraData"  : "",
  "gasLimit"   : "0x2fefd8",
  "nonce"      : "0x0000000000000042",
  "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp"  : "0x00",
  "alloc": {},
  "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    }
}
```

*create dir inside childblock*
```sh
mkdir chaindata
geth --datadir=./chaindata
```
#### deallocate
```sh
az vm deallocate --resource-group blockchain --name ethnode1
```
#### reallocate
```sh
az vm start --resource-group blockchain --name ethnode1
az vm list-ip-addresses --resource-group blockchain --name ethnode1 --output table
ssh 255.255.255.255
```

### install as service
```sh

scp ./res/geth.service 52.191.197.206:/lib/systemd/system/
scp ./res/geth.conf 52.191.197.206:/etc/rsyslog.d/
sudo systemctl enable geth
sudo systemctl start geth

--other
sudo systemctl restart rsyslog
sudo systemctl daemon-reload

```
