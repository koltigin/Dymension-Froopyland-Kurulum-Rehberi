# RollApp Kurulumu

## RollApp Yükleme
```shell
curl -L https://dymensionxyz.github.io/roller/install.sh | bash
```

```shell
roller version
```

Çıktı aşağdaki gibi olacak;
```
💈 Roller version <latest-version>
💈 Build time: <build-time>
💈 Git commit: <git-commit>
```

## RollApp Ayarları Yapama
```shell
roller config init --interactive
```
- Ardından aşağıdaki seçimleri yapacaksınız;
  1. Ağ seçimi
  2. EVM
  3. RollApp ID belirleme. Örnek : berlin_91919191-2
    - İsim : Küçük İngilizce harfler
    - EIP155 : EIP155 rollapp kimliğini temsil eden 1 ila 10 haneli sayı
    - Sürüm : Sürümü temsil eden 1 ila 5 haneli sayı
  4. RollApp tokeninin İngilizce harflerle yazılmış adı. Örneğin BTC, PEPE, DYM
  5. RollApp token arzınızı belirleyin (varsayılan: 1.000.000.000)
  6. Celestia seçiyoruz.

- Aşağıdaki gibi bir çıktı içinde adresleriniz olacak.
```shell
🔑 Addresses:

Sequencer <network> | Address used to publish state updates to the Dymension Hub
Relayer   <network> | Address that handles the relaying of IBC packets
DA        <network> | Address used to publish data on-chain to the DA network
```

Bu adreslere discord'da `#🚰・celestia-faucet` ve `#🚰・froopyland-faucet` kanallarından `$request cuzdan-adresi` yazarak token istiyoruz. 
Ardından bir süre sonra faucet kanallarında `$balance cuzdan-adresi` yazarak tokenlerin gelip gelmediğini kontrol ediyoruz. 
🔴 Tokenler gelmeden bir sonraki adıma geçmiyoruz.

## RollApp Kaydetme
```shell
roller register
```
Aşağıdaki gibi bir çıktı alacaksınız ve kayıt işleminiz gerçekleşecek ve rollapp-id'niz oluşacak.
```shell
💈 Rollapp '<rollapp-id>' has been successfully registered on the hub.
```

## RollApp Çalıştırma
Bu işlemleri tmux ya da screen içerisinde yapabilirsiniz.
### Screen içinde çalıştırma
Screen yüklü değilse kurmak için aşağıdaki kod ile yükleyin.
```shell
apt install screen
```
Screen açıp çalıştırma
```shell
screen -S rollapp
roller run
```
Screenden çıkmak için `CTRL+A D` tuşlarını kullanın.
### Tmux içinde çalıştırma
tmux yüklü değilse kurmak için aşağıdaki kod ile yükleyin.
```shell
sudo apt install -y tmux
```
tmux açıp çalıştırma
```shell
tmux new -s rollapp
roller run
```
tmux ekranından çıkmak için `CTRL+B` basıp ellerinizi bıraktıktan sonra `D` tuşuna basın.

Aşağıdaki gibi bir çıktı alacaksınız. `Starting` yazan yerde `channel-id` göreceksiniz.
![image](https://github.com/koltigin/Dymension-Froopyland-Kurulum-Rehberi/assets/102043225/0311bdc6-2536-42f8-a588-18a224a8322c)

- Tablo açıklamaları:
  ```
  Height: en son RollApp blok yüksekliği
  Hub: Dymension Hub'da yayınlanan en son RollApp blok yüksekliği
  Port 8545: EVM RPC, EVM akıllı sözleşmelerini yayınlamak için bir RPC ağ geçidi sağlar (varsa)
  Port 26657: Düğüm RPC, düğüme yönelik istekler için bir RPC ağ geçidi sağlar
  Port 1317: Dinlenme uç noktası, düğüme yapılan istekler için bir REST ağ geçidi sağlar
  Log file path: RollApp günlüklerinin PATH'idir
  ```

🔴 `channel-id` almadan işleme devam etmiyoruz.

## IBC Transfer İşlemi
Öncelikler RollApp kanallarını kontrol ediyoruz.
```shell
roller relayer channel show
```
- Aşağıdaki gibi bir çıktı olacak.
`💈 Relayer Channels: src, channel-0 <-> channel-1, dst`

### IBC Transferi
Aşağıdaki kodda `src-channel` yazan yere kanalı yazıyoruz `channel-0` gibi. `base-denom` yazan yere tokenimizin adını başına `u` ekleyerek yazıyoruz. Adedini de siz belirlersiniz.
```shell
rollapp_evm tx ibc-transfer transfer transfer src-channel dym1g8sf7w4cz5gtupa6y62h3q6a4gjv37pgefnpt5 5000000000000000000000000<base-denom> --from rollapp_sequencer --keyring-backend test --home ~/.roller/rollapp --broadcast-mode block
```

Tokenleri Kontrol Etme ve Token İsteme

Belirli bir süre sonra Discord'da `#🚰・celestia-faucet` ve `#🚰・froopyland-faucet` kanallarında gönderdiğiniz tokenin adrese ulaşıp ulaşmadığını öğrenmek için aşağıdaki kodu gönderiyoruz.
`rollapp-id` yerine kendi id'nizi yazıyorsunuz.

`$balances dym1g8sf7w4cz5gtupa6y62h3q6a4gjv37pgefnpt5 rollapp-id`

Token talep etmek için de aşağıdaki kodu kullanıyoruz.

`$request cuzdan-adresi rollapp-id`

## ⚠️🚨 Cüzdanları Yedekleme
⚠️🚨 RollApp kurarken 3 cüzdanımız oluşmuştu. Bunları yedeklememiz gerekiyor. Aşağıdaki işlemleri yapmayı unutmayalım. 
- Cüzdan key'lerini görmek için;
 ```
roller keys list
```
- Cüzdan key'lerini yedeklemek için;
```
roller keys export hub_sequencer
```
```
 roller keys export rollapp_sequencer
```
```
 roller keys export my_celes_key
```
