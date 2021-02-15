---
title: Adatok másolása az Office 365-ből Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatokat az Office 365-ból egy Azure Data Factory-folyamat másolási tevékenységének használatával támogatott fogadó adattárakba.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: a7df69e7c5701074b40d6fa8340d8a0e247f00de
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393003"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Adatok másolása az Office 365-ből az Azure-ba Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory integrálható [Microsoft Graph](/graph/data-connect-concept-overview)adatkapcsolattal, így méretezhető módon hozhatja létre az Office 365-bérlő gazdag szervezeti adatait az Azure-ban, és elemzési alkalmazásokat készíthet, és elemzéseket nyerhet az értékes adategységek alapján. Az Privileged Access Management integrációja biztonságos hozzáférés-vezérlést biztosít az Office 365 értékes, kiszolgált adataihoz.  Tekintse meg [ezt a hivatkozást](/graph/data-connect-concept-overview) a Microsoft Graph adatok összekapcsolásának áttekintéséhez, és tekintse meg [ezt a hivatkozást](/graph/data-connect-policies#licensing) a licencelési információkhoz.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az Office 365-ból származó adatok másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek
Az ADF Office 365-összekötő és a Microsoft Graph adatok összekapcsolása lehetővé teszi az Exchange e-mailek által engedélyezett postaládák, például a címjegyzék-névjegyek, a naptári események, az e-mailek, a felhasználói adatok, a postaláda-beállítások és más típusú adatkészletek méretezését.  A rendelkezésre álló adatkészletek teljes listáját [itt](/graph/data-connect-datasets) tekintheti meg.

