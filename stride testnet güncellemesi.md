# Bu güncellemeyi eğer 155420. bloğa ulaştıysanız yapmanız gerekiyor. Ulaşmadıysanız yapmayın, ulaşmayı bekleyin! Kaçıncı blokta olduğunuza aşağıdaki komut sayesinde bakabilirsiniz.

```
strided status 2>&1 | jq .SyncInfo
```

## Komutu girdiğinizde eğer aşağıdaki görseldeki gibi `155420` yazıyorsa güncellemeyi yapabilirsiniz.

![image](https://user-images.githubusercontent.com/101462877/183854322-8598c231-56cd-4464-acee-f262f7e78b57.png)

# Güncelleme komutları:

```
sudo systemctl stop strided
cd $HOME
rm -rf stride
git clone https://github.com/Stride-Labs/stride.git && cd stride
git checkout 15e65e9a364804671425051606fe0be6536452fe
make build
sudo cp $HOME/stride/build/strided /usr/local/bin
sudo systemctl restart strided && journalctl -fu strided -o cat
```
