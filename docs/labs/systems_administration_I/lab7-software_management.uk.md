- - -
author: Wale Soyinka contributors: Steven Spencer, tianci li tested on: 8.8 tags:
  - лабораторна вправа
  - управління програмним забезпеченням
- - -

# Лабораторна робота 7: Керування та встановлення програмного забезпечення

## Цілі

Виконавши цю лабораторну роботу, ви зможете

- Відправляти пакети запитів для отримання інформації
- Встановлювати програмне забезпечення з бінарних пакетів
- Вирішувати деякі основні проблеми залежностей
- Компілювати та встановлювати програмне забезпечення з джерел

Приблизний час виконання цієї лабораторної роботи: 90 хвилин

## Бінарні та вихідні файли

Програми, які зараз встановлено у вашій системі, залежать від кількох факторів. Основний фактор залежить від груп програмних пакетів, вибраних під час встановлення операційної системи. Інший фактор залежить від того, що було зроблено з системою з моменту її використання.

Ви побачите, що одним із ваших рутинних завдань як системного адміністратора є керування програмним забезпеченням. Це часто включає:

- встановлення нового програмного забезпечення
- видалення програмного забезпечення
- оновлення вже встановленого програмного забезпечення

Програмне забезпечення можна встановити в системах на базі Linux кількома способами. Ви можете встановити з вихідного коду або попередньо скомпільованих двійкових файлів. Останній спосіб є найпростішим, але він також найменш настроюваний. Коли ви встановлюєте з попередньо скомпільованих двійкових файлів, більшу частину роботи вже зроблено за вас, але навіть тоді вам потрібно знати назву та де знайти потрібне програмне забезпечення.

Майже все програмне забезпечення спочатку надходило як вихідні файли мови програмування C або "C++". Вихідні програми зазвичай розповсюджуються як архіви вихідних файлів. Зазвичай файли tar’ed або gzip d або bzip2. Загальні файли tar’ed або gzip d або bzip2.

Більшість розробників привели свій вихідний код у відповідність до стандартів GNU, що полегшує спільний доступ. Це також означає, що пакунки будуть скомпільовані на будь-якій UNIX або UNIX-подібній системі (наприклад, Linux).

RPM є основним інструментом для керування програмами (пакетами) у дистрибутивах на основі Red Hat, таких як Rocky Linux, Fedora, Red Hat Enterprise Linux (RHEL), openSuSE, Mandrake тощо.

Програми, які використовуються для керування програмним забезпеченням у дистрибутивах Linux, називаються менеджерами пакетів. Прикладами є наступні:

- The Red Hat Package Manager (`rpm`). Пакунки мають суфікс «.rpm»
- The Debian package management system (`dpkg`).  Пакунки мають суфікс «.deb»

Далі наведено деякі популярні параметри командного рядка та синтаксис для команди RPM:

**rpm**

Usage: rpm [OPTION...]

**ЗАПИТ ПАКЕТІВ**

```
Query options (with -q or --query):
  -c, --configfiles                  list all configuration files
  -d, --docfiles                     list all documentation files
  -L, --licensefiles                 list all license files
  -A, --artifactfiles                list all artifact files
      --dump                         dump basic file information
  -l, --list                         list files in package
      --queryformat=QUERYFORMAT      use the following query format
  -s, --state                        display the states of the listed files
```

**ПЕРЕВІРКА ПАКЕТІВ**

```
Verify options (with -V or --verify):
      --nofiledigest                 don't verify digest of files
      --nofiles                      don't verify files in package
      --nodeps                       don't verify package dependencies
      --noscript                     don't execute verify script(s)
```

**ВСТАНОВЛЕННЯ, ОНОВЛЕННЯ ТА ВИДАЛЕННЯ ПАКЕТІВ**

```
Install/Upgrade/Erase options:
      --allfiles                     install all files, even configurations which might otherwise be skipped
  -e, --erase=<package>+             erase (uninstall) package
      --excludedocs                  do not install documentation
      --excludepath=<path>           skip files with leading component <path>
      --force                        shorthand for --replacepkgs --replacefiles
  -F, --freshen=<packagefile>+       upgrade package(s) if already installed
  -h, --hash                         print hash marks as package installs (good with -v)
      --noverify                     shorthand for --ignorepayload --ignoresignature
  -i, --install                      install package(s)
      --nodeps                       do not verify package dependencies
      --noscripts                    do not execute package scriptlet(s)
      --percent                      print percentages as package installs
      --prefix=<dir>                 relocate the package to <dir>, if relocatable
      --relocate=<old>=<new>         relocate files from path <old> to <new>
      --replacefiles                 ignore file conflicts between packages
      --replacepkgs                  reinstall if the package is already present
      --test                         don't install, but tell if it would work or not
  -U, --upgrade=<packagefile>+       upgrade package(s)
      --reinstall=<packagefile>+     reinstall package(s)
```