Egyelőre egyetlen másolási tevékenységen belül csak az **Office 365 adatait másolhatja át az [Azure Blob Storageba](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1ba](connector-azure-data-lake-store.md)és JSON formátumban [Azure Data Lake Storage Gen2ba](connector-azure-data-lake-storage.md)** (setOfObjects típus). Ha az Office 365-et más típusú adattárakba vagy más formátumba kívánja betölteni, az első másolási tevékenységet egy későbbi másolási tevékenységgel is felhasználhatja, hogy az összes [támogatott ADF-célhelyre](copy-activity-overview.md#supported-data-stores-and-formats) betöltse az adatmennyiséget (lásd a "támogatott adattárak és formátumok" tábla "fogadó" oszlopát).

>[!IMPORTANT]
>- Az adat-előállítót és a fogadó adattárat tartalmazó Azure-előfizetésnek ugyanannak a Azure Active Directory (Azure AD) Bérlőnek kell lennie, mint az Office 365-bérlőnek.
>- Győződjön meg arról, hogy a másolási tevékenységhez használt Azure Integration Runtime régió, valamint a célhely abban a régióban van, ahol az Office 365 bérlő felhasználói postaládája található. A Azure IR hely meghatározásának megismeréséhez [tekintse meg a](concepts-integration-runtime.md#integration-runtime-location) következőt:. A támogatott Office-régiók és a hozzájuk tartozó Azure-régiók listájáért tekintse meg a [táblázatot](/graph/data-connect-datasets#regions) .
>- Az egyszerű szolgáltatás hitelesítése az Azure Blob Storage, Azure Data Lake Storage Gen1 és Azure Data Lake Storage Gen2 számára támogatott hitelesítési mechanizmus.

## <a name="prerequisites"></a>Előfeltételek

Az Office 365-ből az Azure-ba másolt adatok másolásához a következő előfeltételek szükségesek:

- Az Office 365-bérlői rendszergazdának be kell fejeznie az [itt](/graph/data-connect-get-started)leírtak szerint végrehajtandó műveleteket.
- Azure AD-Webalkalmazás létrehozása és konfigurálása Azure Active Directoryban.  Útmutatásért lásd: [Azure ad-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
- Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához fog használni az Office 365-hoz:
    - Bérlő azonosítója. Útmutatásért lásd: [bérlői azonosító lekérése](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    - Az alkalmazás azonosítója és az alkalmazás kulcsa.  Útmutatásért lásd az [alkalmazás-azonosító és a hitelesítési kulcs beszerzése](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)című témakört.
- Adja hozzá azt a felhasználói identitást, aki az adatelérési kérelmet az Azure AD-webalkalmazás tulajdonosaként hozza létre (az Azure AD webalkalmazás > beállítások > tulajdonos > tulajdonos hozzáadása). 
    - A felhasználói identitásnak az Office 365 szervezetben kell lennie, és nem lehet vendég felhasználó.

## <a name="approving-new-data-access-requests"></a>Új Adathozzáférési kérelmek jóváhagyása

Ha első alkalommal kér adatokat ehhez a környezethez (az adattábla egy kombinációja, a "folyamatban" állapotba kerül a másolási tevékenység állapota, és csak akkor jelenik meg, ha a ["Részletek" hivatkozásra kattint a műveletek területen](copy-activity-overview.md#monitoring) , és csak akkor jelenik meg, ha az állapot "RequestingConsent" értékre van állítva.  Az adathozzáférés-jóváhagyó csoport tagjának jóvá kell hagynia a kérést a Privileged Access Management az adatgyűjtés folytatásához.

Tekintse [át,](/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) hogy a jóváhagyó hogyan hagyja jóvá az adatelérési kérelmet, és tekintse át a Privileged Access Managementával [való általános](/graph/data-connect-pam) integrációval kapcsolatos magyarázatot, beleértve az adathozzáférés jóváhagyó csoportjának beállítását is.

## <a name="policy-validation"></a>Szabályzat érvényesítése

Ha az ADF-t egy felügyelt alkalmazás részeként hozza létre, és az Azure házirendek hozzárendelései a felügyeleti erőforráscsoport erőforrásain találhatók, akkor minden másolási tevékenység esetében az ADF ellenőrzi, hogy a szabályzat-hozzárendelések érvénybe léptetése megtörtént-e. A támogatott szabályzatok listáját [itt](/graph/data-connect-policies#policies) találja.

## <a name="getting-started"></a>Első lépések

>[!TIP]
>Az Office 365 Connector használatának áttekintését lásd: [adatok betöltése az office 365-ből](load-office-365-data.md) című cikk.

A másolási tevékenységgel rendelkező folyamatokat a következő eszközök vagy SDK-k egyikével hozhatja létre. Válasszon egy hivatkozást, amely részletes utasításokat tartalmaz egy másolási tevékenységgel rendelkező folyamat létrehozásához. 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager sablon](quickstart-create-data-factory-resource-manager-template.md). 

A következő szakaszokban részletesen ismertetjük az Office 365-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Office 365 társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **Office 365** | Yes |
| office365TenantId | Az Azure-bérlő azonosítója, amelyhez az Office 365-fiók tartozik. | Yes |
| servicePrincipalTenantId | Adja meg a bérlői adatokat, amelyek alatt az Azure AD-webalkalmazás található. | Yes |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Yes |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt Integration Runtime.  Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | No |

>[!NOTE]
> A **office365TenantId** és a **servicePrincipalTenantId** és a megfelelő érték közötti különbség:
>- Ha Ön nagyvállalati fejlesztő, aki az Office 365-adatok alapján fejleszti az alkalmazást a saját szervezete használatára, akkor mindkét tulajdonsághoz ugyanazt a bérlői azonosítót kell megadnia, amely a szervezet HRE-bérlői azonosítója.
>- Ha Ön egy ISV-fejlesztő, aki egy alkalmazást fejleszt ügyfelei számára, akkor a office365TenantId lesz az ügyfél (Application Installer) HRE-bérlő azonosítója, és a servicePrincipalTenantId a vállalat HRE-bérlője lesz.

**Példa**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az Office 365 adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az Office 365-ből származó adatok másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **Office365Table** | Yes |
| tableName | Az Office 365-ből kinyerni kívánt adatkészlet neve. [Itt](/graph/data-connect-datasets#datasets) tekintheti meg a kinyeréshez elérhető Office 365-adatkészletek listáját. | Yes |

Ha a, a `dateFilterColumn` , `startTime` `endTime` és `userScopeFilterUri` az adatkészletben beállítást választotta, akkor továbbra is támogatott, míg az új modellt a tevékenység forrásaként fogja használni.

**Példa**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Office 365-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="office-365-as-source"></a>Office 365 forrásként

Az Office 365-ből származó adatok másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **Office365Source** | Yes |
| allowedGroups | Csoport kiválasztási predikátuma  Ezzel a tulajdonsággal legfeljebb 10 olyan felhasználói csoportot választhat ki, amelyekhez az adott adatlekérdezést kéri.  Ha nincsenek megadva csoportok, akkor a rendszer az összes szervezetre vonatkozó adatvisszaadás után visszaadja az értékeket. | No |
| userScopeFilterUri | Ha a `allowedGroups` tulajdonság nincs megadva, a teljes bérlőn alkalmazott predikátum kifejezés használatával szűrheti az Office 365-ből kinyerni kívánt sorokat. A predikátum formátumának meg kell egyeznie Microsoft Graph API-k lekérdezési formátumával, például `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` :. | No |
| dateFilterColumn | A DateTime szűrő oszlop neve. Ezzel a tulajdonsággal korlátozhatja azt az időtartományt, amelyre az Office 365-adatkivonatot ki kell olvasni. | Igen, ha az adatkészlet egy vagy több DateTime oszloppal rendelkezik. [Itt](/graph/data-connect-filtering#filtering) tekintse meg a DateTime típusú szűrőt igénylő adatkészletek listáját. |
| startTime | A szűréshez kezdjen el DateTime értéket. | Igen, ha meg `dateFilterColumn` van adva |
| endTime | A szűréshez záró DateTime érték. | Igen, ha meg `dateFilterColumn` van adva |
| outputColumns | A fogadóba másolandó oszlopok tömbje. | No |

**Példa**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
            "source": {
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).