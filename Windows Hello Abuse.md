
Maintainer of Rubeus, BOF Net, Okta terrify, sharpblock, swetpotato, beaconeye. Ceri Coburn
Made part of Bloodhound.pw, ROADtools. ldapdomaindump
mitm6 etc. etc.


What is Windows Hello? 
- MS Passwordless technology.
- Two key pairs generated when you login, can be used for passkeys for different systems. 
- In terms of differences, there aren't any differences between hello and hello for business, except business uses certs. 
- So starting with key storage providors (KSPs)
KSPs such as
- RIP
- Microsoft platform/smart card/playform key storage providor. 
- KSPs are designed for cryptographic operations. And windows Hello is no differnt, they just use the passport KSP.

Passport KSP is an NGC Svc, where the apps communicate via RPC.
And then another app service that stores all the metadata assoc w/ any windows Hello keys. stored in `\Microsoft\Ngc` in appdata

PAssport key storage provider is a proxy that doesn't implement any pub/priv things itself.  It proxy's it off and on to the KSPs.  Each is enrolled in a container folder as a Guid. 
Each has a collection of protectors, key, metadata, etc. 

Breaking down into containers itself, all metadataa is split against dat files, so you can see which .dat contains which SID.  Which backing KSP, and even an Azure Recovery Key. 


Protectors are your windows hello auth methods.
IE) the 5 known types, 
These have 15.dat which have 3 intermediate pins encrypted, these are the same across all but are different depending on the protector being used.   it has a sign, decrypt and a potential external pin. 
Each of the known protectors have the IDs.
- 1 - pin
- 2 - bio
- 3 - azure recovery
- 4 - missed???
- 5 - PReboot protector
- 6 - Companion device protector (deprecated in win10)

The pin protector can be both alphanumeric and numeric, but default is numberic only.  If it is numeric, inside of 7.dat it stores the pin length.  This is the pin for windows hello.
1.dat contains backend keystorage used for encrypting the protector
2.dat contains the software key storage provider key used to protect the biometric data. 

The pin is decrypted using DPAPING encryped blob. 
basically, the user enters in a pin, which goes through a key derivation function. Iteventually returns the RSA private key for what's on the computer.

Pin protector for TPM decryption. The contents of 15.dat is tpm encrypted, so the pin just unseals the tpm.  So 2.dat isn't present for pin protector, because it's already fixed. 


Pin protector abuse.
- If TPM backend, pin protector is robust, 4 digit pins brute forced in 70 days.  Software backed changes this though. 
- The hashcat type is 28100.
- But because we know the length of the pin, you can decrypt it with a mask attack, even 8 digits takes seconds, 11 digits takes days......

Bio Protector - Decryption key encrypted as Windows Vault Credentials.
Bio Protector is protected by the system DPAPI key.  And you can reverse the pins to bypass biometrics all together. 
Details fo rthis is based on the vault credentials.  Won't go into details on how the vault is protected, but you decrypt within windows vault which gives you the aes key to get the 3 intermediate pins of 15.dat.

Recovery Protectors.
- IF recovery key reset is enabled. 
- 3 intermediate pins are encrypted with DPAPI key.  Then puib is fetched from cred.microsoft.com/getencryptionkey/v1.
- result stored in 9.dat as a result, and isn't inside of a protector folder.
- So the reverse process goes to cred.microsoft.com/secret. 
- Requi
- E4f546 on th left, gets a response from the MS server on the right, and gets the DPAPI common header when decrypted.

Preboot protector
- Believes it's for scenarios where you go from bitlocker straight to desktop.  But 15.dat being protected by bitlocker. So a companion device protector is intended for IoT scenarios.  An Opaque blob is set, but looks like the intermediate pins. 
- deprecated the companion one

Protector recap
- encrypt 3 intermediate pins
- inputs to protectorss differ depending on type
- bio protector doesn't need biometrics to decrypt
- pin protector value if no tpm

Keys
- intermediate pins proect everything and are constant for duration fot he container.  4 key types
- vault uses decrypt pin
- entra key - sign pin
	- used in windows hello for business
- passkey - sign pin
	- fido/passkey websites
- third party - extrernal pin
	- ie okta fastpass
	proxy encryption to backend encryption provider, either software or TPM.  If software you just get the pin ez pz.  Common metadata files are across all key types. but depends.
	Vault key is used for recovering plaintext password for local account but not for winhello for business.
	found on insecurity

Passkey enroll keys.  
- 7.dat contains lots of other data, CBOR is the encoding.
- Condays relay iparty id
- user id
- username
- displayname
- sha256 of CNG key blob is the webauthn credential id.
- 11.dat 

passkey abuse
- Can hijack navigator.credentials.get to hijack and proxy the assertion requests to compromised hosts, and use the web certificates to login to webauthn websites, you can sign assertion and fake being the suer

Entra Key
- Created during WHFb
- Allow and use this key to sign the PRTs used for requesting a credential to entra.  You can get the cloud TGT or TGT and use this to auth locally too. 
- Uses key sign pare from intermediate pins,  and is made up fot he tenant ID and user ID
	Using this, you can use the windows hello enroll keys to request a new PRT from a compromised device, and can ignore mimikatzz.  And can use Dirk Jan's CVE for KDFv1.  And use this to sign a new PRT
	Microsoft said they fixed it, but as they deprecated it and said they fixed it, but they didn't. so lol.
