# interchain-queries
``
cd $HOME
git clone https://github.com/Stride-Labs/interchain-queries.git
cd interchain-queries
go build
sudo mv interchain-queries /usr/local/bin/icq
``

``
cd $HOME && mkdir .icq
``

``
sudo tee $HOME/.icq/config.yaml > /dev/null <<EOF
default_chain: stride-testnet
chains:
  gaia-testnet:
    key: wallet
    chain-id: GAIA
    rpc-addr: http://127.0.0.1:23657      # use your own Gaiae RPC endpoint here
    grpc-addr: http://127.0.0.1:23090     # use your own Gaia GRPC endpoint here
    account-prefix: cosmos
    keyring-backend: test
    gas-adjustment: 1.2
    gas-prices: 0.001uatom
    key-directory: /root/.icq/keys
    debug: false
    timeout: 20s
    block-timeout: ""
    output-format: json
    sign-mode: direct
  stride-testnet:
    key: wallet
    chain-id: STRIDE-TESTNET-2
    rpc-addr: http://127.0.0.1:16657      # use your own Strid GRPC endpoint here
    grpc-addr: http://127.0.0.1:16090     # use your own Strid GRPC endpoint here
    account-prefix: stride
    keyring-backend: test
    gas-adjustment: 1.2
    gas-prices: 0.001ustrd
    key-directory: /root/.icq/keys
    debug: false
    timeout: 20s
    block-timeout: ""
    output-format: json
    sign-mode: direct
cl: {}
EOF
``

``
icq keys restore --chain stride-testnet wallet
icq keys restore --chain gaia-testnet wallet
``


``
sudo tee /etc/systemd/system/icqd.service > /dev/null <<EOF
[Unit]
Description=Interchain Query Service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which icq) run --debug
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
``


``
sudo systemctl daemon-reload
sudo systemctl enable icqd
sudo systemctl restart icqd
``

``
journalctl -u icqd -f -o cat
``
