# Документация по работе с REST API в каталоге товаров uCat

## Содержание

* Назначение
* Описание
* [Интерактивный справочник](https://nikalogic.github.io/ucat-sync-api-doc/)
* Принцип работы с REST API
  * Типы запросов к API
  * Коды ответов
  * Авторизация пользователей
* [Доступные запросы к API](#Доступные-запросы-к-api)
  * (1.) Получить список Поставщиков
  * (2.) Товарные позиции одного Поставщика
  * [(3.) Подробная информация о товарной позиции](#3-Подробная-информация-о-товарной-позиции)
  * [(4.) Изображения одного продукта](#4-Получить-информацию-только-о-фотографиях)
  * [(5.) Передать статус по товару](#5-Передать-статус-обработки-товара-торговой-сетью)
  * [(6.) Поиск товарной позиции по штрихкоду](#6-Поиск-товарной-позиции-по-штрихкоду)
  * [(7.) Импорт артикулов торговой сети в базу uCat](#7-Импорт-артикулов-торговой-сети-в-базу-ucat)
  * [(8.) Список изменившихся товаров](#8-Список-изменившихся-товаров)
  * [(9.) Список GTIN базовых единиц, на которые была оформлена подписка](#10-Список-доступных-gtin-базовых-единиц-на-которые-была-оформлена-подписка)
  * [(10.) Кол-во подписок на товары](#9-Количество-подписок)
  * [(11.) Получение информации из справочников](#11-Расшифровка-значения-справочника)
  * [(12.) Изображения одного товара](#12-Изображения-одного-товара)
  * [(13.) Информация об активности поставщика](#13-Информация-об-активности-поставщика)
  * [(14.) Информация об активности поставщиков](#14-Информация-об-активности-поставщиков)
  * [(15.) Информация о сертификатах](#15-Информация-о-сертификатах)
  * [(16.) Изображения одного товара по уникальному идентификатору (ucatID)](#16-Изображения-одного-товара-по-уникальному-идентификатору-ucatid)
* [Пример ответа по одному товару](https://github.com/xainse/ucat-sync-api-doc#Пример-ответа-по-одному-товару)


## Назначение
[REST](https://uk.wikipedia.org/wiki/REST) [API](https://uk.wikipedia.org/wiki/Прикладний_програмний_інтерфейс) в каталоге uCat предназначен для работы с системами партнеров автоматически с доступом через web. 
Данные при работе через REST API отдаются в формате JSON. Взаимодействие с REST API идет в синхронном режиме,на каждый запрос клиент получает ответ с результатом запроса.

## Описание
Для доступа к данным каталога uCat, через REST API партнеру выдается *ключ доступа*. Ключ обеспечивает идентификацию клиента и определяет уровень доступа к данным.
Для того, что-бы получить ключ, нужно обратиться к администрации каталога.
Ключ представляет собой символьную строку.

### Принцип работы с REST API
Что-бы получить информацию из каталога, нужно обратиться по определенной ссылке, с указанием параметров.
Например: Данный запрос выводит список производителей, зарегистрированных в каталоге. 
<pre>
https://client.ucat.com.ua/api-sync/v1/manufacturers?authKey=auth_key
</pre>

#### Пример ответа:
- код ответа сервера: 200 OK
- тело ответа:

```javascript
[
  {
    name: "- Тестовый Дистрибьютор -",
    OKPO: "",
    primaryGLN: "1000000000002"
  },
  {
    name: "- Тестовый Поставщик -",
    OKPO: "1000000000",
    primaryGLN: "1000000000011"
  },
  {
    name: ""Cantine Baroncini s.r.l."",
    OKPO: null,
    primaryGLN: "8001495000003"
  },
  {
    name: ""Groupe GVG"",
    OKPO: null,
    primaryGLN: "3014296700107"
  },
  {
    name: ""LES CHAIS MOLESAN"",
    OKPO: null,
    primaryGLN: "3012112000103"
  }
]
```

#### Описание частей запроса

Параметры | Описание
--------- | ---------
https:// | Защищенный протокол передачи данных
client.ucat.com.ua/ | текущий корень каталога uCat
api-sync/ | корень приложения, отвечающего за API
v1/ | версия REST API, в приделах одной версии синтаксис запросов при обращении существенно не меняется
manufacturers | запрос на получение списка производителей
authKey | ключ доступа

### Типы запросов к API
* GET - используется для получения информации;
* POST - используется для отправки информации в каталог;

### Коды ответов
При запросе к каталогу веб сервер в заголовке отдает [код состояния HTTP](https://ru.wikipedia.org/wiki/Список_кодов_состояния_HTTP), вместе с телом ответа.

Код | Значение
--- | --------
200 | Код возвращается при каждом удачном обращении к API
401 | Ошибка авторизации, неправильный ключ
403 | Доступ к информации закрыт (но она есть в каталоге)
404 | В каталоге не найденные данные по вашему запросу

### Авторизация пользователей
Авторизация пользователей проходит по хеш-ключу, который передается в GET параметре *authKey*
* хеш-ключ выдается администраторами каталога  uCat;
* параметр **authKey** должен передаваться в каждом запросе к REST API;
* при работе с REST API сесии не поддерживаются.  

### Доступные запросы к API
Параметры запросов
* **?authKey=<ключ_авторизации>** - Ключ добавляется к каждому запросу к API. Например https://client.ucat.com.ua/api-sync/v1/manufacturers?authKey=<ключ_авторизации>. Ключ выдается администратором.
* **GLN-номер компании** - GLN [Глобальный номер размещения](http://gs1.org.ua/uk/gs1/identificationbarcode/gln.csp), международный номер места производства выдается организацией GS1  
* **GTIN** - штрихкод товара. GTIN - Global Trade Item Number
* **api-sync/v1** - префикс версии API

Название | Тип | Формат | Комментарий
---------|-----|--------|------------
(1.) Список поставщиков | GET | /manufacturers | Работает без подписки. Выводить поставщиков зарегистрированных в каталоге
(2.) Продукты одного Поставщика | GET | /manufacturers/{GLN-номер компании}/products|Выводиться список всех опубликованных в каталоге товаров поставщика
(3.) Подробная информация о товарной позиции | GET | /manufacturers/{GLN-номер компании}/products/{идентификатор_товара}|Выдается информация о товаре и о его упаковках. Информация выдается в иерархическом виде. На верхнем уровне самая большая упаковка, на нижнем уровне сам товар.
(4.) Изображения одного продукта | GET | /manufacturers/{GLN-номер компании}/products/{GTIN}/images|Информация о доступных изображениях товара в разных разрешениях.
(5.) Передать статус по товару | POST | /response|Передать в каталог uCat статус полученного товара в системе клиента.
(6.) Поиск по штрихкоду |GET|/products/{идентификатор_товара}/search|Поиск по товарам, которые доступны для вас в каталоге
(7.) Импорт артикулов| POST|/upload-sku|Для синхронизации ассортимента, клиент может загрузить соответствие своих артикулов к штрихкодам (GTIN).
(8.) Список изменившихся товаров|GET|/last-changed-gtins|Получить список товаров, которые изменились с последнего получения информации по этим товарам
(9.) Список GTIN базовых единиц, на которые была оформлена подписка|GET|/get-subscribe-product-gtin|После оформлении подписки, клиент может проверять какие товары ему доступны по подписке
(10.) Кол-во подписок | GET|/get-count-subscribe-product|Запрос возвращает количество различных подписок
(11.) Получить значение кода справочника|GET|/handbook/search/{код справочника}|В ответе расшифровка значения и какой это справочник
(12.) Изображения одного товара|GET|/images/{GTIN}|Информация о доступных изображениях товара в разных разрешениях.
(13.) Информация об активности поставщика|GET|/manufacturer-activity|Запрос возвращает информацию про поставщика по ОКПО
(14.) Информация об активности поставщиков|GET|/manufacturers-activity|Запрос возвращает информацию про поставщиков с постраничной навигацией
(15.) Информация о сертификатах|GET|/products/<GTIN>/certificates|Запрос возвращает информацию о сертификатах продукта
(16.) Изображения одного товара по уникальному идентификатору (ucatID)|GET|/images-by-id/<ucatID>|Информация о доступных изображениях товара в разных разрешениях.

### 3. Подробная информация о товарной позиции

Запрос возвращает подробную информацию о товарной позиции. Информация возвращается в иерархическом виде, вместе с упаковками
```
Коробка
|
 -- Припак
    |
     -- Единица товара
```

#### Формат запроса
```html
https://client.ucat.com.ua/api-sync/v1/manufacturers/{GLN-номер компании}/products/{идентификатор_товара}?fields=<список полей через запятую>&authKey=<ключ авторизации>
```
Тип запроса: **GET**

#### Параметры запроса

* **{GLN-номер компании}** - GLN - номер производителя/дистрибьютора товара
* **{идентификатор_товара}** - идентификатор товарной позиции в виде штрих-кода(GTIN), внутреннего артикула поставщика(InternalSupplierCode) или идентификатора uCat(ucatID)
* **{fields}** - необязательный параметр, содежит в себе список полей, которые должны быть в ответе по товару. ChildGTIN всегда будет присутствовавть в ответе, для построения иерархии.

#### Пример ответа в формате JSON 

[Посмотреть пример по товару GTIN-4820000944281](GTIN-4820000944281.json)


### 4. Получить информацию только о фотографиях

В запросе на получение фотографий, нужно указать GLN поставщика и GTIN (штрихкод) товара.

#### Формат запроса
```html
https://client.ucat.com.ua/api-sync/v1/manufacturers/{GLN-номер компании}/products/{GTIN}/images?authKey=<ключ авторизации>
```
Тип запроса: **GET**

#### Пример ответа в формате JSON
Если все параметры правильные, товар есть в базе и доступен
```javascript
[
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_p.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=KvOIyNVv9653BpEQIZ78yKc0GZI%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_p.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_p.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_p.jpg",
    planogram: "true" // даный флаг, обозначает что фото сделано с лицевой стороны
  },
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_2.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=DEC3H6nFVbtbiPeK%2BiKBACyz7do%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_2.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_2.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_2.jpg",
    planogram: "false"
  },
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_1.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=gfcdqDwapmgzBscmUW2HL9%2B3m1M%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_1.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_1.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_1.jpg",
    planogram: "false"
  },
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_3.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=GlqkfS841ZuEPbrqUId5%2FWUfvRk%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_3.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_3.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_3.jpg",
    planogram: "false"
  }
]
```

Если товар не найден в базе или не доступен
```javascript
{
  code: "404 Not Found",  
  error: "Product photos that you requested is not found."
}
```

### 5. Передать статус обработки товара торговой сетью
Для того, что-бы поставщики, знали статус обработки товаров торговой сетью, нужно передать этот статус в каталог, после того, как торговая сеть получает информацию о товаре.
**Формат запроса:**
```html
  https://client.ucat.com.ua/api-sync/v1/response?authKey=<ключа авторизации>
```
Тип запроса: **POST**

В теле POST запроса, передаются следующие параметры:
* **providerGLN** - GLN-номер поставщика товара, о котором передается информация, берется из информации о товаре
* **GTIN** - штрихкод товара
* **versionProduct** - версия товара которую обработала торговая сеть, берется из информации о товаре
* **status** - принимает одно из четырех значений. Статусы передаются в латинском написании:
  * ACCEPTED (ПОЛУЧЕНО) – информация о товаре получена торговой сетью, и рассматривается возможность внесения данной информации во внутреннюю систему учета
  * SYNCHRONIZED (ПОДТВЕРЖДЕНО) – информация  о товаре получена торговой сетью и добавлена в их внутреннюю систему учета.
  * REVIEW (НА РАССМОТРЕНИИ, второе значение “Обнаружена ошибка”)- в информации о товаре обнаружена ошибка, товар не загружен во внутреннюю систему учета.
  * REJECT (ОТКЛОНЕНО) – торговая сеть не хочет получать информацию или обновления по данному товару. 
* **description** - необязательный параметр, используется для описании ошибки.

На данный запрос, сервер ответит подтверждением или ошибкой, если у пользователя нет прав отвечать на данный запрос. 
1) Положительный ответ:
- код ответа сервера: 201 Created

2) Ошибка при загрузке:
- код ответа сервера: 400 Bad Request
- тело ответа:

```javascript
  {
    "code" : "400", 
    "error": "You donn\’t recive product of such version"
  }
```

### 6. Поиск товарной позиции по штрихкоду
Пользователь REST API может пользоваться поиском по товарам по штрихкоду.

**Формат запроса:**

```html
    https://client.ucat.com.ua/api-sync/v1/products/{идентификатор_товара}/search?authKey=<ключ_авторизации>
```

Тип запроса: **GET**

#### Параметры запроса

* **{идентификатор_товара}** - идентификатор товарной позиции в виде штрих-кода(GTIN), внутреннего артикула поставщика(InternalSupplierCode) или идентификатора uCat(ucatID)

**Пример ответов:**

1) Товар найден в каталоге по штрихкоду - вернется ответ с [информацией о товаре](https://github.com/xainse/ucat-sync-api-doc#Пример-ответа-по-одному-товару)
- код ответа сервера: 200 OK
- информация о товаре

2) Товар есть в каталоге, но торговая сеть не оформила на него подписку, API возвращает сообщение об ошибке
- код ответа сервера: 403 Forbidden
- тело ответа: 

```javascript
{
  "code": "403 Forbidden",
  "error": "Not exists subscribe to product"
}
```
3) Товара есь в каталоге но не опубликован на торговую сеть
- код ответа сервера: 403 Forbidden
- тело ответа:  

```javascript
{
  "code" : "403 Forbidden", 
  "error": "Data of the product is not available to you"
}
```

4) Товара нет в каталоге
- код ответа сервера: 404 Not Found
- тело ответа: 

```javascript
  {
    "code" : "404 Not Found", 
    "error": "Not exists product"
  }
```


### 7. Импорт артикулов торговой сети в базу uCat
Торговая сеть имеет возможность загрузить в каталог внутриние артикулы товаров и привязать их к штрихкодам в базе.
Для загрузки нужно передать в каталог CSV файл в специальном формате с данными. 

 [Шаблон CSV файла, для передачи внутренних артикулов торговой сети](https://github.com/Nikalogic/ucat-sync-api-doc/blob/master/docs/import-sku-template.csv)
 
* Кодировка файла UTF-8
* В файле с данными должны сохраняться две строчки заголовков. Первая строка на русском языке и вторая латиницей. Это сделанно для удобства работы с файлом в ручном режиме.

При передаче файла через REST API данные не проверяются сразу. При успешном получении файла - передается код 200, который означает, что файл загрузился. 
Результат обработки файла можно посмотреть в админчасти каталога uCat на странице.

**Формат запроса:**

```html
  https://client.ucat.com.ua/api-sync/v1/upload-sku?authKey=<ключ_авторизации>
```

Тип запроса: **POST**

*[Пример отправки файла на REST API скриптом](http://bit.ly/2e8jRYV)*

**Параметры:**
Файл можно передавать в любой переменной, например *file-sku*

**Примеры ответов:**

1) Файл загружен:
- код ответа сервера: 200 OK
- тело ответа: 

```javascript
  {
    "success":"true",
    "error":"Файл загружен"
  }
```

2) Ошибка при загрузке файла:
- код ответа сервера: 400 Bad Request 
- тело ответа: 

```javascript
  {
    "success": "false",
    "error": "Файл не загружен"
  }
```

В ответе можно увидеть только факт загрузки файла, а не валидации его контента.

### 8. Список изменившихся товаров

Данный запрос используется для того, чтобы ритейлер не проверял каждый раз все товары и не сравнивал их текущие значения со старыми значениями добавлен запрос к API который показывает список штрих-кодов, которые добавились или изменились за время, прошедшее с того момента, когда ритейлера забирал данные последний раз.

**Формат запроса:**
```html
  https://client.ucat.com.ua/api-sync/v1/last-changed-gtins?onlyApiDistribution=<только_товары_полученные_по_API>&authKey=<ключ_авторизации>
```
Тип запроса: **GET**

**Параметры запроса**

* **onlyApiDistribution** - не обязательный параметр запроса. При передаче параметра onlyApiDistribution=**true** учитываются только товары полученные посредством API (json). По умолчанию учитываются все способы передачи данных (onlyApiDistribution=**false**).

**Примеры ответов:**
Ответ сгруппирован по  производителям и измененным и новым товарам.

1) Если есть изменившиеся товары за период.
- код ответа сервера: 200 OK
- тело ответа:  

```javascript
{
	// Список объектов, которые описывают разных ритейлеров
	//  Производитель номер 1
	{
		"GLN": "номер gln",
		"nameUk": "Название компании на украинском",
		"OKPO": "номер ОКПО",
		"changedGTINs" : [
			"штрих-код 1",
			"штрих-код 2",
			"штрих-код 3",
			//...
			"штрих-код n"
		],
		"newGTINs" : [
			"штрих-код 1",
			"штрих-код 2",
			"штрих-код 3",
			//...
			"штрих-код n"
		]
	}
	// ...
	// Поставщик номер N
	{
		"GLN": "номер gln",
		"nameUk": "Название компании на украинском",
		"OKPO": "номер ОКПО",
		"changedGTINs" : [
			"штрих-код 1",
			"штрих-код 2",
			"штрих-код 3",
			//...
			"штрих-код n"
		],
		"newGTINs" : [
			"штрих-код 1",
			"штрих-код 2",
			"штрих-код 3",
			//...
			"штрих-код n"
		]
	}
}
```

2) Если за период с последнего обращения к товарам изменений не произошло
- код ответа сервера: 304 Not Modified
- тело ответа: 

```javascript
  {}
```

### 9. Количество подписок
Запрос возвращает количество оформленных подписок в каталоге. 

**Формат запроса:**
```html
  https://client.ucat.com.ua/api-sync/v1/get-count-subscribe-product?authKey=<ключ_авторизации>
```

**Пример ответа:**
- код ответа сервера: 200 OK
- тело ответа: 

```javascript
{
  "countSubscribeToMarket"  : "1",   // Кол-во подписок на торговые рынки (страны)
  "countSubscribeToProvider": "20",  // Кол-во подписок на поставщиков
  "countSubscribeToGTIN"    : "300",  // Кол-во оформленных подписок на штрихкоды
  "countSubscribeProduct"   : "4000" // Кол-во доступных базовых единиц, суммирует штрихкоды по всем видам подписок
}
```

В случае ошибки, выводится описание ошибки.


### 10. Список доступных GTIN базовых единиц на которые была оформлена подписка

Запрос возвращает все доступные штрихкоды из каталога для Торговой Сети.

**Формат запроса:**
```html
  https://client.ucat.com.ua/api-sync/v1/get-subscribe-product-gtin?authKey=<ключ_авторизации>

``` 

**Пример ответа:**
1) Список доступных штрихкодов из каталога
- код ответа сервера: 200 OK
- тело ответа: 

```javascript
{
  "0":"GTIN1",
  "1":"GTIN2",
  "2":"GTIN3",
  "3":"GTIN4",
    ...
  "999...":"GTIN999..."
}
```
2) Если у Торговой Сети не оформленна подписка на товары или товары не доступны по другим причинам, выводиться ошибка
- код ответа сервера: 404 Not Found
- тело ответа: 

```javascript
  {
    "code" : "404 Not Found", 
    "error": "Not isset subscription"
  }
```

### 11. Расшифровка значения справочника

Можно передать код из любого справочника и в ответ получить название справочника и расшифровку значения.

**Формат запроса**
```html
https://client.ucat.com.ua/api-sync/v1/handbook/search/{код справочника}?authKey=<код авторизации>
```

Тип запроса: **GET**

Пример запроса https://client.ucat.com.ua/api-sync/v1/handbook/search/CASE?authKey=<ключа авторизации>

**Пример ответа в формате JSON**

```javascript
[
  {
    hanbookName: "Info product unit descriptor",
    codeValue: "CASE",
    nameRu: "Коробка",
    nameUk: "Коробка",
    nameEn: "Box"
  }
]
```

## 12. Изображения одного товара

В запросе на получение изображений нужно указать GTIN (штрихкод) товара.

#### Формат запроса
```html
https://client.ucat.com.ua/api-sync/v1/images/{GTIN}?authKey=<ключ авторизации>
```
Тип запроса: **GET**

#### Пример ответа в формате JSON
Если все параметры правильные, товар есть в базе и доступен
```javascript
[
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_p.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=KvOIyNVv9653BpEQIZ78yKc0GZI%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_p.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_p.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_p.jpg",
    planogram: "true" // даный флаг, обозначает что фото сделано с лицевой стороны
  },
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_2.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=DEC3H6nFVbtbiPeK%2BiKBACyz7do%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_2.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_2.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_2.jpg",
    planogram: "false"
  },
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_1.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=gfcdqDwapmgzBscmUW2HL9%2B3m1M%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_1.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_1.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_1.jpg",
    planogram: "false"
  },
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_3.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=GlqkfS841ZuEPbrqUId5%2FWUfvRk%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_3.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_3.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_3.jpg",
    planogram: "false"
  }
]
```

Если товар не найден в базе или не доступен
```javascript
{
  code: "404 Not Found",  
  error: "Images not found"
}
```

## 13. Информация об активности поставщика

В запросе нужно указать номер ОКПО поставщика и ключ авторизации.

#### Формат запроса
```html
https://client.ucat.com.ua/api-sync/v1/manufacturer-activity?id=<ОКПО поставщика>&authKey=<ключ авторизации>
```
Тип запроса: **GET**

#### Пример ответа в формате JSON
Если параметры переданы правильно:
```javascript
{
    "success": true,
    "data": {
        "item": {
        "firstActivityDate": null,
        "lastActivityDate": "2017-04-03",
        "refusalDate": null,
        "regNumber": "182863",
        "name": "Житомирський маслозавод (рудь)",
        "status": "Работают",
        "comment": "прислала большой шаблон после заполнения, переслал Людмиле",
        "email": null,
        "tariffPlanName": "Мої ритейлери, 4-5 ритейлери, 201-300 SKU",
        "limitClientList": [
            {
                "name": "Атлантіс (Алеф-Віналь)",
                "gln": "4820024220002"
            },
            {
                "name": "Штраус Україна",
                "gln": "4820082500009"
            },
            {
                "name": "Арда трейдінг",
                "gln": "4823052500009"
            }
        ],
        "tariffPlanEndDate": null
        }
    }
}
```
Если параметры переданы с ошибкой, данные не доступны или не найдены, будет возвращена ошибка в таком формате:
```javascript
{
    "success": false,
    "data": null,
    "message": "Data not found"
}
```

## 14. Информация об активности поставщиков

Из-за большого обьема данных предусмотрена возможность получать информацию частями указывая страницу.
В запросе нужно указать номер страницы и ключ авторизации.

#### Формат запроса
```html
https://client.ucat.com.ua/api-sync/v1/manufacturers-activity?page=<номер страницы>&authKey=<ключ авторизации>
```
Тип запроса: **GET**

#### Пример ответа в формате JSON
Если параметры переданы правильно:
```javascript
{
    "success": true,
    "data": {
        "items": [
            {
                "firstActivityDate": null,
                "lastActivityDate": "2017-04-03",
                "refusalDate": null,
                "regNumber": "182863",
                "name": "Житомирський маслозавод (рудь)",
                "status": "Работают",
                "comment": "прислала большой шаблон после заполнения, переслал Людмиле",
                "email": null,
                "tariffPlanName": null,
                "limitClientList": null,
                "tariffPlanEndDate": null
            },
            {
                "firstActivityDate": null,
                "lastActivityDate": "2015-01-16",
                "refusalDate": null,
                "regNumber": "37016058",
                "name": "РЕКТЕ",
                "status": "Приостановлен",
                "comment": "по замерам он сказал что в курсе",
                "email": null,
                "tariffPlanName": "Мої ритейлери, 4-5 ритейлери, 201-300 SKU",
                "limitClientList": [
                    {
                        "name": "Атлантіс (Алеф-Віналь)",
                        "gln": "4820024220002"
                    },
                    {
                        "name": "Штраус Україна",
                        "gln": "4820082500009"
                    },
                    {
                        "name": "Арда трейдінг",
                        "gln": "4823052500009"
                    }
                ],
                "tariffPlanEndDate": "2015-10-07 00:00:00"
            },
            {
                "firstActivityDate": null,
                "lastActivityDate": "2015-03-16",
                "refusalDate": null,
                "regNumber": "35881156",
                "name": "ПБС-Україна",
                "status": "Приостановлен",
                "comment": "работают по привату Таврии, будут подключаться если будут изменения по сетям, которые используют каталог в работе",
                "email": null,
                "tariffPlanName": "Private",
                "limitClientList": null,
                "tariffPlanEndDate": "2015-10-10 00:00:00"
            },
            {
                "firstActivityDate": null,
                "lastActivityDate": "2015-03-02",
                "refusalDate": null,
                "regNumber": "37117467",
                "name": "Фітор Ко",
                "status": "Приостановлен",
                "comment": "проплатил полностью и погасил свій долг",
                "email": null,
                "tariffPlanName": "Private",
                "limitClientList": null,
                "tariffPlanEndDate": "2015-11-01 00:00:00"
            },
            {
                "firstActivityDate": null,
                "lastActivityDate": "2017-03-17",
                "refusalDate": null,
                "regNumber": "37010082",
                "name": "Ал-Пластик",
                "status": "Работают",
                "comment": "спрашивал о публикации. Рассказал что всё готово и опубликовано.",
                "email": "al-plastik.zhyzhko@ukr.net",
                "tariffPlanName": "STANDARD - для клиентов Таврии",
                "limitClientList": null,
                "tariffPlanEndDate": "2015-11-01 00:00:00"
            },
            {
                "firstActivityDate": null,
                "lastActivityDate": "2015-10-29",
                "refusalDate": null,
                "regNumber": "31826636",
                "name": "Ельба ",
                "status": "Приостановлен",
                "comment": "в общем они не знают будут ли работать и с Альянс маркетом и с Пакко",
                "email": null,
                "tariffPlanName": "Private",
                "limitClientList": null,
                "tariffPlanEndDate": "2015-11-17 00:00:00"
            },
            {
                "firstActivityDate": null,
                "lastActivityDate": "2015-01-15",
                "refusalDate": null,
                "regNumber": "38572699",
                "name": "ВАІЕР ТОВ",
                "status": "Приостановлен",
                "comment": "перупредил о замерах",
                "email": null,
                "tariffPlanName": "Private",
                "limitClientList": null,
                "tariffPlanEndDate": "2015-11-04 00:00:00"
            },
            {
                "firstActivityDate": null,
                "lastActivityDate": "2017-02-14",
                "refusalDate": null,
                "regNumber": "34415111",
                "name": "Ві ТА Ві Дизайн ТОВ",
                "status": "Работают",
                "comment": "без каталога пока обходятся по другим сетям кроме новуса",
                "email": null,
                "tariffPlanName": "Улюблений ритейлер",
                "limitClientList": [
                    {
                        "name": "Атлантіс (Алеф-Віналь)",
                        "gln": "4820024220002"
                    },
                    {
                        "name": "Штраус Україна",
                        "gln": "4820082500009"
                    },
                    {
                        "name": "Арда трейдінг",
                        "gln": "4823052500009"
                    }
                ],
                "tariffPlanEndDate": "2016-12-31 00:00:00"
            },
            {
                "firstActivityDate": null,
                "lastActivityDate": "2016-10-05",
                "refusalDate": null,
                "regNumber": "37670844",
                "name": "Амиго и C",
                "status": "Работают",
                "comment": "запросили АВР, отправили, все ок",
                "email": "yuliya.shevel@amigotoys.com",
                "tariffPlanName": null,
                "limitClientList": null,
                "tariffPlanEndDate": null
            }
        ],
        "_links": {
            "self": "https://client.ucat.com.ua/api-sync/v1/manufacturers-activity?page=1",
            "next": "https://client.ucat.com.ua/api-sync/v1/manufacturers-activity?page=2",
            "last": "https://client.ucat.com.ua/api-sync/v1/manufacturers-activity?page=143"
        },
        "_meta": {
            "totalCount": "2843",
            "pageCount": 143,
            "currentPage": "1",
            "perPage": 20
        }
    }
}
```

## 15. Информация о сертификатах

В запросе нужно указывать GTIN (штрих-код) товара и ключ авторизации

#### Формат запроса

```html
https://client.ucat.com.ua/api-sync/v1/products/{GTIN}/certificates?authKey=<ключ_авторзации>
```

Тип запроса: **GET**

#### Пример успешного ответа:

```javascript
[
    {
        "gtin": 6411200108344,
        "informationProviderGln": 4829900017231,
        "brandName": "Nordic",
        "description": "Висівки вівсяні Nordic 700г",
        "certificateType": "Сертификат соответствия",
        "activeTillDate": "2014-07-17 00:00:00",
        "certificateNumber": "UA1.003.X007908.13",
        "sku": "1112717",
        "files": [
            "http://ucat-test.s3-eu-west-1.amazonaws.com/client/0000228/certificates/cert-4829900017231-1-68.pdf?AWSAccessKeyId=AKIAJIMBSKJ2WTNIOXZQ&Signature=jYk5epPZz28zP7tkW1dXTCFfrrk%3D&Expires=1502367082"
        ]
    },
    {
        "gtin": 6411200108344,
        "informationProviderGln": 4829900017231,
        "brandName": "Nordic",
        "description": "Висівки вівсяні Nordic 700г",
        "certificateType": "Гигиеническое заключение",
        "activeTillDate": "2016-09-25 00:00:00",
        "certificateNumber": "05.03.02-03/87301",
        "sku": "1112717",
        "files": [
            "http://ucat-test.s3-eu-west-1.amazonaws.com/client/0000228/certificates/cert-4829900017231-53abe89ec5b92-49.pdf?AWSAccessKeyId=AKIAJIMBSKJ2WTNIOXZQ&Signature=GV1DZw6sNJjxS8AenBcjfd%2FQA7E%3D&Expires=1502367082"
        ]
    }
]
```

**Если не найдены сертификаты, в ответе будет пустой JSON массив, а не ошибка**

### Ответы в случае ошибки

Если не найден товар, возвращается соответствующая ошибка 404

```javascript
{
    "code": "404 Not Found",
    "error": "Product not found"
}
```

## 16. Изображения одного товара по уникальному идентификатору (ucatID)

В запросе на получение изображений нужно указать ucatID (уникальный идентификатор) товара.

#### Формат запроса
```html
https://client.ucat.com.ua/api-sync/v1/images-by-id/{ucatID}?authKey=<ключ авторизации>
```
Тип запроса: **GET**

#### Пример ответа в формате JSON
Если все параметры правильные, товар есть в базе и доступен
```javascript
[
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_p.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=KvOIyNVv9653BpEQIZ78yKc0GZI%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_p.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_p.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_p.jpg",
    planogram: "true" // даный флаг, обозначает что фото сделано с лицевой стороны
  },
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_2.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=DEC3H6nFVbtbiPeK%2BiKBACyz7do%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_2.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_2.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_2.jpg",
    planogram: "false"
  },
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_1.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=gfcdqDwapmgzBscmUW2HL9%2B3m1M%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_1.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_1.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_1.jpg",
    planogram: "false"
  },
  {
    original: "https://ucat-live.s3-eu-west-1.amazonaws.com/products/04820000944281/images/04820000944281_3.jpg?AWSAccessKeyId=AKIAIPT3T5GLJRUDEZPA&Signature=GlqkfS841ZuEPbrqUId5%2FWUfvRk%3D&Expires=1486987484",
    600x600: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/600x600_04820000944281_3.jpg",
    200x200: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/200x200_04820000944281_3.jpg",
    50x50: "https://ucat-live.s3-eu-west-1.amazonaws.com/products.public/04820000944281/images/50x50_04820000944281_3.jpg",
    planogram: "false"
  }
]
```

Если товар не найден в базе или не доступен
```javascript
{
  code: "404 Not Found",  
  error: "Images not found"
}
```


## Пример ответа по одному товару

Ответы отдаются в формате JSON. [Посмотреть пример по товару GTIN-4820000944281](GTIN-4820000944281.json)

Пример ниже содежит информацию о товаре 3228020490329

На верхнем уровне идет информация об упаковке, например:
* Ящик со штрих-кодом 3228025025427, в котором находится 6 блоков
* Блок без штрих-кода, в котором находится 6 единиц товара
* Товар, штрихкод 3228020490329

```javascript
[
  {
    "ucatID": 83029,
    "GTIN": "3228025025427",
    "BarCodeType": "EAN_UCC_13_SYMBOL",
    "ReplacedTradeItemIdentification": null,
    "InformationProviderGLN": 4820096010006,
    "InternalSupplierCode": null,
    "BrandId": 371,
    "BrandName": "President",
    "ShortBrandName": null,
    "LanguageSpecificBrandNameRu": "Президент",
    "LanguageSpecificBrandNameUk": "Президент",
    "LanguageSpecificBrandNameEn": "President",
    "SubBrandId": null,
    "SubBrand": null,
    "LanguageSpecificShortBrandNameRu": null,
    "LanguageSpecificShortBrandNameUk": null,
    "LanguageSpecificShortBrandNameEn": null,
    "DescriptionTextRu": "Сыр President Cheddar плавленый для бургеров 40% 20г*10шт 200г",
    "DescriptionTextUk": "Сир President Cheddar плавлений для бургерів 40% 20г*10шт 200г",
    "DescriptionTextEn": null,
    "ShortDescriptionTextRu": "Сыр President Ched пл д/бург 200г",
    "ShortDescriptionTextUk": "Сир Presiden Ched пл д/бург 200г",
    "ShortDescriptionTextEn": null,
    "OriginalDescriptionRu": null,
    "OriginalDescriptionUk": null,
    "OriginalDescriptionEn": null,
    "SEODescriptionRu": null,
    "SEODescriptionEn": null,
    "SEODescriptionUk": null,
    "ShortAdditionalNameUk20": "Сир PresChed бур200г",
    "ShortAdditionalNameRu60": null,
    "ShortAdditionalNameUk60": "Сир President Cheddar плавлений д/бургер 40% 20г*10шт 200г",
    "ShortAdditionalNameEn60": null,
    "AdditionalTradeItemDescriptionRu": null,
    "AdditionalTradeItemDescriptionUk": null,
    "AdditionalTradeItemDescriptionEn": null,
    "CountryOfOrigin": 250,
    "CountryOfLastProcessing": 804,
    "ManufacturerUcatId": 3161,
    "ManufacturerGLN": null,
    "ManufacturerNameRu": null,
    "ManufacturerNameUk": "Стефромаджеро",
    "ManufacturerNameEn": "Stefromagero",
    "ManufacturingPlantGLN": null,
    "version": 12,
    "DistributorName": [],
    "ImporterName": [],
    "LogisticOperatorName": [],
    "ProductionCapacityAddressRu": null,
    "ProductionCapacityAddressUk": null,
    "ProductionCapacityAddressEn": null,
    "VariantNumber": null,
    "VariantName": null,
    "DescriptionProductRu": null,
    "DescriptionProductEn": null,
    "DescriptionProductUk": null,
    "ChildGTIN": {
      "ucatID": 83111,
      "GTIN": null,
      "BarCodeType": null,
      "ReplacedTradeItemIdentification": null,
      "InformationProviderGLN": 4820096010006,
      "InternalSupplierCode": "3228020490329p",
      "BrandId": 371,
      "BrandName": "President",
      "ShortBrandName": null,
      "LanguageSpecificBrandNameRu": "Президент",
      "LanguageSpecificBrandNameUk": "Президент",
      "LanguageSpecificBrandNameEn": "President",
      "SubBrandId": null,
      "SubBrand": null,
      "LanguageSpecificShortBrandNameRu": null,
      "LanguageSpecificShortBrandNameUk": null,
      "LanguageSpecificShortBrandNameEn": null,
      "DescriptionTextRu": "Сыр President Cheddar плавленый для бургеров 40% 20г*10шт 200г",
      "DescriptionTextUk": "Сир President Cheddar плавлений для бургерів 40% 20г*10шт 200г",
      "DescriptionTextEn": null,
      "ShortDescriptionTextRu": "Сыр President Ched пл д/бург 200г",
      "ShortDescriptionTextUk": "Сир Presiden Ched пл д/бург 200г",
      "ShortDescriptionTextEn": null,
      "OriginalDescriptionRu": null,
      "OriginalDescriptionUk": null,
      "OriginalDescriptionEn": null,
      "SEODescriptionRu": null,
      "SEODescriptionEn": null,
      "SEODescriptionUk": null,
      "ShortAdditionalNameUk20": "Сир PresChed бур200г",
      "ShortAdditionalNameRu60": null,
      "ShortAdditionalNameUk60": "Сир President Cheddar плавлений д/бургер 40% 20г*10шт 200г",
      "ShortAdditionalNameEn60": null,
      "AdditionalTradeItemDescriptionRu": null,
      "AdditionalTradeItemDescriptionUk": null,
      "AdditionalTradeItemDescriptionEn": null,
      "CountryOfOrigin": 250,
      "CountryOfLastProcessing": 804,
      "ManufacturerUcatId": 3161,
      "ManufacturerGLN": null,
      "ManufacturerNameRu": null,
      "ManufacturerNameUk": "Стефромаджеро",
      "ManufacturerNameEn": "Stefromagero",
      "ManufacturingPlantGLN": null,
      "version": 11,
      "DistributorName": [],
      "ImporterName": [],
      "LogisticOperatorName": [],
      "ProductionCapacityAddressRu": null,
      "ProductionCapacityAddressUk": null,
      "ProductionCapacityAddressEn": null,
      "VariantNumber": null,
      "VariantName": null,
      "DescriptionProductRu": null,
      "DescriptionProductEn": null,
      "DescriptionProductUk": null,
      "ChildGTIN": {
        "ucatID": 83028,
        "GTIN": "3228020490329",
        "BarCodeType": "EAN_UCC_13_SYMBOL",
        "ReplacedTradeItemIdentification": null,
        "InformationProviderGLN": 4820096010006,
        "InternalSupplierCode": "49716",
        "BrandId": 371,
        "BrandName": "President",
        "ShortBrandName": null,
        "LanguageSpecificBrandNameRu": "Президент",
        "LanguageSpecificBrandNameUk": "Президент",
        "LanguageSpecificBrandNameEn": "President",
        "SubBrandId": null,
        "SubBrand": null,
        "LanguageSpecificShortBrandNameRu": null,
        "LanguageSpecificShortBrandNameUk": null,
        "LanguageSpecificShortBrandNameEn": null,
        "DescriptionTextRu": "Сыр President Cheddar плавленый для бургеров 40% 20г*10шт 200г",
        "DescriptionTextUk": "Сир President Cheddar плавлений для бургерів 40% 20г*10шт 200г",
        "DescriptionTextEn": null,
        "ShortDescriptionTextRu": "Сыр President Ched пл д/бург 200г",
        "ShortDescriptionTextUk": "Сир Presiden Ched пл д/бург 200г",
        "ShortDescriptionTextEn": null,
        "OriginalDescriptionRu": null,
        "OriginalDescriptionUk": null,
        "OriginalDescriptionEn": null,
        "SEODescriptionRu": null,
        "SEODescriptionEn": null,
        "SEODescriptionUk": null,
        "ShortAdditionalNameUk20": "Сир PresChed бур200г",
        "ShortAdditionalNameRu60": null,
        "ShortAdditionalNameUk60": "Сир President Cheddar плавлений д/бургер 40% 20г*10шт 200г",
        "ShortAdditionalNameEn60": null,
        "AdditionalTradeItemDescriptionRu": null,
        "AdditionalTradeItemDescriptionUk": "молоко знежирене відновлене, сири тверді (5% сиру Чеддер), масло, сироватка, молочний білок, емульгатори: Е450, Е452, загущувач: Е407, сіль харчова, консервант: Е202, барвник: Е160а. Можлива присутність яєчного білку.",
        "AdditionalTradeItemDescriptionEn": null,
        "CountryOfOrigin": 250,
        "CountryOfLastProcessing": 804,
        "ManufacturerUcatId": 3161,
        "ManufacturerGLN": null,
        "ManufacturerNameRu": null,
        "ManufacturerNameUk": "Стефромаджеро",
        "ManufacturerNameEn": "Stefromagero",
        "ManufacturingPlantGLN": null,
        "version": 12,
        "DistributorName": [],
        "ImporterName": [
          {
            "nameUk": "ДчП \"Лакталіс Україна\"",
            "nameEn": "SC \"Lactalis Ukraine\"",
            "nameRu": "ДчП \"Лакталис Украина\""
          }
        ],
        "LogisticOperatorName": [],
        "ProductionCapacityAddressRu": null,
        "ProductionCapacityAddressUk": null,
        "ProductionCapacityAddressEn": "39 av. C. Prost. BP 852.39008 Lons le Saunier, France",
        "VariantNumber": null,
        "VariantName": null,
        "DescriptionProductRu": "Сыр President Cheddar плавленый для бургеров 40% 20г*10шт 200г. Калорийность в 100 г продукта: 242 ккал. Массовая доля жира: 40  %. Пищевая (питательная) ценность в 100г продукта: Белки - 14г., Жиры - 18г., Углеводы - 6г. Условия хранения: хранить при температуре от 4°C до 15°C - 1  год. Страна производства: Франция. Содержание ГМО: Не содержит указанного вещества. Количество: 10 шт.",
        "DescriptionProductEn": "Calories in 100 grams of product: 242 kcal. Fat content: 40  %. Nutritional value of 100 grams: Proteins - 14g, Fats - 18g, Carbs - 6g. Storage: Store at temperatures from 4°C to 15°C - 1  year. Manufacturer: Stefromagero. Country of origin: FRANCE. Address production capacity: 39 av. C. Prost. BP 852.39008 Lons le Saunier, France. GMO: The product is free from the indicated substance. Amount: 10 Piece",
        "DescriptionProductUk": "Сир President Cheddar плавлений для бургерів 40% 20г*10шт 200г. Склад: молоко знежирене відновлене, сири тверді (5% сиру Чеддер), масло, сироватка, молочний білок, емульгатори: Е450, Е452, загущувач: Е407, сіль харчова, консервант: Е202, барвник: Е160а. Можлива присутність яєчного білку. Калорійність в 100г продукту: 242 ккал. Масова частка жиру: 40  %. Харчова (поживна) цінність у 100г продукту: Білки - 14г., Жири - 18г., Вуглеводи - 6г. Умови зберігання: зберігати за температури від 4°C до 15°C - 1  рік. Виробник: Стефромаджеро. Країна виробництва: Франція. Імпортери: ДП \"Лакталіс-Україна\". Вміст ГМО: Не містить вказаної речовини. Кількість: 10 шт.",
        "ChildGTIN": null,
        "QuantityOfChild": null,
        "UnitDescriptor": "BASE_UNIT_OR_EACH",
        "Brick": 10000029,
        "FunctionalNameTextRu": "Сыры/Творог/Заменители сыра и творога (длительного хранения)",
        "FunctionalNameTextUk": "Сир / Замінник сиру (тривалого зберігання)",
        "FunctionalNameTextEn": "Cheese/Cheese Substitutes (Shelf Stable)",
        "UkrZed": "0406 30 31 00",
        "DKPP": "10.51.40-70.00",
        "UkrMonopoly": null,
        "UkrStatistics": 222,
        "FirstOptAnnualReport": "202",
        "FirstRtReport": null,
        "AlcoholClass": null,
        "DSTU": null,
        "TU": null,
        "GSTU": null,
        "SOU": null,
        "TargetMarketCountryCode": 804,
        "TargetMarketSubDivisionCode": null,
        "CheeseFat": "40",
        "GeneticallyModified": "FREE_FROM",
        "CaloricValue": "242",
        "Fats": "18",
        "Carbs": "6",
        "Proteins": "14",
        "PercentageOfAlcohol": null,
        "Sugar": null,
        "IsDiabetic": "false",
        "WidthValue": "0.102",
        "WidthUoM": "MR",
        "DepthValue": "0.029",
        "DepthUoM": "MR",
        "HeightValue": "0.090",
        "HeightUoM": "MR",
        "NetWeightValue": "0.2000",
        "NetWeightUoM": "KG",
        "ToleranceMinValue": null,
        "ToleranceMinUoM": null,
        "ToleranceMaxValue": null,
        "ToleranceMaxUoM": null,
        "GrossWeightValue": "0.2080",
        "GrossWeightUoM": "KG",
        "NetContentValue": "10.0000",
        "NetContentUoM": "PC",
        "DryMassOfMainProductValue": null,
        "DryMassOfMainProductUoM": null,
        "QuantityOfBaseUnit": null,
        "IsPackagingMarkedReturnable": "false",
        "PackagingType": "U07",
        "PackagingMaterial": "U06",
        "TypeOfProductPlacementInBox": null,
        "OrientationTypeOnShelf": null,
        "NumberOfBaseUnitsInPackagingInWidth": null,
        "NumberOfBaseUnitsInPackagingInDepth": null,
        "NumberOfBaseUnitsInPackagingInHeight": null,
        "PackagingPerforationType": null,
        "MinimumLifespanFromArrival": null,
        "MinimumLifespanFromProduction": 365,
        "MinimumLifespanFromProductionValue": "1.0000",
        "MinimumLifespanFromProductionUoM": "ANN",
        "StorageHandlingTemperatureMaximum": "15.0",
        "StorageHandlingTemperatureMinimum": "4.0",
        "OpenedTradeItemLifespanValue": null,
        "OpenedTradeItemLifespanUoM": "DA",
        "LifespanFromProductionGroup1Value": "365.0000",
        "MaximumStorageHandlingTemperatureGroup1": "15.0",
        "MinimumStorageHandlingTemperatureGroup1": "4.0",
        "LifespanFromProductionGroup2Value": null,
        "MaximumStorageHandlingTemperatureGroup2": null,
        "MinimumStorageHandlingTemperatureGroup2": null,
        "LifespanFromProductionGroup3Value": null,
        "MaximumStorageHandlingTemperatureGroup3": null,
        "MinimumStorageHandlingTemperatureGroup3": null,
        "LifespanFromProductionGroup4Value": null,
        "MaximumStorageHandlingTemperatureGroup4": null,
        "MinimumStorageHandlingTemperatureGroup4": null,
        "WarrantyPeriodValue": null,
        "WarrantyPeriodUoM": null,
        "DeliveryToMarketTemperatureMaximum": null,
        "DeliveryToMarketTemperatureMinimum": null,
        "ConsumerUsageStorageInstructionsRu": null,
        "ConsumerUsageStorageInstructionsUk": null,
        "ConsumerUsageStorageInstructionsEn": null,
        "Wetness": null,
        "NonGTINPalletDepthValue": null,
        "NonGTINPalletDepthUoM": "MR",
        "NonGTINPalletWidthValue": null,
        "NonGTINPalletWidthUoM": "MR",
        "NonGTINPalletHeightValue": null,
        "NonGTINPalletHeightUoM": "MR",
        "NonGTINPalletLayerHeightValue": null,
        "NonGTINPalletLayerHeightUoM": "MR",
        "NonGTINPalletGrossWeightValue": null,
        "NonGTINPalletGrossWeightUoM": "KG",
        "NonGTINPalletAdditionalWeightValue": null,
        "NonGTINPalletAdditionalWeightUoM": "KG",
        "NonGTINPalletLayerWeightValue": null,
        "NonGTINPalletType": null,
        "QuantityOfTradeItemsPerPallet": null,
        "QuantityOfLayersPerPallet": null,
        "QuantityOfTradeItemsPerPalletLayer": null,
        "NonGTINPalletTermsAndConditions": null,
        "NonGTINPalletStackingFactor": null,
        "NonGTINPalletNetHeightValue": null,
        "NonGTINPalletNetHeightUoM": "MR",
        "NonGTINPalletNetWeightValue": null,
        "NonGTINPalletNetWeightUoM": "KG",
        "IsAPrivateLabel": "false",
        "IsAConsumerUnit": "true",
        "IsTradeItemABaseUnit": "true",
        "IsAnOrderableUnit": "true",
        "IsADespatchUnit": "false",
        "IsOrderQuantityMinimumMultiples": "true",
        "IsAVariableUnit": "false",
        "IsUnitWithAverageValues": "false",
        "OrderQuantityMinimum": "36.000",
        "IsBasePriceDeclarationRelevant": "false",
        "IsTradeItemSeasonal": "false",
        "hasImg": "true",
        "IsMeasured": "true",
        "ProductStatus": "mainAssortment",
        "DateResetProductStatus": null,
        "Season": null,
        "Collection": null,
        "Size": null,
        "Color": null,
        "Fragrance": null,
        "TypeProduct": null,
        "TypePollution": null,
        "TypeWash": null,
        "ApplicationTime": null,
        "Classification": null,
        "Density": null,
        "Gender": null,
        "TypeUsing": null,
        "SortingLaundry": null,
        "TypeHair": null,
        "TypeSurface": null,
        "TypeSkin": null,
        "Age": null,
        "ChildWeight": null,
        "HazardClass": null,
        "CustomAttributeExists": false,
        "PromotionalTypeCode": null,
        "NonPromotionalItem": null,
        "FreeQuantityOfProduct": null,
        "FreeQuantityOfProductUoM": null,
        "FreeQuantityOfNextLowerLevelTradeItem": null,
        "FreeQuantityOfNextLowerLevelTradeItemUoM": null,
        "IsPriceOnPack": "false",
        "VATRate": "20",
        "VATRateRU": null,
        "EffectiveDate": "17.07.2017",
        "PublicationDate": "17.07.2017",
        "StartAvailabilityDateTime": "30.03.2016",
        "EndAvailabilityDateTime": null,
        "ConsumerAvailabilityDateTime": "30.03.2016 16:38",
        "IsBlocked": "false",
        "BlockingStartDate": null,
        "BlockingEndDate": null,
        "DescriptionBlockingText": null,
        "IsActual": "false",
        "ActualityDate": "19.05.2016",
        "IsSample": "false",
        "LastPhotoDate": "23.03.2016 17:06",
        "LastMeasurementDate": "06.04.2016 10:41",
        "BrickAttributes": null,
        "Images": [
          {
            "name": "03228020490329_p.jpg",
            "isPlanogram": 1,
            "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/03228020490329/images/600x600_03228020490329_p.jpg"
          },
          {
            "name": "03228020490329_1.jpg",
            "isPlanogram": 0,
            "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/03228020490329/images/600x600_03228020490329_1.jpg"
          },
          {
            "name": "03228020490329_2.jpg",
            "isPlanogram": 0,
            "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/03228020490329/images/600x600_03228020490329_2.jpg"
          },
          {
            "name": "03228020490329_3.jpg",
            "isPlanogram": 0,
            "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/03228020490329/images/600x600_03228020490329_3.jpg"
          }
        ],
        "Certificates": null,
        "InformationProviderLogo": "http://ucat-test.s3-eu-west-1.amazonaws.com/clients.public/0000029/logo/logo-4820096010006.jpg",
        "IsAnInvoiceUnit": "true",
        "PublicURL": {
          "ru": "https://ucat.com.ua/ru/postavshchiki/dchp-laktalis-ukraina/syr-president-cheddar-plavlenyi-dlya-burgerov-40-20g10sht-200g-3228020490329",
          "uk": "https://ucat.com.ua/uk/postachalnyky/dchp-laktalis-ukraina/syr-president-cheddar-plavlenyi-dlia-burheriv-40-20h10sht-200h-3228020490329",
          "en": "https://ucat.com.ua/en/suppliers/sc-lactalis-ukraine/syr-president-cheddar-plavlenyi-dlya-burgerov-40-20g10sht-200g-3228020490329"
        }
      },
      "QuantityOfChild": 6,
      "UnitDescriptor": "PREPACK",
      "Brick": null,
      "FunctionalNameTextRu": "Сыры/Творог/Заменители сыра и творога (длительного хранения)",
      "FunctionalNameTextUk": "Сир / Замінник сиру (тривалого зберігання)",
      "FunctionalNameTextEn": "Cheese/Cheese Substitutes (Shelf Stable)",
      "UkrZed": "0406 30 31 00",
      "DKPP": "10.51.40-70.00",
      "UkrMonopoly": null,
      "UkrStatistics": 222,
      "FirstOptAnnualReport": null,
      "FirstRtReport": null,
      "AlcoholClass": null,
      "DSTU": null,
      "TU": null,
      "GSTU": null,
      "SOU": null,
      "TargetMarketCountryCode": 804,
      "TargetMarketSubDivisionCode": null,
      "CheeseFat": null,
      "GeneticallyModified": null,
      "CaloricValue": null,
      "Fats": null,
      "Carbs": null,
      "Proteins": null,
      "PercentageOfAlcohol": null,
      "Sugar": null,
      "IsDiabetic": "false",
      "WidthValue": "0.104",
      "WidthUoM": "MR",
      "DepthValue": "0.186",
      "DepthUoM": "MR",
      "HeightValue": "0.092",
      "HeightUoM": "MR",
      "NetWeightValue": "1.2480",
      "NetWeightUoM": "KG",
      "ToleranceMinValue": null,
      "ToleranceMinUoM": null,
      "ToleranceMaxValue": null,
      "ToleranceMaxUoM": null,
      "GrossWeightValue": "1.2800",
      "GrossWeightUoM": "KG",
      "NetContentValue": "6.0000",
      "NetContentUoM": "PC",
      "DryMassOfMainProductValue": null,
      "DryMassOfMainProductUoM": null,
      "QuantityOfBaseUnit": 6,
      "IsPackagingMarkedReturnable": "false",
      "PackagingType": "CAS",
      "PackagingMaterial": "104",
      "TypeOfProductPlacementInBox": 2,
      "OrientationTypeOnShelf": null,
      "NumberOfBaseUnitsInPackagingInWidth": null,
      "NumberOfBaseUnitsInPackagingInDepth": null,
      "NumberOfBaseUnitsInPackagingInHeight": null,
      "PackagingPerforationType": null,
      "MinimumLifespanFromArrival": null,
      "MinimumLifespanFromProduction": null,
      "MinimumLifespanFromProductionValue": null,
      "MinimumLifespanFromProductionUoM": null,
      "StorageHandlingTemperatureMaximum": null,
      "StorageHandlingTemperatureMinimum": null,
      "OpenedTradeItemLifespanValue": null,
      "OpenedTradeItemLifespanUoM": "DA",
      "LifespanFromProductionGroup1Value": null,
      "MaximumStorageHandlingTemperatureGroup1": null,
      "MinimumStorageHandlingTemperatureGroup1": null,
      "LifespanFromProductionGroup2Value": null,
      "MaximumStorageHandlingTemperatureGroup2": null,
      "MinimumStorageHandlingTemperatureGroup2": null,
      "LifespanFromProductionGroup3Value": null,
      "MaximumStorageHandlingTemperatureGroup3": null,
      "MinimumStorageHandlingTemperatureGroup3": null,
      "LifespanFromProductionGroup4Value": null,
      "MaximumStorageHandlingTemperatureGroup4": null,
      "MinimumStorageHandlingTemperatureGroup4": null,
      "WarrantyPeriodValue": null,
      "WarrantyPeriodUoM": null,
      "DeliveryToMarketTemperatureMaximum": null,
      "DeliveryToMarketTemperatureMinimum": null,
      "ConsumerUsageStorageInstructionsRu": null,
      "ConsumerUsageStorageInstructionsUk": null,
      "ConsumerUsageStorageInstructionsEn": null,
      "Wetness": null,
      "NonGTINPalletDepthValue": null,
      "NonGTINPalletDepthUoM": "MR",
      "NonGTINPalletWidthValue": null,
      "NonGTINPalletWidthUoM": "MR",
      "NonGTINPalletHeightValue": null,
      "NonGTINPalletHeightUoM": "MR",
      "NonGTINPalletLayerHeightValue": null,
      "NonGTINPalletLayerHeightUoM": "MR",
      "NonGTINPalletGrossWeightValue": null,
      "NonGTINPalletGrossWeightUoM": "KG",
      "NonGTINPalletAdditionalWeightValue": null,
      "NonGTINPalletAdditionalWeightUoM": null,
      "NonGTINPalletLayerWeightValue": null,
      "NonGTINPalletType": null,
      "QuantityOfTradeItemsPerPallet": null,
      "QuantityOfLayersPerPallet": null,
      "QuantityOfTradeItemsPerPalletLayer": null,
      "NonGTINPalletTermsAndConditions": null,
      "NonGTINPalletStackingFactor": null,
      "NonGTINPalletNetHeightValue": null,
      "NonGTINPalletNetHeightUoM": "MR",
      "NonGTINPalletNetWeightValue": null,
      "NonGTINPalletNetWeightUoM": "KG",
      "IsAPrivateLabel": "false",
      "IsAConsumerUnit": "false",
      "IsTradeItemABaseUnit": "false",
      "IsAnOrderableUnit": "false",
      "IsADespatchUnit": "false",
      "IsOrderQuantityMinimumMultiples": null,
      "IsAVariableUnit": "false",
      "IsUnitWithAverageValues": "false",
      "OrderQuantityMinimum": null,
      "IsBasePriceDeclarationRelevant": "false",
      "IsTradeItemSeasonal": "false",
      "hasImg": "true",
      "IsMeasured": "true",
      "ProductStatus": "mainAssortment",
      "DateResetProductStatus": null,
      "Season": null,
      "Collection": null,
      "Size": null,
      "Color": null,
      "Fragrance": null,
      "TypeProduct": null,
      "TypePollution": null,
      "TypeWash": null,
      "ApplicationTime": null,
      "Classification": null,
      "Density": null,
      "Gender": null,
      "TypeUsing": null,
      "SortingLaundry": null,
      "TypeHair": null,
      "TypeSurface": null,
      "TypeSkin": null,
      "Age": null,
      "ChildWeight": null,
      "HazardClass": null,
      "CustomAttributeExists": false,
      "PromotionalTypeCode": null,
      "NonPromotionalItem": null,
      "FreeQuantityOfProduct": null,
      "FreeQuantityOfProductUoM": null,
      "FreeQuantityOfNextLowerLevelTradeItem": null,
      "FreeQuantityOfNextLowerLevelTradeItemUoM": null,
      "IsPriceOnPack": "false",
      "VATRate": "20",
      "VATRateRU": null,
      "EffectiveDate": "08.04.2016",
      "PublicationDate": "08.04.2016",
      "StartAvailabilityDateTime": "30.03.2016 16:54",
      "EndAvailabilityDateTime": null,
      "ConsumerAvailabilityDateTime": "30.03.2016 16:54",
      "IsBlocked": "false",
      "BlockingStartDate": null,
      "BlockingEndDate": null,
      "DescriptionBlockingText": null,
      "IsActual": "false",
      "ActualityDate": "19.05.2016",
      "IsSample": "false",
      "LastPhotoDate": "05.04.2016 14:11",
      "LastMeasurementDate": "06.04.2016 10:42",
      "BrickAttributes": null,
      "Images": [
        {
          "name": "prod_00000083111_p.jpg",
          "isPlanogram": 1,
          "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/00000083111_prod/images/600x600_prod_00000083111_p.jpg"
        },
        {
          "name": "prod_00000083111_1.jpg",
          "isPlanogram": 0,
          "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/00000083111_prod/images/600x600_prod_00000083111_1.jpg"
        }
      ],
      "InformationProviderLogo": "http://ucat-test.s3-eu-west-1.amazonaws.com/clients.public/0000029/logo/logo-4820096010006.jpg",
      "IsAnInvoiceUnit": "false",
      "PublicURL": null
    },
    "QuantityOfChild": 6,
    "UnitDescriptor": "CASE",
    "Brick": null,
    "FunctionalNameTextRu": "Сыры/Творог/Заменители сыра и творога (длительного хранения)",
    "FunctionalNameTextUk": "Сир / Замінник сиру (тривалого зберігання)",
    "FunctionalNameTextEn": "Cheese/Cheese Substitutes (Shelf Stable)",
    "UkrZed": "0406 30 31 00",
    "DKPP": "10.51.40-70.00",
    "UkrMonopoly": null,
    "UkrStatistics": 222,
    "FirstOptAnnualReport": null,
    "FirstRtReport": null,
    "AlcoholClass": null,
    "DSTU": null,
    "TU": null,
    "GSTU": null,
    "SOU": null,
    "TargetMarketCountryCode": 804,
    "TargetMarketSubDivisionCode": null,
    "CheeseFat": null,
    "GeneticallyModified": null,
    "CaloricValue": null,
    "Fats": null,
    "Carbs": null,
    "Proteins": null,
    "PercentageOfAlcohol": null,
    "Sugar": null,
    "IsDiabetic": "false",
    "WidthValue": "0.218",
    "WidthUoM": "MR",
    "DepthValue": "0.292",
    "DepthUoM": "MR",
    "HeightValue": "0.194",
    "HeightUoM": "MR",
    "NetWeightValue": "7.6800",
    "NetWeightUoM": "KG",
    "ToleranceMinValue": null,
    "ToleranceMinUoM": null,
    "ToleranceMaxValue": null,
    "ToleranceMaxUoM": null,
    "GrossWeightValue": "7.8400",
    "GrossWeightUoM": "KG",
    "NetContentValue": "6.0000",
    "NetContentUoM": "PC",
    "DryMassOfMainProductValue": null,
    "DryMassOfMainProductUoM": null,
    "QuantityOfBaseUnit": 36,
    "IsPackagingMarkedReturnable": "false",
    "PackagingType": "CAS",
    "PackagingMaterial": "104",
    "TypeOfProductPlacementInBox": 1,
    "OrientationTypeOnShelf": null,
    "NumberOfBaseUnitsInPackagingInWidth": null,
    "NumberOfBaseUnitsInPackagingInDepth": null,
    "NumberOfBaseUnitsInPackagingInHeight": null,
    "PackagingPerforationType": null,
    "MinimumLifespanFromArrival": null,
    "MinimumLifespanFromProduction": null,
    "MinimumLifespanFromProductionValue": null,
    "MinimumLifespanFromProductionUoM": null,
    "StorageHandlingTemperatureMaximum": null,
    "StorageHandlingTemperatureMinimum": null,
    "OpenedTradeItemLifespanValue": null,
    "OpenedTradeItemLifespanUoM": "DA",
    "LifespanFromProductionGroup1Value": null,
    "MaximumStorageHandlingTemperatureGroup1": null,
    "MinimumStorageHandlingTemperatureGroup1": null,
    "LifespanFromProductionGroup2Value": null,
    "MaximumStorageHandlingTemperatureGroup2": null,
    "MinimumStorageHandlingTemperatureGroup2": null,
    "LifespanFromProductionGroup3Value": null,
    "MaximumStorageHandlingTemperatureGroup3": null,
    "MinimumStorageHandlingTemperatureGroup3": null,
    "LifespanFromProductionGroup4Value": null,
    "MaximumStorageHandlingTemperatureGroup4": null,
    "MinimumStorageHandlingTemperatureGroup4": null,
    "WarrantyPeriodValue": null,
    "WarrantyPeriodUoM": null,
    "DeliveryToMarketTemperatureMaximum": null,
    "DeliveryToMarketTemperatureMinimum": null,
    "ConsumerUsageStorageInstructionsRu": null,
    "ConsumerUsageStorageInstructionsUk": null,
    "ConsumerUsageStorageInstructionsEn": null,
    "Wetness": null,
    "NonGTINPalletDepthValue": "1.200",
    "NonGTINPalletDepthUoM": "MR",
    "NonGTINPalletWidthValue": "0.800",
    "NonGTINPalletWidthUoM": "MR",
    "NonGTINPalletHeightValue": "1.503",
    "NonGTINPalletHeightUoM": "MR",
    "NonGTINPalletLayerHeightValue": "0.194",
    "NonGTINPalletLayerHeightUoM": "MR",
    "NonGTINPalletGrossWeightValue": "733.9400",
    "NonGTINPalletGrossWeightUoM": "KG",
    "NonGTINPalletAdditionalWeightValue": null,
    "NonGTINPalletAdditionalWeightUoM": null,
    "NonGTINPalletLayerWeightValue": "101.9200",
    "NonGTINPalletType": 11,
    "QuantityOfTradeItemsPerPallet": 91,
    "QuantityOfLayersPerPallet": 7,
    "QuantityOfTradeItemsPerPalletLayer": 13,
    "NonGTINPalletTermsAndConditions": 2,
    "NonGTINPalletStackingFactor": 1,
    "NonGTINPalletNetHeightValue": "1.358",
    "NonGTINPalletNetHeightUoM": "MR",
    "NonGTINPalletNetWeightValue": "713.44",
    "NonGTINPalletNetWeightUoM": "KG",
    "IsAPrivateLabel": "false",
    "IsAConsumerUnit": "false",
    "IsTradeItemABaseUnit": "false",
    "IsAnOrderableUnit": "false",
    "IsADespatchUnit": "true",
    "IsOrderQuantityMinimumMultiples": null,
    "IsAVariableUnit": "false",
    "IsUnitWithAverageValues": "false",
    "OrderQuantityMinimum": null,
    "IsBasePriceDeclarationRelevant": "false",
    "IsTradeItemSeasonal": "false",
    "hasImg": "true",
    "IsMeasured": "true",
    "ProductStatus": "mainAssortment",
    "DateResetProductStatus": null,
    "Season": null,
    "Collection": null,
    "Size": null,
    "Color": null,
    "Fragrance": null,
    "TypeProduct": null,
    "TypePollution": null,
    "TypeWash": null,
    "ApplicationTime": null,
    "Classification": null,
    "Density": null,
    "Gender": null,
    "TypeUsing": null,
    "SortingLaundry": null,
    "TypeHair": null,
    "TypeSurface": null,
    "TypeSkin": null,
    "Age": null,
    "ChildWeight": null,
    "HazardClass": null,
    "CustomAttributeExists": false,
    "PromotionalTypeCode": null,
    "NonPromotionalItem": null,
    "FreeQuantityOfProduct": null,
    "FreeQuantityOfProductUoM": null,
    "FreeQuantityOfNextLowerLevelTradeItem": null,
    "FreeQuantityOfNextLowerLevelTradeItemUoM": null,
    "IsPriceOnPack": "false",
    "VATRate": "20",
    "VATRateRU": null,
    "EffectiveDate": "06.04.2016",
    "PublicationDate": "06.04.2016",
    "StartAvailabilityDateTime": "30.03.2016 16:38",
    "EndAvailabilityDateTime": null,
    "ConsumerAvailabilityDateTime": "30.03.2016 16:38",
    "IsBlocked": "false",
    "BlockingStartDate": null,
    "BlockingEndDate": null,
    "DescriptionBlockingText": null,
    "IsActual": "false",
    "ActualityDate": "19.05.2016",
    "IsSample": "false",
    "LastPhotoDate": "23.03.2016 17:07",
    "LastMeasurementDate": "06.04.2016 10:42",
    "BrickAttributes": null,
    "Images": [
      {
        "name": "03228025025427_p.jpg",
        "isPlanogram": 1,
        "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/03228025025427/images/600x600_03228025025427_p.jpg"
      },
      {
        "name": "03228025025427_1.jpg",
        "isPlanogram": 0,
        "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/03228025025427/images/600x600_03228025025427_1.jpg"
      },
      {
        "name": "03228025025427_2.jpg",
        "isPlanogram": 0,
        "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/03228025025427/images/600x600_03228025025427_2.jpg"
      },
      {
        "name": "03228025025427_3.jpg",
        "isPlanogram": 0,
        "link": "http://ucat-test.s3-eu-west-1.amazonaws.com/products.public/03228025025427/images/600x600_03228025025427_3.jpg"
      }
    ],
    "InformationProviderLogo": "http://ucat-test.s3-eu-west-1.amazonaws.com/clients.public/0000029/logo/logo-4820096010006.jpg",
    "IsAnInvoiceUnit": "false",
    "PublicURL": null
  }
]
```
