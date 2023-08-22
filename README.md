# Dymension Froopyland Kurulum Rehberi

![dymension-GitHub](https://github.com/koltigin/Dymension-Froopyland-Kurulum-Rehberi/assets/102043225/78f67420-91be-4af9-89ee-e8c88e3cb726)

## Bağlantılar
 ✔️ [Website](https://dymension.xyz/)<br>
 ✔️ [Blockchain Explorer](https://dymension.explorers.guru/)<br>
 ✔️ [Doküman](https://docs.dymension.xyz/)<br>
 ✔️ [GitHub](https://github.com/dymensionxyz)<br>
 ✔️ [Discord](https://discord.gg/TUVbtZMMpz)<br>

## Gereksinimler 
| Bileşenler | Minimum Gereksinimler | **Tavsiye Edilen Gereksinimler** | 
| ------------ | ------------ | ------------ |
| CPU |	4 | 4 |
| RAM	| 16 GB | 16 GB |
| Storage	| 120 GB SSD | 250 GB SSD |

## Sistemi Güncelleme
```shell
apt update && apt upgrade -y
```

## Gerekli Kütüphanelerin Kurulması
```shell
apt install make clang pkg-config libssl-dev libclang-dev build-essential git curl ntp jq llvm tmux htop screen gcc lz4 -y < "/dev/null"
```

## Go Kurulumu
```shell
ver="1.20"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
rm -rf /usr/local/go
tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm -rf "go$ver.linux-amd64.tar.gz"
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

## Değişkenleri Yükleme
aşağıda değiştirmeniz gereken yerleri yazıyorum.
* `$DYM_NODENAME` validator adınız
* `$DYM_WALLET` cüzdan adınız
*  Eğer portu başka bir node kullanıyorsa aşağıdan değiştirebilirsiniz. `11` yazan yere farklı bir değer girmelisiniz yine iki haneli olacak şekilde.
```shell
echo "export DYM_NODENAME=$DYM_NODENAME"  >> $HOME/.bash_profile
echo "export DYM_WALLET=$DYM_WALLET" >> $HOME/.bash_profile
echo "export DYM_PORT=11" >> $HOME/.bash_profile
echo "export DYM_CHAIN_ID=froopyland_100-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Örnek
Node (`DYM_NODENAME`) ve Cüzdan (`DYM_WALLET`) adımızın `Mehmet` olduğunu ve kullanacağınız portun (`DYM_PORT`) da `16656` olacağını varsayalım. Kod aşağıdaki şekilde düzenlenecektir. 
```shell
echo "export DYM_NODENAME=Mehmet"  >> $HOME/.bash_profile
echo "export DYM_WALLET=Mehmet" >> $HOME/.bash_profile
echo "export DYM_PORT=16" >> $HOME/.bash_profile
echo "export DYM_CHAIN_ID=froopyland_100-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## dymension Froopyland 100-1'in Kurulması

```shell
git clone https://github.com/dymensionxyz/dymension.git --branch v1.0.2-beta
cd dymension
make install
dymd version
```
Versiyon çıktısı `v1.0.2-beta` olacak.

## Uygulamayı Yapılandırma ve Başlatma
Aşağıdaki kodlarda herhangi bir değişilik yapmadan kopyalayıp yapıştırıyoruz.

```
dymd config keyring-backend test
dymd config chain-id $DYM_CHAIN_ID
dymd init --chain-id $DYM_CHAIN_ID $DYM_NODENAME

# Genesis Dosyasının Kopyalanması
wget https://raw.githubusercontent.com/dymensionxyz/testnets/main/dymension-hub/froopyland/genesis.json -O $HOME/.dymension/config/genesis.json

# Minimum GAS Ücretinin Ayarlanması
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.25udym"|g' $HOME/.dymension/config/app.toml

# Indexer'i Kapatma (Opsiyonel)
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.dymension/config/config.toml

# SEED ve PEERS Ayarlanması
SEEDS="ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@testnet-seeds.polkachu.com:20556,92308bad858b8886e102009bbb45994d57af44e7@rpc-t.dymension.nodestake.top:666,284313184f63d9f06b218a67a0e2de126b64258d@seeds.silknodes.io:26157"
PEERS="e7857b8ed09bd0101af72e30425555efa8f4a242@148.251.177.108:20556,3410e9bc9c429d6f35e868840f6b7a0ccb29020b@46.4.5.45:20556,e7857b8ed09bd0101af72e30425555efa8f4a242@148.251.177.108:20556,3410e9bc9c429d6f35e868840f6b7a0ccb29020b@46.4.5.45:20556,138009ae8a3435eab5df2d58844239077c83c92a@161.97.180.20:16657,cb120ed9625771d57e06f8d449cb10ab99a58225@57.128.114.155:26656"
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.babylond/config/config.toml

# Prometheus'u Aktif Etme
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.dymension/config/config.toml

# Pruning'i Ayarlama
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.dymension/config/app.toml

# Portları Ayarlama
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${DYM_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${DYM_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${DYM_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${DYM_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${DYM_PORT}660\"%" $HOME/.dymension/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${DYM_PORT}317\"%; s%^address = \":8080\"%address = \":${DYM_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${DYM_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${DYM_PORT}091\"%" $HOME/.dymension/config/app.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${DYM_PORT}657\"%" $HOME/.dymension/config/client.toml

# External Adres Ekleme
PUB_IP=`curl -s -4 icanhazip.com`
sed -e "s|external_address = \".*\"|external_address = \"$PUB_IP:${DYM_PORT}656\"|g" ~/.dymension/config/config.toml > ~/.dymension/config/config.toml.tmp
mv ~/.dymension/config/config.toml.tmp  ~/.dymension/config/config.toml

# Servis Dosyası Oluşturma
tee /etc/systemd/system/dymd.service > /dev/null << EOF
[Unit]
Description=dymension Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which dymd) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```

## Servisi Başlatma
```shell
systemctl daemon-reload
systemctl enable dymd
```

## StateSync Yükleme ([Obajay](https://github.com/obajay/))
```shell
SNAP_RPC=http://dymension.rpc.t.stavr.tech:17087
peers="f85a4dd43cc31b2ef7363667fcfcf2c5cd25ef04@dymension.peers.stavr.tech:17086"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.dymension/config/config.toml
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.dymension/config/config.toml
dymd tendermint unsafe-reset-all --home /root/.dymension
wget -O $HOME/.dymension/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Dymension/addrbook.json"
systemctl start dymd
```

## Logları Kontrol Etme
```shell
journalctl -u dymd -f -o cat
```  

## Cüzdan Oluşturma

### Yeni Cüzdan Oluşturma
`$DYM_WALLET` bölümünü değiştirmiyoruz kurulumun başında cüzdanımıza değişkenler ile isim belirledik.
```shell 
dymd keys add $DYM_WALLET
```  

### Var Olan Cüzdanı İçeri Aktarma
```shell
dymd keys add $DYM_WALLET --recover
```

## Cüzdan ve Valoper Bilgileri
Burada cüzdan ve valoper bilgilerimizi değişkene ekliyoruz.

```shell
DYM_WALLET_ADDRESS=$(dymd keys show $DYM_WALLET -a)
DYM_VALOPER_ADDRESS=$(dymd keys show $DYM_WALLET --bech val -a)
```

```shell
echo 'export DYM_WALLET_ADDRESS='${DYM_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export DYM_VALOPER_ADDRESS='${DYM_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Faucet
 adresine giderek token istiyoruz. 

### Cüzdan Bakiyesine Bakma
```
dymd query bank balances $DYM_WALLET_ADDRESS
```

🔴 **BU AŞAMADAN SONRA NODE'UMUZUN EŞLEŞMESİNİ BEKLİYORUZ.**

## Senkronizasyonu Kontrol Etme
`false` çıktısı almadıkça bir sonraki yani validator oluşturma adımına geçmiyoruz.
```shell
dymd status 2>&1 | jq .SyncInfo
```

🔴 **Eşleşme tamamlandıysa aşağıdaki adıma geçiyoruz.**


## Validator Oluşturma
 Aşağıdaki komutta aşağıda berlirttiğim yerler dışında bir değişiklik yapmanız gerekmez;
   - `identity`  burada `XXXX1111XXXX1111` yazan yere [keybase](https://keybase.io/) sitesine üye olarak size verilen kimlik numaranızı yazıyorsunuz.
   - `details` `Always forward with the Anatolian Team 🚀` yazan yere kendiniz hakkında bilgiler yazabilirsiniz.
   - `website`  `https://anatolianteam.com` yazan yere varsa bir siteniz ya da twitter vb. adresinizi yazabilirsiniz.
   - `security-contact`  E-posta adresiniz.
 ```shell 
dymd tx staking create-validator \
--amount=1000000uc4e \
--pubkey=$(dymd tendermint show-validator) \
--moniker=$DYM_NODENAME \
--chain-id=$DYM_CHAIN_ID \
--commission-rate=0.10 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.05 \
--min-self-delegation="1" \
--gas-prices=0.1udym \
--gas-adjustment=1.5 \
--gas=auto \
--from=$DYM_WALLET \
--details="Always forward with the Anatolian Team 🚀" \
--security-contact="xxxxxxx@gmail.com" \
--website="https://anatolianteam.com" \
--identity="XXXX1111XXXX1111" \
--yes
```

## YARARLI KOMUTLAR

## Logları Kontrol Etme 
```
journalctl -fu dymd -o cat
```

### Sistemi Başlatma
```
systemctl start dymd
```

### Sistemi Durdurma
```
systemctl stop dymd
```

### Sistemi Yeniden Başlatma
```
systemctl restart dymd
```

### Node Senkronizasyon Durumu
```
dymd status 2>&1 | jq .SyncInfo
```
```
curl -s localhost:26657/status | jq .result.sync_info
```

### Validator Bilgileri
```
dymd status 2>&1 | jq .ValidatorInfo
```

### Node Bilgileri
```
dymd status 2>&1 | jq .NodeInfo
```

### Node ID Öğrenme
```
dymd tendermint show-node-id
```

### Node IP Adresini Öğrenme
```
curl icanhazip.com
```

### Cüzdanların Listesine Bakma
```
dymd keys list
```

### Cüzdan Adresini Görme
```
dymd keys show $DYM_WALLET --bech val -a
```

### Cüzdanı İçeri Aktarma
```
dymd keys add $DYM_WALLET --recover
```

### Cüzdanı Silme
```
dymd keys delete $DYM_WALLET
```

### Cüzdan Bakiyesine Bakma
```
dymd query bank balances $DYM_WALLET_ADDRESS
```

### Bir Cüzdandan Diğer Bir Cüzdana Transfer Yapma
```
dymd tx bank send $DYM_WALLET_ADDRESS GONDERILECEK_CUZDAN_ADRESI 100000000udym
```

### Proposal Oylamasına Katılma
```
dymd tx gov vote 1 yes --from $DYM_WALLET --chain-id=$DYM_CHAIN_ID --gas-prices 0.00001udym --gas-adjustment 1.5 --gas auto -y
```

### Validatore Stake Etme / Delegate Etme
```
dymd tx staking delegate $DYM_VALOPER_ADDRESS 100000000uc4e --from=$DYM_WALLET --chain-id=$DYM_CHAIN_ID --gas-prices 0.00001udym --gas-adjustment 1.5 --gas auto -y
```

### Mevcut Validatorden Diğer Validatore Stake Etme / Redelegate Etme
`srcValidatorAddress`: Mevcut Stake edilen validatorün adresi
`destValidatorAddress`: Yeni stake edilecek validatorün adresi 
```
dymd tx staking redelegate srcValidatorAddress destValidatorAddress 100000000uc4e --from=$DYM_WALLET --chain-id=$DYM_CHAIN_ID --gas-prices 0.00001udym --gas-adjustment 1.5 --gas auto -y
```

### Ödülleri Çekme
```
dymd tx distribution withdraw-all-rewards --from=$DYM_WALLET --chain-id=$DYM_CHAIN_ID --gas-prices 0.00001udym --gas-adjustment 1.5 --gas auto -y
```

### Komisyon Ödüllerini Çekme

```
dymd tx distribution withdraw-rewards $DYM_VALOPER_ADDRESS --from=$DYM_WALLET --commission --chain-id=$DYM_CHAIN_ID --gas-prices 0.00001udym --gas-adjustment 1.5 --gas auto -y
```

### Validator İsmini Değiştirme
`YENI-NODE-ADI` yazan yere yeni validator/moniker isminizi yazınız. TR karakçer içermemelidir.
```
dymd tx staking edit-validator \
--moniker=YENI-NODE-ADI \
--chain-id=$DYM_CHAIN_ID \
--from=$DYM_WALLET \
--gas-prices 0.00001udym \
--gas-adjustment 1.5 \
--gas auto -y
```

### Validator Komisyon Oranını Degiştirme
`commission-rate` yazan bölümdeki değeri değiştiriyoruz.
```
dymd tx staking edit-validator --commission-rate "0.02" --moniker=$DYM_NODENAME --from $DYM_WALLET --chain-id $DYM_CHAIN_ID --gas-prices 0.00001udym --gas-adjustment 1.5 --gas auto -y
```

### Validator Bilgilerinizi Düzenleme
Bu bilgileri değiştirmeden önce https://keybase.io/ adresine kayıt olarak aşağıdaki kodda görüldüğü gibi 16 haneli (XXXX0000XXXX0000) kodunuzu almalısınız. Ayrıca profil resmi vs. ayarları da yapabilirsiniz. 
`$DYM_NODENAME` ve `$DYM_WALLET`: Validator (Moniker) ve cüzdan adınız, değiştirmeniz gerekmez. Çünkü değişkenlere ekledik.
```
dymd tx staking edit-validator \
--moniker=$DYM_NODENAME \
--identity=XXXX0000XXXX0000 \
--website="VARSA WEBSITENIZI YAZABILIRSINIZ" \
--details="BU BOLUME KENDINIZI TANITAN BIR CUMLE YAZABILIRSINIZ" \
--chain-id=$DYM_CHAIN_ID \
--from=$DYM_WALLET
```

### Validatoru Jail Durumundan Kurtarma 
```
dymd tx slashing unjail --from $DYM_WALLET --chain-id $DYM_CHAIN_ID --gas-prices 0.00001udym --gas-adjustment 1.5 --gas auto -y

```

### Node'u Tamamen Silme 

```
systemctl stop dymd && \
systemctl disable dymd && \
rm /etc/systemd/system/dymd.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .dymension dymension && \
rm -rf $(which dymd)
sed -i '/DYM_/d' ~/.bash_profile
```

# Hesaplar:

[Anatolian Team](https://anatolianteam.com)

[Twitter](https://twitter.commehmetkoltigin)

[Medium](https://medium.com/@mehmetkoltigin)

[YouTube](https://www.youtube.com/channel/UCmLgaftx5e38BE0E7gpY2dA)

[Discord](https://discordapp.com/users/837933958280904737)

[Telegram](https://t.me/mehmetkoltigin)
