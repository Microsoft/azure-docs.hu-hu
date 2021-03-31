---
title: Az Azure Blockchain Workbench konfigurációs metaadat-referenciája
description: Az Azure Blockchain Workbench előzetes verziójának alkalmazás-konfigurációs metaadatok áttekintése.
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: f0ba19bf1d7fdf05014ac199fae9392b5c3249d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87073079"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Az Azure Blockchain Workbench konfigurációs referenciája

Az Azure Blockchain Workbench alkalmazások a konfigurációs metaadatok és az intelligens szerződési kódok által meghatározott többrésztvevős munkafolyamatok. A konfigurációs metaadatok a blockchain alkalmazás magas szintű munkafolyamatait és interakciós modelljét határozzák meg. Az intelligens szerződések határozzák meg a blockchain alkalmazás üzleti logikáját. A Workbench konfigurációs és intelligens szerződési kódot használ a blockchain alkalmazás felhasználói élményének létrehozásához.

A konfigurációs metaadatok a következő információkat határozzák meg az egyes blockchain-alkalmazásokhoz:

* A blockchain-alkalmazás neve és leírása
* Egyedi szerepkörök azon felhasználók számára, akik a blockchain alkalmazásban működhetnek vagy részt vehetnek
* Egy vagy több munkafolyamat. Az egyes munkafolyamatok állapotjelző gépnek minősülnek az üzleti logika áramlását vezérelve. A munkafolyamatok egymástól függetlenek lehetnek, vagy egymással kezelhetik egymást.

Az egyes definiált munkafolyamatok a következőket határozzák meg:

* A munkafolyamat neve és leírása
* A Munkafolyamat állapota.  Az egyes állapotok az üzleti logika vezérlési folyamatának egyik fázisa. 
* A következő állapotba való áttérés lépései
* Az egyes műveletek elindítására jogosult felhasználói szerepkörök
* A programkódokban az üzleti logikát képviselő intelligens szerződések

## <a name="application"></a>Alkalmazás

A blockchain-alkalmazások olyan konfigurációs metaadatokat, munkafolyamatokat és felhasználói szerepköröket tartalmaznak, amelyek az alkalmazásban működhetnek vagy részt vehetnek.

