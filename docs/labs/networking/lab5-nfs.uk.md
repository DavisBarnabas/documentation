---
author: Wale Soyinka
contributors: Steven Spencer, Ganna Zhyrnova
tested on: Всі версії
tags:
  - файлова система мережі
  - nfs
  - exportfs
  - rpc
  - nfs-сервер
---

# Лабораторна робота 5: NFS

## Завдання

Виконавши цю лабораторну роботу, ви зможете

- встановити та налаштувати NFS
- обмінюватися файлами та каталогами між системами Linux за допомогою NFS
- використовувати звичайні утиліти NFS для запитів або вирішення проблем NFS

Приблизний час виконання цієї лабораторної роботи: 40 хвилин

## NFS

NFS - це абревіатура від Network File System. Це дозволяє обмінюватися файлами та папками через мережу з іншими системами. NFS забезпечує простий спосіб зробити вміст локальної файлової системи доступним для кількох користувачів (або систем) у мережі.

Цей спільний доступ традиційно здійснюється між системами, подібними до UNIX/Linux, але системи під керуванням операційних систем Microsoft Windows також можуть отримати доступ до спільних ресурсів NFS, якщо на них встановлено належне програмне забезпечення.

Підтримка NFS має бути включена або скомпільована в ядро.

Як і більшість мережевих концепцій, NFS має клієнтську та серверну частини. Серверна сторона складається з системи, яка експортує (обмінює) файлові системи в інші системи. Клієнтська сторона складається із систем, яким потрібен доступ до файлової системи, експортованої сервером.

Для NFSv4 потрібні служби таких програм (демонів):

- portmap  - зіставляє програми RPC зі звичайними мережевими портами
- mountd   -  обробляє вхідні запити на монтування
- nfsd - це основна програма NFS, яка обробляє фактичну передачу файлів

## /etc/exports

Файл конфігурації `/etc/exports` служить списком контролю доступу для визначення файлових систем, які можна експортувати через NFS авторизованим клієнтам. Він надає інформацію для `mountd` і демона файлового сервера NFS на основі ядра `nfsd`.

Директиви в `/etc/exports` використовують наступний синтаксис:

```bash
shareable_directory  allowed_clients(options_affecting_allowed_clients) 
```

## Завдання 1

### Управління NFS

У цій вправі ви надасте спільний доступ (експортуєте) локальний каталог, який буде надано спільно з вашою партнерською системою, але спочатку ви навчитеся керувати службою NFS.

NFS — це клієнтсько-серверна програма на основі Remote Procedure Call (RPC). Тому корисно мати під рукою утиліти RPC, які можна використовувати для запитів, налагодження та здійснення викликів RPC до серверів RPC (наприклад, серверів NFS). `rpcinfo` є однією з таких корисних утиліт. Його синтаксис використання та параметри показані тут:

```bash
SYNOPSIS
     rpcinfo [-m | -s] [host]
     rpcinfo -p [host]
     rpcinfo -T transport host prognum [versnum]
     rpcinfo -l [-T transport] host prognum versnum
     rpcinfo [-n portnum] -u host prognum [versnum]
     rpcinfo [-n portnum] [-t] host prognum [versnum]
     rpcinfo -a serv_address -T transport prognum [versnum]
     rpcinfo -b [-T transport] prognum versnum
     rpcinfo -d [-T transport] prognum versnum
```

#### Щоб запустити NFS

1. Переконайтеся, що ви ввійшли до системи як користувач із правами адміністратора.

2. Почніть із встановлення пакета `nfs-utils`. Цей пакет містить різноманітні утиліти для використання з клієнтами та серверами NFS. Впишіть:

    ```bash
    dnf -y install nfs-utils
    ```

3. Серед іншого, щойно встановлений пакет nfs-utils також містить модуль обслуговування systemd (`nfs-server.service`), необхідний для керування демоном NFS у системі. Використовуйте `systemctl` для перегляду деяких допоміжних служб, які «Wants» модуль NFS-сервера. Впишіть:

    ```bash
     systemctl show  -p "Wants"  nfs-server
    ```
    **Вихід**
    ```bash
    Wants=nfs-idmapd.service nfsdcld.service rpcbind.socket rpc-statd-notify.service rpc-statd.service auth-rpcgss-module.service network-online.target
    ```

    Деякі важливі та помітні служби, яких потребує nfs-сервер, це `nfs-idmapd`, `nfsdcld`, `rpcbind`, `rpc-statd-notify`, `rpc-statd`, `auth-rpcgss-module`.

