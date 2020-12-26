# Копирование всей прошивки в архив
BACKUP old:
```sh
tar -cvpzf /tmp/lumi_stock.tar.gz -C / --exclude='./tmp/*' --exclude='./dev/*' --exclude='./run/*' --exclude='./proc/*' --exclude='./sys/*' --exclude='./overlay/*' --exclude='./mnt/*' .
````
# Клонирование и сборка своей прошивки
```sh
git clone https://github.com/itProfi/openwrt.git
cd openwrt
./scripts/feeds update -a
./scripts/feeds install -a
cp config_xiaomi_lumi .config
./scripts/feeds update node
rm ./package/feeds/packages/node
rm ./package/feeds/packages/node-*
./scripts/feeds install -a -p node
ln -s files_xiaomi_lumi_my files
make defconfig
OPTIONAL ./scripts/diffconfig.sh > config_xiaomi_lumi_my
````
Возможно, оф. фиды
````
src-git packages https://git.openwrt.org/feed/packages.git^2974079d3
src-git luci https://git.openwrt.org/project/luci.git^51f55b58b
src-git routing https://git.openwrt.org/feed/routing.git^02b4dbf
src-git telephony https://git.openwrt.org/feed/telephony.git^6e019c9
src-git freifunk https://github.com/freifunk/openwrt-packages.git^73454e6fe
````
Возможно, доп фид?
````
src-git node https://github.com/openlumi/openwrt-node-packages.git
````
# Установка Domoticz

В репозитории OpenWrt 19.07 находится достаточно старая версия domoticz.
Чтобы установить свежую версию, воспользуйтесь готовым скриптом, который
нужно будет запустить в консоли

```sh
wget https://github.com/openlumi/xiaomi-gateway-openwrt/raw/master/files/domoticz.sh -O - | sh
````
Он установит все необходимые библиотеки, domoticz и плагин для работы с zigbee.

# Прошивка Zigate и подключение Zigate plugin к Domoticz

Для модуля jn5169, установленного на нашем шлюзе используется прошивка ZiGate.
Чтобы прошить её в модуль, воспользуетесь командой

```sh
wget https://github.com/openlumi/ZiGate/releases/download/snapshot-20201201/ZigbeeNodeControlBridge_JN5169_FULL_FUNC_DEVICE_31e_115200.bin -O /tmp/zigate.bin 
jnflash /tmp/zigate.bin
jntool erase_pdm
```
## Настройка плагина

Всё что нужно, это в разделе Setup > Hardware добавить элемент с типом
`Zigate plugin`, вписать имя в поле Name и нажать `Add`

![Добавление Zigate plugin](https://github.com/openlumi/xiaomi-gateway-openwrt/blob/master/images/zigate_plugin.png)

Для настройки использовать

    Zigate Model: DIN
    Serial Port: /dev/ttymxc1


спустя несколько минут плагин проинициализируется и запустит панель управления
на порту 9440.


## Панель управления Zigate

![Панель управления](https://github.com/openlumi/xiaomi-gateway-openwrt/blob/master/images/zigate_dashboard.png)

В данной панели вы можете включить режим сопряжения с дочерними устройствами и 
изменять настройки. 
Чтобы включить режим добавления устройств, включите переключатель 
`Accept new hardware` в верхнем правом углу.


Более подробно о панели вы можете прочитать на странице плагина
[https://github.com/pipiche38/Domoticz-Zigate-Wiki/blob/master/en-eng/WebUserInterfaceNavigation.md](https://github.com/pipiche38/Domoticz-Zigate-Wiki/blob/master/en-eng/WebUserInterfaceNavigation.md)

После добавления дочернего zigbee устройства оно появится в интерфейсе domoticz.

