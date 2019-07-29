
<!-- README.md is generated from README.Rmd. Please edit that file -->

# ryandexdirect - пакет для работы с API Яндекс.Директ версии 4, Live 4 и 5. <a href='https:/selesnow.github.io/ryandexdirect'><img src='https://raw.githubusercontent.com/selesnow/ryandexdirect/master/inst/ryandexdirect.png' align="right" height="139" /></a>

## Краткое описание.

Пакет ryandexdirect предназначен для загрузки данных из Яндекс Директ в R, с помощью функций данного пакета вы можете работать с перечисленными ниже сервисами и службами API Яндекса с помощью готовых функций, не углубляясь при этом в документацию по работе с этими API сервисами.

+ [Сервис Reports](https://tech.yandex.ru/direct/doc/reports/reports-docpage/) - Предназначен для получения статистики по аккаунту рекламодателя.
+ [API Директа версии 4 и Live 4](https://tech.yandex.ru/direct/doc/dg-v4/concepts/About-docpage/) - Через API внешние приложения добавляют и редактируют кампании, объявления, фразы, задают ставки, получают статистику показов.
+ [API Директа версии 5](https://tech.yandex.ru/direct/doc/dg/concepts/about-docpage/) - Через API внешние приложения добавляют и редактируют кампании, объявления, фразы, задают ставки, получают статистику показов.

Пакет позволяет вам выполнять следующие действия:

1. Авторизовываться в API.
2. Получать список различных объектов рекламного кабинета, рекламных кампаний, групп объявлений, объявлений, для агентских аккаунтов можно запрашивать список клиентов, и параметры каждого клиента.
3. Управлять показами на уровне рекламных кампаний, групп объявлений и объявлений.
4. Загружать статистику.
5. Упаравлять ставками ключевых слов и автотаргетингов.

## Установка пакета ryandexdirect.

Установка пакета осуществляется из репозитория CRAN 

`install.packages("ryandexdirect")`

или GitHub, для этого сначала требуется установить и подключить пакет devtools.

`install.packages("devtools")`

`library(devtools)`

После чего можно устанавливать пакет ryandexdirect.

`install_github('selesnow/ryandexdirect')`

### Пример кода
```r
# Установка пакетов
install.packages("ryandexdirect")

# Подключение пакета
library(ryandexdirect)

# Авторизация 
yadirAuth(Login     = "my_yandex_login",  # логин пользователя Яндекс.Директ
          TokenPath = "C:/direct/tokens") # путь к папаке в которой будет храниться файл с учётными данными
	  
# ###################################################
# Загрузка различных объектов из рекламного аккаунта
# ###################################################

# Список рекламных кампаний
camp <- yadirGetCampaign(Logins    = "my_yandex_login",
                         TokenPath = "C:/direct/tokens",
                         States    = "ON",
                         Types     = "TEXT_CAMPAIGN")

# Список ключевых слов
kw <- yadirGetKeyWords(Login       = "my_yandex_login",
                       TokenPath   = "C:/direct/tokens",
                       CampaignIds = camp$Id[1:5],
                       States      = "ON")

# Список групп объявлений
adgroups <- yadirGetAdGroups(Login       = "my_yandex_login",
                             TokenPath   = "C:/direct/tokens",
                             CampaignIds = camp$Id[c(1,2)],
                             Types       = "TEXT_AD_GROUP",
                             Statuses    = c("ACCEPTED", "MODERATION"))

# Список объявлений
ads <- yadirGetAds(Login       = "my_yandex_login",
                   TokenPath   = "C:/direct/tokens",
                   CampaignIds = camp$Id[c(1,2)])

# Список быстрых ссылок 
links <- yadirGetSiteLinks(Login       = "my_yandex_login",
                           TokenPath   = "C:/direct/tokens")

# ###################################################
# Загрузка справочной информации
# ###################################################

# Справочник валют
currency <- yadirGetDictionary(DictionaryName = "Currencies",
                               Language       = "en",
                               Login          = "my_yandex_login",
                               TokenPath      = "C:/direct/tokens")

# Георгафический справочник
regions <- yadirGetDictionary(DictionaryName = "GeoRegions",
                              Language       = "ru",
                              Login          = "my_yandex_login",
                              TokenPath      = "C:/direct/tokens")

# ###################################################
# Загрузка статистики
# ###################################################

# простейший отчёт за прошлый месяц
simple_report <- yadirGetReport(DateRangeType = "LAST_MONTH",  # относительный период
                                FieldNames    = c("Date", "Clicks", "Impressions"),
                                Login         = "my_yandex_login",
                                TokenPath     = "C:/direct/tokens")

# отчёт по конверсиям с моделью аттрибуции за статичный период
attribution_report <- yadirGetReport(DateFrom          = "2018-11-15", # статичный период, дата начала
                                     DateTo            = "2018-11-20", # статичный период, дата завершения
                                     FieldNames        = c("Date", 
                                                           "Conversions"),
                                     Goals             = c(27475434, 38234732),
                                     AttributionModels = c("LC", "FC"),
                                     Login             = "my_yandex_login",
                                     TokenPath         = "C:/direct/tokens")

# отчёт с применением фильтрации
filtring_report <- yadirGetReport(DateRangeType = "LAST_30_DAYS",
                                  FieldNames    = c("Date", "Clicks", "Impressions"),
                                  FilterList    = c("Conversions GREATER_THAN 1", 
                                                    "Impressions LESS_THAN 3500"),
                                  Login         = "my_yandex_login",
                                  TokenPath     = "C:/direct/tokens")

# ###################################################
# Управление ставками
# ###################################################

# запрашиваем ставки
bids  <- yadirGetKeyWordsBids(kw$Id)

# запрашиваем ставки и данные торго на поиске
bids_search <- yadirGetKeyWordsBids(kw$Id,
                                    AuctionBids = "search")
                                    
# запрашиваем ставки и данные торго в сетях
network_bids <- yadirGetKeyWordsBids(kw$Id,
                                     AuctionBids = "network")

# запрашиваем список рекламных кампаний
camp <- yadirGetCampaign()

# меняем ставки
setbid     <- yadirSetKeyWordsBids(CampaignIds = camp$Id[1],
                                   SearchBid   = 9)

# меняем ставки
autosetbids <- yadirSetAutoKeyWordsBids(KeywordIds = kw$Id,
                                        TargetTrafficVolume = 50)
```

### Виньетки
Пакет имеет подробные виньетки отдельно по каждой из своих возможностей, в которой наиболее подробно описаны детали работы с его функционалом. На данный момент в пакете `ryandexdirect` доступны 3 виньетки.

1. Загрзка статистики из API Яндекс Директ: `vignette("yandex-direct-get-statistic", package = "ryandexdirect")`
2. Авторизация и работа с учётными данными: `vignette("yandex-direct-auth", package = "ryandexdirect")`
3. Управление ставками ключевых слов в Яндекс Директ: `vignette("yandex-direct-keyword-bids", package = "ryandexdirect")`

### Ссылки
1. [Полная, официальная документация по работе с пакетом ryandexdirect](https://selesnow.github.io/ryandexdirect/).
2. Баг репорты, предложения по доработке и улучшению функционала ryandexdirect оставлять [тут](https://github.com/selesnow/ryandexdirect/issues). 
3. [Список релизов](https://github.com/selesnow/ryandexdirect/releases).
4. [Телеграмм канал R4marketing](https://t.me/R4marketing).
5. [Группа в Вконтакте](https://vk.com/data_club).

### Автор пакета
Алексей Селезнёв, Head of analytics dept. at [Netpeak](https://netpeak.net)
<Br>email: selesnow@gmail.com
<Br>skype: selesnow
<Br>facebook: [facebook.com/selesnow](https://facebook.com/selesnow)
<Br>blog: [alexeyseleznev.wordpress.com](https://alexeyseleznev.wordpress.com/)

### Бейджи
[![Rdoc](http://www.rdocumentation.org/badges/version/ryandexdirect)](http://www.rdocumentation.org/packages/ryandexdirect)
[![rpackages.io rank](http://www.rpackages.io/badge/ryandexdirect.svg)](http://www.rpackages.io/package/ryandexdirect)
[![](https://cranlogs.r-pkg.org/badges/ryandexdirect)](https://cran.r-project.org/package=ryandexdirect)