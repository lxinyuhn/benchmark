

### Benchmark ---作业一


```bash
cargo build --release --features runtime-benchmarks
```

weights.rs:

```bash
./target/release/node-template benchmark \
    --chain dev \
    --execution wasm \
    --wasm-execution compiled \
    --pallet pallet_template \
    --extrinsic '*' \
    --steps 20 \
    --repeat 10 \
    --raw \
    --output ./pallets/template/src/weights.rs
```


### Start a private chain

### 1. Chain Spec: ---作业二:

```bash
# Export the local chain spec to json
./target/release/node-template build-spec --disable-default-bootnode --chain local > customSpec.json
```

### 2. generate keys
keystore.txt

### 3. edit customeSpec.json
      "palletAura": {
        "authorities": [
          "5HBpKCqEYtCQ1gmCbkf1LKRLPMEY2vnDQH2qviphXn3BD6jC",
          "5GxHNR9DZa4KEZnJm9UyYo5mLbfVWPfuw8HwDPGwJvHSnNRa"
        ]
      },
      "palletGrandpa": {
        "authorities": [
          ["5HhFjcAvtzpod1aQPMibF2NQtmX7n4Udf4M4RHDGiK4Pj65Y", 1],
          ["5CeSrafRJ51Hh4EdjkR6CraD2eq5Tvz7PidxXBP4qu8v4TWa", 1]
        ]
      },
      ...
      "palletSudo": {
        "key": "5HBpKCqEYtCQ1gmCbkf1LKRLPMEY2vnDQH2qviphXn3BD6jC"
      }      


### 4.  Export raw json
```bash
./target/release/node-template build-spec --chain=customSpec.json --raw --disable-default-bootnode > customSpecRaw.json 
```
    
### start Peer1  ---作业三:
```bash
#  first blockchain node
./target/release/node-template purge-chain --base-path /tmp/node01 --chain local -y

./target/release/node-template \
--base-path /tmp/node01 \
--chain ./customSpecRaw.json \
--port 30333 \
--ws-port 9945 \
--rpc-port 9933 \
--telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
--validator \
--rpc-methods Unsafe \
--name MyNode01

```

node1 [add private key](https://polkadot.js.org/apps/?rpc=ws://127.0.0.1:9945#/rpc)

### start Peer2
```bash
# add a second node
./target/release/node-template purge-chain --base-path /tmp/node02 --chain local -y

# purge chain (only required for new/modified dev chain spec)
./target/release/node-template purge-chain --base-path /tmp/node02 --chain local -y

# start node02
./target/release/node-template \
  --base-path /tmp/node02 \
  --chain ./customSpecRaw.json \
  --port 30334 \
  --ws-port 9946 \
  --rpc-port 9934 \
  --telemetry-url 'wss://telemetry.polkadot.io/submit/ 0' \
  --validator \
  --rpc-methods Unsafe \
  --name MyNode02 \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/<Peer1 ID>

```

node2 [add private key](https://polkadot.js.org/apps/?rpc=ws://127.0.0.1:9946#/rpc)

Restart nodes