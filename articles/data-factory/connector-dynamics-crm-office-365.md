---
title: Az Adatmásolás a Dynamicsban (Common Data Service)
description: Megtudhatja, hogyan másolhat adatokat a Microsoft Dynamics CRM vagy a Microsoft Dynamics 365 (Common Data Service) rendszerből a fogadó adattárakba vagy a támogatott forrásból származó adattárakból a Dynamics CRM-be vagy a Dynamics 365-be egy másolási tevékenységgel egy adatfeldolgozó-folyamaton keresztül.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 02/02/2021
ms.openlocfilehash: 63816a40aa710d26dc036dfe82018883e917beb6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428470"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Adatok másolása a és a rendszerből a Dynamics 365 (Common Data Service) vagy a Dynamics CRM-be a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan lehet másolási tevékenységet használni a Azure Data Factoryban az adatok Microsoft Dynamics 365 és Microsoft Dynamics CRM rendszerbe való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás-és fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Dynamics 365 (Common Data Service) vagy a Dynamics CRM adatait átmásolhatja bármely támogatott fogadó adattárba. Az adatok bármely támogatott forrásból származó adattárból is átmásolhatók a Dynamics 365 (Common Data Service) vagy a Dynamics CRM-be. A másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez a Dynamics-összekötő a 7 – 9 verziójú Dynamics-verziót is támogatja online és helyszíni rendszereken egyaránt. Pontosabban:

- 7-es verzió a Dynamics CRM 2015-hez.
- A 8-as verzió a Dynamics CRM 2016-re és a Dynamics 365 korábbi verziójára mutat.
- A 9-es verzió leképezi a Dynamics 365 újabb verzióját.

Tekintse át a következő táblázatot a Dynamics-verziók és-termékek támogatott hitelesítési típusairól és konfigurációjáról.

