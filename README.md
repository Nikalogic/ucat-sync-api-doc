# Документация по работе с REST API в каталоге товаров uCat #

## Содержание ##

* Назначение
* Описание
* Принцип работы с REST API
  * Типы запросов к API
  * Коды ответов
  * Авторизация пользователей
* [Доступные запросы к API](https://github.com/xainse/ucat-sync-api-doc#Доступные-запросы-к-api)
  * 1) Получить список Поставщиков
  * 2) Товарные позиции одного Поставщика
  * 3) Подробная информация о товарной позиции
  * 4) Изображения одного продукта
  * 5) Передать статус по товару
  * 6) Поиск товарной позиции по штрихкоду
  * 7) Иморт файла с артикулами торговой сети
  * 8) Список изменившихся товаров
  * 9) Список доступных товарных позиций, на которые оформленна подписка
  * 10) Передать статус товара в Торговой Сети
  * 11) Получение информации из справочников
* [Пример ответа по одному товару](https://github.com/xainse/ucat-sync-api-doc#Пример-ответа-по-одному-товару)


## Назначение ##
[REST](https://uk.wikipedia.org/wiki/REST) [API](https://uk.wikipedia.org/wiki/Прикладний_програмний_інтерфейс) в каталоге uCat предназначен для работы с системами партнеров автоматически с доступом через web. 
Данные при работе через REST API отдаются в формате JSON. Взаимодействие с REST API идет в синхронном режиме,на каждый запрос клиент получает ответ с результатом запроса.

## Описание ##
Для доступа к данным каталога uCat, через REST API партнеру выдается *ключ доступа*. Ключ обеспечивает идентификацию клиента и определяет уровень доступа к данным.
Для того, что-бы получить ключ, нужно обратиться к администрации каталога.
Ключ представляет собой символьную строку.

### Принцып работы с REST API###
Что-бы получить информацию из каталога, нужно обратиться по определенной ссылке, с указанием параметров.
Например: Данный запрос выводит список производителей, зарегистрированных в каталоге. 
<pre>
https://client.ucat.com.ua/api-sync/v1/manufacturers?authKey=auth_key
</pre>

**Пример ответа:**
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

**Описание частей запроса**

Параметры | Описание
--------- | ---------
https:// | Защищенный протокол передачи данных
client.ucat.com.ua/ | текущий корень каталога uCat
api-sync/ | корень приложения, отвечающего за API
v1/ | версия REST API, в приделах одной версии синтаксис запросов при обращении существенно не меняется
manufacturers | запрос на получение списка производителей
authKey | ключ доступа

###Типы запросов к API###
* GET - используется для получения информации;
* POST - используется для отправки информации в каталог;

###Коды ответов###
При запросе к каталогу веб сервер в заголовке отдает [код состояния HTTP](https://ru.wikipedia.org/wiki/Список_кодов_состояния_HTTP), вместе с телом ответа.

Код | Значение
--- | --------
200 | Код возвращается при каждом удачном обращении к API
401 | Ошибка авторизации, неправильный ключ
403 | Доступ к информации закры (но она есть в каталоге)
404 | В каталоге не найденные данные по вашему запросу

###Авторизация пользователей###
Авторизация пользователей проходит по хеш-ключу, который передается в GET параметре *authKey*
* хеш-ключ выдается администраторами каталога  uCat;
* параметр **authKey** должен передаваться в каждом запросе к REST API;
* при работе с REST API сесии не поддерживаются.  

###Доступные запросы к API###
Параметры запросов
* **?authKey=[ключ_авторизации]** - Ключ добавляется к каждому запросу к API. Например https://client.ucat.com.ua/api-sync/v1/manufacturers?authKey=<[ключ_авторизации]. Ключ выдается администратором.
* **GLN-номер компании** - GLN [Глобальный номер размещения](http://gs1.org.ua/uk/gs1/identificationbarcode/gln.csp), международный номер места производства выдается организацией GS1  
* **GTIN** - штрихкод товара. GTIN - Global Trade Item Number

Название | Тип | Формат | Комментарий
---------|-----|--------|------------
1. Список поставщиков |GET|api-sync/v1/manufacturers|Работает без подписки. Выводить поставщиков зарегистрированных в каталоге
2. Продукты одного Поставщика|GET|api-sync/v1/manufacturers/[GLN-номер компании]/products|Выводиться список всех опубликованных в каталоге товаров поставщика
3. Товарные позиции одного Поставщика|GET|api-sync/v1/manufacturers/[GLN-номер компании]/products/[GTIN]|Выдается информация о товаре и о его упаковках. Информация выдается в иерархическом виде. На верхнем уровне самая большая упаковка, на нижнем уровне сам товар.
4. Изображения одного продукта|GET|api-sync/v1/manufacturers/[GLN-номер компании]/products/[GTIN]/images|Инфмация о доступных изображениях товара в разном разрешениях.
5. Передать статус по товару|POST|api-sync/v1/response|Передать в каталог uCat статус полученного товара в системе клиента.
6. Поиск по оштрихкоду|GET|api-sync/v1/products/[GTIN]/search|Поиск по товарам, которые доступны для вас в каталоге
7. Импорт артикулов|POST|api-sync/v1/upload-sku|Для синхронизации асортимента, клиент может загрузить соответствие своих артикулов к штрихкодам (GTIN).
8. Список изменившихся товаров|GET|api-sync/v1/last-changed-gtins|Получить список товаров, которые изменились с последнего получения информации по этим товарам
9. Кол-во подписок|GET|api-sync/v1/get-count-subscribe-product|Запрос возвращает количество различных подписок
10. Список GTIN базовых единиц, на которые была оформлена подписка|GET|api-sync/v1/get-subscribe-product-gtin|После оформлении подписки, клиент может проверять какие товары ему доступны по подписке
11. Получить значение кода справочника|GET|api-sync/v1/handbook/search/<код справочника>|В ответе расшифровка значения и какой это справочник

###4. Получить информацию только о фотографиях

В запросе на получение фотографий, нужно указать GLN поставщика и GTIN (штрихкод) товара.

**Формат запроса**
```html
https://clients.ucat.com.ua/api-sync/v1/manufacturers/[GLN-номер компании]/products/[GTIN]/images?authKey=<ключ авторизации>
```
Тип запроса: **GET**

**Пример ответа в формате JSON**
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

###5. Передать статус обработки товара торговой сетью###
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
    "message": "You donn\’t recive product of such version"
  }
```

###6. Поиск товарной позиции по штрихкоду###
Пользователь REST API может пользоваться поиском по товарам по штрихкоду.

**Формат запроса:**

```html
    https://client.ucat.com.ua/api-sync/v1/products/[GTIN]/search?authKey=[ключ_авторизации]>
```

Тип запроса: **GET**

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
  "message": "Data of the product is not available to you"
}
```

4) Товара нет в каталоге
- код ответа сервера: 404 Not Found
- тело ответа: 

```javascript
  {
    "code" : "404 Not Found", 
    "message": "Not exists product"
  }
```


###7. Импорт артикулов торговой сети в базу uCat###
Торговая сеть имеет возможность загрузить в каталог внутриние артикулы товаров и привязать их к штрихкодам в базе.
Для загрузки нужно передать в каталог CSV файл в специальном формате с данными. 

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
    "message":"Файл загружен"
  }
```

2) Ошибка при загрузке файла:
- код ответа сервера: 400 Bad Request 
- тело ответа: 

```javascript
  {
    "success": "false",
    "message": "Файл не загружен"
  }
```

###8. Список изменившихся товаров###

Данный запрос используется для того, чтобы ритейлер не проверял каждый раз все товары и не сравнивал их текущие значения со старыми значениями добавлен запрос к API который показывает список штрих-кодов, которые добавились или изменились за время, прошедшее с того момента, когда ритейлера забирал данные последний раз.

**Формат запроса:**
```html
  https://client.ucat.com.ua/api-sync/v1/last-changed-gtins?authKey=[ключ_авторизации]
```
Тип запроса: **GET**

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

###9. Количество подписок###
Запрос возвращает количество оформленных подписок в каталоге. 

**Формат запроса:**
```html
  https://client.ucat.com.ua/api-sync/v1/get-count-subscribe-product?authKey=[ключ_авторизации]
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


###10. Список доступных GTIN базовых единиц на которые была оформлена подписка###

Запрос возвращает все доступные штрихкоды из каталога для Торговой Сети.

**Формат запроса:**
```html
  https://client.ucat.com.ua/api-sync/v1/get-count-subscribe-product?authKey=[ключ_авторизации]

``` 

**Пример ответа:**
1) Список доступных штрихкодов из каталога
- код ответа сервера: 200 OK
- тело ответа: 

