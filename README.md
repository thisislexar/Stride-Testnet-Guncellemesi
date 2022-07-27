<h1 align="center">Ödüllü Stride Testnet-2 Güncellemesi

## Merhabalar, bugün daha önceden kurmuş olduğumuz Stride testnet'i için yeni ağ güncellemesini yapacağız. Bu ağ için testnet ödüllü olacak. Sorularınız olursa: [LossNode Chat](https://t.me/LossNode)

![180230551-dbc0d5f0-b087-483f-9e7a-95711a820209](https://user-images.githubusercontent.com/101462877/181244736-f34b3814-9bfd-4390-8c4a-570f6eb2a985.png)


## Stride'ı ilk kez kuracaksanız aşağıya kurulum video linkini bırakıyorum. 


![hqdefault](https://user-images.githubusercontent.com/101462877/181249315-ecc0c4a0-92cf-4eb6-bff0-62477700ef0c.jpg)

https://www.youtube.com/watch?v=SLSZi48ZssE&ab_channel=Cryptoloss

# İlk olarak gerekli kurulumları yapmak için sistemi durduruyoruz:

```
sudo systemctl stop strided
cd $HOME && rm -rf stride
```

# Gerekli kurulumları yapıyoruz:

```
git clone https://github.com/Stride-Labs/stride.git
cd stride
git checkout 3cb77a79f74e0b797df5611674c3fbd000dfeaa1
make build
sudo cp $HOME/stride/build/strided /usr/local/bin
```

# Genesis dosyasını güncelliyoruz:

```
wget -qO $HOME/.stride/config/genesis.json "https://raw.githubusercontent.com/Stride-Labs/testnet/main/poolparty/genesis.json"
```


# Peer'ları güncelliyoruz ki ağa daha hızlı katılabilelim:

```
SEEDS="c0b278cbfb15674e1949e7e5ae51627cb2a2d0a9@seedv2.poolparty.stridenet.co:26656"
PEERS="d6583df382d418872ab5d71d45a1a8c3d28ff269@138.201.139.175:21016,05d7b774620b7afe28bba5fa9e002b436786d4c3@195.201.165.123:20086,d28cfff8b2fe03b597f67c96814fbfd19085b7c3@168.119.124.158:26656,a9687b78c13d39d2f96ec0905c6aa201671f61f0@78.107.234.44:25656,6922feb0ca2eab2be07d60fbfd275319bcd83ec9@77.244.66.222:26656,48b1310bc81deea3eb44173c5c26873c23565d33@34.135.129.186:26656,a3afae256ad780f873f85a0c377da5c8e9c28cb2@54.219.207.30:26656,dd93bd24192d8d3151264424e44b0f213d2334dc@162.55.173.64:26656,d46c3c3de3aacb7c75bbbbf1fe5c168f0c100f26@135.181.131.116:26683,c765007c489ddbcb80249579534e63d7a00407d0@65.108.225.158:22656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.stride/config/config.toml
```

# Stride için gerekli konfigürasyonları güncelliyoruz:

```
sed -i '/STRIDE_CHAIN_ID/d' ~/.bash_profile
echo "export STRIDE_CHAIN_ID=STRIDE-TESTNET-2" >> $HOME/.bash_profile
source $HOME/.bash_profile
strided config chain-id $STRIDE_CHAIN_ID
```

# Zincir verilerini sıfırlıyoruz:

```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.stride/config/config.toml
strided tendermint unsafe-reset-all --home $HOME/.stride
```

# Sistemi tekrar başlatıyoruz:

```
sudo systemctl start strided && journalctl -fu strided -o cat
```

## Bu noktada log'lar akmaya başlayacaktır. Akmayıp takılı kalsa bile Ctrl + C yapın ve durdurun. Aşağıdaki kodu girelim ve node'umuzun güncel bloğu yakalayıp yakalamadığını kontrol edelim. 

```
strided status 2>&1 | jq .SyncInfo
```

Bu kodu ilk girdiğinizde aşağıdaki gibi bir çıktı alabilirsiniz:

![image](https://user-images.githubusercontent.com/101462877/181246588-cb7e6258-7d67-4fd3-ace2-d3a31a268468.png)

Endişelenmeyin node'unuz eşlere bağlanmaya çalışıyordur. Bir süre bekledikten sonra kodu tekrar denediğinizde aşağıdaki gibi çıktı alacaksınız:

![image](https://user-images.githubusercontent.com/101462877/181246869-10b3b014-9876-4bcf-a299-9d3257567034.png)

Eğer `catching_up` kısmı sizde `true` olarak gözüküyorsa bekleyin. Güncel bloğa erişmeye çalışıyorsunuz.


## Bir süre bekledikten ve `false` çıktısını aldıktan sonra validatör kurmaya başlayabiliriz. Eğer ilk testnet ağına katıldıysanız oradaki cüzdanınızdaki token'lar bu ağa aktarılmıştır. Faucet almadan direkt validatörümüzü kurabiliriz.

Eski cüzdanı recover etmek için kod:

```
strided keys add <CUZDANADINIZ> --recover
```

Burada sizden cüzdanı daha önce oluştururken verdiği mnemonic kelimeleri ister. Kenara not ettiğiniz o kelimeleri doğrudan yapıştırın.

Yeni cüzdan oluşturmak için kod:

```
strided keys add <CUZDANADINIZ>
```

# İsterseniz cüzdan bakiyesini de kontrol edelim:

```
strided query bank balances $STRIDE_CUZDANI_ADRESI
```

# Ardından validatörümüzü oluşturalım:

```
strided tx staking create-validator \
--amount=9900000ustrd \
--pubkey=$(strided tendermint show-validator) \
--moniker=<VALIDATORISMINIZ> \
--chain-id=STRIDE-TESTNET-2 \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.1" \
--min-self-delegation="1" \
--fees=250ustrd \
--gas=200000 \
--from=<CUZDANISMINIZ> \
--website="https://linktr.ee/lossnode" \
--details="Testing the Stride" \
-y
```

Bu aşamada değiştirmeniz gereken yerler;

- `<VALIDATORISMINIZ>`: Kurmak istediğiniz validatörün adı.

- `<CUZDANISMINIZ>`: Validatöre delege edeceğiniz cüzdanın adı.

## Komutu girdikten sonra size bir Txhash verecektir. Üzerine tıklayıp explorer üzerinden validatörünüzün kurulup kurulmadığına bakabilirsiniz.

# Güncelleme bu kadardı, validatör olarak kullanabileceğiniz bazı komutları aşağıya bırakıyorum.

# Validatörünüze token delege etmek için kod:

```
strided tx staking delegate <STRIDE_VALOPER_ADRESINIZ> <MIKTAR>ustrd --from=<CUZDANADINIZ> --chain-id=STRIDE-TESTNET-2 --gas=auto
```

# Logları kontrol etmek için kod:

```
journalctl -u strided -f -o cat
```

# Senkronize durumunu kontrol etmek için kod:

```
strided status 2>&1 | jq .SyncInfo
```

# Ağ üzerindeki aktif validatörlerin listesine erişmek isterseniz:

```
strided q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

# En sonuncu kod için validatörlere [Explorer](https://stride.explorers.guru/validators) üzerinden de göz atabilirsiniz.
