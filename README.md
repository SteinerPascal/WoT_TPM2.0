# WoT_TPM2.0
Describing how WoT TD can be secured by a TPM2.0 on a RPi

## Background information
This Repo got created to showcase how a TPM 2.0 can enhance the security of a [Web of Things](https://www.w3.org/WoT/) (WoT) [Thing Description](https://www.w3.org/TR/wot-thing-description/) (TD). It describes an experimental way and some learnings in order to secure a TD. It got created as part of a semester assignment for the [MSE](https://www.msengineering.ch/) in IT.

## Hardware resources used
 - RPi 3 model b v1.2
 - Let's Trust TPM module for RPi. Bought [here](https://pi3g.com/letstrust-tpm/)
 - OS: Raspbian GNU/Linux 10

## Software stack used
- [tpm2-tss](https://github.com/tpm2-software/tpm2-tss)
- [tpm2-abrmd](https://github.com/tpm2-software/tpm2-abrmd)
- [tpm2-tools](https://github.com/tpm2-software/tpm2-tools)

## Software stack explained 

### tpm2-tss
TSS is an abbrevation for TPM Software Stack and contains a whole set of software stacks to interact with the tpm. Below you can see a visualization:

![Image of TSS]()

### tpm2-abrmd
This contains some storage manager tools for the TPM2.0

### tpm2-tools
Contains commandline tools. This provides the API we are using for our PoC. Tools relies heavily on the tss ESAPI interface.

## Setup
- Get the newest version of Raspian
```
sudo apt-get update && sudo apt-get upgrade
```

- Adapt boot config to activate TPM
```
sudo nano /boot/config.txt
dtparam=spit=on
dtoverlay=tpm-slb9670
sudo reboot
```
- move tpm2_install.sh script to new folder (it will install the softwarestack in this folder)

```
sudo bash tpm2_install.sh
```
- Create primary key under the owner (SRK) hierarchy (taking sha256 and rsa 2048 as default)
```
sudo tpm2_createprimary -C e -c ./primary/primary.ctx
```
- Create childkey (sha256 and rsa 2048)
```
tpm2_create -G rsa -u ./child/rsa.pub -r ./child/rsa.priv -C ./primary/primary.ctx
```
- Load child key into the TPM
```
tpm2_load -C ./primary/primary.ctx -u ./child/rsa.pub -r ./child/rsa.priv -c ./load/rsa.ctx
```
- openssl create has digest of file (in our case the TD json)
```
 openssl dgst -sha256 -binary -out ./openssl/hash.bin ObserverThing.json 
```
- alternatively use TPM to create hash
```
tpm2_hash -C o -g sha256 -o ./tpmhash/hash.bin -t ./tpmhash/ticket.bin ObserverThing.json
```

- sign hash with tpm
```
tpm2_sign -c ./load/rsa.ctx -g sha256 -o ./tpmhash/sig.rssa ./tpmhash/hash.bin
or 
tpm2_sign -c ./load/rsa.ctx -g sha256 -o ./openssl/sig.rssa -f plain ./openssl/hash.bin
```

- create DER encoded public key
```
sudo tpm2_readpublic -c ./load/rsa.ctx -f der -o ./child/pub-rsa.der
```

- openssl verify
```
sudo openssl dgst -verify ./child/pub-rsa.der  -keyform der -sha256 -signature ./openssl/sig.rssa ./openssl/hash.bin
```

- verify with tpm
```
sudo tpm2_verifysignature -c ./load/rsa.ctx -g sha256 -m ./tpmhash/hash.bin -s ./tpmhash/sig.rssa
```