## Завдання 1

### Встановлення, запит і видалення пакетів

У цій лабораторній роботі ви дізнаєтеся, як користуватися системою RPM і встановити зразок програми у вашій системі.

!!! tip "Підказка"

    У вас є багато варіантів, звідки отримати пакети Rocky Linux. Ви можете вручну завантажити їх із надійних [або ненадійних] сховищ. Ви можете отримати їх із дистрибутива ISO. Ви можете отримати їх із централізованого спільного розташування за допомогою таких протоколів, як nfs, git, https, ftp, smb, cifs тощо. Якщо вам цікаво, ви можете переглянути наступний офіційний веб-сайт і переглянути відповідний репозиторій для потрібного пакета(ів):
    
    https://download.rockylinux.org/pub/rocky/8.8/

#### Запит пакетів для отримання інформації.

1. Щоб переглянути список усіх пакетів, які зараз встановлено у вашій локальній системі, введіть:

    ```
    $ rpm -qa
    python3-gobject-base-*
    NetworkManager-*
    rocky-repos-*
    ...<OUTPUT TRUNCATED>...
    ```

    Ви повинні побачити довгий список.

2. Давайте заглибимося глибше та дізнаємося більше про один із пакетів, встановлених у системі. Ми розглянемо NetworkManager. Ми використовуватимемо параметри --query (-q) і --info (-i) з командою `rpm`. Впишіть:

    ```
    $ rpm -qi NetworkManager
    Name        : NetworkManager
    Epoch       : 1
    ...<OUTPUT TRUNCATED>...
    ```

   Це величезна кількість інформації (метаданих)!

3. Скажімо, нас цікавить лише поле «Підсумок» попередньої команди. Ми можемо використовувати опцію --queryformat rpm, щоб фільтрувати інформацію, яку ми отримуємо з опції запиту.

    Наприклад, щоб переглянути лише поле «Підсумок», введіть:

    ```
    $ rpm -q --queryformat '%{summary}\n' NetworkManager
    ```

    Назва поля нечутлива до регістру.

4. Щоб переглянути поля «Версія» та «Зведення» встановленого типу пакета NetworkManager:

    ```
    $ rpm -q --queryformat '%{version}  %{summary}\n' NetworkManager 
    ```

5. Введіть команду, щоб переглянути інформацію про пакет bash, встановлений у системі.

    ```
    $ rpm -qi bash
    ```

    !!! note "Примітка" 

     Попередні вправи стосувалися запитів і роботи з пакетами, уже встановленими в системі. У наступних вправах ми почнемо працювати з пакетами, які ще не встановлені. Ми будемо використовувати програму DNF для завантаження пакетів, які використовуватимемо в наступних кроках.

6. Спочатку переконайтеся, що програму `wget` ще не встановлено в системі. Впишіть:

    ```
    $ rpm -q wget
    package wget is not installed
    ```

    Схоже, що `wget` не встановлено в нашій демонстраційній системі.

7. Починаючи з Rocky Linux 8.x, команда `dnf download` дозволить вам отримати останній пакет `rpm` для `wget`. Впишіть:

    ```
    dnf download wget
    ```

8. Використовуйте команду `ls`, щоб переконатися, що пакет завантажено у ваш поточний каталог. Впишіть:

    ```
    $ ls -lh wg*
    ```

9. Використовуйте команду `rpm`, щоб отримати інформацію про завантажений wget-*.rpm. Впишіть:

    ```
    $ rpm -qip wget-*.rpm
    Name        : wget
    Architecture: x86_64
    Install Date: (not installed)
    Group       : Applications/Internet
    ...<TRUNCATED>...
    ```

10. З результатів попереднього кроку, що саме таке пакет `wget`? Підказка: ви можете використовувати опцію формату запиту rpm, щоб переглянути поле опису пакета завантаження.

11.  Якщо вас цікавить пакет `wget files-.rpm`, ви можете перелічити всі файли, включені до пакета, ввівши:

    ```
    $ rpm -qlp wget-*.rpm | head
    /etc/wgetrc
    /usr/bin/wget
    ...<TRUNCATED>...
    /usr/share/doc/wget/AUTHORS
    /usr/share/doc/wget/COPYING
    /usr/share/doc/wget/MAILING-LIST
    /usr/share/doc/wget/NEWS
    ```

