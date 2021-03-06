# OPN-Fabric-ABCD
(Anybody can deploy ; ) )



This project provides a utility to bootstrap a fabric network. A fabric network requires:
 - Generating root certificates for orderer and peer organization
 - Generating crypto materials(certificates for peers, orderers and users)
 - Generating channel artificats + anchor peer tx
 - Start orderer cluster
 - Start org-peers
 - Create a channel
 - Add peers to channel
 
This  projects has different sub modules to achieve one or more of the above tasks.

## Sub modules overview

|

|___ root_CA   :   openssl configuration for generating root CA for different orgs

|___ fabric-crypto :  generates channel artifacts

|___ ansible-playbooks : playbooks to start orderer, peer-prg and CA

|___ docker-compose : docker compose files (needed to execute playbooks)

|___ Utils : Util binaries

|___ Utils/opn-cryptogen : generates msp for peers, users and orderers. Require org root ca

|___ Utils/fabric-ca-client : generates msp for additional users/peers.

        


## Prerequisites

 -  Provision VMs for your Orgs(preferably three)
 -  Run ansible playbook [ansible-playbooks/base_pb.yaml]. This install all the dependencies on target VMs
 

## Generating root CAs

Navigate to root_CA folder. You would like to have a look over openssl_root.conf to modify some of the certificates parameters.
Run to generate the private key(!! fabric only supports ECDSA signatures, no RSA):
```sh
openssl ecparam -genkey -name secp384r1  -out private/pkey.pem
```
Run to generate public certificate (add SANs details to openssl_root.conf for tls(optional)):
```
openssl req -config openssl_root.cnf -new -x509 -sha384 -extensions v3_ca -key private/pkey.pem -out certs/crt.pem
```

### Generating crypto materials for the entities

Fabric-ca-server is optional. As generating crypto-material with fabric-ca-server is quite laborious. OPN-Cryptogen takes in root CA certificates and a crypto-config file and return the MSP definition.

|
|
|
|
|
|
|
|
|
|
|
|
|
|
|
|
|
|
|
|
|
WIP......
====================================================================
https://docs.docker.com/network/network-tutorial-overlay/#use-the-default-overlay-network

sudo docker swarm init --advertise-addr=158.176.67.249

    Swarm initialized: current node (47vxwl1a30cb4qjtury4hwokl) is now a manager.

    To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-0r9o8tuv7ymdrq22i1r2x7fmna9ppsq2ipyvbyute06kbuctha-drkxq7ps9tekhesuueiagou27 158.176.67.252:2377

  docker swarm join --token SWMTKN-1-4lu8j66w0gd96jksjylzxkj6olrk3ff1wlqpk1n6255b7048jt-cf3jzx98lo2o4m8pm6g7zuilp 158.176.67.254:2377
  docker network create --attachable --driver overlay opn-net
  get the network id: pz9ov2y5ghy7mix945v7b83ea


  Start alpine in interactive mode:
  sudo docker run -it --name alpine4 --network opn alpine
=============================================================================================================



Generate certificates:
./byfn.sh generate -o etcdraft


Join channel:
export CHANNEL_NAME=mychannel

peer channel create -o 34.93.54.97:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/opn.com/orderers/orderer1.opn.com/msp/tlscacerts/tlsca.opn.com-cert.pem
 peer channel join -b mychannel.block
Update anchor peer:
peer channel update  -f ./channel-artifacts/  -o 34.93.54.97:7050 -c $CHANNEL_NAME --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/opn.com/orderers/orderer1.opn.com/msp/tlscacerts/tlsca.opn.com-cert.pem

peer chaincode install -n mycc -v 1.0 -p github.com/chaincode/chaincode_opn02/go/

peer chaincode instantiate -o 34.93.54.97:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/opn.com/orderers/orderer1.opn.com/msp/tlscacerts/tlsca.opn.com-cert.pem -C $CHANNEL_NAME -n mycc -v 1.0 -c '{"Args":["init","a", "100", "b","200"]}' -P "OR('ArabJOMSP.peer')"

peer chaincode instantiate -o orderer1.opn.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/opn.com/orderers/orderer1.opn.com/msp/tlscacerts/tlsca.opn.com-cert.pem -C $CHANNEL_NAME -n mycc -v 1.0 -c '{"Args":["init","a", "100", "b","200"]}' -P "OR ('ArabAMSP.peer')"
peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'

============================================================================
1. Create order cryto material(ordere msp)+tls
2. 

===================
Securing docker socket:
https://docs.docker.com/engine/security/https/

sudo docker rm -f $(sudo docker ps -aq) && sudo docker volume prune  

 sudo rm -rf /opt/docker-compose && sudo rm -rf /etc/fabric-crypto



peer chaincode invoke -o 34.93.54.97:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/opn.com/orderers/orderer1.opn.com/msp/tlscacerts/tlsca.opn.com-cert.pem -C $CHANNEL_NAME -n mycc --peerAddresses 158.176.67.249:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/arabae.opn.com/peers/peer0.arabae.opn.com/tls/ca.crt --peerAddresses peer0:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/arabjo.opn.com/peers/peer0.arabjo.opn.com/tls/ca.crt -c '{"Args":["invoke","a","b","10"]}


CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/arabjo.opn.com/users/Admin@arabjo.opn.com/msp
CORE_PEER_ADDRESS=peer0.arabjo.opn.com:10051
CORE_PEER_LOCALMSPID="ArabJOMSP"
CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/arabjo.opn.com/peers/peer1.arabjo.opn.com/tls/ca.crt


Discovery:
discover --configFile conf.yaml --peerTLSCA ../crypto-config/peerOrganizations/arabae.opn.com/peers/peer0.arabae.opn.com/tls/ca.crt --userKey ../crypto-config/peerOrganizations/arabae.opn.com/users/User1@arabae.opn.com/msp/keystore/d48bb4e36b6f0760ab12af5629521ff49875fdd9c5e05e01a7f722a35f5bd4e4_sk --userCert ../crypto-config/peerOrganizations/arabae.opn.com/users/User1@arabae.opn.com/msp/signcerts/User1@arabae.opn.com-cert.pem --MSP ArabAEMSP saveConfig
