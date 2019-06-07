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

## Build

Go to `go/src/github.com/cosmos/sdk-application-tutorial`

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

## Understanding Cosmos

Cosmos separates out the interface nicely from the backend. The CLI or Rest server essentially makes calls to a module called the "querier" which triages the requests at end points and then queries the keeper module. 

The Keeper module maintains the getters and setters for chain state and other core Cosmos Modules. 

A Types module maintains the format of the data that should be expected to retrieve from the state. 


![Cosmos Query Pathway](https://github.com/jpthor/blockchain/blob/master/images/cosmos-query-pathway.png)


To send a transaction, the same interface composes messages and these are wrapped in transactions that are then fired off to the Keeper. 

This then updates state which must conform to some checks and balances in the application logic. 


![Cosmos Tx Pathway](https://github.com/jpthor/blockchain/blob/master/images/cosmos-tx-pathway.png)
