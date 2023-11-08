
# <h1 align="center">Namada-Gentx</h1>
![corenodenamada](https://github.com/molla202/Namada-Test-Yeni/assets/91562185/967edc5f-892a-40d6-a125-1ef6fba3a478)

 * [Topluluk kanalımız](https://t.me/corenodechat)<br>
 * [Topluluk Twitter](https://twitter.com/corenodeHQ)<br>
 * [Namada Website](https://www.namada.net/)<br>
 * [Discord](https://discord.gg/RrQ2S9zg)<br>
 * [Twitter](https://twitter.com/namada)<br>
 * [Resmi Dokuman](https://docs.namada.net/introduction/testnets)<br>


##  Gereksinimler
| Bileşenler | Minimum Gereksinimler | 
| ------------ | ------------ |
| CPU |	6+ |
| RAM	| 16+ GB |
| Storage	| 400 GB SSD |

# ÇALIŞTIRMIYORUZ SADACE GENTX OLUSTURUYORUZ. SEÇİLİRSENİZ ÇALIŞTIRICAKSINIZ BU YÜZDEN İŞ GÖREBİLİCEK BİR YERDE OLUSTURUP DENEYEBİLİRSİNİZ. 4 CPU 8 RAMLİ MAKINADADA GENXT OLUSTURABİLİRSİNİZ BU YÜZDEN HATA ALMAMAK İÇİN RAM EKLEMESİ YAPALIM ALTAKİ KODLA
```
sudo fallocate -l 10240M /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```
### Update ve gerekliler
```
cd $HOME
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config git make libssl-dev libclang-dev libclang-12-dev -y
sudo apt install jq build-essential bsdmainutils ncdu gcc git-core chrony liblz4-tool -y
sudo apt install original-awk uidmap dbus-user-session protobuf-compiler unzip -y
```
```
cd $HOME
sudo apt update
```
```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
```
👉 Not: 1 deyip enterlayıp devam edin
```
$HOME/.cargo/env
```
```
curl https://deb.nodesource.com/setup_18.x | sudo bash
```
```
sudo apt install cargo nodejs -y < "/dev/null"
```
### Versiyon kontrol
```
cargo --version
```
```
node -v
```
### Go kurulumu
```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.21.3.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```
```
go version
```
### Rustup & protoc'u yükleyin.
```
cd $HOME && rustup update
PROTOC_ZIP=protoc-23.3-linux-x86_64.zip
curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v23.3/$PROTOC_ZIP
sudo unzip -o $PROTOC_ZIP -d /usr/local bin/protoc
sudo unzip -o $PROTOC_ZIP -d /usr/local 'include/*'
rm -f $PROTOC_ZIP
```
```
protoc --version
```
### Değişkenleri ayarlayın.
```
sed -i '/public-testnet/d' "$HOME/.bash_profile"
sed -i '/NAMADA_TAG/d' "$HOME/.bash_profile"
sed -i '/WALLET_ADDRESS/d' "$HOME/.bash_profile"
sed -i '/CBFT/d' "$HOME/.bash_profile"
```
```
echo "export NAMADA_TAG=v0.23.2" >> ~/.bash_profile
echo "export CBFT=v0.37.2" >> ~/.bash_profile
echo "export WALLET=wallet" >> ~/.bash_profile
echo "export BASE_DIR=$HOME/.local/share/namada" >> ~/.bash_profile
```
### YOUR_MONIKER Kısmını değiştirin
```
echo "export VALIDATOR_ALIAS=YOUR_MONIKER" >> ~/.bash_profile
```
```
source ~/.bash_profile
```
### Namada kurulum
```
screen -S namada
```
```
cd $HOME && git clone https://github.com/anoma/namada && cd namada && git checkout $NAMADA_TAG
```
```
make build-release
```
👉 Not: zaman alır screenden cıkmak ıstersenız `ctrl a d` girmek için `screen -r namada`
```
cargo fix --lib -p namada_apps
```
```
cd $HOME && git clone https://github.com/cometbft/cometbft.git && cd cometbft && git checkout $CBFT
```
```
make build
```
```
cd $HOME && cp $HOME/cometbft/build/cometbft /usr/local/bin/cometbft && \
cp "$HOME/namada/target/release/namada" /usr/local/bin/namada && \
cp "$HOME/namada/target/release/namadac" /usr/local/bin/namadac && \
cp "$HOME/namada/target/release/namadan" /usr/local/bin/namadan && \
cp "$HOME/namada/target/release/namadaw" /usr/local/bin/namadaw && \
cp "$HOME/namada/target/release/namadar" /usr/local/bin/namadar
```
```
cometbft version
```
```
namada --version
```
### Servis oluşturalım
```
sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
[Unit]
Description=namada
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.local/share/namada
Environment=TM_LOG_LEVEL=p2p:none,pex:error
Environment=NAMADA_CMT_STDOUT=true
ExecStart=/usr/local/bin/namada node ledger run 
StandardOutput=syslog
StandardError=syslog
Restart=always
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable namadad
```
### Gentx oluşturun
```
namada client utils init-genesis-validator --alias $VALIDATOR_ALIAS \
--max-commission-rate-change 0.01 --commission-rate 0.05 \
--net-address $(wget -qO- eth0.me):26656
```
### Toml dosyasının çıktısını alın
```
cat $HOME/.local/share/namada/pre-genesis/$VALIDATOR_ALIAS/validator.toml
```
### PR işlemi

https://github.com/anoma/namada-testnets
