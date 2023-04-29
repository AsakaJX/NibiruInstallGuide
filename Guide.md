# Nibiru. Гайд по установке и синхронизации.
## Установка Nibiru:

### 1. Быстрая установка используя скрипт от Nodes.Guru
```shell
wget -q -O nibiru.sh https://api.nodes.guru/nibiru.sh && chmod +x nibiru.sh && sudo /bin/bash nibiru.sh
```
### 2. После завершения работы скрипта, выполните эту команду:
```shell
source $HOME/.bash_profile
```
### 3. Теперь создайте кошелек. (Не забудьте сохранить адресс кошелька)
```shell
nibid keys add wallet
```
### 4. Далее вам нужно запросить токены для зачисления на созданный кошелек. Это можно сделать либо через дискорд [Nibiru](https://discord.gg/nibirufi) в чате [#faucet](https://discord.com/channels/947911971515293759/984840062871175219), либо выполнив эти команды:
```shell
FAUCET_URL="https://faucet.itn-1.nibiru.fi/"
ADDR="ЗДЕСЬ ВСТАВЬТЕ РАНЕЕ СОХРАННЕНЫЙ АДРЕСС КОШЕЛЬКА"
curl -X POST -d '{"address": "'"$ADDR"'", "coins": ["11000000unibi","100000000unusd","100000000uusdt"]}' $FAUCET_URL
```
- [x] **Установка ноды завершена, теперь переходим к этапу синхронизации.**
## Синхронизация ноды.

## Подготовка к синхронизации.
```shell
killall nibid
systemctl enable nibid

apt install lz4 -y
systemctl stop nibid

cp $HOME/.nibid/data/priv_validator_state.json $HOME/.nibid/priv_validator_state.json.backup 

nibid tendermint unsafe-reset-all --home $HOME/.nibid --keep-addr-book 

curl https://snapshots2-testnet.nodejumper.io/nibiru-testnet/nibiru-itn-1_2023-04-28.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.nibid

mv $HOME/.nibid/priv_validator_state.json.backup $HOME/.nibid/data/priv_validator_state.json 

curl -s https://snapshots2-testnet.nodejumper.io/nibiru-testnet/addrbook.json > $HOME/.nibid/config/addrbook.json

PEERS="a1b96d1437fb82d3d77823ecbd565c6268f06e34@nibiru-testnet.nodejumper.io:27656,6fdcccd1ceef63dbf7ad04bbd6346057f6700b44@144.91.106.81:27656,e3b678986ea18d95943a07ee09b331da027a9fbc@173.212.248.45:26656,84ea430e328275fea556875aacfa5f0b36308272@146.190.40.144:26656,508619459ca0e387cb231727984f43410a4c9cbb@81.0.220.100:26656,38f93e80523c985e8231a67299b52ee75faad192@81.0.218.88:26656,b44db9854bf7ad419eaa973334433992c623c97c@167.172.138.167:27656,aaff99ce425ac9d062d1bca6f75987656e137307@138.201.34.19:26656,4f6df6ce7d69129019cfb4eea5554a6f6edb217a@65.21.141.104:11656,8692da09e683b94c0e90a3ce83e4902459c3d044@31.223.32.35:14546,6b8aca01c8ad5154ee9f00903a7d37bf4c744abf@178.18.252.136:26656,cd44f2d2fc1ded3a63c64f46ed67f783c2d93d57@144.76.223.24:36656,cb619ab3c59e3e0191e196814bb8df0996699580@38.242.200.220:26656,8d66071d96758a6b62ca8222470bf0d382fe99e2@65.108.75.107:19656,b89394eb5f1de8a697366a370d42ecafa583a941@81.0.218.95:26656,5cdd088bb7b16e33c1915db041eff9d05d235981@158.220.97.106:26656,ac19505205266666a050c3a915daf3679eaa8a3b@185.192.97.237:26656,62cd58d1d04c3611e048c993bcd5deb83aef075f@212.86.105.41:26656,ac1aefbae8270db40a3996be578ffcfedab81048@34.27.171.122:26656,7c46c78da011011ae949946afbbd1a0eb611bfa2@65.109.4.131:26656"

sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.nibid/config/config.toml
```

### 1. Запускаем синхронизацию ноды.
```shell
systemctl restart nibid && journalctl -u nibid -f -o cat
```
### После запуска команды можете спокойно закрывать терминал. Команда теперь будет выполняться в фоне на сервере.

### 2. Проверяем синхронизированна ли нода. (Этап синхронизации ноды занимает длительное время (вплоть до нескольких дней))
```shell
curl -s localhost:26657/status | jq .result.sync_info.catching_up
```
- [x] Если команда возвратила значение ``false``, значит нода синхронизированна.

## Дополнительные команды.
### Просмотр логов ноды.
```shell
journalctl -u nibid -f
```
### Выключение ноды
```shell
systemctl disable nibid
systemctl stop nibid
killall nibid
```
### Перезапуск ноды
```shell
systemctl restart nibid
```
