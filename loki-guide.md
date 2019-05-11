## Loki Dev Notes

This is my easy guide to setting up on a Linux server.

Loki: https://loki.network/
Loki Docs: https://lokidocs.com/
Loki Service Node Guide: https://lokidocs.com/ServiceNodes/SNFullGuide/
Loki Service Node Update Guide: https://lokidocs.com/ServiceNodes/UpdateGuide/




### Linux Guide

Digital Ocean, Ubuntu, 25gb drive, 1gb RAM.

When creating your node, add your SSH PUB KEY so that you can ssh in without a password.

[See my SSH Guide](https://github.com/jpthor/devnotes/blob/master/terminal.md)

Make sure you create a non-root user "snode":
```
adduser snode
usermod -aG sudo snode
mkdir /home/snode/.ssh
cp -rf /root/.ssh/* /home/snode/.ssh/
chown -R snode:snode /home/snode/.ssh
```

—
1. **Start**

Ssh in to your service node

```
ssh user@<ip-address>
```

Prep to update machine:
```
sudo apt-get update

sudo apt-get upgrade
```

2. **Installing**

Get the latest stable release: https://github.com/loki-project/loki/releases
```
wget https://github.com/loki-project/loki/releases/download/v2.0.2/loki-linux-x64-v2.0.2.zip <---- check version
sudo apt install unzip
unzip loki-linux-x64-v2.0.2.zip
rm -f loki-linux-x64-v2.0.2.zip
```

3. **Start screen**

This runs parallel processes on the same machine:

```
screen -ls
screen -x <lokidprocess>
```


4. **Start Daemon**

```
cd loki-linux-x64-v2.0.2
./lokid --service-node
prepare_registration
```

Copy the registration message after inputting correct information.

5. **Lock stake from Wallet**

From a local or another remote wallet (recommend local).

If new wallet that is not co-located with the one above:
- Repeat steps 1-3
- Start Wallet and restore from seed (or no seed for new wallet):
```
./loki-wallet-cli  --restore-deterministic-wallet
```

Remove the password obligation:
```
set ask-password 0
```

Then paste in the registration message from Step (4).


6. **Finish**

Exit all screens and logout

```
ctrl-AD
Exit
```

### Updating

Do steps 1-3.

Kill the daemon:
```
ctrl-C
```

Step back and enter new folder:
```
cd ..
cd loki-linux-x64-vX.X.X     <---- new version
```

Start the daemon
```
./lokid --service-node
```

Log out (Step 6)

### Other

Other code snippets for ease of use:

Create an alias to make life easier:
```
ln -snf loki-linux-x64-v2.0.2 loki
```


```
sudo systemctl stop lokid.service
sudo sed -i -e 's/1.0.4/2.0.0/g' /etc/systemd/system/lokid.service
sudo systemctl daemon-reload
sudo systemctl start lokid.service
# Confirm the version. Should output: Loki 'Festive Freya' (v2.0.0-53452b9)
~/loki-linux-x64-2.0.0/lokid version
# Print your service node status.
~/loki-linux-x64-2.0.0/lokid print_sn_status
```
```
loki-wallet-cli --command "show_transfers" --wallet-file=/path/to/wallet --password 'password' | egrep '^ '| sed 's/^ //g'| sed 's/ \+/,/g' >> my_txns.csv
```


// Copying Key
```
//Exit snode
cd .. && cd .loki
sudo rm key <pw>
scp ~/.loki/key  snode@157.230.132.137:~/.loki/key
rm ~/.loki/key
```


Notes

```
./lokid --service-node
./loki-wallet-cli
set ask-password 0
print_quorum_state
```

Remove key:
```
rm ~/.loki/key
```
WALLET
```
./loki-wallet-cli  --restore-deterministic-wallet
--daemon-address doopool.xyz:22020
set_daemon doopool.xyz:22020
```
