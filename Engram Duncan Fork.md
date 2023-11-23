Engram Duncan Fork

```console
# Başlamadan önce eski dosyaları kaldırıyoruz.
rm -rf tokio-docker
```

```console
# güncelleme
sudo apt update -y && sudo apt upgrade -y

# repoyu klonlayıp yapılandıralım
git clone https://github.com/engram-network/tokio-docker.git 
cd tokio-docker
git checkout dencun

chmod +x ./scripts/*.sh
./scripts/install-docker.sh
./scripts/install-asdf.sh

# Consensus ve execution dosyalarını oluşturuyoruz.
 mkdir -p execution consensus
```

Deposit Bilgilerinin Kurulumu

```eth2-val-tools``` ve ```ethereal``` yüklü olduğunu kontrol ediyoruz.

```console
eth2-val-tools --help
ethereal version
```

```
# Bu işlemi ilk defa node kuruyorsanız uygulayın.Bu komut ile mnemonic oluşturuyor ve yedekliyoruz, akabinde bir EVM cüzdana import ediyoruz.
 eth2-val-tools mnemonic
```
> Çıkan 24 kelimeyi metamaska import edin.

>Faucetten token isteyin: https://faucet-tokio.engram.tech/


```console
# İçersine giriyoruz
nano ./scripts/validator-deposit-data.sh

# Aşağıdaki kısımları değiştiriyoruz:
amount: # 32000000000
withdrawals-mnemonic: # yukarda oluşturduğumuz mnemonicleri giriyoruz
validators-mnemonic: # yukarda oluşturduğumuz mnemonicleri giriyoruz
from: # mnemonicleri import ettiğimiz ve token aldığımız cüzdan adresi
privatekey: # mnemonicleri import ettiğimiz cüzdanın private keyi

# Deposit ile işlemi bitiriyoruz
bash ./scripts/validator-deposit-data.sh
```
>Deposit işleminin onaylanmasını bekleyin.
>Explorerdan kontrol edin:https://tokioscan-v2.engram.tech/

Public Key Oluşturma
```console
# On-chain deposit datasını düzenliyoruz.
docker run -it --rm -v $(pwd)/validator_keys:/app/validator_keys engramnet/staking-deposit-cli:dencun existing-mnemonic --num_validators=1 --validator_start_index=0

# Kodu çalıştırdıktan sonra soruları cevaplıyoruz.
1.İngilizce için 3,Türkçe için 11 yazıp enter'a basın.
2.Deposit datası min. heighttan başlayacağı için 0 yazıp enter'a basın.
3.24 kelimeyi girip enter a basın.(İsterseniz her kelimenin yalnızca ilk 4 harfini girmeniz yeterlidir.)
4.Chain ismini seçiyoruz. testnet yazıp enter a basın.
5.Min. 8 karakter/sayyıdan oluşan şifre oluşturun.(Şifre dosyasını tokio-docker/custom_config_data klasöründe bulabilirsiniz.)
6.Şifreyi tekrar girin.

# Daha sonra aşağıdaki gibi bir ekran karşınıza çıkacak.

███████╗███╗░░██╗░██████╗░██████╗░░█████╗░███╗░░░███╗  ████████╗░█████╗░██╗░░██╗██╗░█████╗░
██╔════╝████╗░██║██╔════╝░██╔══██╗██╔══██╗████╗░████║  ╚══██╔══╝██╔══██╗██║░██╔╝██║██╔══██╗
█████╗░░██╔██╗██║██║░░██╗░██████╔╝███████║██╔████╔██║  ░░░██║░░░██║░░██║█████═╝░██║██║░░██║
██╔══╝░░██║╚████║██║░░╚██╗██╔══██╗██╔══██║██║╚██╔╝██║  ░░░██║░░░██║░░██║██╔═██╗░██║██║░░██║
███████╗██║░╚███║╚██████╔╝██║░░██║██║░░██║██║░╚═╝░██║  ░░░██║░░░╚█████╔╝██║░╚██╗██║╚█████╔╝
╚══════╝╚═╝░░╚══╝░╚═════╝░╚═╝░░╚═╝╚═╝░░╚═╝╚═╝░░░░░╚═╝  ░░░╚═╝░░░░╚════╝░╚═╝░░╚═╝╚═╝░╚════╝░      
                                                                  
Creating your keys.
Creating your keys:               [####################################]  32/32          
Creating your keystores:          [####################################]  32/32          
Creating your depositdata:        [####################################]  32/32          
Verifying your keystores:         [####################################]  32/32          
```

```console
# Docker compose'u açalım:
sudo nano docker-compose.yml

#Aşağıdaki kısımları değiştirelim
identity=Huemint << Discord adınızı girin.
enr-address=0.0.0.0 << IPAddresinizi girin.
graffiti=Huemint << Nickname girin.

#Çalıştıralım
docker compose up -d


# Çalıştırdıktan sonra:
[+] Running 4/4
 ⠿ Network tokio_default_default                           Created
 ⠿ Container striatum_init                                 Exited
 ⠿ Container striatum_el                                   Started
 ⠿ Container lighthouse_init                               Exited
 ⠿ Container lighthouse_cl                                 Started
 ⠿ Container lighthouse_vc                                 Started
```

<h1 align="center">Log kontrolleri</h1>

```console
# İlk olarak buradan loglara bakıyoruz:
docker logs lighthouse_cl -f
# başta herhangi bir şekilde sync olmayacağını göreceksiniz fakat biraz zaman verin.
```