| Mező | Leírás | Kötelező |
|-------|-------------|:--------:|
| ApplicationName | Az alkalmazás egyedi neve. A megfelelő intelligens szerződésnek ugyanazt a **ApplicationName** -t kell használnia a vonatkozó szerződési osztályhoz.  | Yes |
| DisplayName | Az alkalmazás felhasználóbarát megjelenítendő neve. | Yes |
| Leírás | Az alkalmazás leírása. | No |
| ApplicationRoles | A [ApplicationRoles](#application-roles)gyűjteménye. Azok a felhasználói szerepkörök, akik az alkalmazásban működhetnek vagy részt vehetnek.  | Yes |
| Munkafolyamatok | [Munkafolyamatok](#workflows)gyűjteménye. Az egyes munkafolyamatok állapotjelző gépnek minősülnek az üzleti logika áramlását vezérelve. | Yes |

Példa: [példa a konfigurációs fájlra](#configuration-file-example).

## <a name="workflows"></a>Munkafolyamatok

Az alkalmazás üzleti logikája olyan állapotú gépként is modellezhető, amelyben a művelet hatására az üzleti logikának az egyik állapotból a másikba való áthelyezése történik. A munkafolyamat ilyen állapotok és műveletek gyűjteménye. Minden munkafolyamat egy vagy több olyan intelligens szerződést tartalmaz, amely a programkódokban lévő üzleti logikát képviseli. Egy végrehajtható szerződés a munkafolyamat egy példánya.

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Name | Egyedi munkafolyamat neve. A megfelelő intelligens szerződésnek ugyanazt a **nevet** kell használnia a vonatkozó szerződési osztályhoz. | Yes | 50 |
| DisplayName | A munkafolyamat felhasználóbarát megjelenítendő neve. | Yes | 255 |
| Leírás | A munkafolyamat leírása. | No | 255 |
| Kezdeményezők | A [ApplicationRoles](#application-roles)gyűjteménye. A munkafolyamatban szerződések létrehozására jogosult felhasználókhoz hozzárendelt szerepkörök. | Yes | |
| StartState | A munkafolyamat kezdeti állapotának neve. | Yes | |
| Tulajdonságok | [Azonosítók](#identifiers)gyűjteménye. Azokat az adatelemzési eszközöket jelöli, amelyek leolvashatók a láncból, vagy megjeleníthetők a felhasználói élmény eszközben. | Yes | |
| Konstruktor | Meghatározza a munkafolyamat egy példányának létrehozásához szükséges bemeneti paramétereket. | Yes | |
| Functions | A munkafolyamatban végrehajtható [függvények](#functions) gyűjteménye. | Yes | |
| Állapotok | Munkafolyamat- [állapotok](#states)gyűjteménye. | Yes | |

Példa: [példa a konfigurációs fájlra](#configuration-file-example).

## <a name="type"></a>Típus

Támogatott adattípusok.

| Típus | Leírás |
|-------|-------------|
| address  | A Blockchain, például a *szerződések* vagy a *felhasználók*. |
| array    | Egyszintű tömb, egész szám, bool, pénz vagy idő típusú tömb. A tömbök lehetnek statikusak vagy dinamikusak. A tömbben található elemek adattípusának megadásához használja a **ElementType** . Lásd: [példa konfiguráció](#example-configuration-of-type-array). |
| logikai     | Logikai adattípus. |
| szerződés | Szerződés típusú címe. |
| Enum     | Megnevezett értékek enumerálása. Az enumerálás típusának használatakor megadhatja a EnumValues listáját is. Minden érték 255 karakterből állhat. Az érvényes értékek a következők: kis-és nagybetűk (A-Z, a-z) és számok (0-9). Lásd: [példa konfiguráció és használat a soliding-ben](#example-configuration-of-type-enum). |
| int      | Egész adattípus. |
| pénzt    | Pénz adattípus. |
| állapot    | Munkafolyamat állapota. |
| sztring  | Sztring adattípusú. 4000 karakter maximális száma. Lásd: [példa konfiguráció](#example-configuration-of-type-string). |
| felhasználó!     | Felhasználó típusú címe. |
| time     | Idő adattípus. |
|`[ Application Role Name ]`| Bármely, az alkalmazás szerepkörben megadott név. Korlátozza a felhasználókat a szerepkör típusának. |

### <a name="example-configuration-of-type-array"></a>Tömb típusú konfiguráció

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Tömb típusú tulajdonság használata

Ha a konfigurációban típus tömbként definiál egy tulajdonságot, meg kell adnia egy explicit Get függvényt, amely a szilárdtesten lévő tömb típusának nyilvános tulajdonságát adja vissza. Például:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Karakterlánc típusú konfiguráció

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Példa Enum típusú konfigurációra

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Számbavételi típus használata a Szilárdtestben

Miután meghatározta a felsorolást a konfigurációban, a számbavételi típusokat a Szilárdtestben is használhatja. Megadhat például egy PropertyTypeEnum nevű enumerálást.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

A karakterláncok listájának egyeznie kell a konfiguráció és az intelligens szerződés között, hogy érvényes és konzisztens deklarációk legyenek a Blockchain Workbenchben.

Hozzárendelési példa:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Példa a Function paraméterre: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Konstruktor

Meghatározza a munkafolyamat egy példányának bemeneti paramétereit.

| Mező | Leírás | Kötelező |
|-------|-------------|:--------:|
| Paraméterek | Intelligens szerződés elindításához szükséges [azonosítók](#identifiers) gyűjteménye. | Yes |

### <a name="constructor-example"></a>Konstruktor – példa

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Functions

A munkafolyamaton végrehajtható függvények meghatározása.

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Name | A függvény egyedi neve. A megfelelő intelligens szerződésnek ugyanazt a **nevet** kell használnia a megfelelő függvényhez. | Yes | 50 |
| DisplayName | A függvény felhasználóbarát megjelenítendő neve. | Yes | 255 |
| Leírás | A függvény leírása | No | 255 |
| Paraméterek | A függvény paramétereinek megfelelő [azonosítók](#identifiers) gyűjteménye. | Yes | |

### <a name="functions-example"></a>Függvények – példa

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Állapotok

Egy munkafolyamaton belül egyedi állapotok gyűjteménye. Az egyes állapotok rögzítik az üzleti logika vezérlési folyamatának lépéseit. 

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Name | Az állapot egyedi neve. A megfelelő intelligens szerződésnek ugyanazt a **nevet** kell használnia a megfelelő állapothoz. | Yes | 50 |
| DisplayName | Az állapot felhasználóbarát megjelenítendő neve. | Yes | 255 |
| Leírás | Az állapot leírása. | No | 255 |
| KészültségiSzint paraméter értéke | A Blockchain Workbench felhasználói felületén megjelenő egész érték, amely az üzleti logika vezérlési folyamatán belüli előrehaladást mutatja. | Yes | |
| Stílus | Vizuális mutató, amely azt jelzi, hogy az állapot sikeres vagy sikertelen állapotot képvisel-e. Két érvényes érték létezik: `Success` vagy `Failure` . | Yes | |
| Áttűnések | Elérhető [átmenetek](#transitions) gyűjteménye az aktuális állapotból a következő készletbe. | No | |

### <a name="states-example"></a>Állapotok – példa

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Áttűnések

A következő állapothoz elérhető műveletek. Egy vagy több felhasználói szerepkör minden állapotban végrehajthat egy műveletet, ahol egy művelet egy állapotot áttérhet a munkafolyamat egy másik állapotára. 

| Mező | Leírás | Kötelező |
|-------|-------------|:--------:|
| AllowedRoles | Az áttérés kezdeményezésére jogosult alkalmazások listája. Lehetséges, hogy a megadott szerepkör összes felhasználója el tudja végezni a műveletet. | No |
| AllowedInstanceRoles | Az átállást kezdeményező, az intelligens szerződésben résztvevő vagy megadott felhasználói szerepkörök listája. A példányok szerepkörei a munkafolyamatokon belüli **tulajdonságokban** vannak meghatározva. A AllowedInstanceRoles egy intelligens szerződés egy példányában részt vevő felhasználót jelöl. A AllowedInstanceRoles lehetővé teszi, hogy egy adott szerződési példányban egy felhasználói szerepkörre korlátozza a műveletet.  Előfordulhat például, hogy csak azt szeretné engedélyezni, hogy a szerződést (InstanceOwner) létrehozó felhasználó a szerepkör típusa (tulajdonos) helyett az összes felhasználó számára is lemondható legyen, ha a szerepkört a AllowedRoles-ben adta meg. | No |
| DisplayName | Az átmenet felhasználóbarát megjelenítendő neve. | Yes |
| Leírás | Az átmenet leírása. | No |
| Függvény | Az átmenetet kezdeményező függvény neve. | Yes |
| NextStates | A sikeres áttérés utáni lehetséges következő állapotok gyűjteménye. | Yes |

### <a name="transitions-example"></a>Átmenetek – példa

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Alkalmazás-szerepkörök

Az alkalmazás szerepkörei olyan szerepköröket határoznak meg, amelyek hozzárendelhetők olyan felhasználókhoz, akik az alkalmazáson belül szeretnének működni vagy részt venni. Az alkalmazás szerepkörei a blockchain alkalmazásban és a megfelelő munkafolyamatokban is korlátozhatják a műveleteket és a részvételt. 

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Name | Az alkalmazás szerepkörének egyedi neve. A megfelelő intelligens szerződésnek ugyanazt a **nevet** kell használnia a megfelelő szerepkörhöz. Az alaptípusok nevei le vannak foglalva. Az alkalmazás-szerepkör nem nevezhető el ugyanazzal a névvel, mint a [Type](#type)| Yes | 50 |
| Leírás | Az alkalmazás szerepkörének leírása. | No | 255 |

### <a name="application-roles-example"></a>Példa az alkalmazás szerepköreire

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Azonosítók

Az azonosítók a munkafolyamat-tulajdonságok, a konstruktorok és a függvények paramétereinek leírására szolgáló információk gyűjteményét jelölik. 

| Mező | Leírás | Kötelező | Maximális hossz |
|-------|-------------|:--------:|-----------:|
| Name | A tulajdonság vagy paraméter egyedi neve. A megfelelő intelligens szerződésnek ugyanazt a **nevet** kell használnia a megfelelő tulajdonsághoz vagy paraméterhez. | Yes | 50 |
| DisplayName | A tulajdonság vagy paraméter felhasználóbarát megjelenítendő neve. | Yes | 255 |
| Leírás | A tulajdonság vagy a paraméter leírása. | No | 255 |
| Típus | Tulajdonság [adattípusa](#type). | Yes |

### <a name="identifiers-example"></a>Azonosítók – példa

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Példa konfigurációs fájlra

Az adatátviteli szolgáltatás olyan intelligens szerződési forgatókönyv, amely nagy értékű adategységek megvásárlását és értékesítését igényli. Az értékesítők az adategységek átadásával intelligens szerződést hozhatnak létre az eszközeik listázásához. A vásárlók az intelligens szerződéssel kapcsolatos műveletek elvégzésével elvégezhetik az ajánlatokat, és más felek is végezhetnek műveleteket az eszköz vizsgálatához és értékeléséhez. Miután megtörtént a vizsgálat és az értékelés is, a vevő és az eladó megerősíti az értékesítést, mielőtt a szerződés befejeződik. A folyamat minden pontján minden résztvevő megtekinti a szerződés állapotát a frissítés során. 

További információk, például a kódrészletek: [Asset-átviteli minta az Azure Blockchain workbenchhez](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

A következő konfigurációs fájl az Asset átviteli mintája:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench REST API leírása](/rest/api/azure-blockchain-workbench)
