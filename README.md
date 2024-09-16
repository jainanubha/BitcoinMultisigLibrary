# LibMultiSig
Library for creating & analyzing Bitcoin multisignature transactions.
- Create multisignature transactions, inputs, outputs and sign them
- Produce raw transaction
- Multisig transactions support: P2SH, P2WSH, P2SH-P2WSH, P2TR
- Multiple variants of P2TR multisig: only key path multisig, m-of-n tapscript multisig, multiple m-of-m tapscript multisig, multiple m-of-m musig tapscript multisig, only script path multisig
- Provides addresses, transaction size and locking , unlocking scripts and sizes
## Requirements
This library can be setup and run locally. This section lists the necessary steps and requirements.
### Clone this (libmultisig) repository
```
$ git clone https://github.com/IeeeBlockchainPaper/libmultisig.git
```
### Build bitcoind
This repository requires a bitcoind with version 0.21.0 or later as it supports Schnorr signatures and P2TR transactions.

If you do not already have the Bitcoin Core repo on your local machine, then clone it with:
```
$ git clone https://github.com/bitcoin/bitcoin.git
```

Note the path of the bitcoin repository and add it to the config.ini file in your libmultisig repository. For example, set:
SOURCE_DIRECTORY=/Users/username/bitcoin

This repository will use bitcoind wallet. Be sure to build bitcoind with both legacy and HD wallet support.
Do not run bitcoind or sync with the blockchain. This repository works in **regtest** mode. It starts the node instances via scripts, so you don't need to run bitcoind in regtest mode yourself.

### Python
Verify you have python3 installed:
```
$ python3 --version
```

All other necessary imports are made in the scripts itself.

## Usage
Run main.py:
```
$ python3 main.py
```
You will be prompted:
```
Please select one of the following options:
Please enter 1 for P2SH M of N
Please enter 2 for P2WSH M of N
Please enter 3 for P2WSH over P2SH M of N
Please enter 4 for P2TR
Input: 
```
On selection of 1,2 and 3, you will be asked to provide number of keys:
```
Please enter N: 
Please enter M:
```
You will be prompted to provide the key for each cosigner:
```
Enter extended private key for cosigner:
Enter the derivation path for cosigner. If you are not sure what this is, leave this field unchanged.
```

Then the public-private key pairs are generated in code. The selected type of transaction output will be created using scripts. 
In order to sign spending the transaction, you will be asked:
```
Please specify the order in which you would like to apply the M private keys.
Application will prompt for M times. Each time, pass the key number out of [1, 2, ... M] and press Enter.
```
On selection of 4 (for P2TR), you will be asked to select the Taproot key and script path configurations as specified below:
```
Input: 4

Please enter 1 for 'only key path multisig'
Please enter 2 for 'm-of-n tapscript multisig'
Please enter 3 for 'multiple m-of-m tapscript multisig'
Please enter 4 for 'multiple m-of-m musig tapscript multisig'
Please enter 5 for 'only script path multisig'
Input:
```
Following are the different configurations of P2TR multisig transactions:
### Only Key Path Multisig Transaction
A minimal P2TR multisignature protocol can be created by only specifying the spending condition as the key path as shown:<br/>
![only key path](https://github.com/user-attachments/assets/4f1e2bf3-9202-43f3-95f4-a833844e2795)
<br/>
Key path is created by aggregating n public keys. Though it is not essential to provide the script path, in the taproot implementation in Bitcoin
core, an unspendable script path is added to the taproot by computing the output key point as mentioned:<br/>
Q = P + int(hash<sub>TapTweak</sub>(bytes(P))) ∗ G
<br/> 
Here, P is the internal key created using the musig protocol. G is the generator point of the curve and Q is the taproot output key used to lock
the funds.
### m-of-n Tapscript Multisig Transaction
A multisig protocol can be generated using tapscripts as shown:<br/>
![2-of-3 TapScript Multisig Transaction](https://github.com/user-attachments/assets/7e84cff9-ca33-474a-a5c6-7a84fda21630) <br/>
Taproot output key is generated by tweaking the aggregated musig key with the Merkle root of the taptree.
### Multiple m-of-m TapScripts Transaction
A multisig protocol can be generated using tapscripts as shown: <br/>
![Multiple 2-of-2 TapScripts Multisig Transaction](https://github.com/user-attachments/assets/ef013d47-95b7-4212-b6d5-3e68921a582f) <br/>
A taproot multisig protocol can also be created by an aggregated key as the key path and multiple m-of-m tapscript paths.
### Multiple m-of-m Musigs Transaction
A multisig protocol can be generated using tapscripts as shown: <br/>
![Multiple 2-of-2 Musigs Multisig Transaction](https://github.com/user-attachments/assets/f276e53a-b58e-4a1a-8327-f4653f474315) <br/>
In the taptree, a total of nCm tapleafs are included, each using tapscript as <Musig\ Aggregate Key> OP_CHECKSIG.
### Only Script Path Multisig Transaction
A multisig protocol can be generated using tapscripts as shown: <br/>
![Taproot6](https://github.com/user-attachments/assets/8e39e900-ece6-4858-8da5-4ae5a00c8a45) <br/>
The unspendable path is created by setting the taproot internal key as a “Nothing Up My Sleeve” (NUMS) point.

## Disclaimer
This Python3 library provides an easy interface to create estimates of the size, the weigth, and the virtual size of different multisignature transaction input, output, and witness types. <br/>
This library is still in development and is not suitable to be used in production environment.
