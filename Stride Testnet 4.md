<h1 align="center">Ödüllü Stride Testnet-4 Güncellemesi
  
## Merhabalar, bugün daha önceden kurmuş olduğumuz Stride testnet'i için yeni ağ güncellemesini yapacağız. Sorularınız olursa: [LossNode Chat](https://t.me/LossNode)
  
![180230551-dbc0d5f0-b087-483f-9e7a-95711a820209](https://user-images.githubusercontent.com/101462877/181244736-f34b3814-9bfd-4390-8c4a-570f6eb2a985.png)
  
  

# Gerekli kurulumları yapmak için sistemi durduralım:

```
sudo systemctl stop strided
cd $HOME && rm -rf stride
```
  
  
# Binary dosyaları için gerekli komutları girelim:

```
git clone https://github.com/Stride-Labs/stride.git
cd stride
git checkout cf4e7f2d4ffe2002997428dbb1c530614b85df1b
make build
sudo cp $HOME/stride/build/strided /usr/local/bin
```
  
    
# Genesis dosyasını indirelim ve seeds/peers ayarlarını yapalım:

```
wget -qO $HOME/.stride/config/genesis.json "https://raw.githubusercontent.com/Stride-Labs/testnet/main/poolparty/genesis.json"
SEEDS="d2ec8f968e7977311965c1dbef21647369327a29@seedv2.poolparty.stridenet.co:26656"
PEERS="2771ec2eeac9224058d8075b21ad045711fe0ef0@34.135.129.186:26656,a3afae256ad780f873f85a0c377da5c8e9c28cb2@54.219.207.30:26656,328d459d21f82c759dda88b97ad56835c949d433@78.47.222.208:26639,bf57701e5e8a19c40a5135405d6757e5f0f9e6a3@143.244.186.222:16656,f93ce5616f45d6c20d061302519a5c2420e3475d@135.125.5.31:54356"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.stride/config/config.toml
```
    
# Konfigürasyon ayarlarını yapalım:

```
sed -i '/STRIDE_CHAIN_ID/d' ~/.bash_profile
echo "export STRIDE_CHAIN_ID=STRIDE-TESTNET-4" >> $HOME/.bash_profile
source $HOME/.bash_profile
strided config chain-id $STRIDE_CHAIN_ID
```  
  
# Verileri sıfırlayıp sistemi başlatalım, logları kontrol edelim:

```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.stride/config/config.toml
strided tendermint unsafe-reset-all --home $HOME/.stride
sudo systemctl start strided 
journalctl -fu strided -o cat
```  

## Güncel bloğu [Explorer](https://stride.explorers.guru/blocks)'dan takip edebilirsiniz. Logların Explorer'daki güncel block ile eşleşmesini bekleyelim.
  
# Cüzdan adımızı unuttuysak aşağıda komut ile öğrenebiliriz.
  
```
strided keys list
```  
 
![image](https://user-images.githubusercontent.com/101462877/185595895-fa82b8a9-f9d7-430a-b94a-a28854ffbf2d.png)

 
# Senkronize durumunu kontrol edelim, false yazısı aldıktan sonra validator oluşturacağız:

```
strided status 2>&1 | jq .SyncInfo
```
  
 ![image](https://user-images.githubusercontent.com/101462877/185596087-3c513754-25f7-4712-9ffc-8a0c25c282e7.png)

# İsterseniz cüzdan bakiyesini de kontrol edelim:

```
strided query bank balances $STRIDE_CUZDANI_ADRESI
```

# Eğer cüzdanınızda bakiye gözükmüyorsa [Discord](https://discord.gg/5ESUHHu6dS)'a gidip faucet token'i alın.
  
![image](https://user-images.githubusercontent.com/101462877/185596727-fbc95e0a-1625-4f84-a8dd-4efd522d0cc0.png)

# Ardından validatörümüzü oluşturalım:

```
strided tx staking create-validator \
--amount=9900000ustrd \
--pubkey=$(strided tendermint show-validator) \
--moniker=<VALIDATORISMINIZ> \
--chain-id=STRIDE-TESTNET-4 \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.1" \
--min-self-delegation="1" \
--fees=250ustrd \
--gas=200000 \
--from=<CUZDANISMINIZ> \
--website="http://linktr.ee/LossNode" \
--details="Testing the Stride" \
-y
```

Bu aşamada değiştirmeniz gereken yerler;

- `<VALIDATORISMINIZ>`: Kurmak istediğiniz validatörün adı.

- `<CUZDANISMINIZ>`: Validatöre delege edeceğiniz cüzdanın adı.

## Komutu girdikten sonra size bir Txhash verecektir. Kopyalayıp [Explorer](https://stride.explorers.guru) üzerinden validatörünüzün kurulup kurulmadığına bakabilirsiniz.

# Güncelleme bu kadardı, validatör olarak kullanabileceğiniz bazı komutları aşağıya bırakıyorum.

# Validatörünüze token delege etmek için kod:

```
strided tx staking delegate <STRIDE_VALOPER_ADRESINIZ> <MIKTAR>ustrd --from=<CUZDANADINIZ> --chain-id=STRIDE-TESTNET-4 --gas=auto --fees=250ustrd -y
```

# Logları kontrol etmek için kod:

```
journalctl -u strided -f -o cat
```

# Senkronize durumunu kontrol etmek için kod:

```
strided status 2>&1 | jq .SyncInfo
```
