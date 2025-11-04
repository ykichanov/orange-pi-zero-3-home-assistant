1. Обновление Debian
apt update -y
apt upgrade -y
reboot
2. Устанавливаем необходимые утилиты для окружения HAOS
apt install \
apparmor \
cifs-utils \
curl \
dbus \
jq \
libglib2.0-bin \
lsb-release \
network-manager \
nfs-common \
systemd-journal-remote \
systemd-resolved \
udisks2 \
wget -y
3. Включение AppArmor и CGroupV1
echo "extraargs=apparmor=1 security=apparmor" >> /boot/orangepiEnv.txt
sed -i -e "1 s/$/ systemd.unified_cgroup_hierarchy=0/" /boot/orangepiEnv.txt
update-initramfs -u
reboot
4. Проверка AppArmor
systemctl status apparmor.service
Вы должны увидеть active (excited)
5. Проверка CGroupV1
findmnt -lo source,target,fstype,options -t cgroup,cgroup2
Вы должны увидеть много строк cgroup в source колонке
6. sudo su –
7. Устанавливаем Docker-CE командой
curl -fsSL get.docker.com | sh
8. Теперь нам нужно обязательно добавить пользователей orangepi и root в группу доступа docker, для этого выполняем поочередно две команды
usermod -aG docker orangepi
usermod -aG docker root
9. Теперь мы скачиваем нужную версию OS-Agent по ссылке https://github.com/hom…-agent/releases/latest, нам нужен дистрибутив os-agent_1.6.0_linux_aarch64.deb. Нам нужен именно aarch64 Для этого выполняем команду
wget https://github.com/hom….6.0_linux_aarch64.deb
Протестируйте установку, запустив
gdbus introspect --system --dest io.hass.os --object-path /io/hass/os
Должно появиться сообщение небольшое сообщение в котором не содержится ошибок, это означает, что все сделано правильно и агент успешно установлен.
10. Установите Home Assistant Supervised
wget https://github.com/hom…sistant-supervised.deb
apt install --fix-broken ./homeassistant-supervised.deb
11. Исправления после установки
systemctl enable systemd-resolved.service
systemd-resolve --flush-caches
/etc/init.d/networking restart
reboot
