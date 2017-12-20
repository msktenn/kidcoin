#### brew install azure client
```sh
brew install azure-cli
```
#### login to azure
```sh
az login
```
#### create a group in azure preveri
```sh
az group create --name preveri --location eastus
```
**output should look something like this**
```json
{
  "id": "/subscriptions/a19f1335-8f39-4ee7-9d88-e431f3f95584/resourceGroups/blockchain",
  "location": "eastus",
  "managedBy": null,
  "name": "preveri",
  "properties": {
     "provisioningState": "Succeeded"
  },
   "tags": null
}
```

#### create a vm ethnode1
```sh
az vm create --resource-group preveri --name ethnode1 --image UbuntuLTS --size Standard_A0 --admin-username preveri --generate-ssh-keys
```
**output should look something like this**
```json
{
  "fqdns": "",
  "id": "/subscriptions/a19f1335-8f39-4ee7-9d88-e431f3f95584/resourceGroups/blockchain/providers/Microsoft.Compute/virtualMachines/ethnode1",
  "location": "eastus",
  "macAddress": "00-0D-3A-15-F3-B8",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.224.181.80",
  "resourceGroup": "preveri",
  "zones": ""
}
```
Save the publicIpAddress and privateIpAddress you will need them later.

#### get preveri repository on local machine
```sh
mkdir ~/code/
cd ~/code/
git clone git@github.com:msktenn/preveri.git
```
#### setup ethereum on new machine (ethnode1)
```sh
#move profile over to ethnode1
scp ~/code/preveri/res/.profile preveri@[IPAddress]:.profile

#ssh to ethnode1
ssh preveri@[IPAddress]
```

```sh
#install ethereum on ethnode1
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install geth

#prepare directory for private ethereum network
sudo mkdir /opt/preveri

sudo chown preveri /opt/preveri
sudo chown preveri /lib/systemd/system
sudo chown preveri /etc/rsyslog.d
exit
```
#### setup private ethereum network
```sh
#copy genesis.json to ethnode1
scp ~/code/preveri/res/genesis.json preveri@[IPAddress]:/opt/preveri
scp ~/code/preveri/res/geth.service preveri@[IPAddress]:/lib/systemd/system
scp ~/code/preveri/res/geth.conf preveri@[IPAddress]:/etc/rsyslog.d

#ssh to ethnode1
ssh preveri@[IPAddress]
```

```sh
sudo chown root /lib/systemd/system
sudo chown root /etc/rsyslog.d
# initialize preveri network
geth --datadir /opt/preveri/chaindata init /opt/preveri/genesis.json

#setup geth service on ethnode1
sudo chown preveri /var/log/geth.log
sudo chown preveri /lib/systemd/system/geth.service
sudo systemctl enable geth
sudo systemctl restart rsyslog
sudo systemctl start geth
```


#### when you are done with ethnode1 deallocate it to save money
```sh
az vm deallocate --resource-group preveri --name ethnode1
```

#### reallocate but remember you will get a new ip addresses
```sh
az vm start --resource-group preveri --name ethnode1
az vm list-ip-addresses --resource-group preveri --name ethnode1 --output table
ssh preveri@[IPAddress]
```

### Other commands you may need
```sh
az group delete --name preveri --no-wait --yes
sudo systemctl restart rsyslog
sudo systemctl daemon-reload
```
