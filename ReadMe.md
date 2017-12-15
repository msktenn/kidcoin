#brew install azure client
brew install azure-cli

#login to azure
az login

#create a group in azure
az group create --name blockchain --location eastus

#save output
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

#create a vm
az vm create --resource-group blockchain --name ethnode1 --image UbuntuLTS --size Standard_A0 --generate-ssh-keys

# save output
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

# move over .profiles
# TODO: Fix this section

# ssh to Machine
ssh 52.224.181.80

# setup ethereum
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install geth

#setup your network
mkdir childblock
vi genesis.json
##create file with following data
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
##make dir inside childblock
mkdir chaindata
geth --datadir=./chaindata