12. Давайте переглянемо вміст файлу `/usr/share/doc/wget/AUTHORS`, який зазначено як частину пакета `wget`. Ми будемо використовувати команду `cat`. Впишіть:

    ```
    $ cat /usr/share/doc/wget/AUTHORS
    cat: /usr/share/doc/wget/AUTHORS: No such file or directory
    ```

    `wget` [ще] не встановлено в нашій демонстраційній системі! Отже, ми не можемо переглянути файл АВТОРІВ, який запаковано з ним!

13. Перегляньте список файлів, які постачаються з іншим пакетом (curl), який *вже* встановлено в системі. Впишіть:

    ```
    $ rpm -ql curl
    /usr/bin/curl
    /usr/lib/.build-id
    /usr/lib/.build-id/fc
    ...<>...
    ```

    !!! note "Примітка"

     Ви помітите, що вам не потрібно було посилатися на повну назву пакета `curl` у попередній команді. Це тому, що `curl` уже встановлено.

#### Розширені знання про назву пакета

* **Повна назва пакета**: коли ви завантажуєте пакет із надійного джерела (наприклад, веб-сайт постачальника, сховище розробника), ім’я завантаженого файлу є повним ім’ям пакета, наприклад -- htop-3.2 .1-1.el8.x86_64.rpm. Під час використання команди `rpm` для встановлення/оновлення цього пакунка об’єкт, керований командою, має бути повним ім’ям (або відповідним символом узагальнення) пакунка, наприклад:

    ```
    $ rpm -ivh htop-3.2.1-1.el8.x86_64.rpm
    ```

    ```
    $ rpm -Uvh htop-3.2.1-1.*.rpm
    ```

    ```
    $ rpm -qip htop-3.*.rpm
    ```

    ```
    $ rpm -qlp wget-1.19.5-11.el8.x86_64.rpm
    ```


    Повна назва пакета відповідає умовам іменування, подібним до цього —— `[Package_Name]-[Version]-[Release].[OS].[Arch].rpm` або `[Package_Name]-[Version]-[Release].[OS].[Arch].src.rpm`

* **Package name**: Оскільки RPM використовує базу даних для керування програмним забезпеченням, після завершення встановлення пакета база даних матиме відповідні записи. Наразі операційний об’єкт команди `rpm` має лише ввести назву пакета. як от:

    ```
    $ rpm -qi bash
    ```

    ```
    $ rpm -q systemd
    ```

    ```
    $ rpm -ql chrony
    ```



## Завдання 2

### Цілісність упаковки

1. Можна завантажити або отримати пошкоджений або зіпсований файл. Перевірте цілісність пакета `wget`, який ви завантажили. Впишіть:

    ```
    $ rpm -K  wget-*.rpm
    wget-1.19.5-10.el8.x86_64.rpm: digests signatures OK
    ```

    Повідомлення «digests signatures OK» у вихідних даних показує, що з пакетом все гаразд.

2. Давайте проявити зловмисність і навмисно змінити завантажений пакет. Це можна зробити, додавши будь-що до оригінального пакету або вилучивши щось із нього.  Будь-що, що змінює пакунок у спосіб, не передбачений початковими пакувальниками, пошкодить пакунок. Ми змінимо файл за допомогою команди echo, щоб додати рядок «haha» до пакета. Впишіть:

    ```
    $ echo haha >> wget-1.19.5-10.el8.x86_64.rpm 
    ```

3. Тепер спробуйте знову перевірити цілісність пакета за допомогою параметра -K rpm.

    ```
    $ rpm -K  wget-*.rpm
    wget-1.19.5-10.el8.x86_64.rpm: DIGESTS SIGNATURES NOT OK
    ```

    Зараз це зовсім інше повідомлення. Вихід «DIGESTS SIGNATURES NOT OK» є чітким попередженням про те, що вам не слід намагатися використовувати або інсталювати пакет. Більше не варто довіряти.

4. Використовуйте команду `rm`, щоб видалити пошкоджений файл пакета `wget` і завантажити нову копію за допомогою `dnf`. Впишіть:

    ```
    $ rm wget-*.rpm  && dnf download wget
    ```

    Ще раз перевірте, чи нещодавно завантажений пакет пройшов перевірку цілісності RPM.

## Завдання 3

### Встановлення пакетів

Під час спроби інсталювати програмне забезпечення у вашій системі ви можете наштовхнутися на проблеми «невдалих залежностей». Це особливо часто трапляється під час використання низькорівневої утиліти RPM для керування програмами в системі вручну.

Наприклад, якщо ви спробуєте встановити пакет «abc.rpm», інсталятор RPM може скаржитися на деякі невдалі залежності. Він може сказати вам, що для пакета «abc.rpm» спочатку потрібно встановити інший пакет «xyz.rpm». Проблема залежностей виникає через те, що програми майже завжди залежать від іншого програмного забезпечення чи бібліотеки. Якщо необхідної програми або спільної бібліотеки ще немає в системі, перед установкою цільової програми потрібно виконати цю передумову.

Утиліта RPM низького рівня часто знає про взаємозалежності між програмами. Однак зазвичай він не знає, як і де отримати програму чи бібліотеку, необхідні для вирішення проблеми. Іншими словами, RPM знає *що* і *як*, але не має вбудованої можливості відповісти на запитання *де*. Саме тут сяють такі інструменти, як `dnf`, `yum` тощо.

#### Для встановлення пакетів

У цій вправі ви спробуєте встановити пакет `wget` (wget-*.rpm).

1. Спробуйте встановити програму `wget`. Використовуйте параметри командного рядка RPM -ivh. Впишіть:

    ```
    $ rpm -ivh wget-*.rpm
    error: Failed dependencies:
        libmetalink.so.3()(64bit) is needed by wget-*
    ```

    Відразу - проблема залежності! Зразок результату показує, що для `wget` потрібен якийсь файл бібліотеки під назвою "libmetalink.so.3"

    !!! note "Примітка"

     Відповідно до результатів тесту вище, для пакета wget-*.rpm потрібно встановити пакет libmetalink-*.rpm. Іншими словами, libmetalink є необхідною умовою для встановлення wget-*.rpm. Ви можете примусово встановити пакет wget-*.rpm за допомогою параметра «nodeps», якщо знаєте, що робите, але це, як правило, ПОГАНА практика.

2. RPM дав нам корисну підказку про те, чого не вистачає. Ви пам’ятаєте, що rpm знає, що і як, але не обов’язково знає, де. Давайте скористаємося утилітою `dnf`, щоб визначити назву пакета, який надає відсутню бібліотеку. Впишіть:

    ```
    $ dnf whatprovides libmetalink.so.3
    ...<TRUNCATED>...
    libmetalink-* : Metalink library written in C
    Repo        : baseos
    Matched from:
    Provide    : libmetalink.so.3
    ```

3. З результату нам потрібно завантажити пакет `libmetalink`, який містить відсутню бібліотеку. Зокрема, нам потрібна 64-розрядна версія бібліотеки. Викличмо окрему утиліту (`dnf`), щоб допомогти нам знайти та завантажити пакет для нашої демонстраційної 64-розрядної (x86_64) архітектури. Впишіть:

    ```
    dnf download --arch x86_64  libmetalink
    ```

4. Тепер у вашому робочому каталозі має бути принаймні 2 пакети rpm. Команда `ls` підтверджує це.

5. Встановіть відсутню залежність `libmetalink`. Впишіть:

    ```
    $ sudo rpm -ivh libmetalink-*.rpm
    ```

6. З установленою залежністю тепер ми можемо повернутися до нашої початкової мети встановлення пакета `wget`. Впишіть:

    ```
    $ sudo rpm -ivh wget-*.rpm
    ```

    !!! note "Примітка"

     RPM підтримує транзакції. У попередніх вправах ми могли виконати одну транзакцію rpm, яка включала оригінальний пакет, який ми хотіли встановити, і всі пакети та бібліотеки, від яких він залежав. Було б достатньо однієї такої команди, як наведена нижче:

            ```
            $  rpm -Uvh  wget-*.rpm  libmetalink-*.rpm
            ```

7. Момент істини зараз. Спробуйте запустити програму `wget` без будь-яких опцій, щоб перевірити, чи вона встановлена. Впишіть:

    ```
    $ wget
    ```

8. Давайте подивимось `wget` у дії. Використовуйте `wget`, щоб завантажити файл з Інтернету з командного рядка. Впишіть:

    ```
    wget  https://kernel.org
    ```

    Це завантажить стандартний index.html з веб-сайту kernel.org!

9. Використовуйте `rpm`, щоб переглянути список усіх файлів, які постачаються з програмою `wget`.

10. Використовуйте `rpm`, щоб переглянути будь-яку документацію, яка постачається разом із `wget`.

11. Використовуйте `rpm`, щоб переглянути список усіх двійкових файлів, які встановлюються з пакетом `wget`.

12. Вам потрібно було встановити пакет `libmetalink`, щоб встановити `wget`. Спробуйте запустити або виконати `libmetalink` з командного рядка. Впишіть:

    ```
    $ libmetalink
    -bash: libmetalink: command not found
    ```

    !!! attention "Увага"

     Що дає? Чому ви не можете запустити або виконати `libmetalink`?


#### Щоб імпортувати відкритий ключ через rpm

!!! tip "Підказка" 

    Ключі GPG, які використовуються для підпису пакетів, що використовуються в проекті Rocky Linux, можна отримати з різних джерел, таких як веб-сайт проекту, FTP-сайт, носії розповсюдження, локальні джерела тощо. На випадок, якщо відповідний ключ відсутній у вашій системі RL, ви можете використати опцію `--import` `rpm`, щоб імпортувати відкритий ключ Rocky Linux із вашої локальної системи RL, виконавши: `sudo rpm --import / etc/pki/rpm-gpg/RPM-GPG-KEY-rockyofficial`

!!! question "Питання"

    Під час встановлення пакетів, яка різниця між `rpm -Uvh` і `rpm -ivh`. 
    Зверніться до сторінки довідки щодо `rpm`.

## Завдання 4

### Видалення пакетів

Видалити пакунки так само легко, як і встановити за допомогою менеджера пакетів (RPM) Red Hat.

 У цій вправі ви спробуєте використати `rpm` для видалення деяких пакунків із системи.

#### Щоб видалити пакети

1. Видаліть пакет `libmetalink` із вашої системи. Впишіть:

    ```
    $ sudo rpm -e libmetalink
    ```

    !!! question "Питання"

     Поясніть, чому не вдалося видалити пакет?


2. Чистим і правильним способом видалення пакетів за допомогою RPM є видалення пакетів разом із їхніми залежностями. Щоб видалити пакет `libmetalink`, нам також доведеться видалити пакет `wget`, який залежить від нього. Впишіть:

    ```
    $ sudo rpm -e libmetalink wget
    ```

    !!! note "Примітка"

     Якщо ви хочете зламати пакунок, який покладається на libmetalink, і *примусово* видалити пакунок із вашої системи, ви можете використати параметр rpm `--nodeps` так: `$ sudo rpm -e --nodeps libmetalink`.
    
     **i.** Параметр “nodeps” означає відсутність залежностей. Тобто ігнорувати всі залежності.  
     **ii.** Наведене вище лише для того, щоб показати вам, як примусово видалити пакет із вашої системи. Іноді це потрібно робити, але зазвичай це *не дуже добре*.   
     **iii.** Примусове видалення пакета «xyz», на який покладається інший встановлений пакет «abc», фактично робить пакет «abc» непридатним для використання або дещо зламаним.

## Завдання 5

### DNF - менеджер пакетів

DNF — це менеджер пакетів для дистрибутивів Linux на основі RPM. Це наступник популярної утиліти YUM. DNF підтримує сумісність із YUM, і обидві утиліти мають дуже схожі параметри командного рядка та синтаксис.

DNF є одним із багатьох інструментів для керування програмним забезпеченням на основі RPM, таким як Rocky Linux. Порівняно з `rpm`, ці інструменти вищого рівня допомагають спростити встановлення, видалення та запити пакетів. Важливо зазначити, що ці інструменти використовують базову структуру, надану системою RPM. Ось чому корисно зрозуміти, як використовувати RPM.

DNF (та інші інструменти, подібні до нього) діє як обгортка навколо RPM і надає додаткові функції, яких не пропонує RPM. DNF знає, як працювати із залежностями пакетів і бібліотек, а також знає, як використовувати налаштовані репозиторії для автоматичного вирішення більшості проблем.

Загальні параметри, які використовуються з утилітою `dnf`:

```
    usage: dnf [options] COMMAND

    List of Main Commands:

    alias                     List or create command aliases
    autoremove                remove all unneeded packages that were originally installed as dependencies
    check                     check for problems in the packagedb
    check-update              check for available package upgrades
    clean                     remove cached data
    deplist                   [deprecated, use repoquery --deplist] List package's dependencies and what packages provide them
    distro-sync               synchronize installed packages to the latest available versions
    downgrade                 Downgrade a package
    group                     display, or use, the groups information
    help                      display a helpful usage message
    history                   display, or use, the transaction history
    info                      display details about a package or group of packages
    install                   install a package or packages on your system
    list                      list a package or groups of packages
    makecache                 generate the metadata cache
    mark                      mark or unmark installed packages as installed by user.
    module                    Interact with Modules.
    provides                  find what package provides the given value
    reinstall                 reinstall a package
    remove                    remove a package or packages from your system
    repolist                  display the configured software repositories
    repoquery                 search for packages matching keyword
    repository-packages       run commands on top of all packages in given repository
    search                    search package details for the given string
    shell                     run an interactive DNF shell
    swap                      run an interactive DNF mod for remove and install one spec
    updateinfo                display advisories about packages
    upgrade                   upgrade a package or packages on your system
    upgrade-minimal           upgrade, but only 'newest' package match which fixes a problem that affects your system

```

#### Щоб використовувати `dnf` для інсталяції пакета

Якщо припустити, що ви видалили утиліту `wget` із вправи, ми використаємо DNF для встановлення пакета в наступних кроках. Процес із 2-3 кроків, який нам був потрібний раніше, коли ми встановлювали `wget` через `rpm`, слід скоротити до одноетапного процесу за допомогою `dnf`. `dnf` тихо вирішить будь-які залежності.

1. Спочатку переконайтеся, що `wget` і `libmetalink` видалено з системи. Впишіть:

    ```
    $ sudo rpm -e wget libmetalink
    ```

    Після видалення, якщо ви спробуєте запустити `wget` з CLI, ви побачите повідомлення на зразок *wget: команда не знайдена*

2. Тепер використовуйте dnf для встановлення `wget`. Впишіть:

    ```
    $ sudo dnf -y install wget
    Dependencies resolved.
    ...<TRUNCATED>...
    Installed:
    libmetalink-*           wget-*
    Complete!
    ```

    !!! tip "Підказка"

     Параметр "-y", використаний у попередній команді, пригнічує підказку "[y/N]" для підтвердження дії, яку збирається виконати `dnf`. Це означає, що всі дії підтвердження (або інтерактивні відповіді) будуть «так» (y).


3. DNF надає параметр «Група середовища», який спрощує додавання нового набору функцій до системи. Щоб додати цю функцію, зазвичай потрібно інсталювати декілька пакунків окремо, але використовуючи `dnf`, все, що вам потрібно знати, це назва або опис потрібної функції. Використовуйте `dnf`, щоб відобразити список усіх доступних груп. Впишіть:

    ```
    $ dnf group list
    ```

4. Нас цікавить група/функція "Інструменти розробки". Давайте дізнаємося більше про цю групу. Впишіть:

    ```
   $ dnf group info "Development Tools"
   ```

5. Пізніше нам знадобляться деякі програми з групою «Засоби розробки». Встановіть групу «Інструменти розробки» за допомогою `dnf`, виконавши:

    ```
    $ sudo dnf -y group install "Development Tools"
    ```

#### Щоб використовувати `dnf` для видалення пакетів


1. Щоб використати `dnf` для видалення типу пакета `wget`:

    ```
    $ sudo dnf -y remove wget
    ```

2. Використовуйте `dnf`, щоб переконатися, що пакет видалено із системи. Впишіть:

    ```
    $ sudo dnf -y remove wget
    ```

3. Спробуйте використати/запустити `wget`. Впишіть:

    ```
    $ wget
    ```

#### Щоб використовувати `dnf` для оновлення пакета

DNF може перевірити та встановити останню версію окремих пакетів, доступних у сховищах. Його також можна використовувати для встановлення певних версій пакетів.

1. Використовуйте опцію списку з `dnf`, щоб переглянути всі доступні версії програми `wget`, доступні для вашої системи. Впишіть

    ```
    $ dnf list wget
    ```

2. Якщо ви хочете лише побачити, чи доступні оновлені версії для пакета, скористайтеся опцією перевірки оновлення з `dnf`. Наприклад, для типу пакета `wget`:

    ```
    $ dnf check-update wget
    ```

3. Тепер перерахуйте всі доступні версії пакета ядра для вашої системи. Впишіть:

    ```
    $ sudo dnf list kernel
    ```

4. Тепер перевірте, чи доступні оновлені пакети для встановленого пакета ядра. Впишіть:

    ```
    $ dnf  check-update kernel
    ```

5. Оновлення пакетів можуть відбуватися через виправлення помилок, нові функції або виправлення безпеки. Щоб переглянути, чи є якісь оновлення безпеки для пакета ядра, введіть:

    ```
    $ dnf  --security check-update kernel
    ```

#### Щоб використовувати `dnf` для оновлень системи

DNF можна використовувати для перевірки та встановлення останніх версій усіх пакетів, встановлених у системі. Періодична перевірка наявності оновлень є важливим аспектом адміністрування системи.

1. Щоб перевірити наявність оновлень для пакетів, які ви зараз інсталювали у своїй системі, введіть:

    ```
    $ dnf check-update
    ```

2. Щоб перевірити наявність оновлень безпеки для всіх пакетів, встановлених у вашій системі, введіть:

    ```
    $ dnf --security check-update
    ```

3. Щоб оновити всі пакети, встановлені у вашій системі, до найновіших версій, доступних для вашого дистрибутива:

    ```
    $ dnf -y check-update
    ```

## Завдання 6

### Створення програмного забезпечення з вихідного коду

Усе програмне забезпечення/програми/пакети походять із звичайних текстових файлів, які можна прочитати людиною. Файли спільно відомі як вихідний код. Пакети RPM, встановлені в дистрибутивах Linux, створюються з вихідного коду.

У цій вправі ви завантажите, скомпілюєте та встановите приклад програми з її вихідних файлів. Для зручності вихідні файли зазвичай поширюються як один стиснутий файл, який називається tar-ball (вимовляється як tar-dot-gee-zee).

