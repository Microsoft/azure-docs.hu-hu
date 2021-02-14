---
title: Adatok másolása a SharePoint Online-listáról Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatokat a SharePoint Online-listáról egy Azure Data Factory-folyamat másolási tevékenységének használatával támogatott fogadó adattárakba.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: 3f05c90ba3c7e6b47009cbb597c56dac8a01427a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393428"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Adatok másolása a SharePoint Online-listáról Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok SharePoint Online-listáról történő másolásához. A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

A SharePoint Online-lista összekötője a következő tevékenységeknél támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A SharePoint Online-listából származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Pontosabban, ez a SharePoint-lista online-összekötő az egyszerű szolgáltatásnév hitelesítést használja, és az OData protokollon keresztül kéri le az adatgyűjtést.

> [!TIP]
> Ez az összekötő támogatja az adatok másolását a SharePoint Online- **listáról** , de a fájl nem. Megtudhatja, hogyan másolhat fájl másolása [fájlból a SharePoint Online-](#copy-file-from-sharepoint-online) ba szakaszból.

## <a name="prerequisites"></a>Előfeltételek

A SharePoint-lista online összekötője egyszerű szolgáltatásnév használatával csatlakozik a SharePointhoz. A következő lépésekkel állíthatja be:

1. Az alkalmazás [Azure ad-Bérlővel való regisztrálását](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)követően regisztráljon egy Azure Active Directory (Azure ad) alkalmazásbeli entitást. Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

2. A SharePoint Online-hely engedélyezésének engedélyezése a regisztrált alkalmazás számára: 

    > [!NOTE]
    > Ehhez a művelethez SharePoint Online-webhely tulajdonosi engedélye szükséges. A tulajdonos megkereséséhez nyissa meg a webhely kezdőlapját – > kattintson a jobb oldali sarokban lévő "X tagok" elemre, > ellenőrizze, hogy ki a "tulajdonos" szerepkörrel rendelkezik.

    1. Nyissa meg a SharePoint Online-webhely hivatkozást `https://[your_site_url]/_layouts/15/appinv.aspx` (például cserélje le a webhely URL-címét).
    2. Keresse meg a regisztrált alkalmazás AZONOSÍTÓját, töltse ki az üres mezőket, majd kattintson a "létrehozás" gombra.

        - Alkalmazás tartománya: `localhost.com`
        - Átirányítási URL-cím: `https://www.localhost.com`
        - Engedély kérésének XML-fájlja:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![SharePoint-engedélyezési engedély](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Az alkalmazáshoz kattintson a "megbízható it" elemre.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel meghatározhatja Data Factory a SharePoint Online-lista összekötőhöz kapcsolódó entitásokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A SharePoint Online-listák társított szolgáltatásai a következő tulajdonságokat támogatják:

| **Tulajdonság**        | **Leírás**                                              | **Kötelező** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| típus                | A Type tulajdonságot a következőre kell beállítani: **SharePointOnlineList**.  | Yes          |
| siteUrl             | A SharePoint Online-webhely URL-címe, például: `https://contoso.sharepoint.com/sites/siteName` . | Yes          |
| servicePrincipalId  | A Azure Active Directoryban regisztrált alkalmazás alkalmazás-(ügyfél-) azonosítója. | Yes          |
| servicePrincipalKey | Az alkalmazás kulcsa. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Yes          |
| tenantId            | A bérlő azonosítója, amely alatt az alkalmazás található.          | Yes          |
| Connectvia tulajdonsággal          | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További információ az [Előfeltételek](#prerequisites)közül, a cikk korábbi részében. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime használja. | No           |

**Példa**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). A következő szakasz az SAP-táblázat adatkészletében támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet **Type** tulajdonságát **SharePointOnlineLResource** értékre kell állítani. | Yes |
| listName | A SharePoint Online-lista neve. | Yes |

**Példa**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).  A következő szakasz a SharePoint Online-lista forrása által támogatott tulajdonságok listáját tartalmazza.

### <a name="sharepoint-online-list-as-source"></a>SharePoint Online-lista forrásként

Az adatok SharePoint Online-listáról történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának **Type** tulajdonságát **SharePointOnlineListSource** értékre kell állítani. | Yes |
| lekérdezés | Egyéni OData-lekérdezési beállítások az adatszűréshez. Példa: `"$top=10&$select=Title,Number"`. | No |
| httpRequestTimeout | A válasz kéréséhez szükséges HTTP-kérelem időtúllépése (másodpercben). Az alapértelmezett érték 300 (5 perc). | No |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> Azure Data Factory nem választhat egynél több *választási* adattípust egy SharePoint Online-lista forrásaként.

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Adattípusok leképezése a SharePoint Online-listához

Az adatok SharePoint Online-listából való másolása során a rendszer a következő leképezéseket használja a SharePoint Online-lista adattípusai és Azure Data Factory köztes adattípusok között. 

| **SharePoint Online-adattípus**                 | **OData adattípusa**                                  | **Azure Data Factory időközi adattípus** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Egysoros szöveg                             | Edm.String                                           | Sztring                                   |
| Több sornyi szöveg                          | Edm.String                                           | Sztring                                   |
| Choice (választható menü)                    | Edm.String                                           | Sztring                                   |
| Szám (1, 1,0, 100)                            | Edm.Double                                           | Dupla                                   |
| Pénznem ($, ¥, &euro; )                              | Edm.Double                                           | Dupla                                   |
| Dátum és idő                                   | EDM. DateTime                                         | DateTime                                 |
| Keresés (az ezen a helyen található információk)       | Edm.Int32                                            | Int32                                    |
| Igen/nem (jelölőnégyzet)                              | Edm.Boolean                                          | Logikai                                  |
| Person or Group (Személy vagy csoport)                                 | Edm.Int32                                            | Int32                                    |
| Hiperhivatkozás vagy kép                            | Edm.String                                           | Sztring                                   |
| Számított (más oszlopok alapján számított számítás) | EDM. String/EDM. Double/EDM. DateTime/EDM. Boolean | String/Double/DateTime/Boolean     |
| Melléklet                                      | Nem támogatott                                        |                                          |
| Feladat eredménye                                    | Nem támogatott                                        |                                          |
| Külső adatok                                   | Nem támogatott                                        |                                          |
| Managed Metadata (Kezelt metaadat)                                | Nem támogatott                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Fájl másolása a SharePoint Online-ból

A SharePoint Online-ból **webes tevékenység** használatával másolhatja a fájlt, amellyel hitelesítheti és megragadhatja a hozzáférési tokent a spongyatől, majd átadhatja azt a későbbi **másolási tevékenységnek** , hogy a **forrásként a http-összekötővel** másolja az

![SharePoint-másolási folyamat](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Kövesse az [Előfeltételek](#prerequisites) SZAKASZT a HRE-alkalmazás létrehozásához és a SharePoint Online-hoz való engedélyezéséhez. 

2. **Webes tevékenység** létrehozása a hozzáférési jogkivonat lekéréséhez a SharePoint Online-ból:

    - **URL-cím**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2` . Cserélje le a bérlő AZONOSÍTÓját.
    - **Metódus**: Post
    - **Fejlécek**:
        - Content-Type: Application/x-www-Form-urlencoded
    - **Törzs**:  `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]` . Cserélje le az ügyfél-azonosítót, az ügyfél titkos kulcsát, a bérlő AZONOSÍTÓját és a bérlő nevét.

    > [!CAUTION]
    > A biztonságos kimenet beállítás értékeként állítsa be az igaz értéket a webes tevékenységben, hogy megakadályozza a jogkivonat értékének egyszerű szövegként való naplózását. Az értéket használó további tevékenységeknek a biztonságos bemenet beállítását True értékre kell állítani.

3. A SharePoint Online-fájl tartalmának másolásához a forrásként egy **másolási tevékenységgel** rendelkező, http-összekötővel rendelkező lánc:

    - HTTP-társított szolgáltatás:
        - **Alap URL-cím**: `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value` . Cserélje le a webhely URL-címét és a relatív elérési útvonalat a fájlra. Példa a fájl relatív elérési útjára `/sites/site2/Shared Documents/TestBook.xlsx` .
        - **Hitelesítés típusa:** Névtelen *(a másolási tevékenység forrásaként konfigurált tulajdonosi jogkivonat használata később)*
    - Adatkészlet: válassza ki a kívánt formátumot. A fájl a következőként történő másolásához válassza a "bináris" típust.
    - Másolási tevékenység forrása:
        - **Kérelem metódusa**: Get
        - **További fejléc**: használja az alábbi kifejezést `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}` , amely a felsőbb rétegbeli webes tevékenység által generált tulajdonosi jogkivonatot használja engedélyezési fejlécként. Cserélje le a webes tevékenység nevét.
    - Konfigurálja a másolási tevékenység fogadóját a szokásos módon.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintse meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
