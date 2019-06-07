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
