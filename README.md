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