Tool 
github.com/ccob/shwmae
features allow you to enum all winhello keys enrolled on device.  Any keys used you can dump and extract and use away from the device.  Also dumps the hashcat hashes for offline cracking.
Can also do the webauthn proxy, arbitrary signing, prt auth, and key dumps. 
Can proxy it across github even.  


Unprivileged Windows Hello Abuse
- With regular user accesss, you can dsome other fun *stuff*.
- Windows hello for business PRT with Entra. He's explaining primary refresh tokens - no.
	- basically just an SSO token.
	- Windows reqs by asking entra, which sends a json webtoken.  These are signed with the cert of the device. So entra can see if a device is legit or not. And if it's trusted. 
	- Results in prim refresh keys tying back to device.
- If using windows hello keys it sends a specific payload.  Because it signs the data with the winhello priv eys
- The request nonc-value is the msot interesting. it exists because it's time-bound.  The presence expires after 5 min. So fater 5 min you need to get a new nonce. Which is why you need hardware access to device to get the signature to get it. 
- Can see the username, and the assertion field. 
- The assertion field is another JWT.
- Assertion is signed with the priv key of winhello for business, and signed with a private RSA key. And in the payloads you see the issuer, username, audience, and timestmaps to protect against replay. 
	- Problem is, this assertion is generated with WHFB key.
	- If you want to use the windows hello key for the user session you can just ask the OS to sign it for us.
	- So you can use ms key storage provider from software. pin is cached when you sign in so it doesn't prompt user, and you don't have to brute force it. 
	- Need to use native NCrypt methods since C# methods for RSA keys are limited to sfotware keys.  And you need zero admin rights. 

Generating assertion from user session 
- Can generate a signature you can use for auth prurposes. 
- In the overlaying web token, there's just a nonce, nonce is an input we can control as an attacker.  We can change the timestamps from being anything.  If we change it, put in timestamp from the future. depends, so you can change the time range and make the limit unlimited. So you can do a golden assertion.
- - If you have access to a user session, you can do a golden assertion and make a token that lasts for whatever, adn can use it tfor the future.
- The PRT after this is tied to the private/transport keys and only accessible by the TPM. so you'd still have to use it with the same device. which sounds issue.  
	- not an issue, but can use the same assertion on a different device. So you can use RDP. LOL.
	- you can use remote desktop to auth to other devices with WHFB.  So you don't need more and more creds, and can use passwordless windos to auth. 
- If we RDP to another device that doesn't need TPM, the resulting PRT isn't hardware protected,  so if you have machine elsewhere and rdp to it, you can just run mimikatz, extract the PRT, and you're off to the races. So if you RDP to an unprotected device w/o the TPM that is entra/hybrid joined, you're exposing your creds, because the PRT is an SSO token for your account.  And bypasses your MFA.
- - There's no way to limit from RDP'ing from non TPM devices. 
- Can use this to work around limits

So you take colden assertion, and use it with any other device in the tenant at any time, like registering it to another device you add to domain or wahtever.  All signed based on WHFB keys.  But you can sign em arbitrarily.
Reported to microsoft, and they changed it a "lil" bit, so they put a nonce on the inner assertion, so now it's also restricted to 5min because the nonce expires.  Which means you calc a new signature, 
	- Fixed july of last year
	- problem was if windows does this new behavior, if you don't enforce the new control is worthless.
	- Windows will now include a nonce in the assertion which limits assertion validity to 5 minutes. 
	- They only enforce server side in may of this year. 
	- Now fixed. Can't make a golden assertion but less relevant.
	- But you can still do smoe cool *stuff*. 
		- still have 5 minutes. does not stop us from requesting a PRT on a different device without TPM within the 5 minutes. meaning we can still use the assertion to bypass TPM protection.  And it will be valid for 90 days and can be used to provision new keys, new FIDO, new sign ins.  Not mitigated anywheree, TPM, Hello ESS, VVBS, LSA PPL, whatever.
- To see this in action you generate an assertion, copy it to another thing, req a new primary refresh token with a rogue device. 
- See get a prim refresh token. No TPM here.  Can use this to sign into anything. And it is vald 90 days until revoked. 
- Looking at tokens, have the RSA/ MFA claim,  WHFB passwordless auth.  MFA whatever, can just jump around em'
- This all hinges on a device.
Entra mitigations
- Requiring device compliance, restrict device join
- monitor for new devices and use fo existing WHFB keys
- don't RDP to untrusted hosts w/ sensitive accounts, etc. 
DON'T allow non-TPM logins. 
Some endpoint mitigations
- Use Windows Hello ESS
- physical key
- no tpm = no windows hello
- alert on container file access
	- ngcctrlsvc is legitimate
		- other processes aren't
\what is windows ESS
- Feature in the hardware, ESS is not seen vary much, SDEV/SDCP tables in ACPI are required, this is for securing devices via face and fingerprint, not a lot out there. But a lot of newer devices have the capability. This is coming next. to get pwnd.
- They rewrote the container structi in json for this.  And it's all in one file, and new processes running, like bioiso.exe and sdgiso are running, so it runs in tandem with isolated lsass. 
- 