Vulnerablity-Discovery

Background on wireless home mesh networks.
Why they're good targets
Waht's the difference

Two types of security flaws from the control plane protocols.

Then a couple vulnerabilities across several vendors, and then touching on defense. 

# Background on home wireless networks

Difference between traditional is, these are very helpful in that a single access point can't provie adequate coverage,  not just one but multiple APs cover the home.
- APs form mesh where one is the internet gateway, and the rest are just extender nodes.  We know all of this. 
- So the wireless mesh technology is mature now, works well. 

Increas in popularity,  Orbi, Deco, Linksys, Asus all help by giving you access to these AP bundles.  IT's cableless, too and many different brands to choose from. 
Market for mesh will grow over time.  

## Tech details?

Consists of a gateway node, and multiple extender nodes, client connects to any node to get to the internet.  
Backhaul links are isolated from other network, as they are critical.  They carry control plane data, and data plane traffic.  These pass config information, actual data, other data.  These links are interesting because of their importance.

## So a motivating Q, how to change Wi-Fi Passwords in mesh?

- What comms are involved for the backhaul links?
Two possibilities, maybe passwords are centrally managed, so the only node that keeps track is the gateway node, so the rest just forwards password change requests. 

	Second possibility is passwords are manage in a distributed manerr.

Turns out passwords are distributed.  And this is done through Network Access POlicy Synchronization (NAPS).
	- This info is also given, DNS, Web UI PWD, Switch SSID, etc.  All done distributed. 

To do this sync, there is a collections of protocols.  This is the NAPS

NAPS creates a novel attack surface unique to the mesh.  So a malicious client can exploit these interfaces.

### NAPS, how implemented?

Inteded for chatting between backhaul links.  
Protocols are adhoc crypto, proprietary, or be folloiwng the optional Wi-Fi EasyMesh standard.

These varying protocolas together are called NAPS.

The lowest layer that represents NAPS is at the IEEE1905 layer. And the highest layer can go up to the app layer. 

# Threat Model

Consider an authenticated attacker. 
- User is valid, maybe a guest user. 
- In terms of settings, can eb a whome/coffe shop or mall
- Assume attacker can use ARP poisoning to perform MITM.
- Their goals: 1 obtain root shell
- Goal 2 : Steal WPA2/3 passphrases of backhaul/fronthaul links. 

Now you can ask, if the attacker is already authenticated, why would you need to get another fronthaul link. When you already have one,..,
- It's because you want to investigate if an attacker can continuously obtain the freshest wifi password and avoid being booted. 
- So before we get into more technical details, 

# Overall Results

- Seven vendors were included in the study, Netgear, Asus, tp-link, linksys, wyze apmlifiy willl put PIC HERE.

# The flaws
1. Bootstrap of trust, so misisng cross-layer trust.  Access points can't figure out if they're talking to a real access point or not, some don't know who they are. They don't double check. 
2. Cross-layer trust compromise
	1. Even if the trust is properly established, there are other ways to compromise others, via cross-layer interactions.
# Cross Layer Trust Flaw

Missing Cross-layer trust:
- Trust at link layer is well established.
- No trust anchors for NAPS layer (not bootstrapped properly.)
- Attackers can manipulate NAPS protocols. 
- So the trust anchors are not bootstrapped properly during network setup. So they can exrt control over wireless nodes.

ORBI uses SOAP over TLS,  However we see that every orbi node uses self-signed certs for this purpose.
- Exploit is straightforward, you set up a fake TLS server, and use ARP poisoning to inspect TLS traffic between the Extender, and the Gateway.  This is possible because these use self-signed certs, so you can then just attack the passphrases.
Hash is NetGEAR_Orbi_MACGateway_Extender_password......
Gateway nodes MAC address, and extender node mac are in the middle.

Attacker builds cred to gain access.
1. Attacker calculates the MD5 hash, and sends to auth to the extender node.  Then they can get valid soap commands.
2. Attacker builds a SOAP API by calculating the hash of a malicious password under their control. After calling the vulnerable API you can update the system password for the extender node.
3. Next run telnet unlocking script as listens on port 23, which accepts this.  So the attacker connects to extender noes to gain root.
4. orbihack.py

Can get a root shell this way, get wirless interface information, WPA2/3 passwords, etc. 

Wyze MQTT with TLS
- In Wyze, MQTTS is shared among ALL Wyze devices. 
- Can take wyze and hunt for key.
- Unpack firmware, 
- Wiretap the control data which has back and fronthaul passphrases.

AmpliFi's usecase.
- Self-signed certs of inter-AP TLS connections
- Perform TLS MiTM, and inspect the encrypted payload, we find that front and backhaul phrases were in unencrypted formats called MEssagePack.. LOL

Wi-Fi EasyMesh
- Open standard for NAPS.
- Follows own control protocols.
- Uses layer 2.5 protocol to transmit passwords, but the standard has no authentication.
- Attacker extends public key to gateway node.
	- Public and private key-pair. Blending the pub key and their key they use DH to make their own new key.  So Follow the pic.
	- Std. DH.

# Cross-layer trust vulnerabilities.
- Logical segmentation like VLAN, attacker always hits NAPS endpoints, even if crypto protected.  So tno actual logical isolation like in VLAN.
- Crypto-failures and software vulns still exist, allowing attacker to abuse the NAPS connection. 
- Once one-layer fails, all layers fail. 
ASUS AiMesh protocol
- Encrypted TCP protocol.
- Using GroupID as the credential so AI Mesh connections on TCP give security.  The GUID is held by an extender and gateway node. 
- Starts with nonce exchange, but this isn't done security.  These are blended to make a session key. Which is used for access policies like passphrases. 
- We mention that GroupID "guaranties security" so if group_ID leaked in the 802..11 layer, which breaks security.
1. Group_id is broadcased at the 802.11 layer,  we just sniff for the hashed "group_id " over the air.
2. In the beacon phrame,  The vendor type-length-value and vendor specific data is in there.  We can see a hash within there stored at type 0x3.
3. After obtaining this hash, the ID can be brute-forced.  
   Can then exploit the cfg_server's ssh management key installation to gain root access. 
   Weak key-lenght makes it insecure.

Linksys.
- Uses Zero-knowledge protocol encrypting all control data.  The gateway holds the cryptoverifiers, and extenders hold the passwords.
- So still pub and priv key pairs. so an MiTM machine knows nothing, no creds are transmitted. It's basically just normal TLS.
- But you can do a preauth injection.
	- ``attacker taining some clientID/srpuser field, can use to steal stored SRP passwords.`
- Then you just masquerade as a bad guy anyway.
- So bad guy starts HTTP server hosting shell script.
- Then they use openSSL to try to download the attack on the attacker machine.  Then once there, the user changes priv lvl of shell script, invokes the shell script, launches the revers shell, by calling the command injection again. 
- After executing the command you get root shell.


Wireless client list needs to be checked for anomaly's.
Read academic paper for more details.  In a nutshell, are two streps to heal these compromised networks. 
- First is engineers rotating WPA keys, or link layer values to new values to do key evolution. Deetz in paper.
- Recommended that network isolation or wireless VLAN, and SSIDs are done.
- Finally Blackhat sound byte insights
	1. wireless security is making a comeback
	2. Home WMN control protocls are novel
		1. Novel things are good targets
	3. Standard communities and vendors put more effort on security to more carefully design and analyze security lists when developing new solutions such as NAPs.
	4. github seclab/ucr