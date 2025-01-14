# IOTA Client Software Development Kit(SDK) for ESP32

This SDK is built on top of [iota.c](https://github.com/iotaledger/iota.c) and [ESP-IDF](https://github.com/espressif/esp-idf) for IOTA client development on ESP32 MCU series.

Learn more about [iota.c](https://github.com/iotaledger/iota.c) please visit [IOTA C Client Documentation](https://iota-c-client.readthedocs.io/en/latest/index.html)

## Example Commands

This SDK provides some example for testing and learn IOTA Client application, commands are shown below:

**Client API**

- `node_info` - Get info from the connected node
- `api_msg_index` - Find messages by a given Index
- `api_get_balance` - Get balance from a given address
- `api_msg_children` - Get children from a given message ID
- `api_msg_meta` - Get metadata from a given message ID
- `api_address_outputs` - Get output ID list from a given address
- `api_get_output` - Get the output object from a given output ID
- `api_tips` - Get tips from the connected node
- `api_send_msg` - Send out a data message to the Tangle
- `api_get_msg` - Get a message from a given message ID

**Wallet**

- `mnemonic_gen` - Generate a random mnemonic sentence
- `mnemonic_update` - Replace wallet mnemonic.
- `balance` - Get address balance by the index rage.
- `address` - Get address by the index range.
- `send` - Send tokens from the given index to the receiver address.
- `sensor` - Send sensor data to the Tangle periodically.

**System**

- `help` - List commands
- `free` - Get the size of available heap.
- `heap` - Get heap info
- `stack` - Get main stack info
- `version` - Get esp32 and wallet versions
- `restart` - system reboot

**Node Events**

The node event API is in charge of publishing information about events within the node software.
- Event Config : `menuconfig->IOTA Wallet->Event Config`
- Command : `node_events`
- Argument : `<event_select>`
- Usage : `node_events <event_select>`

*Note :*
- *The paramter <event_select> should be a one byte hexadecimal value of range 0 - FF.* Eg: `node_events 2F`
- *To stop receiving events, <event_select> should be 0 :* `node_events 0`
- *The set bit positions of the <event_select> hexadecimal value determines the events to be subscribed. Refer the table below for events, bit positions and its config parameters.*

| Event | Bit Position | Menu Config |
|:---:|:---:|:---:|
| milestones/latest | 1 | * |
| milestones/confirmed | 1 | * |
| messages | 2 | * |
| messages/referenced | 3 | * |
| messages/indexation/[index] | 4 | Index |
| messages/[messageId]/metadata | 5 | Message Id |
| outputs/[outputId] | 6 | Output Id |
| transactions/[transactionId]/included-message | 7 | Transaction Id |
| addresses/[bech32_address]/outputs | 8 | BECH32 Address |
| addresses/ed25519/[address]/outputs | 8 | ED25519 Address |
*Position of bit to be counted from the LSB side.*

## Requirements

This project was tested on `ESP32-DevKitC V4` and `ESP32-C3-DevKitC 02` dev boards.

- [ESP32-DevKitC V4](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/hw-reference/modules-and-boards.html#esp32-devkitc-v4)
- [ESP32-C3-DevKitC-02](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/hw-reference/esp32c3/user-guide-devkitc-02.html#esp32-c3-devkitc-02)

## Build system setup

Please follow documentations to setup your toolchain and development framework.

- [esp32 get started](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/index.html)
- [esp32-c3 get started](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/get-started/index.html)

**Notice: This SDK is tested under [esp-idf/release/v4.3](https://github.com/espressif/esp-idf/tree/release/v4.3)**

Clone esp-idf from GitHub

```
$ git clone --recursive https://github.com/espressif/esp-idf.git
$ cd esp-idf
$ git submodule update --init --recursive
$ ./install.sh
$ source ./export.sh
```

### Build Wallet Application

Clone the wallet source code

```
git clone https://github.com/iotaledger/esp32-client-sdk.git
cd esp32-client-sdk
git submodule update --init --recursive
```

Here we need to set the target device before configuration.

For ESP32

```
$ idf.py set-target esp32
```

For ESP32-C3

```
$ idf.py set-target esp32c3
```

Wallet configuration and building application, please change the WiFi setting in `IOTA Wallet -> WiFi`

By default, the wallet uses a `random` mnomonic, you can set the mnomonic in `(random) Mnemonic` by **menuconfig**.

```
$ idf.py menuconfig

IOTA Wallet  --->
      WiFi  --->
      SNTP  --->
      Event Config  --->
  (https://api.lb-0.h.chrysalis-devnet.iota.cafe/) IOTA Node URL
  (443) port number
  (random) Mnemonic
  [*] English Mnemonic Only
  (60) Sensor Sampling Period
  [ ] Testing Applicatio

$ idf.py build
```

### Run the Example Application

![](image/wallet_console.png)

[Transaction Message](https://explorer.iota.org/testnet/message/9e3c7e9c49ef9b776744976e787b4a1c87429d7f2888e4f468ff9986aabb4af1)

![](image/transaction_message.png)

[Data Message](https://explorer.iota.org/testnet/message/992692eb38daa75c5211b3dd6cc10fc29aaa4fe004f2b446e00b2bb851662fc8)

![](image/data_message.png)

**Notice: these messages are on the `testnet` that might not be found after a network reset.**

## Troubleshooting

`E (38) boot_comm: This chip is revision 2 but the application is configured for minimum revision 3. Can't run.`

I'm using ESP32-C3 Rev2 but the current ESP-IDF uses Rev 3 as default, we need to change it via `idf.py menuconfig`

```
Component config ---> ESP32C3-Specific ---> Minimum Supported ESP32-C3 Revision (Rev 3)  ---> Rev 2
```
