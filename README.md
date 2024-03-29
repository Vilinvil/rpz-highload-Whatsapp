**Ильинский Владислав**

[Телеграм для связи](https://t.me/Vilin0)

[Методические указания](https://github.com/init/highload/blob/main/homework_architecture.md)

## 1. Тема, аудитория, функционал

### Тема: мессенджер Whatsapp

### MVP функционал

1. Отправка сообщений в чате.
2. Просмотр чата.
3. Вложения фото, видео, файлы, голосовые.
4. Поиск сообщений по чатам.
5. Группировка чатов по папкам.
6. Групповой чат с несколькими пользователями.
7. Статус сообщения(отправлено, доставлено, прочитано)

**Ключевые особенности**

* Эмоции на сообщения в виде смайликов.

* Голосовые звонки внутри мессенджера

* Проектировать будем с учетом отсутствия постоянного хранения сообщений на серверах.
Т.е. сообщения могут храниться только пока адресат не доступен. 
После доставки сообщения удаляются с серверов.
Можно хранить метаданные о статусе доставке, времени отправки, получения и т.д.

### Исходные данные для расчетов

| Метрика                                        |   Значение   |
|------------------------------------------------|:------------:|
| Аудитория                                      |   Весь мир   |
| MAU                                            |   482  млн   |
| DAU                                            |   193 млн    |
| Сообщений в день                               |  23100 млн   |
| Голосовых сообщений в день                     |   1400 млн   |
| Голосовые звонки минут в день                  |   200 млн    |
| Реакций на сообщения в день                    |  1930 млн*   |
| Сообщения с вложениями фото в день             |   320 млн*   |
| Сообщения с вложениями видео в день            |   150 млн*   |
| Сообщения с вложениями остальных файлов в день |   30 млн*    |
| Запросов на получение новых сообщений в день   |   4825 млн   |
| Скачиваний за 2023 год                         |    80 млн    |
| Средний размер текстового сообщения            | 1/1024 Мбайт | 
| Средний размер 1 мин голосового сообщения      |   1 Мбайт    | 
| Средний размер фото                            |   4 Мбайт*   | 
| Средний размер видео                           |  200 Мбайт*  | 
| Средний размер остальных файлов                |  10 Мбайт*   | 
| Средний размер 1 минуты звонка                 |  0.72 Мбайт  | 
| Средний размер профиля                         |  0.6 Мбайт   |
| Средняя продолжительность голосового сообщения |    1 мин     | 
| Средний размер голосового звонка               |    10 мин    | 
| Коэффициент пиковых нагрузок                   |     1.5      |
| Коэффициент запаса                             |      2*      | 

"*" - обозначены предполагаемые величины

Т.к. распределение по количеству пользователей довольно равномерное по странам [4] и регионам [6]
не будем концентрироваться на определенный регион.

## 2. Расчет нагрузки
### Результаты расчетов

| Метрика                                  |   RPS   | Пиковое RPS | Пиковый RPS * коэф. запаса | Трафик, Гб/с | Трафик * коэф. запаса, Гб/с |
|------------------------------------------|:-------:|:-----------:|:--------------------------:|:------------:|:---------------------------:|
| Отправка текстовых сообщений             | 267 361 |   401 041   |          802 082           |      2       |              4              | 
| Отправка голосовых сообщений             | 16 204  |   24 306    |           48 612           |     1335     |            2 670            | 
| Запросы поставить реакцию                |  22338  |   33 507    |           67 014           |   0.00067    |        не учитываем         | 
| Отправка  сообщений с фото               |  3 704  |    5 556    |           11 112           |     116      |             232             | 
| Отправка  сообщений с видео              |  1 736  |    2 604    |           5 208            |     2712     |            5 424            | 
| Отправка  сообщений с остальными файлами |   347   |     520     |           1 040            |      27      |             54              | 
| Запросы на создание голосового звонка    |   231   |     346     |            692             |      13      |             26              | 
| Запросов на получение новых сообщений    | 55 844  |   83 767    |          167 534           | не учитываем |        не учитываем         | 
| Запросы на регистрацию                   |   60    |     90      |            180             |     0.3      |             0.6             | 

| Метрика хранилища                        |      Значение       | Значение * коэф запаса |
|------------------------------------------|:-------------------:|:----------------------:|
| Трафик переходящий в хранилище в день    |     9 040 Тбайт     |         18 080         |
| В общем хранилище за год                 |     3 222 Пбайт     |      6 444 Пбайт       |
| Рост хранилища из-за новых пользователей | 12.25 Пбайт   в год |   24.5 Пбайт   в год   |

### Рассчитаем RPS
Я взял monthly active users(MAU) и количество сообщений в день
Whatsapp за 2022 с сайта [1] и поделил их на 5.

Daily active users(DAU) я взял с учетом сайтов [2] и [3]
приблизительно как 40% от MAU и поделил также на 5.

**RPS запросов на отправку текстовых сообщений**: 23100 млн в день (24 ч * 60 мин * 60 с) =
267361 запросов/сек


Количество голосовых сообщений в день взято с официального поста whatsapp и поделено на 5 [5]

**RPS запросов на отправку голосовых сообщений**: 1400 млн в день / (24 ч * 60 мин * 60 с) =
16204 запросов/сек

**RPS запросов на поставку реакций**: 1930 млн в день / (24 ч * 60 мин * 60 с) = 
22338 запросов/сек

Только 2% сообщений используют вложения [8].

**RPS запросов на отправку сообщений с фото**: 320 млн в день /
(24 д * 60 мин * 60 с) = 3704 запросов/сек

**RPS запросов на отправку сообщений с видео**: 150 млн в день /
(24 д * 60 мин * 60 с) = 1736 запросов/сек

**RPS запросов на отправку сообщений с остальными**: 30 млн в день /
(24 д * 60 мин * 60 с) = 347 запросов/сек

Количество голосовых и видео звонков есть здесь [6]. Но т.к. там упоминается и видео,
и голосовых звонков поделим на 2, и поделил также на 5. 
Примем среднее время звонка 10 мин согласно [11].
Тогда кол-во запросов на создание звонка: 200 млн мин в день \ 10 мин = 20 млн в день

**RPS запросов на создание звонка** = 20 млн в день / (24 ч * 60 мин * 60 с) = 231 запрос в секунду

25 заходов во все мессенджеры в день в среднем на одного человека [14].
Тогда DAU * 25 = 193 млн * 25 = 4825 млн в день

**RPS запросов на получение новых сообщений**: 4825 млн в день / (24ч * 60м * 60с) =
55845 запросов/сек

Количество скачиваний взял за 2023 год и поделил на 5 [7]. Количество регистраций примем равным
количеству скачиваний. Авторизации рассчитывать не будем, т.к. в WhatsApp авторизация постоянна.

**RPS запросов на регистрацию**: 80 млн в год / (365 д * 24ч * 60 мин * 60 с) = 3 запроса/сек 
25% всех сообщений отправляется в промежуток с 12ч до 16ч

23100 млн * 0.25 / (4ч * 60м *60с) = 401042 запросов/сек

Примерно равномерно 75% всех сообщений отправлены с 12 по 24ч
Около 18% всех отправлено сообщений с 8ч до 12ч см. рисунок 1. [8], [9]

**Коэффициент пиковых нагрузок** = пиковое RPS / среднее RPS = 401042 / 267361 = 1.5

### Расчет трафика

Примерно 86% всех сообщений до 10 слов длинной [8].
1 слово в английской в среднем состоит из 5 символов [12].
4 байта максимально занимает символ в UTF8

Пусть средний размер текстового сообщения равным:
10 слов * 5 букв * 4 байта = 200 Байт

С учетом необходимости метаданных: времени отправки, статуса, id-ков и т.д.
примем размер одного сообщения в 1 Кбайт

**Трафик текстовых сообщений в день**: 267 361 запросов/сек * 1 Кбайт * 8 / (1024^2) =
2 Гб\с

Пусть средний размер голосового звонка 1 минута
Примем средний размер голосового в 1 мин с битрейтом 128 Кбит\с в 1 Мбайт.

**Трафик сообщений с голосовыми в день**: 1.4 млрд в день * 1 Мбайт = 1335 Тбайт в день

Пусть одна реакция это константа 4 байт.
**Тогда трафик реакций в день**: 22 338 RPS * 4 байт * 8 / (1024^3) = 0.00067 Гб/с

Особо данных по средним размерам вложений нет. 
Примем средний размер вложения фото 4 Мбайт, видео 200 Мбайт(10 мин 1080p),
остальных файлов 10 мб.

**Трафик сообщений с фото в день**: 3704 RPS * 4 Мбайт * 8 / 1024 =
116 Гб/с

**Трафик сообщений с видео в день**: 1736 RPS  * 200 Мбайт * 8 / 1024 =
2712 Гб/с 

**Трафик сообщений с остальными файлами в день**: 347 RPS * 10 Мбайт * 8 / 1024 =
27 Гб/с

Средний размер минуты голосового звонка 0.72 Мбайт [13].
**Трафик голосовых звонков в день**: 200 млн в день / (24 ч * 60м * 60с) * 0.72 Мбайт
* 8 / 1024 = 13 Гб/с

Запросы на получение не учитываем в трафике, т.к. основная там трафик, это сами сообщения, а
их учли выше.

Пусть аватарка весит 0.5 Мбайта.

Пусть при регистрации загружается аватарка, номер телефона и имя. 
Номер телефона состоит из 12 символов и весит в UTF8 12 байт. 

Пусть имя состоит из 30 символов в среднем и весит в UTF8 30*4 байт = 120 байт.

С учетом необходимости метаданных примем размер профиля 0.6 Мбайт.

**Трафик регистраций**: 60 RPS * 0.6 Мбайт * 8 / 1024 = 0.3 Гб/с

**Суммарный трафик**: 2 Гб\с + 116 Гб/с + 2712 Гб/с + 27 Гб/с + 13 Гб/с + 0.3 Гб/с =
2870.3 Гб/с

### Расчет хранилища

С учетом того, что примерно 32% всех сообщений прочитывается за минуту.
50% сообщений прочитывается за час [8]. Будем считать, что хранить нам необходимо
только 30% общего объема в день. Оседать будет весь трафик кроме регистраций и голосовых звонков.
Хранить будем на протяжении 1 года и только непрочитанные сообщения согласно MVP.

Трафик переходящий в хранилище: (2 Гб\с + 116 Гб/с + 2712 Гб/с + 27 Гб/с) * 0.3 * 
(24ч * 60м * 60с) / (8 * 1024)= 9040 Тбайт ежедневно 

**В общем хранилище**:  9040 Тбайт в день * 365 д = 3222 Пбайт

**Рост хранилища из-за новых пользователей**: Дневной сумарный трафик / DAU *
Кол-во регистраций в год = 2870.3 Гб/с * (24ч * 60м * 60с) / 193 млн в день * 80 млн в год /
(8 * 1024)= 12.25 Пбайт в год 

## 3. Глобальная балансировка 

### Распределение дата центров

| Регион           |       Расположение датацентров        | 
|------------------|:-------------------------------------:|
| Азия             | Нью-Делли(Индия), Джакарта(Индонезия) |
| Европа           |    Лулео(Швеция), Стамбул(Турция)     | 
| Северная Америка |    Темпл(США, Техас), Калифорния)     | 
| Южная Америка    |          Сан-Паулу(Бразилия)          | 
| Африка           |            Лагос(Нигерия)             | 


| Регионы и страны | Процент от общего числа MAU | MAU         |
|------------------|-----------------------------|-------------|
| Asia             | 47.9 %                      | 212 360 000 | 
| Europe           | 19.4 %                      | 86 120 000  | 
| Africa           | 12.6 %                      | 55 920 000  | 
| South America    | 11.2 %                      | 49 520 000  | 
| North America    | 8.9 %                       | 39 280 000  | 
| India            | 24.2 %                      | 107 160 000 | 
| Brazil           | 6.3 %                       | 27 860 000  | 
| United States    | 4.1 %                       | 18 260 000  | 
| Indonesia        | 3.9 %                       | 17 380 000  | 
| Mexico           | 3.1 %                       | 13 940 000  | 
| Russia           | 3 %                         | 13 340 000  | 

![World_MAU.jpg](World_MAU.jpg)
Рисунок 1. Расположение дата центров на глобальной карте MAU

В таблице ниже можно увидеть распределение аудитории по условным регионам и 6 стран топа по MAU,
основанные на данных [4], посчитал в экселе [10].
Выбирал ориентируясь на расположение основного количества пользователей, 
карту морских кабелей [15] и карту расположения дата центров [16] и дата центров Meta*** в частности [17].

Взял 2 дата центра в Азии, ввиду географической протяженности, большого количества аудитории и 
из соображений отказоустойчивости. В Европе и Северной Америке возьмем тоже по 2 дата центра
из-за географической протяженности, отказоустойчивости и более высоким требованиям к скорости
работы приложения у аудитории в развитых странах. В Южной Америке возьмем 1 дата центр в Бразилии,
т.к. в ней находится 6.3% MAU 27 млн пользователей. В Африке также поставим 1 дата центр, т.к. в общем 
регион вносит существенный вклад в MAU 12.6% 55.9 млн. 

**Азия:**
* Один в Нью-Делии(Индия) как в первой по численности MAU стране.
* Один в Джакарте(Индонезия) тоже страна с существенным вкладом в MAU и для лучшего покрытия
Тихоокеанского региона(Япония, Южная корея, Филипины ...)

**Европа:**
* Один дата центр в Швеции город Лулео(Швеция), т.к. там расположен дата центр Facebook. Расстояние до
Стокгольма порядка 1т км, что будет приводить к примерно 10мс дополнительной задержке, но общее время
до остальных стран, входящий в покрытие этого дата центра приемлемо см. рисунок 2 

![RTT_Stockholm.png](RTT_Stockholm.png)

Рисунок 2. Пинг из Стокгольма в другие города.

* Второй дата центр решил расположить в Стамбуле(Турция) для дополнительного покрытия
северных регионов Африки и азиатских стран на Аравийском полуострове(Саудовская Аравия, Иран, Израиль...)
Пинг для стран покрывающих этим дата центров достаточный см. рисунок 3.

![RTT_Istanbul.png](RTT_Istanbul.png)

Рисунок 3. Пинг из Стамбула в другие города.

 **Северная Америка:**
* Один дата центр в Темпл(США, Техас), т.к. там расположен в реальной жизни дата центр Meta** и
будет покрывать Мексику с существенной долей MAU 3.1% 14млн.
*  Второй расположим в Сан-Хосе(США, Калифорния) т.к. там есть центр расположения большого
кол-ва дата центров и для лучшего покрытия западной части США.


**Южная Америка:**
* Расположим дата центр в Сан-Паулу(Бразилия), т.к. там расположено скопление дата центров.

**Африка:**
* Расположим дата центр в Лагосе (Нигерия), т.к. там расположены дата центры и для примерно
центрального расположения на континенте.

### Технологии
1. Использовать будем Latency based DNS на уровне крупных регионов по типу континентов. 
Т.к. в общем случае метрика задержки более объективна чем географическая близость в Geo based DNS. 
2. Будем использовать BGP Anycast внутри регионов для более точной настройки распределения
между дц нагрузки и возможности переадресовать пользователей в случае инцидентов из одного дц в другой.

## 4. Локальная балансировка
Для локальной балансировки будем использовать L7 уровень из-за более
гибкой возможности настройки(выделения функциональных групп бекендов,
более равномерного распределения нагрузки, обработки медленных клиентов).

Решено было использовать кластер легковесных серверов реализующих
логику балансировки на application сервера, ssl termination, кеширование, 
архивирование контента. Это решение было выбрано в пользу sidecar серверов
для сокращения оверхеда на создание коннектов до application серверов, и
поддержании их с постоянном разогнанном состоянии. Также в таком случае
из-за уменьшения кол-ва ip доступных из вне, лучше будет кешироваться ssl
session, т.к. с большей вероятностью клиент попадет на нужный ip, и реже 
будет оверхед на ssl handshake. Использование кластера балансинга также 
позволит уменьшить downtime приложения в случаях выкатки новых версий,
падениях, зависаний бекендов благодаря более гибкой настройки механизмов 
балансировки.

На уровне балансировки между машинами в рамках кластера легковесных серверов
будем исползовать BGP Anycast.

Для балансировки межсервисного трафика будем использовать еще один кластер серверов 
для большей изолированности внутренней части системы.

## 5. и 6. Логическая и физическая схема базы данных
[Посмотреть диаграмму](https://drive.google.com/file/d/1x2OSXfwlojPMlo15EMaVncDSHqLiM9o8/view?usp=sharing)

Cassandra как основное хранилище на бекенде, ввиду встроенной возможности шардирования и высокой производительности.
Redis для хранения сессия как быстрое in-memory хранилище. 
Amazon S3 для хранения файлов. 
Kafka для межсервисного взаимодействия и хранения очереди сообщений на удаление из базы данных на бекенде.

Ввиду специфики отсутствия постоянного хранения используем SqlLite для хранения на клиентах.

Бекенд:
* вторичный индекс на id для возможности быстрее удалять сообщения после их прочтения.

Больше индексов не будет, т.к. имеем первичные ключи, в соответствии с которыми будут отсортированы данные в касандре, также по этим полям будет шардироваться бд.

В клиентской базе данных будут индексы: 
* message: chat_id + created_at для возможности получать всех новых сообщений одного чата.
* message: value полнотекстовый индекс для поиска по сообщениям.
* reaction: chat_id + created_at индекс для поиска всех новых реакций в одном чате.
* call_history: created_at индекс для получения в отсортированном виде истории звонков.
* photo_binary, video_binary, file_binary, voice_binary: url индексы для возможности быстрого получения файла по ключу. 

![designations](designations.png)

![schema_backend](schema_backend.png)

![schema_client](schema_client.png)

## Список литературы

1. Общая статистика Whatsapp https://www.bankmycell.com/blog/number-of-whatsapp-users/#1613581803631-96bfa-c1678a7f-e18553c4-2e8a2161-0689e002-e6d075e8-7b5a
2. Частота использования ежедневно в США https://www.statista.com/statistics/814813/frequency-with-which-us-internet-users-visit-whatsapp/
3. Количество ежедневных пользователей https://www.statista.com/statistics/730306/whatsapp-status-dau/
4. Распределение MAU по странам https://worldpopulationreview.com/country-rankings/whatsapp-users-by-country
5. Официальный пост Whatsapp о количестве голосовых сообщений https://blog.whatsapp.com/making-voice-messages-better
6. Количество голосовых звонков https://dataprot.net/statistics/whatsapp-statistics/#:~:text=3.-,Over%202%20billion%20minutes%20of%20voice%20and,are%20sent%20daily%20via%20WhatsApp.&text=There's%20more%20to%20WhatsApp%20than,the%20latest%20WhatsApp%20personal%20statistics.
7. Статистика по скачиваниям https://www.businessofapps.com/data/whatsapp-statistics/
8. Исследование 4 млн сообщений от 100 людей 2016г https://www.researchgate.net/publication/299487660_WhatsApp_Usage_Patterns_and_Prediction_Models
9. Исследование с 6 млн сообщений и 100 участников 2018г https://www.researchgate.net/publication/327918943_WhatsApp_usage_patterns_and_prediction_of_demographic_characteristics_without_access_to_message_content
10. Расчеты MAU по условным регионам https://docs.google.com/spreadsheets/d/1bwwJy5Y3Objel3J5x4IXkPFhqaDwpeiLdYi_NVZZPw0/edit?usp=sharing
11. Среднее кол-во времени на один голосовой звонок https://www.wharftt.com/how-long-can-a-whatsapp-call-last/
12. Исследование английских слов https://norvig.com/mayzner.html
13. Калькулятор голосовых звонков WhatsApp https://3roam.com/whatsapp-data-and-bandwidth-usage-calculator/
14. Среднее кол-во заходов в мессенджеры в день https://www.tadviser.ru/index.php/%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D1%8F:%D0%9C%D0%B5%D1%81%D1%81%D0%B5%D0%BD%D0%B4%D0%B6%D0%B5%D1%80%D1%8B_&#40;Instant_Messenger,_IM&#41;#:~:text=%D0%9A%D0%B0%D0%BA%20%D0%B2%D1%8B%D1%8F%D1%81%D0%BD%D0%B8%D0%BB%D0%BE%D1%81%D1%8C%2C%20%D0%B2%20%D1%81%D1%80%D0%B5%D0%B4%D0%BD%D0%B5%D0%BC%20%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8C,%D0%B8%D0%B4%D1%83%D1%82%20Viber%2C%20Telegram%20%D0%B8%20Skype.)
15. Карта морских кабелей https://www.submarinecablemap.com/
16. Карта дата центров https://www.datacentermap.com/
17. Карта дата центров Meta** https://datacenters.atmeta.com/


** Meta - организация, деятельность которой запрещена на территории Российской Федерации
