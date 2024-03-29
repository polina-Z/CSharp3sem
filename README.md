# Описание работы программы #

   Данная лабораторная работа представляет собой Windows-службу, которая мониторит FTP сервер клиента (в нашем случае папку **`SourceDirectory`**) и проверять: добавился ли файл или нет. Если какой-нибудь файл был добавлен в папку `SourceDirectory`, то прежде всего он будет перемещен по пути **`year/month/day`** и затем переименован в **`fileName_year_month_day_hour_minute_second`**. Потом этот файл исходя из конфигурационных файлов будет зашифрован/не зашифрован, заархивирован/ не заархивирован и перенесен в **`TargetDirectory`**, где в последующем (так же исходя из конфигов) будет разархивирован (если происходила архивация), расшифрован (если происходило шифрование), а затем добавлен/ не добавлен в общий архив (`archive.zip`). Все действия, произведенные над файлом в папке `SourceDirectory`, а также запуск и остановка службы записываются в файл **`LogFile.txt`**.
 
# Структура программы #
    Проект содержит следующие папки и классы:
+ Папка `Options`, содержит следующие классы:
  + Options
  + ArchiveOptions
+ Папка `FileOperations`, содержит следующие классы:
  +	ArchiveManager
  +	EncryptManager
+ Папка `Parsers`, содержит:
  +	класс ConfigurationManager
  +	интерфейс IParser
  +	класс ParserJson
  +	класс ParserXML
+ Классы:
  +	Logger
  +	Program
  +	Service1
  +	Installer1
  
    Также проект содержит файлы `XMLFile.xml`, `XMLFile.xsd`, `JsonFile.json`
    

# Особенности программы #
   Т.к. 2 лабораторная работа тоже являлась Windows-службой, стоит обратить внимание на отличия 3 лабораторной работы, по сравнению со второй. Во второй лабораторной работе мы разработали сервис для мониторинга директорий и манипуляции с файлами (архивирование, шифрование, передача). Но все конфигурируемые свойства, например, были захардкожены. В третьей же лабораторной работе мы реализовали переносимую/изменяемую систему конфигурации сервиса в формате `XML` и `Json`. Также был изменен класс **`Logger`**, который и предоставляет нам весь функционал службы. В него было добавлено больше различных функций работы с файлами (например, отслеживание переименования файла).
   
  Важным пунктом является то, что все `исключительные ситуации обрабатываются и логируются` в файл **`Exception.txt`**. При этом, если данного файлика у нас не было, то он создается, а если файл был создан ранее, то новые данные просто дописываются в конец файла.
   
  Для того, чтобы отобразить каждый логический блок конфигураций был разработан класс **`Options`**. Он содержит объект настроек, по которым работает служба. Настройки архивации были объединены в один блок и описываются классом **`ArchiveOptions`**. 
   
   Дополнительно были разработаны классы **`ArchiveManager`** и **`EncryptManager`**, которые описывают процесс архивации (разархивации) и шифрования (дешифррования) соответственно.
   
**`XMLFile.xml`** - файл, представляющий собой экземпляр класса Options, написанный в формате `XML`.

**`JsonFile.json`** - файл, представляющий собой экземпляр класса Options, написанный в формате `Json`.

**`XMLFile.xsd`** – файл, который содержит описание нашей схемы и с помощью которого происходит валидация файла `XMLFile.xml`.

 Классы **`ParserXML`** и **`ParserJson`** – содержат описание парсеров xml и json. Основным преимуществом является то, что эти парсеры `не используют встроенные парсеры C#` а также являются `универсальными`, т.е. `изменение файлов настроек` (добавление нового тэга/секции в xml или объекта/поля в json) `не повлечет за собой изменения парсера`. Каждый парсер наследуется от интерфейса `IParser`, который в свою очередь описывается одним полем (T GetOptions<T>()).
    
Класс **`ConfigurationManager`** – менеджер конфигураций, который при наличии определённого типа конфигурационного файла (XML/JSON) будет принимать решение о том, какой конфигурационный файл использовать. Менеджер конфигурации получает на вход путь к файлу (с использованием `AppDomain`). Если же подходящий файл не найден, выбрасывается исключение.
    
**`Service1.cs`** – файл службы. В классе Service1 описаны события `OnStart()`, `OnStop()`. В событии OnStart(), происходит процесс `валидации xml файла с помощью xsd файла`.
    
**`Installer1.cs`** – файл установки службы.

	



