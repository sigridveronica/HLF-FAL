# HLF-FAL

<font color="red">This text is red.</font>


**HLF-FAL Configuration Guide**

**Step 1: Define Organizations and Peers**

1. Create Crypto Material for Organizations:
**Organizations and Peers Configuration:**
You'll need to define your organizations (OEM, Supplier, Airline) and their peers in the network configuration files. 

**Path:** /test-network/organizations/cryptogen/
**Files to Edit:** If using cryptogen, edit crypto-config.yaml to include your organizations and peers.

**Example Entry for OEM in crypto-config-oem.yaml:**


```yaml
PeerOrgs:
  - Name: OEM
    Domain: oem.example.com
    EnableNodeOUs: true
    Template:
      Count: 5
    Users:
      Count: 1
```

Adjust the Count under Template for the number of peers and add similar entries for Supplier and Airline with their respective peer counts.


2. Generate the Crypto Material: Run the cryptogen tool using your custom crypto-config.yaml for each organization.

Same directory (/Users/sigridveronica/go/src/github.com/sigridveronica/fabric-samples/test-network/organizations/cryptogen)

Here, Generate the Crypto Material: Run the cryptogen tool using your custom crypto-config.yaml for each organization.

cryptogen generate --config=./crypto-config.yaml —output="organizations"

For every organization:
```bash
cryptogen generate --config=./crypto-config-oem.yaml --output="organizations"
cryptogen generate --config=./crypto-config-airline.yaml --output="organizations"
cryptogen generate --config=./crypto-config-supplier.yaml --output="organizations"
```
**Step 2: Configure Channel Artifacts**

*Channel Configuration:*
Channels are defined in the configtx.yaml file. You'll need to define your channels and which organizations are part of each channel.

Path: /test-network/configtx/
File to Edit: configtx.yaml



Example Entry for Channels:
```yaml
Profiles:
    OEMChannel:
        Consortium: SampleConsortium
        <<: *ChannelDefaults
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *OEM
    AirlineOEMChannel:
        Consortium: SampleConsortium
        <<: *ChannelDefaults
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *OEM
                - *Airline
```

```bash
# Generate the channel configuration transaction for OEMAirlineChannel
configtxgen -profile OEMAirlineChannel -outputCreateChannelTx ./channel-artifacts/oemairlinechannel.tx -channelID oemairlinechannel

# Generate the channel configuration transaction for AllOrgsChannel
configtxgen -profile AllOrgsChannel -outputCreateChannelTx ./channel-artifacts/allorgschannel.tx -channelID allorgschannel
```
Now the files oemairlinechannel.tx and allorgschannel.tx will be generated insite channel-artifacts. Replace ./channel-artifacts/ with the actual path where you want to store the generated channel artifacts.

Adjust the Organizations section under each channel profile to include the correct organizations.

**Step 3: Deploy Network**

After configuring your organizations and channels, you'll need to generate the necessary cryptographic material and channel artifacts.

*Generate Crypto Material:*
```bash
cryptogen generate --config=./organizations/cryptogen/crypto-config.yaml
```

*Generate Channel Artifacts:* Generate the Genesis Block:
The genesis block is the first block on the chain and is used to bootstrap the network. You can generate it using the configtxgen tool with a command like:
```bash
configtxgen -profile OEMChannel -outputCreateChannelTx ./channel-artifacts/oemchannel.tx -channelID oemchannel
configtxgen -profile AirlineOEMChannel -outputCreateChannelTx ./channel-artifacts/airlineoemchannel.tx -channelID airlineoemchannel
```


<span style="color:red">
Real case Sigrid:
</span>
For every organization:
```bash
cryptogen generate --config=./crypto-config-oem.yaml --output="organizations"
cryptogen generate --config=./crypto-config-airline.yaml --output="organizations"
cryptogen generate --config=./crypto-config-supplier.yaml --output="organizations"
```


Replace oemchannel and airlineoemchannel with your channel names and adjust profiles as per your configtx.yaml.

**Step 4: Start the Network**

Use the provided network scripts or Docker Compose files (if available) to start your network.

**Step 5: Create and Join Channels**

For each channel, you'll need to create the channel and then join the relevant peers to it.
```bash
peer channel create -o orderer.example.com:7050 -c oemchannel -f ./channel-artifacts/oemchannel.tx
peer channel join -b oemchannel.block
```

Repeat the process for each channel and for each peer that needs to join a channel, adjusting the command parameters as necessary.

Note:

This guide assumes familiarity with Hyperledger Fabric CLI tools and basic network setup procedures.
The exact commands and file paths might vary based on your specific version of fabric-samples and your network configuration.
Ensure you have all necessary binaries and Docker images for the version of Hyperledger Fabric you are using.
This overview provides a starting point. Given the complexity of Hyperledger Fabric networks, you may need to adjust these instructions based on your specific requirements and the current state of your network configuration.

