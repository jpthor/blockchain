# Cosmos guide

Recommend doing the tutorial first to understand.
https://cosmos.network/docs/tutorial/#requirements


## Set up

Clone the tutorial repo:
https://github.com/cosmos/sdk-application-tutorial


Install Go:
https://golang.org/doc/install?download=go1.12.5.darwin-amd64.pkg

Set your Go Path:
```
mkdir -p $HOME/go/bin
echo "export GOPATH=$HOME/go" >> ~/.bash_profile
echo "export GOBIN=\$GOPATH/bin" >> ~/.bash_profile
echo "export PATH=\$PATH:\$GOBIN" >> ~/.bash_profile
echo "export GO111MODULE=on" >> ~/.bash_profile
source ~/.bash_profile
```

Fix for google-cloud-sdk bug:
.zshrc
```
# The next line updates PATH for the Google Cloud SDK.
source /Users/YOUR_USERNAME/google-cloud-sdk/path.zsh.inc

# The next line enables zsh completion for gcloud.
source /Users/YOUR_USERNAME/google-cloud-sdk/completion.zsh.inc
```

## Build

Go to `go/src/github.com/cosmos/sdk-application-tutorial`

Issue with local/remote:
```
replace github.com/remote/repo => ../repo
```

Then install:
```
make install
```

## Running with CLI and Rest

You should have 4 terminal windows set to: `go/src/github.com/cosmos/sdk-application-tutorial`

They will be doing the following tasks:
1. Run the Daemon
2. Query the CLI
3. Run the Rest Server
4. Query the Rest Server

Daemon:
```
// Config
nscli config chain-id namechain
nscli config output json
nscli config indent true
nscli config trust-node true

// Start
nsd start
```

CLI:
```
nscli help
```

REST:
```
nscli rest-server --chain-id namechain --trust-node
```

Query REST:
```
curl -s http://localhost:1317/auth/accounts/$(nscli keys show jack -a)
```


## Deploying to a VPS:

#TODO


# Rebuilding a Project

Github User: `user` eg: `jpthor`
New project: `project` eg: `binance-musig`
New service: `service` eg: `musigservice`
New app: `serviceApp` eg: `musigServiceApp`
New app constructor: `NewServiceApp` eg: `NewMusigServiceApp`
New token: `token` eg: `mstoken`
New daemon: `d` eg: `msd`
New CLI: `cli` eg: `mscli`
New Client: `client` eg: `msclient`
New Rest Client: `rest` eg: `msrest`
New Store: `store` eg: `storeMS`
New Key: `key` eg: `keyMS`
New KVStoreKey: `KV` eg: `MS`
New Keeper: `Keeper` eg: `msKeeper`



1) Create a directory:`go/src/github.com/USER/PROJECT`
2) `git clone https://github.com/cosmos/sdk-application-tutorial.git`
3) Make the following project renames:

- `cosmos/sdk-application-tutorial` -> `jpthor/binance-musig`
- `nameservice` && `Nameservice` && `namesvc` -> `musigservice`
- `nameServiceApp` -> `musigServiceApp`
- `NewNameServiceApp` -> `NewMusigServiceApp`
- `nametoken` -> `mstoken`
- `nsd` -> `msd`
- `nscli` -> `mscli`
- `nsclient` -> `msclient`
- `nsrest` -> `msrest`
- `storeNS` -> `storeMS`
- `keyNS` -> `keyMS`
- `ns` && `NS` -> `MS`
-  `nsKeeper` -> `msKeeper`


```
type Whois struct {
	Value string         `json:"value"`
	Owner sdk.AccAddress `json:"owner"`
	Price sdk.Coins      `json:"price"`
}
```

```
type MuSig struct {
	WalletAddr string      `json:"walletaddr"`
  MValue string        `json:"mvalue"`
  Signer               `json:"signer"`
	Owner sdk.AccAddress `json:"owner"`
	Price sdk.Coins      `json:"price"`
}
```

Value -> WalletAddr
value -> walletaddr
WhoIs -> MuSig
whois -> musig
whoIs -> muSig
Name -> MuSigName
name -> musigname

BuyName -> GenMuSig
buyName -> genMuSig
buy-name -> gen-musig
buy_name -> gen_musig
Buyer -> Creator
buyer -> creator

namesHandler -> musigidHandler

SetName -> SetSigner
set_name -> set_signer

Bid -> Fee







## Understanding Cosmos

Cosmos separates out the interface nicely from the backend. The CLI or Rest server essentially makes calls to a module called the "querier" which triages the requests at end points and then queries the keeper module.

The Keeper module maintains the getters and setters for chain state and other core Cosmos Modules.

A Types module maintains the format of the data that should be expected to retrieve from the state.


![Cosmos Query Pathway](https://github.com/jpthor/blockchain/blob/master/images/cosmos-query-pathway.png)


To send a transaction, the same interface composes messages and these are wrapped in transactions that are then fired off to a handler which triages the calls for the Keeper.

The Keeper then can update state and application logic can be written to perform checks and balances.


![Cosmos Tx Pathway](https://github.com/jpthor/blockchain/blob/master/images/cosmos-tx-pathway.png)

## Genesis Files

Sets the genesis chain state.

## App File

Initialises everything.
