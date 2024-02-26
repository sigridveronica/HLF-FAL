# HLF-FAL

**HLF-FAL Configuration Guide**

**Step 1: Define Organizations and Peers**

**Organizations and Peers Configuration:**
You'll need to define your organizations (OEM, Supplier, Airline) and their peers in the network configuration files. 

**Path:** /test-network/organizations/cryptogen/
**Files to Edit:** If using cryptogen, edit crypto-config.yaml to include your organizations and peers.

**Example Entry for OEM in crypto-config.yaml:**

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

Adjust the Organizations section under each channel profile to include the correct organizations.

Step 3: Deploy Network

After configuring your organizations and channels, you'll need to generate the necessary cryptographic material and channel artifacts.

Generate Crypto Material:
