# Установка Ubuntu

Для работы и управления микрокомпьютерами Jetson NX и Nano, необходимо установить Ubuntu на хост машину, с которой непосредственно и будет производиться управление.

## Подготовка к установке

### Шаг 1. Загрузка образа
Скачать установочный образ Ubuntu (в нашем случае 22.04) можно с [официального сайта](https://releases.ubuntu.com/22.04/). Здесь доступен х64 образ для рабочего стола или сервера. В этой версии установочный образ занимает больше трех гигабайт.

### Шаг 2. Запись образа на флешку
Для записи на флешку в Linux можно использовать утилиту [Etcher](https://etcher.balena.io/). 
Здесь нужно выбрать образ, вашу флешку и нажать кнопку Flash.

> В Windows для записи образов лучше использовать программу [Rufus](https://rufus.ie/ru/).
> {style="note"}

## Установка Ubuntu

### Шаг 3. Загрузка

После завершения записи на флешку следует перезагрузить компьютер, 
открыть настройки BIOS и выбрать в качестве основного загрузочного устройства вашу флешку.
Или же воспользоваться загрузочными меню, которое открывается по нажатию клавиши **F8**. 
После загрузки с флешки отобразится меню Grub, 
в котором нужно выбрать первый пункт **Try or install Ubuntu**:

![GNU_GRAB.png](GNU_GRAB.png)

Далее дождитесь завершения загрузки системы:

![load_system.png](load_system.png)

### Шаг 4. Запуск установки

Когда система загрузится, в открывшемся окне необходимо выбрать язык системы, 
а затем нажать **Install Ubuntu** или **Установить Ubuntu**, если вы выбрали русский язык:

![ChooseLang.png](ChooseLang.png)

### Шаг 5. Раскладка клавиатуры

В следующем окне мастера нужно выбрать раскладку клавиатуры. 
Если вы выбрали русский язык, то по умолчанию вам будет предложена русская раскладка:

![keyboard.png](keyboard.png)

### Шаг 6. Настройка обновления

Дальше необходимо настроить способ установки и обновления. 
Вы можете выбрать минимальную установку, при которой из системы будет удалено большинство пакетов программ для рабочего стола или оставить обычную установку. 
Галочки **Загрузить обновления во время установки Ubuntu** и **Установить стороннее программное обеспечение...** лучше установить:

![update_system.png](update_system.png)

### Шаг 7. Способ разметки диска

Вы можете выбрать пункт **Стереть диск и установить Ubuntu** для того чтобы установщик автоматически создал все необходимые разделы или же создать нужные разделы вручную. 
Для этого надо выбрать пункт **Другой вариант**.

![disk.png](disk.png)

Мы рассмотрим как создавать разделы для Ubuntu вручную. 
Выберите **Другой вариант** и нажмите **Далее**.

## Разметка диска

### Шаг 8. Таблица разделов

Если у вас чистый диск и на него ещё ничего не было установлено нужно создать таблицу разделов. 
Для этого нажмите кнопку **Новая таблица разделов**:

![table_disk.png](table_disk.png)

> Вы можете пропустить этот шаг если у вас уже есть операционная система и вы хотите установить Ubuntu рядом с ней. 
При создании новой таблицы разделов все данные, которые уже есть на этом диске будут потеряны. 
Система предупредит об этом:
> 
> ![table_disk2.png](table_disk2.png)
> {style="note"}

### Шаг 9. Разделы для загрузчика

По умолчанию в Ubuntu используется таблица разделов GPT. 
В зависимости от конфигурации вашего компьютера разделы, необходимые для загрузчика могут отличаться. 
Для систем с UEFI нужен раздел EFI отформатированный в Fat32, куда будет установлен загрузчик. 
Если же в вашей системе не включен режим UEFI - необходимо создать раздел типа BIOS Boot размером около 1 Мб, на который будет записан загрузчик. 
Дальше мы рассмотрим как создать все эти разделы. 
Вы можете создать только нужные или создать их все.

Для создания раздела BIOS boot выберите **Свободное место** и кликните по кнопке **+**. 
В открывшемся окне выберите размер до 5 Мб, а в поле **Использовать как** выберите **Резервная загрузочная область BIOS**:

![rezerv_bios.png](rezerv_bios.png)

Затем нажмите **ОК**. 
Для создания EFI раздела аналогично выберите свободное место и кликните по кнопке **+**:

![EFI.png](EFI.png)

Далее необходимо выбрать размер раздела около 250-500 мегабайт и использовать как системный раздел EFI:

![EFI_size.png](EFI_size.png)

Кроме того, можно создать раздел, для хранения конфигурации загрузчика, его файлов и файлов ядра в системе. 
Для этого создайте раздел размером около 1000 мегабайт, с файловой системой **Ext2** или **Ext4** и точкой монтирования **/boot**:

![boot.png](boot.png)

### Шаг 10. Корневой раздел

После того, как были созданы разделы загрузчика можно перейти к созданию корневого раздела:

Аналогично, выберите свободное место и создайте раздел. 
Его минимальный размер должен быть не меньше 10 гигабайт, но лучше взять около 30 Гб для того чтобы точно хватило места на все программы. 
Файловую систему можно оставить **Ext4**, а точка монтирования - **/**:

![root.png](root.png)

### Шаг 11. Домашний раздел

Если вы хотите чтобы ваши личные файлы хранились не на корневом разделе и не терялись при переустановке системы необходимо создать домашний раздел. 
Для этого создайте новый раздел и выберите файловую систему **Ext4**, а точку монтирования - **/home**. 
Под раздел можно выделить всё оставшееся свободное место:

![home.png](home.png)

### Шаг 12. Завершение разметки

После того, как всё готово, остается применить изменения. 
Для этого нажмите кнопку **Установить сейчас**:

Система попросит подтвердить это действие. 
На этом этапе новая таблица разделов будет физически создана на диске, если это было выбрано ранее, а все разделы, для которых было включено форматирование будут отформатированы:

![install.png](install.png)

## Продолжение установки

### Шаг 13. Часовой пояс

Выберите свой часовой пояс для корректной синхронизации времени.

### Шаг 14. Создание пользователя

Здесь необходимо указать имя компьютера, а также имя пользователя и пароль от имени которого вы будете использовать систему.

### Шаг 15. Завершение установки

Дождитесь пока установка Ubuntu завершится.

### Шаг 16. Перезагрузка

Когда установка будет завершена установщик предложит вам перезагрузить систему. 
Согласитесь, а затем извлеките установочный носитель и нажмите Enter когда система предложит это сделать.

### Шаг 17. Вход

Дождитесь пока система перезагрузится, затем выберите пользователя, которого создали ранее и введите его пароль.

### Шаг 18. Сетевые учётные записи

Уже почти готово. 
Но система предлагает вам ещё настроить несколько моментов. 
На первом экране мастера вы можете настроить сетевые учётные записи. 
Например, если добавить сетевую учётную запись Google, то календарь в системе синхронизируется с Google календарем.

### Шаг 19. Отправка данных в Canonical

Выберите хотите ли вы, чтобы в Canonical отправлялась информация, которая поможет сделать систему лучше.

### Шаг 20. Местоположение

Выберите нужно ли включать службы определения местоположения.

### Шаг 21. Дополнительные приложения
На последнем шаге мастера вы можете установить некоторые популярные приложения с центра приложений Ubuntu.

> Рекомендуется пропусть данный шаг, поскольку все нужные пакеты будут установлены в следующей [главе](Настройка-Ubuntu-сервера.md)
> {style="note"}

