# Bu güncellemeyi eğer 70500. bloğa ulaştıysanız yapmanız gerekiyor. Ulaşmadıysanız yapmayın, ulaşmayı bekleyin! Kaçıncı blokta olduğunuza aşağıdaki komut sayesinde bakabilirsiniz.

```
strided status 2>&1 | jq .SyncInfo
```

## Komutu girdiğinizde eğer aşağıdaki görseldeki gibi `70500` yazıyorsa güncellemeyi yapabilirsiniz.

![image](https://user-images.githubusercontent.com/101462877/186240028-12dd89fd-469b-45ec-822c-4eda2c980042.png)

# Güncelleme komutları:

```
sudo systemctl stop strided
cd $HOME && rm -rf stride
git clone https://github.com/Stride-Labs/stride.git && cd stride
git checkout 90859d68d39b53333c303809ee0765add2e59dab
make build
sudo mv build/strided $(which strided)
sudo systemctl restart strided && systemctl restart systemd-journald.service && syjournalctl -fu strided -o cat
```
