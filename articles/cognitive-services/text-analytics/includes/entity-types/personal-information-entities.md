---
title: Megnevezett entitások a személyes adatokhoz
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 7484b49ed3c868a1ad3e0f97dffa346f350e127f
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096945"
---
> [!NOTE]
> A védett állapottal kapcsolatos információk (PHI) észleléséhez használja a `domain=phi` paramétert és a modell verzióját, `2020-04-01` vagy később.
>
> Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
A rendszer a következő entitás-kategóriákat adja vissza, amikor kéréseket küld a `/v3.1-preview.3/entities/recognition/pii` végpontnak.


| Kategória   |  Leírás                          |
|------------|-------------|
| [Személy](#category-person)      |  Személyek nevei.  |
| [PersonType](#category-persontype) | Egy személy által birtokolt feladattípusok vagy szerepkörök. |
| [Telefonszám](#category-phonenumber) |Telefonszámok (csak az USA-beli és az EU-telefonszámok). |
| [Szervezet](#category-organization) |  Vállalatoknak, csoportoknak, kormányzati szerveknek és más szervezeteknek.  |
| [Cím](#category-address) | Teljes levelezési címek.  |
| [E-mail](#category-email) | E-mail-címek.   |
| [URL-cím](#category-url) | Webhelyek URL-címei.  |
| [IP](#category-ip) | Hálózati IP-címek.  |
| [Dátum/idő](#category-datetime) | Dátum és napszakok. | 
| [Mennyiség](#category-quantity) | Számok és numerikus mennyiségek.  |
| [Azure-információk](#azure-information) | Azonosítható Azure-információk, például hitelesítési információk.  |
| [Azonosítás](#identification) | Pénzügyi és ország-specifikus azonosítás.  |

### <a name="category-person"></a>Kategória: személy

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Személy

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Személyek nevei. Szintén visszaadott `domain=phi` .

        Az entitás kategóriájának beszerzéséhez adja hozzá `Person` a `pii-categories` paramétert a paraméterhez. `Person` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategória: PersonType

Ez a kategória a következő entitást tartalmazza:


:::row:::
    :::column span="":::
        **Entitás**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Egy személy által birtokolt feladattípusok vagy szerepkörök.

        Az entitás kategóriájának beszerzéséhez adja hozzá `PersonType` a `pii-categories` paramétert a paraméterhez. `PersonType` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategória: telefonszám

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Telefonszámok (csak az USA-beli és az EU-telefonszámok). Szintén visszaadott `domain=phi` .

        Az entitás kategóriájának beszerzéséhez adja hozzá `PhoneNumber` a `pii-categories` paramétert a paraméterhez. `PhoneNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


### <a name="category-organization"></a>Kategória: szervezet

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Szervezet

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek. A nemzetiségek és a vallások nem szerepelnek ebben az entitás-típusban. Szintén visszaadott `domain=phi` .

        Az entitás kategóriájának beszerzéséhez adja hozzá `Organization` a `pii-categories` paramétert a paraméterhez. `Organization` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

#### <a name="subcategories"></a>Alkategóriák

Az ebben a kategóriában lévő entitás a következő alkategóriákkal rendelkezhet.

:::row:::
    :::column span="":::
        **Entitás alkategóriája**

        Orvosi    

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Orvosi vállalatok és csoportok.

        Az entitás kategóriájának beszerzéséhez adja hozzá `OrganizationMedical` a `pii-categories` paramétert a paraméterhez. `OrganizationMedical` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Értéktőzsde

    :::column-end:::
    :::column span="2":::

        Tőzsdei csoportok. 

        Az entitás kategóriájának beszerzéséhez adja hozzá `OrganizationStockExchange` a `pii-categories` paramétert a paraméterhez. `OrganizationStockExchange` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Sporttal kapcsolatos szervezetek.

        Az entitás kategóriájának beszerzéséhez adja hozzá `OrganizationSports` a `pii-categories` paramétert a paraméterhez. `OrganizationSports` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::


### <a name="category-address"></a>Kategória: címe

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Cím

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Teljes levelezési cím. Szintén visszaadott `domain=phi` .

        Az entitás kategóriájának beszerzéséhez adja hozzá `Address` a `pii-categories` paramétert a paraméterhez. `Address` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-email"></a>Kategória: E-mail

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Részletek**

        E-mail-címek. Szintén visszaadott `domain=phi` .
      
        Az entitás kategóriájának beszerzéséhez adja hozzá `Email` a `pii-categories` paramétert a paraméterhez. `Email` az API-válaszban lesz visszaadva, ha a rendszer észleli.

    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


### <a name="category-url"></a>Kategória: URL

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        URL-cím

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Webhelyek URL-címei. Szintén visszaadott `domain=phi` .

        Az entitás kategóriájának beszerzéséhez adja hozzá `URL` a `pii-categories` paramétert a paraméterhez. `URL` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-ip"></a>Kategória: IP

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        IP

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Hálózati IP-címek. Szintén visszaadott `domain=phi` .

        Az entitás kategóriájának beszerzéséhez adja hozzá `IP` a `pii-categories` paramétert a paraméterhez. `IP` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategória: dátum és idő

Ez a kategória a következő entitásokat tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Dátum és napszakok. 

        Az entitás kategóriájának beszerzéséhez adja hozzá `DateTime` a `pii-categories` paramétert a paraméterhez. `DateTime` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
:::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Alkategóriák

Az ebben a kategóriában lévő entitás a következő alkategóriákkal rendelkezhet.

:::row:::
    :::column span="":::
        **Entitás alkategóriája**

        Date

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Naptári dátumok. Szintén visszaadott `domain=phi` .

        Az entitás kategóriájának beszerzéséhez adja hozzá `Date` a `pii-categories` paramétert a paraméterhez. `Date` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategória: mennyiség

Ez a kategória a következő entitásokat tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Mennyiség

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Számok és numerikus mennyiségek.

        Az entitás kategóriájának beszerzéséhez adja hozzá `Quantity` a `pii-categories` paramétert a paraméterhez. `Quantity` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Alkategóriák

Az ebben a kategóriában lévő entitás a következő alkategóriákkal rendelkezhet.

:::row:::
    :::column span="":::
        **Entitás alkategóriája**

        Életkor

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Alábbi korhatárt szabja. 

        Az entitás kategóriájának beszerzéséhez adja hozzá `Age` a `pii-categories` paramétert a paraméterhez. `Age` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="2":::
        **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure-információk

Az entitások kategóriái közé tartoznak az azonosítható Azure-információk, beleértve a hitelesítési adatokat és a kapcsolatok karakterláncait. A paraméterrel nem tért vissza `domain=phi` .

:::row:::
    :::column span="":::
        **Entitás**

        Azure DocumentDB-hitelesítési kulcs 

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Azure Cosmos DB-kiszolgáló engedélyezési kulcsa.   

        Az entitás kategóriájának beszerzéséhez adja hozzá `AzureDocumentDBAuthKey` a `pii-categories` paramétert a paraméterhez. `AzureDocumentDBAuthKey` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Az Azure IAAS adatbázis-kapcsolatainak karakterlánca és az Azure SQL-kapcsolatok karakterlánca.
        

    :::column-end:::
    :::column span="2":::

        Az Azure infrastruktúra-szolgáltatás (IaaS) adatbázisának és az SQL-kapcsolatok karakterláncának a kapcsolatainak karakterlánca.

        Az entitás kategóriájának beszerzéséhez adja hozzá `AzureIAASDatabaseConnectionAndSQLString` a `pii-categories` paramétert a paraméterhez. `AzureIAASDatabaseConnectionAndSQLString` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT-kapcsolatok karakterlánca  

    :::column-end:::
    :::column span="2":::

        Az Azure IoT tartozó kapcsolatok karakterlánca. 
      
        Az entitás kategóriájának beszerzéséhez adja hozzá `AzureIoTConnectionString` a `pii-categories` paramétert a paraméterhez. `AzureIoTConnectionString` az API-válaszban lesz visszaadva, ha a rendszer észleli.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure közzétételi beállítás jelszava  

    :::column-end:::
    :::column span="2":::

        Az Azure közzétételi beállításainak jelszava.

        Az entitás kategóriájának beszerzéséhez adja hozzá `AzurePublishSettingPassword` a `pii-categories` paramétert a paraméterhez. `AzurePublishSettingPassword` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis Cache a kapcsolatok karakterlánca 

    :::column-end:::
    :::column span="2":::

        Redis-gyorsítótárhoz tartozó kapcsolatok karakterlánca.

        Az entitás kategóriájának beszerzéséhez adja hozzá `AzureRedisCacheString` a `pii-categories` paramétert a paraméterhez. `AzureRedisCacheString` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Az Azure-beli szolgáltatott szoftver (SaaS) közötti kapcsolatok karakterlánca.

        Az entitás kategóriájának beszerzéséhez adja hozzá `AzureSAS` a `pii-categories` paramétert a paraméterhez. `AzureSAS` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Service Bus a kapcsolatok karakterlánca

    :::column-end:::
    :::column span="2":::

        Az Azure Service Bus szolgáltatáshoz tartozó kapcsolatok karakterlánca.

        Az entitás kategóriájának beszerzéséhez adja hozzá `AzureServiceBusString` a `pii-categories` paramétert a paraméterhez. `AzureServiceBusString` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage-fiók kulcsa 

    :::column-end:::
    :::column span="2":::

        Egy Azure Storage-fiókhoz tartozó fiók kulcsa. 

        Az entitás kategóriájának beszerzéséhez adja hozzá `AzureStorageAccountKey` a `pii-categories` paramétert a paraméterhez. `AzureStorageAccountKey` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage-fiók kulcsa (általános)

    :::column-end:::
    :::column span="2":::

        Általános fiók kulcsa egy Azure Storage-fiókhoz.

        Az entitás kategóriájának beszerzéséhez adja hozzá `AzureStorageAccountGeneric` a `pii-categories` paramétert a paraméterhez. `AzureStorageAccountGeneric` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server a kapcsolatok karakterlánca 

    :::column-end:::
    :::column span="2":::

        SQL Server rendszert futtató számítógéphez tartozó kapcsolatok karakterlánca.

        Az entitás kategóriájának beszerzéséhez adja hozzá `SQLServerConnectionString` a `pii-categories` paramétert a paraméterhez. `SQLServerConnectionString` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Azonosítás

[!INCLUDE [supported identification entities](./identification-entities.md)]
