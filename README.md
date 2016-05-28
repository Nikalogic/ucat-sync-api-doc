# Документация по работе с REST API в каталоге товаров uCat #

## Содержание ##

* Назначение
* Описание
* Принцип работы с REST API
  * Типы запросов к API
  * Коды ответов
* Доступные запросы к API
  * Получить список Поставщиков
  * Получить список товарных позиций Поставщика
  * Подробная информация о товарной позиции
  * Список изменившихся товарных позиций, с последнего обращения к API
  * Количество оформленных подписок в каталоге
  * Список доступных товарных позиций, на которые оформленна подписка
  * Передать статус товара в Торговой Сети
  * Загрузка файла с артикулами


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

**Результат**

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

###Доступные запросы к API###
Параметры запросов
* **?authKey=<ключ_авторизации>** - Ключ добавляется к каждому запросу к API. Например https://client.ucat.com.ua/api-sync/v1/manufacturers?authKey=<ключ_авторизации>. Ключ выдается администратором.
* **GLN-номер компании** - GLN [Глобальный номер размещения](http://gs1.org.ua/uk/gs1/identificationbarcode/gln.csp), международный номер места производства выдается организацией GS1  
* **GTIN** - штрихкод товара. GTIN - Global Trade Item Number

Название | Тип | Формат | Комментарий
---------|-----|--------|------------
Список поставщиков в каталоге|GET|api-sync/v1/manufacturers|Работает без подписки. Выводить поставщиков зарегистрированных в каталоге
Продукты одного поставщика|GET|api-sync/v1/manufacturers/<GLN-номер компании>/products|Выводиться список всех опубликованных в каталоге товаров поставщика
Информация об одном продукте|GET|api-sync/v1/manufacturers/<GLN-номер компании>/products/<GTIN>|Выдается информация о товаре и о его упаковках. Информация выдается в иерархическом виде. На верхнем уровне самая большая упаковка, на нижнем уровне сам товар.
Передать статус по полученному товару|POST|api-sync/v1/response|Передать в каталог uCat статус полученного товара в системе клиента.
Поиск товара по оштрихкоду|GET|api-sync/v1/products/<GTIN>/search|Поиск по товарам, которые доступны для вас в каталоге
Загрузкить файл с артикулами|POST|api-sync/v1/upload-sku|Для синхронизации асортимента, клиент может загрузить соответствие своих артикулов к штрихкодам (GTIN).
Список изменившихся товаров|GET|api-sync/v1/last-changed-gtins|Получить список товаров, которые изменились с последнего получения информации по этим товарам
Получить подписки|GET|api-sync/v1/get-count-subscribe-product|Запрос возвращает перечень подписок, которые оформил клиент.
Список GTIN базовых единиц, на которые была оформлена подписка|GET|api-sync/v1/get-subscribe-product-gtin|После оформлении подписки, клиент может проверять какие товары ему доступны по подписке

###Пример ответа по одному товару###
Ответы отдаются в формате JSON. [Посмотреть пример по товару GTIN-4820043334735](GTIN-4820043334735.json)

Товар содежит информацию о товаре 4820043334735

На верхнем уровне идет информация об упаковке
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
