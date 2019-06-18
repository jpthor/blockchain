## Libra

Install some dependencies first:
```
brew install libsodium rocksdb pkg-config protobuf cmake
```

Clone the thing
```
git clone https://github.com/libra/libra.git
```

Connect to testnet
```
./scripts/cli/start_cli_testnet.sh
```

Create account
```
account create
-> returns address like f01c1925ed776505ac1229ade986b677a37e26a379d72cacabecae8b313bdd59
```


Mint some Coins
```
account mint f01c1925ed776505ac1229ade986b677a37e26a379d72cacabecae8b313bdd59 10000
```

Query balance
```
query balance f01c1925ed776505ac1229ade986b677a37e26a379d72cacabecae8b313bdd59
-> returns balance
```

Create another account
```
account create
-> returns address like ddff36d12e3400347e2ca23702a2e4b4af3bad4b25de92a8a7c77967565cc13c
```

Transfer
```
transfer f01c1925ed776505ac1229ade986b677a37e26a379d72cacabecae8b313bdd59 ddff36d12e3400347e2ca23702a2e4b4af3bad4b25de92a8a7c77967565cc13c 1000
```
