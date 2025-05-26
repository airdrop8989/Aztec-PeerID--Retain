# 🛡️ Aztec Sequencer: Retain the Same Peer ID (Avoid Peer ID Reset After Data Deletion)

If you're running an Aztec sequencer and noticed your Peer ID changing (especially after deleting node data), this guide shows you how to **retain the same Peer ID** consistently using a custom peer private key.

> ✅ This guide also helps you make your Peer ID appear on [nethermind.io](https://nethermind.io) under the correct sequencer list!

---

## 📌 Step 1: Add `--p2p.peerIdPrivateKey` Flag to Your Aztec Start Command

By default, each reset of your node may result in a **new Peer ID**.

To solve this, modify your `aztec start` command like this:

```bash
aztec start --node --archiver --sequencer \
--network alpha-testnet \
--l1-rpc-urls $SEPOLIA_RPC \
--l1-consensus-host-urls $SEPOLIA_BEACON_RPC \
--sequencer.validatorPrivateKey $VALIDATOR_PRIVATE_KEY \
--sequencer.coinbase $COINBASE \
--p2p.peerIdPrivateKey your-custom-peer-private-key \
--p2p.p2pIp $DEVICE_IP
```

> 🔁 Replace `your-custom-peer-private-key` with the actual private key generated in Step 2 below.

---

## 🧪 Step 2: Generate a Custom Peer ID Private Key

To retain the same Peer ID every time, follow the steps below to generate your custom peer private key:

### 🛠️ 1. Create a working directory

```bash
mkdir aztec-custom-peerid && cd aztec-custom-peerid
```

### 📦 2. Create a `package.json` file

```bash
nano package.json
```

Paste this code into the file:

```json
{
  "name": "aztec-custom-peerid",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "type": "module",
  "author": "",
  "license": "ISC",
  "description": ""
}
```

Save and exit: `CTRL + X`, then press `Y`, then `Enter`.

---

### 🧠 3. Create the JavaScript file to generate the key

```bash
nano index.js
```

Paste the following code:

```js
import { generateKeyPair, marshalPrivateKey } from "@libp2p/crypto/keys";
(async () => {
  const peerIdPrivateKey = await generateKeyPair("secp256k1");
  const privateKeyString = Buffer.from(marshalPrivateKey(peerIdPrivateKey)).toString('hex');
  console.log(privateKeyString);
})();
```

Save and exit.

---

### 📥 4. Install the required dependency

```bash
npm i @libp2p/crypto@4.0.3
```

---

### 🚀 5. Run the script to get your custom private key

```bash
node .
```

This will output a long hexadecimal string – **that's your custom Peer ID private key**.  
**Save it safely** and use it in your `aztec start` command.

---

## ✅ Example Updated Aztec Command with Custom Peer ID

```bash
aztec start --node --archiver --sequencer \
--network alpha-testnet \
--l1-rpc-urls $SEPOLIA_RPC \
--l1-consensus-host-urls $SEPOLIA_BEACON_RPC \
--sequencer.validatorPrivateKey $VALIDATOR_PRIVATE_KEY \
--sequencer.coinbase $COINBASE \
--p2p.peerIdPrivateKey your-custom-peer-private-key \
--p2p.p2pIp $DEVICE_IP
```

Once done, you should see your sequencer **retain the same Peer ID** across resets, and it should properly appear on the **Nethermind dashboard**.

---

## 🛡️ Bonus: Working Toward Guardian Role

> It’s believed that consistently showing up with the same Peer ID may be important to qualify for the `Guardian` role. This guide brings you one step closer!

---

Feel free to fork or share this repo with fellow Aztec node runners.
