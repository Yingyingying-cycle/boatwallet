# BoAT SDK

## Introduction

BoAT Wallet is an IoT-oriented lightweight Ethereum compatible client SDK
written in C. It supports transaction as well as smart contract calling over
Ethereum compatible blockchain.

Though Ethereum and its derivatives are very popular, their wallet clients are
basicly designed for personal computer and smart phone, and thus typically
written in high level languages such as Java, Javascript and Go. However IoT
devices are not as powerful as personal computer and smart phone. IoT device
typically runs a lightweight linux on its application processor or an RTOS on a
processor shared with communication protocol, and high level languages are
seldom supported.

BoAT SDK is instead designed for IoT devices running linux or RTOS. It's written
in C and easy to integrate into traditional lightweight IoT application. It
depends on very few third-party libraries. It doesn't require any third-party
package being imported on-line at runtime. It's important for cellular IoT
devices because cellular operator usually charges by traffic volume.


## Dependency

Current BoAT SDK is only available for linux. An RTOS version is in plan.

1. gcc is required. On Windows Cygwin is required. For cross-compiling, an
appropriate gcc tool chain meeting the target is required.

2. OpenSSL libraries are required for random number generation and AES ciphering.
If a TRNG (True Random Number Generator) is available on the target, it's
recommended to use TRNG to generate seed for CSPRNG. For cross-compiling, if
appropriate target libraries are not available in the cross-compiling tool chain,
you must manually cross-compile it from source.

3. CURL libraries are required for HTTP connection to blockchain node. For cross-
compiling, if appropriate target libraries are not available in the cross-
compiling tool chain, you must manually cross-compile it from source.


## Code Structure
```
boatwallet
|
+---3rd             | Open source code from third-party
|   +---cJSON       | cJSON source code
|   +---curl        | Head files for libcurl (Only use when libcurl is not available in tool chain)
|   \---ecdsa       | Crypto and hash algorithm
|
+---build           | Directory to store object files and executable
|       
+---demo            | Demonstrate how to use BoAT SDK
|
+---docs            | Documents generated from comments by Doxygen
|
+---hwdep           | Hardware dependent source (pure software by default)
|
+---lib             | Directory to store compiled libraries
|
\---src             | Source of BoAT SDK
    +---rpc         | Remote Procedure Call wrapper
    +---utilities   | Utilities such as string manipulation
    +---wallet      | Client protocol
    \---web3        | Web3 interface
```


## How to build
### To build BoAT SDK libraries with demo application
```
$make
```
The built demo application locates at:
boatwallet/build/boatwallet_demo

The built libraries locate at:
boatwallet/lib/libboatwallet.a
boatwallet/lib/libcJSON.a
boatwallet/lib/libecdsa.a
boatwallet/lib/libhwdep.a


### To build BoAT SDK libraries only
```
$make boatwalletlib
$make hwdeplib
$make thirdlibs
```

### To clean everything
```
$make distclean
```

### To clean everything except third-party libraries
```
$make clean
```


## Run the demo application

### Run the demo
To run the demo application, either an Ethereum simulator (e.g. Ganache) or a
real node of an Ethereum compatible blockchain network must be available.
```
$./build/boatwallet_demo http://IPAddress:Port
or
$./build/boatwallet_demo https://IPAddress:Port
```

The parameter following the application executable name is the URL to the
blockchain node's RPC interface. The exact Protocol (http or https), IP address
or site name and Port depend on how the blockchain node is deployed.

For early phase of development it's recommended to run a local Ethereum
simulator instead of deploying a real blockchain network.

Truffle Suite (https://www.truffleframework.com) is a well-known tool to
simulate an Ethereum network and deploy smart contract onto simulated or real
Ethereum compatible network. Ganache is part of the suite and could simulate an
Ethereum network. Ganache is available in both graphics version and command line
version.

To connect a local Ganache Ethereum Simulator, it typically looks like:
```
$./build/boatwallet_demo http://127.0.0.1:7545
```

If you're connecting to a real Ethereum compatible blockchain node, please make
sure you know the appropriate parameters (such as gasLimit) of the network and
you have enough token to pay for gas.

### Before run the demo
The private key and account address generated by Ethereum simulator are surely
different from the ones in the demo. You must modify the demo code with
appropriate private key and account address.

Before run a case calling a smart contract, you must deploy the smart contract
onto the blockchain network. The deployed contract address is surely different
from the one in the demo. You must modify the demo code with appropriate contract
address.


## How to integrate BoAT SDK with your own application

* Step 1
Build BoAT SDK libraries

* Step 2
Add following include file search path to you project:
```
boatwallet/src
boatwalelt/3rd/cJSON
boatwalelt/3rd/ecdsa
```

* Step 3
Your application calls APIs declared in boatwallet/src/wallet/boatwallet.h. See
documents in boatwallet/docs for APIs' details.

* Step 4
Compile your application and link with following libraries in sequence:
```
boatwallet/lib/libboatwallet.a
boatwallet/lib/libecdsa.a
boatwallet/lib/libcJSON.a
boatwallet/lib/libhwdep.a
-lcurl
```


## Documents

Documents locate in docs directory. They are generated by Doxygen from code
comments in both HTML and RTF formats:
* HTML Documents Entry: boatwallet/docs/html/index.html
* RTF Document: boatwallet/docs/rtf/refman.rtf

Note that documents in github may be somewhat older than the code. You can use
Doxygen to manually generate fresh new documents with configuration file
boatwallet/docs/boatwallet.doxy.
