# External RPC Node Setup

### Requirements: 

basic Linux command. A VPS with 1cpu 2gb ram

This guide is for Ubuntu 20.04 (will work on others with small changes)

### Setup the environment

~~~~
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common unzip
~~~~

remove some limits

~~~~
sudo su
echo "fs.file-max = 65000" > /etc/sysctl.d/60-file-max.conf
echo "* soft nofile 65000" > /etc/security/limits.d/60-nofile-limit.conf
echo "* hard nofile 65000" >> /etc/security/limits.d/60-nofile-limit.conf
echo "root soft nofile 65000" >> /etc/security/limits.d/60-nofile-limit.conf
echo "root hard nofile 65000" >> /etc/security/limits.d/60-nofile-limit.conf
~~~~

###  Install JDK & Besu

~~~~
sudo apt install default-jre default-jdk
~~~~

Check if Java works ok:

~~~~
java — version
openjdk 11.0.10 2021–01–19 (any version >= 11 should work)
OpenJDK Runtime Environment (build 11.0.10+9-Ubuntu-0ubuntu1.20.04)
OpenJDK 64-Bit Server VM (build 11.0.10+9-Ubuntu-0ubuntu1.20.04, mixed mode, sharing)
~~~~

3. Download the latest besu binary from https://github.com/hyperledger/besu/releases

Example:
https://hyperledger.jfrog.io/artifactory/besu-binaries/besu/21.1.5/besu-21.1.5.zip
and unzip it somewhere, say /home/ubuntu/besu

make a symlink to be able to run besu from anywhere

~~~~
sudo ln -s /root/besu/bin/besu /usr/bin/besu
~~~~

Check if besu is working

~~~~
besu — version
besu/v21.1.5/linux-x86_64/openjdk-java-11
~~~~

#### You should take a break now, the next part is pretty difficult :)

### Get the blockchain info
You need now 2 things:
the genesis json file (A genesis file is a JSON file which defines the initial state of your blockchain. It can be seen as height 0 of your blockchain. … The state defined in the genesis file contains all the necessary information, like initial token allocation, genesis time, default parameters, and more.)

You can find the genesis file here: [genesis.json](genesis.json)

the enode of some other node + it’s ip.

An enode is a way to describe an Ethereum node in the form of a URI. Every node in the network has a different enode. The enode conatins a 512-bit public key called a node ID, which is used to verify communication from a particular node on the network.

enode://9f092a4c6b6d5b7388b77dd0a4f9783272defac6f46427e18f8e9e3090b6f9e5060c29fe277755ad060aab433f235962……..@127.0.0.1:30303

Easy way to get the enode info: restarting the node and look at the logs. “ INFO | DefaultP2PNetwork | Enode URL…..” Replace the 127.0.0.1 with the VMs Public IP
The port on what is that node running must be opened.
in the same folder where you have the genesis.json file
create the startup script

~~~~
besu --network-id=1214 --p2p-interface=0.0.0.0 --data-path=blockchain_data --genesis-file=genesis.json --identity="RPC Official" --metrics-enabled --metrics-port 9545 --rpc-http-enabled --rpc-ws-enabled --rpc-ws-host="127.0.0.1" --rpc-ws-port=10546 --rpc-ws-api=ETH,NET,WEB3,TRACE,TXPOOL  --rpc-http-api=ETH,NET,WEB3,TRACE,TXPOOL --host-allowlist="*" --rpc-http-cors-origins="all" --rpc-http-port=10545 --rpc-http-host="127.0.0.1" --p2p-port=30311 --bootnodes="enode://9f092a4c6b6d5b7388b77dd0a4f9783272defac6f46427e18f8e9e3090b6f9e5060c29fe277755ad060aab433f23596210b4332bedd9c04bd1bb8861c2246510@136.244.110.3:30310" --logging=DEBUG
~~~~

you can check an example startup script here: [start_node.sh](start_node.sh)

#### Replace:

- /home/ubuntu/blockchain_data with some other folder where you want the blockchain data to be present
- network-id: -> your network ID, taken from the genesis file
- metrics port with some other port of your liking
- to make it publicly available replace: rpc-ws-host=”127.0.0.1" and rpc-http-host=”127.0.0.1" with 0.0.0.0
- restrict rpc-ws-api or rpc-http-api to only what you need 
- add more bootnodes for a faster sync

if all is good, in 1 minute after you start the script it should start syncing

~~~~
New chain head detected (block number=12131), currently mining on top of 12130.
~~~~
