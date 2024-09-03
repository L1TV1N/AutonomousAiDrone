# Руководство по настройке NVIDIA Jetson Nano

## Введение

Это руководство поможет вам установить операционную систему и необходимые пакеты на вашем устройстве NVIDIA Jetson Nano, а также настроить проект для работы с дронами.

### Установка операционной системы

Следуйте инструкциям на сайте NVIDIA, чтобы установить операционную систему на Jetson Nano. Убедитесь, что вы завершили процесс установки, включая настройку часового пояса и создание учетной записи пользователя. Вы можете найти инструкции по установке [здесь](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#write).

#### Обновление и очистка системы

После установки системы рекомендуется обновить её и автоматически удалить ненужные пакеты. Для этого выполните следующую команду в терминале:

```bash
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
```

#### Установка необходимых пакетов

Перед началом установки убедитесь, что у вас установлены `git` и `python3-pip`. Для этого выполните команду:

```bash
sudo apt install nano git python3-pip
```

#### Опционально: Удаление ненужных компонентов операционной системы

Стандартная операционная система Jetson Nano включает множество инструментов, которые могут не понадобиться. Чтобы сэкономить место и ресурсы, вы можете удалить их с помощью следующей команды:

```bash
sudo apt purge snapd lx* nautilus* openbox* printer* rhythmbox* gnome* lightdm* xubuntu* xscreensaver* xfce* lxde* x2go* word* thunderbird libreoffice* chromium-* vim* docker* libvisionworks* ubuntu-wallpapers-bionic light-themes adwaita-icon-theme -y && sudo apt autoremove -y
```

> Обратите внимание, что эта команда удалит *большинство* ненужных пакетов. Пожалуйста, внимательно прочитайте команду перед выполнением, чтобы избежать удаления необходимых пакетов.

### Установка проекта

Клонируйте проект из [репозитория GitHub](https://github.com/sieuwe1/Autonomous-Ai-drone-scripts):

```bash
git clone https://github.com/sieuwe1/Autonomous-Ai-drone-scripts.git
```

#### Установка пакетов для проекта

##### Jetson Inference

Начните с установки необходимых пакетов для установки Jetson Inference:

```bash
sudo apt install cmake libpython3-dev python3-numpy
```

Затем выполните следующие команды для установки Jetson Inference:

> Во время установки убедитесь, что вы отменили выбор всех моделей и в конце выбрали модель `SSD MobileNet V2`. После завершения загрузки также установите PyTorch.

```bash
git clone --recursive https://github.com/dusty-nv/jetson-inference
cd jetson-inference
mkdir build
cd build
cmake ../
make -j$(nproc)
sudo make install
sudo ldconfig
```

*Источник: [Jetson Inference](https://github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md)*

##### Установка оставшихся пакетов

Установите оставшиеся необходимые пакеты с помощью следующей команды:

```bash
python3 -m pip install --upgrade pip
python3 -m pip install scikit-build cmake opencv-python numpy dronekit pyserial keyboard simple-pid
```

#### Утилиты

##### Перенаправление X11

Для просмотра датчиков, медиа и других интерфейсов с графическим пользовательским интерфейсом можно использовать перенаправление X11 через SSH. 

Включите перенаправление X11 в конфигурационном файле `sshd`:

```bash
sudo nano /etc/ssh/sshd_config
```

Найдите параметр `X11Forwarding` и убедитесь, что его значение установлено на `yes`. Это должно выглядеть так:

```
X11Forwarding yes
```

Чтобы подключиться к Jetson Nano с поддержкой X11, используйте следующую команду:

```bash
ssh -X -Y USERNAME@IP
```

###### Просмотр видео

Одним из распространенных случаев использования X11 в проекте дронов является просмотр отладочных видео. Для этого можно использовать пакет `mplayer`, который является легковесным медиаплеером.

Установите `mplayer`:

```bash
sudo apt install mplayer
```

Пример использования `mplayer`:

```bash
mplayer video.mp4
```

### Установка симулятора

Клонируйте симулятор ArduPilot из [репозитория GitHub](https://github.com/ArduPilot/ardupilot/):

```bash
git clone https://github.com/ArduPilot/ardupilot --recursive
cd ardupilot
```

*Источник: [ArduPilot](https://ardupilot.org/dev/docs/building-setup-linux.html#building-setup-linux)*

#### Установка необходимых пакетов

Перед выполнением скрипта установки зависимостей от ArduPilot установите дополнительные пакеты:

```bash
sudo apt install libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev libfreetype6-dev python3-setuptools python3-dev python3 libportmidi-dev
```

После этого установите все зависимости с помощью инструмента от ArduPilot:

```bash
Tools/environment_install/install-prereqs-ubuntu.sh -y
```

Перезагрузите путь (выйдите и войдите снова, чтобы изменения вступили в силу):

```bash
. ~/.profile
```

#### Сборка проекта

Перед сборкой убедитесь, что проект настроен:

```bash
./waf configure
```

После настройки проекта выполните сборку:

```bash
./waf clean
./waf build
```

#### Запуск симулятора

Убедитесь, что текущая директория — это директория скриптов:

```bash
cd Tools/scripts
```

Запустите симулятор для `ArduCopter`:

```bash
sim_vehicle.py -v ArduCopter --map --console
```

### Очистка

Завершите процесс, удалив все неиспользуемые пакеты:

```bash
sudo apt autoremove -y
```

Теперь ваш NVIDIA Jetson Nano готов к использованию для разработки и тестирования проектов с дронами!


# Руководство по настройке NVIDIA Jetson Nano

Вот переработанный текст для публикации на GitHub:

---

## Руководство по установке и настройке Jetson Nano для проекта автономного дрона

### 1. Установка операционной системы

Следуйте официальным инструкциям по установке операционной системы на Jetson Nano. Завершите процесс установки, установив часовой пояс и создав учетную запись пользователя.

### 2. Обновление и очистка системы

Обновите систему и удалите ненужные пакеты:

```bash
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
```

### 3. Установка необходимых программ

В этом руководстве используется текстовый редактор `nano`, который не установлен по умолчанию на Jetson Nano. Вы можете использовать `vim` или установить `nano` с помощью следующей команды:

```bash
sudo apt install nano
```

### 4. Упрощение операционной системы (опционально)

Jetson Nano поставляется с большим количеством предустановленных инструментов, которые могут быть не нужны для проекта дрона. Чтобы сэкономить ресурсы, рекомендуется удалить эти инструменты:

**Внимание:** внимательно проверьте команду перед выполнением, чтобы избежать удаления необходимых пакетов.

```bash
sudo apt purge snapd lx* nautilus* gedit openbox* printer* rhythmbox* gnome* lightdm* xscreensaver* thunderbird libreoffice* chromium-* docker* ubuntu-wallpapers* light-themes && sudo apt autoremove
```

### 5. Удаление Network Manager и WPA Supplicant

Если дрон не требует сложных сетевых настроек, можно использовать стандартный сервис Linux `systemd-networkd`. Убедитесь, что DHCP включен для Ethernet интерфейса перед удалением `network-manager` и `wpasupplicant`:

Создайте файл сетевой конфигурации, если он еще не существует:

```bash
sudo nano /etc/systemd/network/eth0.network
```

Пример содержимого файла:

```ini
[Match]
Name=eth0

[Network]
DHCP=yes
```

Перезапустите и включите `systemd-networkd`:

```bash
sudo systemctl restart systemd-networkd
sudo systemctl enable systemd-networkd
```

Удалите `network-manager` и `wpasupplicant`:

```bash
sudo apt purge network-manager wpasupplicant
```

### 6. Подготовка к установке автономного дрона

Установите необходимые инструменты:

```bash
sudo apt install git python3-pip
```

Клонируйте проект:

```bash
git clone https://github.com/sieuwe1/Autonomous-Ai-drone-scripts.git
```

### 7. Установка Jetson Inference

Установите зависимости:

```bash
sudo apt install cmake libpython3-dev python3-numpy
```

Клонируйте и установите `Jetson Inference`:

```bash
git clone --recursive https://github.com/dusty-nv/jetson-inference
cd jetson-inference
mkdir build
cd build
cmake ../
make -j$(nproc)
sudo make install
sudo ldconfig
```

Подробнее об установке [здесь](https://github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md).

### 8. Установка дополнительных пакетов

```bash
python3 -m pip install --upgrade pip
python3 -m pip install scikit-build cmake opencv-python numpy dronekit pyserial keyboard simple-pid geographiclib
```

### 9. Использование

Для запуска дрона:

```bash
sudo python3 follow_person_main.py --mode=flight --debug_path=debug/flight1
```

Для тестирования в симуляторе измените параметр `--mode` с `flight` на `test`.

### 10. Установка симулятора ArduPilot

Клонируйте репозиторий:

```bash
git clone https://github.com/ArduPilot/ardupilot --recursive
cd ardupilot
```

### 11. Подготовка среды

Установите дополнительные пакеты:

```bash
sudo apt-get install python-dev libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev libsdl1.2-dev libsmpeg-dev python-numpy subversion libportmidi-dev ffmpeg libswscale-dev libavformat-dev libavcodec-dev
```

Обновите `setuptools` и `wheel`:

```bash
pip2 install -U setuptools wheel
```

Запустите скрипт установки окружения:

```bash
Tools/environment_install/install-prereqs-ubuntu.sh -y
```

Обновите пути:

```bash
. ~/.profile
```

### 12. Проблемы с установкой Pygame

Если возникают проблемы с установкой `pygame`, попробуйте установить версию 2.0.0:

```bash
sed -i 's/\$PYTHON_PKGS pygame /$PYTHON_PKGS pygame==2.0.0 /' Tools/environment_install/install-prereqs-ubuntu.sh
```

### 13. Сборка

Настройте проект перед сборкой:

```bash
./waf configure
```

Соберите проект:

```bash
./waf clean
./waf build
```

### 14. Запуск симулятора

Запустите симулятор для ArduCopter:

```bash
sim_vehicle.py -v ArduCopter
```

Для запуска с консолью и/или картой:

```bash
pip install -U --user --verbose wxpython console map
sim_vehicle.py -v ArduCopter --console --map
```

### 15. Очистка

Удалите все неиспользуемые пакеты:

```bash
sudo apt autoremove -y
```