```javascript
[
  "GTIN1",
  "GTIN2",
  "GTIN3",
    ...
  "GTIN5"
]
```
2) Если у Торговой Сети не оформленна подписка на товары или товары не доступны по другим причинам, выводиться ошибка
- код ответа сервера: 404 Not Found
- тело ответа: 

```javascript
  {
    "code" : "404 Not Found", 
    "message": "Not isset subscription"
  }
```

###11. Расшифровка значения справочника###
Можно передать код из любого справочника и в ответ получить название справочника и расшифровку значения.

**Формат запроса**
```html
https://client.ucat.com.ua/api-sync/v1/handbook/search/[код справочника]?authKey=<код авторизации>
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

###Пример ответа по одному товару###
Ответы отдаются в формате JSON. [Посмотреть пример по товару GTIN-4820043334735](GTIN-4820043334735.json)

Товар содежит информацию о товаре 4820043334735

На верхнем уровне идет информация об упаковке, например:
* Ящик штрихкод 4820043334742, в котором находится 16 коробот товара
 * Товар, штрихкод 4820043334735 

```javascript
[
  {
    "GTIN": "4820043334742", //Штрихкод товара
    "ReplacedTradeItemIdentification": null, // Штрихкод старого товара, который заменяет текущий      
    "InformationProviderGLN": "9864276816716", // GTIN компании (поставщика), которая предоставила информацию о товаре 
    "InternalSupplierCode": null, // Артикул товара (SKU) в системе поставщика
    "BarCodeType": "EAN_UCC_13_SYMBOL", // тип штрихкода товара
    "LanguageSpecificBrandNameRu": "Марка Промо", // Название бренда на русском языке
    "LanguageSpecificBrandNameUk": "Марка Промо", // Название бренда на украинском языке
    "LanguageSpecificBrandNameEn": "Marka Promo", // Название бренда на английском языке
    "LanguageSpecificShortBrandNameRu": "Мар Про", // Короткое название бренда на русском языке
    "LanguageSpecificShortBrandNameUk": "Мар Про", // Короткое название бренда на украинском языке
    "LanguageSpecificShortBrandNameEn": "Mar Pro", // Короткое название бренда на английском языке
    "DescriptionTextRu": "Пряники Marka Promo Яблочный сад с начинкой 0,4кг", // Полное название товара на русском 
    "DescriptionTextUk": "Пряники Marka Promo Яблуневий сад з начинкою 0,4кг", // Полное название товара на украинском
    "DescriptionTextEn": null, // Полное название товара на английском
    "ShortDescriptionTextRu": "Пряники Mark Pro Яблоч сад с начин 0,4кг", // Короткое название товара на русском языке
    "ShortDescriptionTextUk": "Пряники Mark Pro Яблун сад з начин 0,4кг", // Короткое название товара на украинском языке
    "ShortDescriptionTextEn": null, // Короткое название товара на английском языке
    "FunctionalNameTextRu": "Печенье\/Сухари (длительного хранения)", // Название категории товара на русском языке
    "FunctionalNameTextUk": "Печиво \/ Сухарі (тривалого зберігання)", // Название категории товара на украинском языке
    "FunctionalNameTextEn": "Biscuits\/Cookies (Shelf Stable)", // Название категории товара на английском языке
    "BrandId": "1918", // Код бренда в справочнике Брендов каталога uCat
    "BrandName": "Marka Promo", // Название бренда в каталоге
    "ShortBrandName": null, // Короткое название бренда в каталоге
    "SubBrandId": null, // Код суб.бренда в каталоге uCat
    "SubBrand": null, // Название суб.бренда в каталоге
    "CountryOfOrigin": "804", // Код страны производства по международному справочнику
    "CountryOfLastProcessing": "804", // Код страны последней обработки товара
    "ManufacturerUcatId": "2810", // Код производителя в каталоге uCat
    "ManufacturerGLN": null, // GLN номер производителя товара
    "ManufacturerNameRu": "ПАО \"Запорожский хлебокомбинат №1\"", // Название производителя на русском языке
    "ManufacturerNameUk": "ПАТ \"Запорізький хлібокомбінат №1\"", // Название производителя на украинском языке
    "ManufacturerNameEn": "PJSC \"Zaporizkyy khlibokombinat №1\"", // Название производителя на английском языке
    "ManufacturingPlantGLN": null, 
    "AdditionalTradeItemDescriptionRu": null, // Дополнительное описание товара на русском языке
    "AdditionalTradeItemDescriptionUk": null, // Дополнительное описание товара на украинском языке
    "AdditionalTradeItemDescriptionEn": null, // Дополнительное описание товара на английском языке
    "version": "9", // Версия изменений товара в каталоге uCat (сколько раз вносились изменения в данный товар)
    "OriginalDescriptionRu": null, // Оринигалное название с упаковки на русском
    "OriginalDescriptionUk": null,
    "OriginalDescriptionEn": null,
    "DistributorNameRu": null,
    "DistributorNameUk": null,
    "DistributorNameEn": null,
    "ImporterNameRu": null,
    "ImporterNameUk": null,
    "ImporterNameEn": null,
    "LogisticOperatorNameRu": null,
    "LogisticOperatorNameUk": null,
    "LogisticOperatorNameEn": null,
    "ProductionCapacityAddressRu": null,
    "ProductionCapacityAddressUk": null,
    "ProductionCapacityAddressEn": null,
    "VariantNumber": null,
    "VariantName": null,
    "ChildGTIN": {
      "GTIN": "4820043334735",
      "ReplacedTradeItemIdentification": null,
      "InformationProviderGLN": "9864276816716",
      "InternalSupplierCode": "190373",
      "BarCodeType": "EAN_UCC_13_SYMBOL",
      "LanguageSpecificBrandNameRu": "Марка Промо",
      "LanguageSpecificBrandNameUk": "Марка Промо",
      "LanguageSpecificBrandNameEn": "Marka Promo",
      "LanguageSpecificShortBrandNameRu": "Мар Про",
      "LanguageSpecificShortBrandNameUk": "Мар Про",
      "LanguageSpecificShortBrandNameEn": "Mar Pro",
      "DescriptionTextRu": "Пряники Marka Promo Яблочный сад с начинкой 0,4кг",
      "DescriptionTextUk": "Пряники Marka Promo Яблуневий сад з начинкою 0,4кг",
      "DescriptionTextEn": null,
      "ShortDescriptionTextRu": "Пряники Mark Pro Яблоч сад с начин 0,4кг",
      "ShortDescriptionTextUk": "Пряники Mark Pro Яблун сад з начин 0,4кг",
      "ShortDescriptionTextEn": null,
      "FunctionalNameTextRu": "Печенье\/Сухари (длительного хранения)",
      "FunctionalNameTextUk": "Печиво \/ Сухарі (тривалого зберігання)",
      "FunctionalNameTextEn": "Biscuits\/Cookies (Shelf Stable)",
      "BrandId": "1918",
      "BrandName": "Marka Promo",
      "ShortBrandName": null,
      "SubBrandId": null,
      "SubBrand": null,
      "CountryOfOrigin": "804",
      "CountryOfLastProcessing": "804",
      "ManufacturerUcatId": "2810",
      "ManufacturerGLN": null,
      "ManufacturerNameRu": "ПАО \"Запорожский хлебокомбинат №1\"",
      "ManufacturerNameUk": "ПАТ \"Запорізький хлібокомбінат №1\"",
      "ManufacturerNameEn": "PJSC \"Zaporizkyy khlibokombinat №1\"",
      "ManufacturingPlantGLN": null,
      "AdditionalTradeItemDescriptionRu": null,
      "AdditionalTradeItemDescriptionUk": "борошно пшеничне 2 с, цукор, начинка (14,0 %) (повидло яблучне (пюре яблучне, цукор, консервант кислота сорбінова), регулятор кислотності кислота лимонна, ароматизатор \"Яблуко\"), вода питна, сироп інвертний (цукор вода питна), жир рослинний \"Шортенінг кондитерський\" (жир рослинний гідрогенізований, рафінований дезодорований, олії пальмова та соняшникова рафіновані, дезодоровані, емульгатор моно та дигліцериди жирних кислот, антиоксиданти: бутил гідроксианізол, бутил гідрокситолуол), молоко сухе знежирене, розпушувачі тіста: гідрокарбонат амонію, бікарбонат натрію; ароматизатор \"Ванільно-вершковий\".",
      "AdditionalTradeItemDescriptionEn": null,
      "version": "10",
      "OriginalDescriptionRu": null,
      "OriginalDescriptionUk": null,
      "OriginalDescriptionEn": null,
      "DistributorNameRu": null,
      "DistributorNameUk": null,
      "DistributorNameEn": null,
      "ImporterNameRu": null,
      "ImporterNameUk": null,
      "ImporterNameEn": null,
      "LogisticOperatorNameRu": null,
      "LogisticOperatorNameUk": null,
      "LogisticOperatorNameEn": null,
      "ProductionCapacityAddressRu": null,
      "ProductionCapacityAddressUk": "Україна, 69032, м. Запоріжжя, вул. Верхня, буд.1",
      "ProductionCapacityAddressEn": null,
      "VariantNumber": null,
      "VariantName": null,
      "ChildGTIN": null,
      "QuantityOfChild": null,
      "UnitDescriptor": "BASE_UNIT_OR_EACH",
      "Brick": "10000161",
      "UkrZed": null,
      "UkrMonopoly": null,
      "UkrStatistics": "251",
      "FirstOptAnnualReport": "298",
      "FirstRtReport": null,
      "TargetMarketCountryCode": "804",
      "TargetMarketSubDivisionCode": null,
      "CheeseFat": null,
      "GeneticallyModified": "FREE_FROM",
      "CaloricValue": "359",
      "Fats": "5.9",
      "Carbs": "71.7",
      "Proteins": "6.1",
      "PercentageOfAlcohol": null,
      "Sugar": null,
      "IsDiabetic": "false",
      "WidthValue": "0.166",
      "WidthUoM": "MR",
      "DepthValue": "0.070",
      "DepthUoM": "MR",
      "HeightValue": "0.235",
      "HeightUoM": "MR",
      "NetWeightValue": "0.4000",
      "NetWeightUoM": "KG",
      "ToleranceMinValue": "3.0000",
      "ToleranceMinUoM": "P1",
      "ToleranceMaxValue": null,
      "ToleranceMaxUoM": null,
      "GrossWeightValue": "0.3950",
      "GrossWeightUoM": "KG",
      "NetContentValue": "0.4000",
      "NetContentUoM": "KG",
      "IsPackagingMarkedReturnable": "false",
      "PackagingType": "U07",
      "PackagingMaterial": "U06",
      "MinimumLifespanFromArrival": null,
      "MinimumLifespanFromProduction": "61",
      "MinimumLifespanFromProductionValue": "2.0000",
      "MinimumLifespanFromProductionUoM": "MON",
      "OpenedTradeItemLifespanValue": null,
      "OpenedTradeItemLifespanUoM": "DA",
      "LifespanFromProductionGroup1Value": "60.0000",
      "MaximumStorageHandlingTemperatureGroup1": "23.0",
      "MinimumStorageHandlingTemperatureGroup1": "13.0",
      "LifespanFromProductionGroup2Value": null,
      "MaximumStorageHandlingTemperatureGroup2": null,
      "MinimumStorageHandlingTemperatureGroup2": null,
      "LifespanFromProductionGroup3Value": null,
      "MaximumStorageHandlingTemperatureGroup3": null,
      "MinimumStorageHandlingTemperatureGroup3": null,
      "LifespanFromProductionGroup4Value": null,
      "MaximumStorageHandlingTemperatureGroup4": null,
      "MinimumStorageHandlingTemperatureGroup4": null,
      "StorageHandlingTemperatureMaximum": "23.0",
      "StorageHandlingTemperatureMinimum": "13.0",
      "DeliveryToMarketTemperatureMaximum": null,
      "DeliveryToMarketTemperatureMinimum": null,
      "ConsumerUsageStorageInstructionsRu": null,
      "ConsumerUsageStorageInstructionsUk": "Зберігати в сухих, чистих, добре вентильованих приміщеннях, які не мають стороннього запаху, не заражені шкідниками хлібних запасів. Пряники не повинні зазнавати впливу сонячних променів, не дозволено зберігати з продуктами, які мають специфічний запах.",
      "ConsumerUsageStorageInstructionsEn": null,
      "Wetness": "75",
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
      "IsAConsumerUnit": "true",
      "IsTradeItemABaseUnit": "true",
      "IsAnOrderableUnit": "true",
      "IsADespatchUnit": "false",
      "IsAVariableUnit": "false",
      "OrderQuantityMinimum": "16.000",
      "IsBasePriceDeclarationRelevant": "false",
      "IsTradeItemSeasonal": "false",
      "hasImg": "true",
      "IsMeasured": "true",
      "Season": null,
      "Collection": null,
      "Size": null,
      "Color": null,
      "ProductStatus": "mainAssortment",
      "DateResetProductStatus": null,
      "DSTU": null,
      "TU": null,
      "PromotionalTypeCode": null,
      "NonPromotionalItem": null,
      "FreeQuantityOfProduct": null,
      "FreeQuantityOfProductUoM": null,
      "FreeQuantityOfNextLowerLevelTradeItem": null,
      "FreeQuantityOfNextLowerLevelTradeItemUoM": null,
      "IsPriceOnPack": "false",
      "VATRate": "20",
      "VATRateRU": null,
      "EffectiveDate": "25.02.2016",
      "PublicationDate": "25.02.2016",
      "StartAvailabilityDateTime": "29.12.2015",
      "EndAvailabilityDateTime": null,
      "ConsumerAvailabilityDateTime": "12.02.2016 10:28",
      "IsBlocked": "false",
      "BlockingStartDate": null,
      "BlockingEndDate": null,
      "DescriptionBlockingText": null,
      "BrickAttributes": null,
      "Images": [
        {
          "name": "04820043334735_p.jpg",
          "isPlanogram": "1",
          "link": "https:\/\/ucat-live.s3-eu-west-1.amazonaws.com\/products.public\/04820043334735\/images\/600x600_04820043334735_p.jpg"
        },
        {
          "name": "04820043334735_3.jpg",
          "isPlanogram": "0",
          "link": "https:\/\/ucat-live.s3-eu-west-1.amazonaws.com\/products.public\/04820043334735\/images\/600x600_04820043334735_3.jpg"
        },
        {
          "name": "04820043334735_5.jpg",
          "isPlanogram": "0",
          "link": "https:\/\/ucat-live.s3-eu-west-1.amazonaws.com\/products.public\/04820043334735\/images\/600x600_04820043334735_5.jpg"
        },
        {
          "name": "04820043334735_1.jpg",
          "isPlanogram": "0",
          "link": "https:\/\/ucat-live.s3-eu-west-1.amazonaws.com\/products.public\/04820043334735\/images\/600x600_04820043334735_1.jpg"
        }
      ],
      "Certificates": null,
      "InformationProviderLogo": null,
      "IsAnInvoiceUnit": "true"
    },
    "QuantityOfChild": "16",
    "UnitDescriptor": "CASE",
    "Brick": null,
    "UkrZed": null,
    "UkrMonopoly": null,
    "UkrStatistics": "251",
    "FirstOptAnnualReport": null,
    "FirstRtReport": null,
    "TargetMarketCountryCode": "804",
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
    "WidthValue": "0.296",
    "WidthUoM": "MR",
    "DepthValue": "0.392",
    "DepthUoM": "MR",
    "HeightValue": "0.240",
    "HeightUoM": "MR",
    "NetWeightValue": "6.3200",
    "NetWeightUoM": "KG",
    "ToleranceMinValue": null,
    "ToleranceMinUoM": null,
    "ToleranceMaxValue": null,
    "ToleranceMaxUoM": null,
    "GrossWeightValue": "6.6500",
    "GrossWeightUoM": "KG",
    "NetContentValue": "16.0000",
    "NetContentUoM": "PC",
    "IsPackagingMarkedReturnable": "false",
    "PackagingType": "CAS",
    "PackagingMaterial": "104",
    "MinimumLifespanFromArrival": null,
    "MinimumLifespanFromProduction": null,
    "MinimumLifespanFromProductionValue": null,
    "MinimumLifespanFromProductionUoM": null,
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
    "StorageHandlingTemperatureMaximum": null,
    "StorageHandlingTemperatureMinimum": null,
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
    "NonGTINPalletHeightValue": "1.825",
    "NonGTINPalletHeightUoM": "MR",
    "NonGTINPalletLayerHeightValue": "0.240",
    "NonGTINPalletLayerHeightUoM": "MR",
    "NonGTINPalletGrossWeightValue": "392.9000",
    "NonGTINPalletGrossWeightUoM": "KG",
    "NonGTINPalletLayerWeightValue": "53.2000",
    "NonGTINPalletType": "11",
    "QuantityOfTradeItemsPerPallet": "56",
    "QuantityOfLayersPerPallet": "7",
    "QuantityOfTradeItemsPerPalletLayer": "8",
    "NonGTINPalletTermsAndConditions": "2",
    "NonGTINPalletStackingFactor": "1",
    "NonGTINPalletNetHeightValue": "1.68",
    "NonGTINPalletNetHeightUoM": "MR",
    "NonGTINPalletNetWeightValue": "372.4",
    "NonGTINPalletNetWeightUoM": "KG",
    "IsAConsumerUnit": "false",
    "IsTradeItemABaseUnit": "false",
    "IsAnOrderableUnit": "false",
    "IsADespatchUnit": "true",
    "IsAVariableUnit": "false",
    "OrderQuantityMinimum": null,
    "IsBasePriceDeclarationRelevant": "false",
    "IsTradeItemSeasonal": "false",
    "hasImg": "true",
    "IsMeasured": "true",
    "Season": null,
    "Collection": null,
    "Size": null,
    "Color": null,
    "ProductStatus": "mainAssortment",
    "DateResetProductStatus": null,
    "DSTU": null,
    "TU": null,
    "PromotionalTypeCode": null,
    "NonPromotionalItem": null,
    "FreeQuantityOfProduct": null,
    "FreeQuantityOfProductUoM": null,
    "FreeQuantityOfNextLowerLevelTradeItem": null,
    "FreeQuantityOfNextLowerLevelTradeItemUoM": null,
    "IsPriceOnPack": "false",
    "VATRate": "20",
    "VATRateRU": null,
    "EffectiveDate": "24.02.2016",
    "PublicationDate": "24.02.2016",
    "StartAvailabilityDateTime": "29.12.2015",
    "EndAvailabilityDateTime": null,
    "ConsumerAvailabilityDateTime": "12.02.2016 10:31",
    "IsBlocked": "false",
    "BlockingStartDate": null,
    "BlockingEndDate": null,
    "DescriptionBlockingText": null,
    "BrickAttributes": null,
    "Images": [
      {
        "name": "04820043334742_p.jpg",
        "isPlanogram": "1",
        "link": "https:\/\/ucat-live.s3-eu-west-1.amazonaws.com\/products.public\/04820043334742\/images\/600x600_04820043334742_p.jpg"
      },
      {
        "name": "04820043334742_1.jpg",
        "isPlanogram": "0",
        "link": "https:\/\/ucat-live.s3-eu-west-1.amazonaws.com\/products.public\/04820043334742\/images\/600x600_04820043334742_1.jpg"
      },
      {
        "name": "04820043334742_2.jpg",
        "isPlanogram": "0",
        "link": "https:\/\/ucat-live.s3-eu-west-1.amazonaws.com\/products.public\/04820043334742\/images\/600x600_04820043334742_2.jpg"
      }
    ],
    "InformationProviderLogo": null,
    "IsAnInvoiceUnit": "false"
  }
]
```