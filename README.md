# Домашнее задание по Linux Security

Уникальный номер: М265852

## Выполненные задания
1. Создание пользователя `user1`.
2. Поиск SUID-файлов и процессов с повышенными привилегиями.
3. Изучение механизма set-UID.
4. Работа с Linux capabilities.
5. Настройка sudo для изменения системного времени.
6. Сбор артефактов и скриншота.

## Раздел 1. Создание пользователя

`groupadd students` — создана группа students.

`adduser -m -u 1234 -G students user1` — создан пользователь user1 с UID 1234, домашней директорией и дополнительной группой students.

`passwd user1` — установлен пароль пользователя.

`chage -M 90 user1` — установлен максимальный срок действия пароля 90 дней.

`id user1` — проверены UID пользователя и его принадлежность к группам.

`chage -l user1` — проверены параметры срока действия пароля.

`ls -ld /home/user1` — проверены существование домашней директории и её права доступа.

`getent passwd user1` — проверена запись пользователя в базе учётных записей.

## Раздел 2. Мониторинг файлов и процессов

`find / -type f -perm -4000 -ls 2>/dev/null > /root/suid-files.out` — найдены файлы с установленным битом set-UID, результаты сохранены в файл.

`cat /root/suid-files.out` — просмотрен список найденных SUID-файлов.
Во втором терминале выполнен вход под пользователем `user1` и запущена команда `passwd` для создания процесса с повышенными привилегиями.

`ps -eo pid,ruid,euid,comm | awk 'NR==1 || ($2>=1000 && $3==0)' > /root/process-monitor.out` — выполнен поиск процессов с эффективным UID 0 и реальным UID обычного пользователя. Результаты сохранены в файл.

## Раздел 3. Изучение механизма set-UID

`sudo -u user1 cat /etc/shadow` — проверена возможность чтения файла /etc/shadow от имени обычного пользователя. В результате: Permission denied.

`cp /usr/bin/cat /home/user1/cat-suid` — скопирована утилита cat в домашнюю директорию пользователя.

`chown root:root /home/user1/cat-suid` — владельцем утилиты назначен root.

`chmod 4755 /home/user1/cat-suid` — установлен бит set-UID.

`ls -l /home/user1/cat-suid` — проверены права доступа и владелец утилиты.

`sudo -u user1 /home/user1/cat-suid /etc/shadow` — проверена работа утилиты с установленным set-UID для чтения файла /etc/shadow.

## Раздел 4. Изучение механизма привилегий

`cp /usr/bin/chown /home/user1/chown-cap` — скопирована утилита chown в домашнюю директорию пользователя.

`chown root:root /home/user1/chown-cap` — владельцем утилиты назначен root.

`chmod 755 /home/user1/chown-cap` — установлены права доступа к утилите.

`setcap cap_chown+ep /home/user1/chown-cap` — назначена capability CAP_CHOWN для изменения владельца файлов.

`getcap /home/user1/chown-cap` — проверена установленная capability.

`sudo -u user1 touch /home/user1/cap-test` — создан тестовый файл от имени пользователя user1.

`ls -l /home/user1/cap-test` — проверен первоначальный владелец файла.

`sudo -u user1 /home/user1/chown-cap root:root /home/user1/cap-test` — выполнена попытка изменить владельца и группу файла с использованием CAP_CHOWN от имени user1.

`ls -l /home/user1/cap-test` — проверен результат изменения владельца файла.

## Раздел 5. Настройка sudo

`command -v date` — определён полный путь к утилите date.

`visudo -f /etc/sudoers.d/user1-time` — создан файл конфигурации sudo с разрешением пользователю user1 изменять системное время.

`chmod 0440 /etc/sudoers.d/user1-time` — установлены необходимые права доступа к файлу конфигурации.

`visudo -c` — проверена корректность конфигурации sudo.

`sudo -l -U user1` — проверены разрешённые команды для пользователя user1.

`sudo -u user1 sudo /usr/sbin/date -s "2026-10-12 12:00:00"` — проверено изменение системного времени от имени пользователя user1.

`date` — проверено текущее системное время.

## Раздел 6. Сбор артефактов

`echo М265852` — выведен уникальный номер для скриншота.

`mkdir -p /root/mephi-linux-security-2026` — создана директория для итоговых артефактов.

`cp /root/suid-files.out /root/mephi-linux-security-2026/suid-files.out` — скопирован список SUID-файлов.

`cp /root/process-monitor.out /root/mephi-linux-security-2026/process-monitor.out` — скопированы результаты мониторинга процессов.

`stat /home/user1 > /root/mephi-linux-security-2026/stat.out` — сохранена информация о домашней директории пользователя.

`stat /home/user1/cat-suid >> /root/mephi-linux-security-2026/stat.out` — добавлена информация о правах и владельце утилиты cat-suid.

`stat /home/user1/chown-cap >> /root/mephi-linux-security-2026/stat.out` — добавлена информация о правах и владельце утилиты chown-cap.

`getcap /home/user1/chown-cap > /root/mephi-linux-security-2026/getcap.out` — сохранены сведения о назначенных capabilities.

`cp /etc/passwd /root/mephi-linux-security-2026/passwd` — скопирован файл учётных записей пользователей.

`cp /etc/shadow /root/mephi-linux-security-2026/shadow` — скопирован файл с данными о хешах паролей.

`cp /etc/group /root/mephi-linux-security-2026/group` — скопирован файл групп пользователей.

`cp /etc/sudoers /root/mephi-linux-security-2026/sudoers` — скопирован основной файл конфигурации sudo.

`cp /etc/sudoers.d/user1-time /root/mephi-linux-security-2026/user1-time` — скопирован файл настройки sudo для пользователя user1.

`history -a` — сохранена история текущего сеанса терминала в файл истории оболочки.

`history > /root/mephi-linux-security-2026/history.out` — история команд сохранена в файл history.out