4. Команда `rpcinfo` здійснює виклики RPC до сервера RPC, а потім звітує про свої результати. `rpcinfo` містить список усіх служб RPC, зареєстрованих у `rpcbind`. Використовуйте `rpcinfo`, щоб запитати на локальному сервері список усіх зареєстрованих служб RPC. Впишіть:

    ```bash
    rpcinfo -p localhost
    ```

    **Вихід**
    ```bash
    program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    ```

    З наведеного вище зразка вихідних даних ми можемо сказати, що служба portmapper зареєстрована на сервері RPC, на якому працює localhost.

    !!! Question "Питання"

     A) Що таке portmapper? 
     B) Дізнайтеся значення різних полів (заголовка стовпця) команди `rpcinfo`? (Програма, версії, прото та сервіс.)

5. Перевірте статус `nfs-server.service`. Впишіть:

    ```bash
    systemctl status nfs-server
    ```
    **Вихід**
   ```bash
    ● nfs-server.service - NFS server and services
    Loaded: loaded (/usr/lib/systemd/system/nfs-server.service; disabled; vendor preset: disabled)
    Active: inactive (dead)
    ```
    nfs-server.service наразі не працює відповідно до результатів нашої демонстраційної системи.

6. Використовуйте systemctl для запуску демона nfs-server. Впишіть:

    ```bash
    systemctl start nfs-server
    ```

7. Ще раз перевірте статус служби nfs-сервера.

8. Виконайте команду `rpcinfo` ще раз, щоб перевірити, чи щось змінилося.

    !!! Question "Питання"
   
        Які нові служби ви побачите у списку `rpcinfo` після запуску nfs-сервера?

9. Переконайтеся, що `nfs-server.service` налаштовано на запуск кожного разу, коли система автоматично перезавантажується. Впишіть:

    ```bash
    systemctl is-enabled nfs-server
    ```

10. Якщо nfs-сервер вимкнено у вашій системі, виконайте команду, щоб налаштувати його для автоматичного запуску з системою.

11. Якщо підсистема брандмауера працює на вашому сервері, вам потрібно буде дозволити/дозволити трафік NFS через брандмауер для віддалених клієнтів NFS. Це можна зробити, запустивши:

    ```bash
    firewall-cmd --permanent --add-service nfs && firewall-cmd --reload
    ```

## Завдання 2

### Експорт загального доступу

Створення загального доступу виконується шляхом створення каталогу або надання спільного доступу до вже існуючого каталогу в локальній файловій системі.

Утиліта `exportfs` використовується для перегляду та підтримки таблиці експортованих файлових систем NFS. Його синтаксис використання та параметри:

```bash
SYNOPSIS
       /usr/sbin/exportfs [-avi] [-o options,..] [client:/path ..]
       /usr/sbin/exportfs -r [-v]
       /usr/sbin/exportfs [-av] -u [client:/path ..]
       /usr/sbin/exportfs [-v]
       /usr/sbin/exportfs -f
       /usr/sbin/exportfs -s
```

!!! note "Примітка"

    Не забудьте замінити всі посилання на сервер<PR> із фактичним іменем хоста ваших партнерів.

#### Щоб створити та експортувати спільний доступ

Ви створите та надасте спільний доступ до каталогу під назвою `/mnt/nfs`. Цей каталог буде вихідною файловою системою, експортованою з сервера NFS.

1. Переконайтеся, що ви ввійшли до системи як користувач із правами адміністратора.

2. Створіть каталог у `/mnt` під назвою `nfs` і перейдіть до цього каталогу.

    ```bash
    mkdir /mnt/nfs && cd /mnt/nfs
    ```

3. Створіть 5 зразків файлів у створеному вами новому каталозі. Впишіть:

    ```bash
     touch {1..5}nfs
    ```

4. Використовуйте функцію оболонки HEREDOC, щоб створити новий запис експорту NFS у `/etc/exports`. Потрібний однорядковий запис - `/mnt/nfs foocentos2(rw) localhost(rw)`. Впишіть:

    ```bash
    cat << EOF > /etc/exports
    /mnt/nfs    172.16.99.0/24(rw)   localhost(rw)
    EOF
    ```
    Ви також можете скористатися будь-яким зручним для вас текстовим редактором для створення запису.

5. Перевірте вміст `/etc/exports`, щоб переконатися, що немає помилок.

6. Після внесення будь-яких змін у файл `/etc/exports` слід виконати команду `exportfs`. Впишіть:

    ```bash
    exportfs -r
    ```

7. Використовуйте позначку `-s` з командою `exportfs`, щоб відобразити поточний список експорту, який підходить для `/etc/exports`. Наприклад, перегляньте список каталогів, дозволених хостів і параметрів. Впишіть:

    ```bash
    exportfs -s
    ```

    Вкажіть свій результат нижче.

## Завдання 3

### Монтування спільних ресурсів NFS

Ця вправа стосується клієнтської сторони NFS. Ви спробуєте отримати доступ до сервера NFS як клієнт.

`showmount` — це зручна утиліта для запиту та показу інформації про монтування на серверах NFS. Він також може показувати стан сервера NFS і список клієнтів, які монтуються з сервера. Його синтаксис і параметри показані тут:

```bash
SYNOPSIS
       showmount [ -adehv ] [ --all ] [ --directories ] [ --exports ] [ --help ] [ --version ] [ host ]

OPTIONS
       -a or --all
              List  both  the  client  hostname or IP address and mounted directory in host:dir format.
       -d or --directories
              List only the directories mounted by some client.
       -e or --exports
              Show the NFS server's export list.
       -h or --help
              Provide a short help summary.
       -v or --version
              Report the current version number of the program.
       --no-headers
              Suppress the descriptive headings from the output.
```

#### Для локального доступу до спільного ресурсу NFS

Ви перевірите конфігурацію сервера NFS із *Вправи 1*, спробувавши отримати доступ до експортованого каталогу з вашого локального комп’ютера, перш ніж тестувати його з віддаленого комп’ютера.

1. Увійшовши як суперкористувач, створіть каталог під назвою `/mnt/nfs-local`. Цей каталог слугуватиме тестовою точкою монтування для спільного ресурсу NFS.

2. Для швидкої перевірки перед польотом запустіть `showmount` як клієнт, щоб показати доступний список експорту на сервері. Впишіть:

    ```bash
    showmount  -e localhost
    ```
    **Вихід**
    ```
    Export list for localhost:
    /mnt/nfs 172.16.99.0/24,localhost
    ```

    Ви повинні побачити будь-який експорт NFS, налаштований на сервері.

3. Тепер ви готові змонтувати спільний ресурс NFS у тестовій точці монтування. Впишіть:

    ```bash
    mount  -t  nfs  localhost:/mnt/nfs   /mnt/nfs-local
    ```

4. Змініть свій PWD на каталог `/mnt/nfs-local` і перегляньте його вміст.

5. Перебуваючи в каталозі `/mnt/nfs-local`, спробуйте видалити деякі файли. Впишіть:

    ```bash
    rm -rf 1nfs  2nfs
    ```
    **Вихід**
    ```bash
    rm: cannot remove '1nfs': Permission denied
    rm: cannot remove '2nfs': Permission denied
    ```

    !!! Question "Питання"

     Чи була ваша спроба видалити файл успішною?

7. Тепер спробуйте створити додаткові файли (6nfs, 7nfs, 8nfs) на спільному ресурсі NFS. Впишіть:

    ```bash
    touch {6..8}nfs
    ```

    !!! Question "Питання"

     Ваша спроба створити файл була успішною? Як ви думаєте, чому це не вдалося?


ВИКОНАЙТЕ ЦЮ ВПРАВУ ЗІ СВОЄЇ ПАРТНЕРСЬКОЇ СИСТЕМИ

#### Для віддаленого доступу до спільного ресурсу NFS

1. Увійшовши в serverPR як суперкористувач, установіть пакет `nfs-utils`, якщо його не встановлено.

2. Створіть каталог під назвою “`/mnt/nfs-remote`”, який слугуватиме точкою монтування для віддаленого ресурсу NFS. Впишіть:

    ```bash
    mkdir   /mnt/nfs-remote
    ```

3. Припускаючи, що IP-адреса віддаленого сервера XY дорівнює 172.16.99.100, змонтуйте спільний ресурс NFS на сервері XY, виконавши:

    ```bash
    mount -t nfs  172.16.99.100:/mnt/nfs  /mnt/nfs-remote
    ```

4. Використовуйте команду `mount`, щоб відобразити додаткову інформацію про спільний ресурс NFS, який ви щойно змонтували. Впишіть:

    ```bash
     mount -t nfs4
    ```
    **Вихід**
    ```bash
    172.16.99.100:/mnt/nfs on /mnt/nfs-remote type nfs4 (rw,relatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255
    ...<SNIP>...
    ```

5. `cd` до точки монтування NFS і спробуйте видалити її вміст. Впишіть:

    ```bash
    cd /mnt/nfs-remote ; rm -f   
    ```

    Ваша спроба була успішною?

6. Вийдіть із serverPR як суперкористувач і знову увійдіть як непривілейований користувач «ying»

7. Під час входу в serverPR як «ying» cd до каталогу, змонтованого на кроці 2. Впишіть:

    ```bash
    cd /mnt/nfs-remote/
    ```

8. Занотуйте вміст довідника. Якщо ви бачите очікувані файли, ви завершили лабораторію NFS!

    !!! question "Питання"
   
        1. Налаштуйте налаштування NFS на локальному сервері (serverXY), щоб суперкористувач у H.Q. (hq.example.org) зможе підключити ваш спільний ресурс nfs (/mnt/nfsXY) для використання на машині hq.
       
        2. Суперкористувач у HQ повинен мати можливість записувати (створювати) нові файли та видаляти файли на вашому спільному ресурсі NFS.

    !!! tip "Порада"
   
        Вам потрібно вимкнути спеціальну обробку NFS файлів, які належать root. Це робиться шляхом вказівки певного параметра, який «скасує» суперкористувача у файлі «/etc/exports».