| Dynamics-verziók | Hitelesítési típusok | Társított szolgáltatási minták |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 online <br/><br/> Dynamics CRM Online | Azure Active Directory (Azure AD) egyszerű szolgáltatásnév <br/><br/> Office 365 | [Dynamics online és Azure AD Service – elsődleges vagy Office 365-hitelesítés](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 helyszíni, internetre irányuló üzembe helyezéssel (internetes telepítéssel) <br/><br/> Dynamics CRM 2016 helyszíni központi TELEPÍTÉSsel <br/><br/> Dynamics CRM 2015 helyszíni központi TELEPÍTÉSsel | IFD | [Helyszíni Dynamics, központi TELEPÍTÉSsel és központi TELEPÍTÉSsel történő hitelesítéssel](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

A Dynamics 365 esetében a következő típusú alkalmazások támogatottak:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Ez az összekötő nem támogatja más típusú alkalmazások, például a pénzügy, a műveletek és a tehetség használatát.

>[!TIP]
>A Dynamics 365-as pénzügyi és-műveletek adatainak másolásához használhatja a [Dynamics AX-összekötőt](connector-dynamics-ax.md).

Ez a Dynamics-összekötő a [Dynamics xrm-eszközökre](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)épül.

## <a name="prerequisites"></a>Előfeltételek

Ha az összekötőt az Azure AD szolgáltatással szeretné használni – az egyszerű hitelesítéshez be kell állítania a kiszolgálók közötti (S2S) hitelesítést Common Data Service vagy Dynamics rendszerbe. A részletes lépésekért tekintse meg [ezt a cikket](/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) .

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyeket a rendszer a Dynamics-specifikus entitások Data Factory definiálásához használ.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Dynamics társított szolgáltatás a következő tulajdonságokat támogatja.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 és Dynamics CRM Online

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot "Dynamics", "DynamicsCrm" vagy "CommonDataServiceForApps" értékre kell beállítani. | Igen |
| Megadva | A Dynamics-példány központi telepítési típusa. Az értéknek "online" értékűnek kell lennie a Dynamics online-hoz. | Igen |
| serviceUri | A Dynamics-példány szolgáltatásának URL-címe, amely ugyanaz, mint a böngészőből. Ilyen például a "https:// \<organization-name> . CRM [x]. Dynamics. com". | Igen |
| authenticationType | A Dynamics-kiszolgálóhoz való kapcsolódáshoz szükséges hitelesítési típus. Az érvényes értékek: "AADServicePrincipal" és "Office 365". | Igen |
| servicePrincipalId | Az Azure AD-alkalmazás ügyfél-azonosítója. | Igen, ha a hitelesítés "AADServicePrincipal" |
| servicePrincipalCredentialType | A szolgáltatás egyszerű hitelesítéséhez használandó hitelesítőadat-típus. Az érvényes értékek: "ServicePrincipalKey" és "ServicePrincipalCert". | Igen, ha a hitelesítés "AADServicePrincipal" |
| servicePrincipalCredential | A szolgáltatás egyszerű hitelesítő adatai. <br/><br/>Ha a "ServicePrincipalKey" hitelesítő adatként használja, `servicePrincipalCredential` akkor egy karakterlánc, amely Azure Data Factory titkosítja a társított szolgáltatás központi telepítését. Vagy a Azure Key Vault titkos kódjára is hivatkozhat. <br/><br/>Ha hitelesítő adatként használja a "ServicePrincipalCert" kifejezést, `servicePrincipalCredential` Azure Key Vault-ben lévő tanúsítványra kell hivatkoznia. | Igen, ha a hitelesítés "AADServicePrincipal" |
| username | A Dynamicshoz való kapcsolódáshoz használt Felhasználónév. | Igen, ha a hitelesítés "Office 365" |
| jelszó | A felhasználónévként megadott felhasználói fiók jelszava. A mező megjelölése a "SecureString" értékkel a biztonságos tárolás Data Factoryban, vagy [a Azure Key Vault tárolt titkos kód hivatkozása](store-credentials-in-key-vault.md). | Igen, ha a hitelesítés "Office 365" |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Ha nincs megadva érték, a tulajdonság az alapértelmezett Azure Integration Runtime-t használja. | Nem |

>[!NOTE]
>A Dynamics Connector korábban a nem kötelező **Szervezetnév** tulajdonságot használta a Dynamics CRM-vagy Dynamics 365 online-példány azonosítására. Habár ez a tulajdonság továbbra is működik, javasoljuk, hogy az új **serviceUri** tulajdonságot a példányok felderítése jobb teljesítményének növelése helyett.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Példa: Dynamics online az Azure AD szolgáltatással – elsődleges és kulcsos hitelesítés

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Példa: Dynamics online az Azure AD szolgáltatással – elsődleges és tanúsítványalapú hitelesítés

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Példa: Dynamics online az Office 365-hitelesítéssel

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>A Dynamics 365 és a Dynamics CRM helyszíni központi TELEPÍTÉSsel

A Dynamics online-hoz képest további tulajdonságok az **állomásnév** és a **port**.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot "Dynamics", "DynamicsCrm" vagy "CommonDataServiceForApps" értékre kell beállítani. | Igen. |
| Megadva | A Dynamics-példány központi telepítési típusa. Az értéknek "OnPremisesWithIfd" értékűnek kell lennie a helyszíni Dynamics számára az internetes eléréssel.| Igen. |
| hostName | A helyszíni Dynamics-kiszolgáló állomásneve. | Igen. |
| port | A helyszíni Dynamics-kiszolgáló portja. | Nem. Az alapértelmezett érték a 443. |
| Szervezetnév | A Dynamics-példány szervezetének neve. | Igen. |
| authenticationType | A Dynamics-kiszolgálóhoz való kapcsolódáshoz szükséges hitelesítési típus. A központi TELEPÍTÉSsel rendelkező Dynamics helyszíni környezetének "központi telepítés" megadása. | Igen. |
| username | A Dynamicshoz való kapcsolódáshoz használt Felhasználónév. | Igen. |
| jelszó | A felhasználónévhez megadott felhasználói fiók jelszava. Ezt a mezőt "SecureString" értékkel jelölheti meg, hogy biztonságosan tárolja Data Factoryban. Vagy tárolhat egy jelszót Key Vaultban, és engedélyezheti a másolási tevékenységet, amikor az Adatmásolás során lekéri. További információ a [Key Vault tárolt hitelesítő adatokról](store-credentials-in-key-vault.md). | Igen. |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Ha nincs megadva érték, a tulajdonság az alapértelmezett Azure Integration Runtime-t használja. | Nem |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Példa: helyszíni Dynamics helyszíni és központi telepítéssel történő hitelesítéssel

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Dynamics-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok a és a Dynamics rendszerbe való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát "DynamicsEntity", "DynamicsCrmEntity" vagy "CommonDataServiceForAppsEntity" értékre kell beállítani. |Igen |
| entityName | A lekérdezni kívánt entitás logikai neve. | Nem a forrásnál, ha a tevékenység forrása "Query" (lekérdezés), igen, fogadóként van megadva |

#### <a name="example"></a>Példa

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Dynamics forrás és a fogadó típusa által támogatott tulajdonságok listáját tartalmazza.

### <a name="dynamics-as-a-source-type"></a>A Dynamics forrás típusa

A Dynamicsből származó adatok másolásához a másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát "DynamicsSource", "DynamicsCrmSource" vagy "CommonDataServiceForAppsSource" értékre kell beállítani. | Igen |
| lekérdezés | A FetchXML egy saját lekérdezési nyelv, amelyet a Dynamics online-ban és a helyszínen is használhat. Tekintse meg a következő példát. További információ: [lekérdezések készítése a FetchXML](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8)). | Nem, ha az `entityName` adatkészletben meg van adva |

>[!NOTE]
>A PK oszlop mindig akkor is másolódik, ha a FetchXML-lekérdezésben konfigurált oszlop nem tartalmazza azt.

> [!IMPORTANT]
>- A Dynamicsből származó adatok másolása esetén a Dynamicsből a fogadóba történő explicit oszlop-hozzárendelés nem kötelező. A leképezést azonban kifejezetten ajánljuk a determinisztikus másolási eredményének biztosításához.
>- Amikor Data Factory importál egy sémát a szerzői műveletek felhasználói felületén, a rendszer kikövetkezteti a sémát. Ezt úgy végezheti el, hogy a legfelső sorokat a Dynamics lekérdezési eredményből a forrás oszlopok listájának inicializálásához. Ebben az esetben a felső sorokban nem szereplő oszlopokat a rendszer kihagyja. Ugyanez a viselkedés a másolási végrehajtások esetében is érvényes, ha nincs explicit leképezés. Áttekintheti és hozzáadhat további oszlopokat a leképezéshez, amelyeket a rendszer a másolási futtatókörnyezet során tiszteletben tart.

#### <a name="example"></a>Példa

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Példa FetchXML-lekérdezésre

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>A Dynamics fogadó típusa

Az adatmásoláshoz a másolási **tevékenység** elfogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát "DynamicsSink", "DynamicsCrmSink" vagy "CommonDataServiceForAppsSink" értékre kell beállítani. | Igen. |
| writeBehavior | A művelet írási viselkedése. Az értéknek "Upsert" értékűnek kell lennie. | Igen |
| alternateKeyName | Az entitáson definiált másodlagos kulcs neve upsert. | Nem. |
| writeBatchSize | Az egyes kötegekben a Dynamicsba írt adatsorok száma. | Nem. Az alapértelmezett érték 10. |
| ignoreNullValues | Azt határozza meg, hogy a rendszer figyelmen kívül hagyja-e a null értékeket a Key mezőktől eltérő bemeneti adatokból az írási művelet során.<br/><br/>Az érvényes értékek **igazak** és **hamisak**:<ul><li>**True (igaz**): a célobjektum nem módosult, ha upsert vagy frissítési műveletet végez. Definiált alapértelmezett érték beszúrása egy beszúrási művelet végrehajtásakor.</li><li>**Hamis**: a upsert vagy frissítési művelet végrehajtásakor a célként megadott objektumban lévő Adatfrissítés NULL értékre. Szúrjon be egy null értéket a beszúrási művelet végrehajtásakor.</li></ul> | Nem. Az alapértelmezett érték **false (hamis**). |

>[!NOTE]
>A fogadó **writeBatchSize** és a **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** másolási tevékenységének alapértelmezett értéke 10. Ezért a 100-es rekordokat a rendszer alapértelmezés szerint egyszerre küldi el a Dynamics számára.

A Dynamics 365 online esetében a [szervezeten belül két egyidejű batch-hívás](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations)van korlátozva. Ha túllépi a korlátot, a rendszer a "kiszolgáló foglalt" kivételt az első kérelem futtatása előtt kidobja. Az egyidejű hívások szabályozásának elkerüléséhez tartsa a **writeBatchSize** 10 vagy kevesebb értékkel.

A **writeBatchSize** és a **parallelCopies** optimális kombinációja az entitás sémájától függ. A séma elemei közé tartozik az oszlopok száma, a sor mérete, valamint a beépülő modulok, a munkafolyamatok és a munkafolyamat-tevékenységek száma. A **writeBatchSize** (10) &times; **parallelCopies** (10) alapértelmezett beállítása a Dynamics szolgáltatásnak megfelelő javaslat. Ez az érték a legtöbb Dynamics-entitás esetében működik, bár lehetséges, hogy nem adja meg a legjobb teljesítményt. A teljesítmény finomhangolásához módosítsa a kombinációt a másolási tevékenység beállításaiban.

#### <a name="example"></a>Példa

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Adattípusok leképezése a Dynamics számára

Az adatok Dynamicsból való másolása során a következő táblázat a Dynamics-adattípusok és a Data Factory köztes adattípusok közötti leképezéseket mutatja be. Annak megismeréséhez, hogy egy másolási tevékenység hogyan képezi le a forrás-sémát és az adattípust egy fogadóra, tekintse meg a [séma és adattípus-leképezések](copy-activity-schema-and-type-mapping.md)című témakört.

Konfigurálja a megfelelő Data Factory adattípust a forrás-Dynamics adattípuson alapuló adatkészlet-struktúrában a következő leképezési táblázat használatával:

| Dynamics-adattípus | Data Factory időközi adattípus | Forrásként támogatott | Fogadóként támogatott |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Hosszú | ✓ | ✓ |
| AttributeTypeCode. Boolean | Logikai | ✓ | ✓ |
| AttributeType. Customer | GUID | ✓ | ✓ (Lásd: [útmutató](#writing-data-to-a-lookup-field)) |
| AttributeType. DateTime | Datetime | ✓ | ✓ |
| AttributeType. decimális | Tizedesjegy | ✓ | ✓ |
| AttributeType. Double | Dupla | ✓ | ✓ |
| AttributeType. EntityName | Sztring | ✓ | ✓ |
| AttributeType. Integer | Int32 | ✓ | ✓ |
| AttributeType. lookup | GUID | ✓ | ✓ (Lásd: [útmutató](#writing-data-to-a-lookup-field)) |
| AttributeType. ManagedProperty | Logikai | ✓ | |
| AttributeType. Memo | Sztring | ✓ | ✓ |
| AttributeType. Money | Tizedesjegy | ✓ | ✓ |
| AttributeType. Owner | GUID | ✓ | ✓ (Lásd: [útmutató](#writing-data-to-a-lookup-field)) |
| AttributeType. lista | Int32 | ✓ | ✓ |
| AttributeType. uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType. String | Sztring | ✓ | ✓ |
| AttributeType. State | Int32 | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |

> [!NOTE]
> A Dynamics-adattípusok **AttributeType. CalendarRules**, **AttributeType. MultiSelectPicklist** és **AttributeType. PartyList** nem támogatottak.

## <a name="writing-data-to-a-lookup-field"></a>Adatírás keresési mezőbe

Ha olyan keresőmezőt szeretne írni, amelynek több célja van, például az ügyfél és a tulajdonos, kövesse az alábbi útmutatást és példát:

1. Győződjön meg arról, hogy a forrás a mező értékét és a hozzá tartozó megcélzott entitás nevét is tartalmazza.
   - Ha az összes rekord ugyanahhoz a megcélzott entitáshoz van rendelve, a következő feltételek egyikét kell biztosítania:
      - A forrásadatok egy olyan oszloppal rendelkeznek, amely a cél entitás nevét tárolja.
      - Hozzáadott egy további oszlopot a másolási tevékenység forrásában a célként megadott entitás definiálásához.
   - Ha különböző rekordok képezhetők le a különböző célcsoportokra, győződjön meg arról, hogy a forrásadatok tartalmaz egy oszlopot, amely a megfelelő cél entitás nevét tárolja.

1. Rendelje hozzá az érték és az entitás – hivatkozás oszlopot a forrásról a fogadóra. Az entitás-hivatkozás oszlopot egy olyan virtuális oszlophoz kell hozzárendelni, amely a speciális elnevezési mintát tartalmaz `{lookup_field_name}@EntityReference` . Az oszlop valójában nem létezik a Dynamicsban. Ez azt jelzi, hogy ez az oszlop a megadott többhelyes keresési mező metaadat oszlopa.

Tegyük fel például, hogy a forrásnak a következő két oszlopa van:

- **GUID** típusú **CustomerField** -oszlop, amely a cél entitás elsődleges kulcsának értéke a dynamicsben.
- **Karakterlánc** típusú **cél** oszlop, amely a célként megadott entitás logikai neve.

Azt is tegyük fel, hogy az ilyen típusú Adatmásolás a fogadó Dynamics entitás mezőjébe **CustomerField** .

A másolási tevékenység oszlop leképezése részen a következő módon képezze le a két oszlopot:

- **CustomerField** a **CustomerField**. Ez a leképezés a normál mezők leképezése.
- **Cél** a **CustomerField \@ EntityReference**. A fogadó oszlop az entitások hivatkozását jelképező virtuális oszlop. Adja meg az ilyen mezőneveket a leképezésben, mivel azok nem jelennek meg a sémák importálásával.

![Dynamics keresési mező oszlopának leképezése](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Ha az összes forrásoldali rekord ugyanahhoz a célként megadott entitáshoz kapcsolódik, és a forrásadatok nem tartalmazzák a célként megadott entitás nevét, az itt látható parancsikon: a másolási tevékenység forrása mezőben adjon hozzá egy további oszlopot. Nevezze el az új oszlopot a minta használatával `{lookup_field_name}@EntityReference` , állítsa be az értéket a célként megadott entitás nevére, majd a szokásos módon folytassa az oszlop-hozzárendelést. Ha a forrás-és a fogadó oszlopainak nevei azonosak, kihagyhatja az explicit oszlop-hozzárendelést, mert a másolási tevékenység alapértelmezés szerint az oszlopok neve szerint van leképezve.

![Dynamics lookup-mező hozzáadása egy entitáshoz – hivatkozási oszlop](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez lásd: [keresési tevékenység](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Data Factory a másolási tevékenységet a forrásként és a fogadóként támogatja, a [támogatott adattárakat](copy-activity-overview.md#supported-data-stores-and-formats)ismertető cikk tartalmazza.