Наступні вправи базуватимуться на відомому вихідному коді проекту Hello. `hello` — це проста програма командного рядка, написана мовою C++, яка лише друкує «hello» на терміналі. Ви можете дізнатися більше про [проект тут](http://www.gnu.org/software/hello/hello.html)

#### Щоб завантажити вихідний файл

1. Використовуйте `curl`, щоб завантажити останній вихідний код програми `hello`. Давайте завантажимо та збережемо файл у папці Downloads.

    https://ftp.gnu.org/gnu/hello/hello-2.12.tar.gz

#### Щоб розархівувати файл

1. Перейдіть до каталогу на вашій локальній машині, де ви завантажили вихідний код привітання.

2. Розпакуйте (розпакуйте) архів за допомогою програми `tar`. Впишіть:

    ```
    $ tar -xvzf hello-2.12.tar.gz
    hello-2.12/
    hello-2.12/NEWS
    hello-2.12/AUTHORS
    hello-2.12/hello.1
    hello-2.12/THANKS
    ...<TRUNCATED>...
    ```

3. Використовуйте команду `l`, щоб переглянути вміст вашого pwd.

    Новий каталог під назвою hello-2.12 повинен був бути створений для вас під час розпаковки.

4. Перейдіть до цього каталогу та перегляньте його вміст. Впишіть:

    ```
    $ cd hello-2.12 ; ls
    ```

5. Перегляд будь-яких спеціальних інструкцій зі встановлення, які можуть постачатися з вихідним кодом, завжди є хорошою практикою. Ці файли зазвичай мають такі назви: INSTALL, README тощо.

    Використовуйте пейджер, щоб відкрити файл INSTALL і прочитати його. Впишіть:

    ```
    $ less INSTALL
    ```

    Вийдіть із пейджера, коли завершите перегляд файлу.

#### Щоб налаштувати пакет

Більшість програм мають функції, які користувач може ввімкнути або вимкнути. Це одна з переваг доступу до вихідного коду та встановлення з нього. Ви можете контролювати настроювані функції програми; це на відміну від прийняття всього, що встановлює менеджер пакетів із попередньо скомпільованих двійкових файлів.

Сценарій, який зазвичай дозволяє налаштувати програмне забезпечення, зазвичай має влучну назву «configure»

1. Скористайтеся командою `ls` знову, щоб переконатися, що у вашому pwd справді є файл під назвою *configure*.

2. Щоб побачити всі параметри, ви можете ввімкнути або вимкнути тип програми `hello`:

    ```
    $ ./configure --help
    ```

    !!! question "Питання"

     З результатів команди, що робить параметр «--prefix»?

3. Якщо ви задоволені стандартними параметрами, які пропонує сценарій налаштування. Впишіть:

    ```
    $ ./configure
    ```

    !!! note "Примітка"

     Сподіваюся, етап налаштування пройшов гладко, і ви можете перейти до етапу компіляції.

    Якщо у вас виникли помилки під час етапу налаштування, вам слід уважно переглянути кінцеву частину виводу, щоб побачити джерело помилки. Помилки *іноді* зрозумілі самі за себе, і їх легко виправити. Наприклад, ви можете побачити таку помилку:

    configure: error: no acceptable C compiler found in $PATH

    Наведена вище помилка означає, що у вас не встановлено компілятор C (наприклад, gcc) у системі або компілятор інстальовано десь не у вашій змінній PATH.

#### Скомпілювати пакет

Ви створите програму hello, виконавши наступні кроки. Ось де деякі програми, які ви встановили за допомогою групи Development Tools за допомогою DNF, стають у нагоді.

1. Використовуйте команду make, щоб скомпілювати пакет після запуску сценарію “configure”. Впишіть:

    ```
    $ make
    ...<OUTPUT TRUNCATED>...
    gcc  -g -O2   -o hello src/hello.o  ./lib/libhello.a
    make[2]: Leaving directory '/home/rocky/hello-2.12'
    make[1]: Leaving directory '/home/rocky/hello-2.12'
    ```

    Якщо все піде добре, цей важливий крок `make` допоможе створити остаточний двійковий файл програми `hello`.

2. Знову перерахуйте файли у вашому поточному робочому каталозі. Ви повинні побачити кілька новостворених файлів, включаючи програму `hello`.

#### Щоб встановити програму

Серед інших службових завдань, останній крок встановлення також передбачає копіювання будь-яких двійкових файлів програми та бібліотек у відповідні папки.

1. Щоб інсталювати програму hello, виконайте команду make install. Впишіть:

    ```
    $ sudo make install
    ```

    Це встановить пакет у місце, указане аргументом префікса за замовчуванням (--prefix), який раніше використовувався у сценарії «configure». Якщо не встановлено жодного --prefix, буде використано стандартний префікс `/usr/local/`.

#### Щоб запустити програму привітання

1. Використовуйте команду `whereis`, щоб побачити, де у вашій системі знаходиться програма `hello`. Впишіть:

    ```
    $ whereis hello
    ```

2. Спробуйте запустити програму `hello`, щоб побачити, що вона робить. Впишіть:

    ```
    $ hello
    ```

3. Знову запустіть `hello` з опцією `--help`, щоб побачити інші речі, які він може робити.

4. Використовуючи `sudo`, знову запустіть `hello` як суперкористувач. Впишіть:

    ```
    $ sudo hello
    ```

    !!! tip "Підказка"

     Рекомендується тестувати програму як звичайний користувач, щоб переконатися, що звичайні користувачі справді можуть користуватися програмою. Дозволи для двійкового файлу можуть бути встановлені неправильно, тому лише суперкористувач може використовувати програми. Звичайно, це передбачає, що ви дійсно хочете, щоб звичайні користувачі могли використовувати програму.

5. Це все. Цю лабораторну роботу ви закінчили.

## Завдання 7

### Перевірка цілісності файлу після встановлення пакета

Після встановлення відповідних пакетів у деяких випадках нам потрібно визначити, чи були пов’язані файли змінені, щоб запобігти зловмисним змінам іншими.

#### Перевірка файлу

Використання параметра "-V" команди `rpm`.

Візьмемо програму синхронізації часу chrony як приклад, щоб проілюструвати значення її виводу. Передбачається, що ви встановили chrony і змінили файл конфігурації (/etc/chrony.conf)

```
$ rpm -V chrony
S.5....T.  c /etc/chrony.conf
```

* **S.5....T.**: Позначає 9 корисних відомостей у вмісті файлу перевірки, а незмінені відомості позначаються знаком «.». Ці 9 корисних відомостей:
  * S: Чи було змінено розмір файлу.
  * M: Чи було змінено тип файлу або дозволи на файл (rwx).
  * 5: Чи змінено контрольну суму файлу MD5.
  * D: Чи було змінено номер пристрою.
  * L: Чи було змінено шлях до файлу.
  * U: Чи було змінено власника файлу.
  * G: Чи було змінено групу, до якої належить файл.
  * T: Чи було змінено mTime (час зміни) файлу.
  * P: Чи була змінена функція програми.

* **c**: Вказує на зміни у файлі конфігурації. Це також можуть бути такі значення:
  * d: файл документації.
  * g: файл-привид. Дуже мало можна побачити.
  * l: файл ліцензії.
  * r: файл readme.

* **/etc/chrony.conf**: Представляє шлях до зміненого файлу.
