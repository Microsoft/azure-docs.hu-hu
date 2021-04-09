---
title: Adatmásolás és átalakítás Azure Data Lake Storage Gen2
description: Megtudhatja, hogyan másolhat adatok Azure Data Lake Storage Gen2ba és onnan, és hogyan alakíthatja át a Azure Data Lake Storage Gen2 adatait Azure Data Factory használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 7a501a86f979bb508052c8957627ebfa7950fd63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597588"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Azure Data Lake Storage Gen2 adatmásolása és átalakítása a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Lake Storage Gen2 (ADLS Gen2) az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-ba beépített Big Data-elemzésekhez rendelt képességek összessége. Az adatok kezeléséhez használhatja fájlrendszerbeli és objektumtárolási módszerekkel egyaránt.

Ez a cikk azt ismerteti, hogyan használhatja az Azure Data Factory másolási tevékenységét adatoknak az Azure Data Lake Storage Gen2-be vagy onnan máshová történő másolásához, valamint hogyan használhatja az Adatfolyamot az adatok átalakítására az Azure Data Lake Storage Gen2-ben. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

>[!TIP]
>A adatközpont-vagy adatraktár-áttelepítési forgatókönyv esetén további információ a [használatból Azure Data Factory az adatok migrálása az adatközpontból vagy az adattárházból az Azure-ba](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Data Lake Storage Gen2-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Másolási tevékenység esetén az alábbi összekötővel rendelkezhet:

- Adatok másolása a-ből/-ból Azure Data Lake Storage Gen2 a fiók kulcsa, a szolgáltatásnév vagy a felügyelt identitások használatával az Azure-erőforrások hitelesítéséhez.
- Másolja a fájlokat a-ként, vagy elemezze vagy készítsen fájlokat a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md).
- A [fájl metaadatainak megőrzése a másolás során](#preserve-metadata-during-copy).
- [ACL](#preserve-acls) -ek megőrzése Azure Data Lake Storage Gen1/Gen2 való másoláskor.

## <a name="get-started"></a>Bevezetés

>[!TIP]
>Az Data Lake Storage Gen2-összekötő használatáról a következő témakörben olvashat: az [adatAzure Data Lake Storage Gen2ba való betöltés](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban információt talál a Data Lake Storage Gen2ra vonatkozó Data Factory entitások definiálásához használt tulajdonságokról.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Data Lake Storage Gen2-összekötő a következő hitelesítési típusokat támogatja. A részletekért tekintse meg a megfelelő részeket:

- [Fiók kulcsának hitelesítése](#account-key-authentication)
- [Egyszerű szolgáltatásnév hitelesítése](#service-principal-authentication)
- [Felügyelt identitások az Azure-erőforrások hitelesítéséhez](#managed-identity)

>[!NOTE]
>- Ha a nyilvános Azure Integration Runtime használatával szeretne csatlakozni a Data Lake Storage Gen2hoz azzal, hogy kihasználja a **megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiók** számára jelölőnégyzetet az Azure Storage tűzfalon, a [felügyelt identitás-hitelesítést](#managed-identity)kell használnia.
>- Ha a Base vagy a COPY utasítást használja az adatok Azure szinapszis-elemzésbe való betöltéséhez, ha a forrás-vagy előkészítési Data Lake Storage Gen2 Azure Virtual Network-végponttal van konfigurálva, akkor a szinapszis által igényelt felügyelt identitás-hitelesítést kell használnia. Tekintse meg a [felügyelt identitás hitelesítése](#managed-identity) szakaszt további konfigurálási előfeltételekkel.

### <a name="account-key-authentication"></a>Fiók kulcsának hitelesítése

A Storage-fiók kulcsos hitelesítésének használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot **AzureBlobFS** értékre kell beállítani. |Yes |
| url | Data Lake Storage Gen2 végpontja a mintázatával `https://<accountname>.dfs.core.windows.net` . | Yes |
| accountKey | Data Lake Storage Gen2hoz tartozó fiók kulcsa. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Yes |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha ez a tulajdonság nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |No |

>[!NOTE]
>A másodlagos ADLS-fájlrendszer végpontja nem támogatott a fiók kulcsos hitelesítésének használatakor. Más hitelesítési típusokat is használhat.

**Példa**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az egyszerű szolgáltatás hitelesítésének használatához kövesse az alábbi lépéseket.

1. Az alkalmazás [regisztrálása Azure ad-Bérlővel](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)című cikk lépéseit követve regisztrálja az alkalmazás entitásait Azure Active Directoryban (Azure ad). Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

2. Adja meg a szolgáltatásnév megfelelő engedélyét. A [fájlok és könyvtárak hozzáférés-vezérlési listájáról](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories) Data Lake Storage Gen2 az engedélyek működésével kapcsolatos példák című részben.

    - **Forrásként**: Storage Explorerban legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához és a fájlrendszerhez, valamint **olvasási** engedéllyel kell rendelkeznie a másolandó fájlokhoz. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob-Adatolvasó** szerepkört.
    - **Fogadóként:** Storage Explorerban legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához és a fájlrendszerhez, valamint **írási** engedéllyel a fogadó mappához. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

>[!NOTE]
>Ha Data Factory felhasználói felületet használ a létrehozáshoz, és az egyszerű szolgáltatás nem a "Storage blob-Adatolvasó/közreműködői" szerepkörrel van beállítva a IAM-ban, akkor a kapcsolódás tesztelése **vagy a mappák** tallózása/navigálása során válassza a "kapcsolódás a fájl elérési útjához" vagy a "Tallózás a megadott elérési úttal" lehetőséget, és adjon meg egy elérési utat a folytatáshoz.

A társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot **AzureBlobFS** értékre kell beállítani. |Yes |
| url | Data Lake Storage Gen2 végpontja a mintázatával `https://<accountname>.dfs.core.windows.net` . | Yes |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Yes |
| servicePrincipalCredentialType | Az egyszerű szolgáltatás hitelesítéséhez használandó hitelesítőadat-típus. Az engedélyezett értékek a következők: **ServicePrincipalKey** és **ServicePrincipalCert**. | Yes |
| servicePrincipalCredential | Az egyszerű szolgáltatás hitelesítő adatai. <br/> Ha a **ServicePrincipalKey** -t használja a hitelesítő adat típusaként, adja meg az alkalmazás kulcsát. Ezt a mezőt **SecureString** kell megjelölni, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). <br/> Ha hitelesítő adatként használja a **ServicePrincipalCert** -t, Azure Key Vault-ben lévő tanúsítványra hivatkozik. | Yes |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. Ezt a mezőt **SecureString** kell megjelölni, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). <br/> Ez a tulajdonság továbbra is támogatott `servicePrincipalId`  +  `servicePrincipalKey` . Mivel az ADF új egyszerű szolgáltatás tanúsítvány-hitelesítését adja meg, az egyszerű szolgáltatásnév hitelesítésének új modellje `servicePrincipalId`  +  `servicePrincipalCredentialType`  +  `servicePrincipalCredential` . | No |
| Bérlő | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekéri a Azure Portal jobb felső sarkában lévő egér fölé. | Yes |
| azureCloudType | Az egyszerű szolgáltatásnév hitelesítéséhez adja meg, hogy milyen típusú Azure-alapú felhőalapú környezetet kell regisztrálni a Azure Active Directory alkalmazáshoz. <br/> Az engedélyezett értékek a következők: **AzurePublic**, **AzureChina**, **AzureUsGovernment** és **AzureGermany**. Alapértelmezés szerint a rendszer az adatfeldolgozó felhőalapú környezetét használja. | No |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |No |

**Példa: egyszerű szolgáltatásnév hitelesítésének használata**

Az egyszerű szolgáltatásnév kulcsát a Azure Key Vault is tárolhatja.

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: egyszerű szolgáltatás tanúsítványalapú hitelesítésének használata**
```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adatok előállítója [felügyelt identitással társítható az Azure-erőforrásokhoz](data-factory-service-identity.md), amely az adott adatelőállítót képviseli. Ezt a felügyelt identitást közvetlenül használhatja Data Lake Storage Gen2 hitelesítéshez, hasonlóan a saját egyszerű szolgáltatásnév használatához is. Lehetővé teszi a kijelölt gyár számára az adatok elérését és másolását a Data Lake Storage Gen2.

Az Azure erőforrás-hitelesítéshez használt felügyelt identitások használatához kövesse az alábbi lépéseket.

1. [Kérje le a Data Factory felügyelt identitás adatait](data-factory-service-identity.md#retrieve-managed-identity) úgy, hogy átmásolja a **felügyelt identitás objektum azonosítóját** a gyári értékkel együtt.

2. Adja meg a felügyelt identitás megfelelő engedélyét. A [fájlok és könyvtárak hozzáférés-vezérlési listájáról](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)a Data Lake Storage Gen2ban talál példákat az engedélyek működéséről.

    - **Forrásként**: Storage Explorerban legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához és a fájlrendszerhez, valamint **olvasási** engedéllyel kell rendelkeznie a másolandó fájlokhoz. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob-Adatolvasó** szerepkört.
    - **Fogadóként:** Storage Explorerban legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához és a fájlrendszerhez, valamint **írási** engedéllyel a fogadó mappához. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

>[!NOTE]
>Ha Data Factory felhasználói felületet használ a létrehozáshoz, és a felügyelt identitás nincs beállítva a "Storage blob-Adatolvasó/közreműködői" szerepkörrel a IAM-ban, akkor a kapcsolódás vagy a Tallózás/Navigálás mappák között válassza a "kapcsolódás a fájl elérési útjához" vagy a "Tallózás a megadott elérési úttal" lehetőséget, és adjon meg egy elérési utat **olvasási + végrehajtás** engedéllyel.

>[!IMPORTANT]
>Ha a Data Lake Storage Gen2ból származó adatok betöltéséhez a Base vagy a COPY utasítást használja az Azure szinapszis Analytics szolgáltatásba, akkor a felügyelt identitások hitelesítésének használatakor a Data Lake Storage Gen2 esetében is kövesse az [útmutató](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)1 – 3. lépését. Ezek a lépések regisztrálják a kiszolgálót az Azure AD-ben, és hozzárendelik a Storage blob adatközreműködői szerepkört a kiszolgálóhoz. Data Factory kezeli a többit. Ha egy Azure Virtual Network-végponttal konfigurálja a blob Storage-t, akkor azt is engedélyeznie kell, hogy a **megbízható Microsoft-szolgáltatások hozzáférjenek ehhez a Storage-fiókhoz** az Azure Storage **-fiók tűzfala és a virtuális hálózatok** beállítások menüjében, a szinapszisok által megkövetelt módon.

A társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot **AzureBlobFS** értékre kell beállítani. |Yes |
| url | Data Lake Storage Gen2 végpontja a mintázatával `https://<accountname>.dfs.core.windows.net` . | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |No |

**Példa**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak Data Lake Storage Gen2 `location` a Format-alapú adatkészlet beállítások területén:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| típus       | Az `location` adatkészletben található Type tulajdonságot **AzureBlobFSLocation** értékre kell állítani. | Yes      |
| Fájlrendszer | A Data Lake Storage Gen2 fájlrendszer neve.                              | No       |
| folderPath | Az adott fájlrendszerben található mappa elérési útja. Ha a mappák szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | No       |
| fileName   | A fájl neve a megadott fileSystem + folderPath. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | No       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját a [másolási tevékenységek konfigurációi](copy-activity-overview.md#configuration) és a [folyamatok és tevékenységek](concepts-pipelines-activities.md)című részben találja. Ez a szakasz a Data Lake Storage Gen2 forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 a forrás típusaként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az adatok ADLS Gen2ból való másolásának számos lehetősége van:

- Másolás az adatkészletben megadott megadott elérési útra.
- Helyettesítő karakteres szűrő a mappa elérési útja vagy fájlneve alapján: `wildcardFolderPath` és `wildcardFileName` .
- Másolja a megadott szövegfájlban definiált fájlokat fájltípusként, lásd: `fileListPath` .

A következő tulajdonságok támogatottak Data Lake Storage Gen2 `storeSettings` Formátum-alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| típus                     | A Type tulajdonságot a `storeSettings` **AzureBlobFSReadSettings** értékre kell állítani. | Yes                                           |
| ***Keresse meg a másolandó fájlokat:*** |  |  |
| 1. lehetőség: statikus elérési út<br> | Másolás az adatkészletben megadott fájlrendszerből vagy mappából vagy fájlból. Ha az összes fájlt egy fájlrendszerből vagy mappából kívánja átmásolni, a következőképpen adhatja meg: `wildcardFileName` `*` . |  |
| 2. lehetőség: helyettesítő karakter<br>- wildcardFolderPath | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott fájlrendszerben helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakter egyezése) `^` <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | No                                            |
| 2. lehetőség: helyettesítő karakter<br>- wildcardFileName | A forrásfájl szűréséhez a megadott fájlrendszer + folderPath/wildcardFolderPath helyettesítő karakterekkel rendelkező fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakter egyezése) `^`  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Yes |
| 3. lehetőség: a fájlok listája<br>- fileListPath | Egy adott fájl másolását jelzi. Mutasson egy szövegfájlra, amely tartalmazza a másolni kívánt fájlok listáját, soronként egy fájlt, amely az adatkészletben konfigurált útvonal relatív elérési útja.<br/>Ha ezt a beállítást használja, ne adja meg a fájl nevét az adatkészletben. További példákat a [fájllista példákban](#file-list-examples)talál. |No |
| ***További beállítások:*** |  | |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. <br>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**).<br>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . |No |
| deleteFilesAfterCompletion | Azt jelzi, hogy a rendszer törli-e a bináris fájlokat a forrás-áruházból, miután sikeresen áthelyezte a célhelyre. A fájl törlése fájl alapján történik, így ha a másolási tevékenység meghiúsul, néhány fájl már át lett másolva a célhelyre, és törlődik a forrásból, míg mások továbbra is a forrás-áruházban maradnak. <br/>Ez a tulajdonság csak bináris fájlok másolási forgatókönyv esetén érvényes. Az alapértelmezett érték: false. |No |
| modifiedDatetimeStart    | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. <br>A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` a dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` a dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték, ki lesz választva.<br/>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Lásd fentebb.                                               | No                                            |
| enablePartitionDiscovery | A particionált fájlok esetében adja meg, hogy szeretné-e elemezni a partíciókat a fájl elérési útján, majd adja hozzá őket további forrásként szolgáló oszlopként.<br/>Az engedélyezett értékek: **false** (alapértelmezett) és **true (igaz**). | No                                            |
| partitionRootPath | Ha engedélyezve van a partíciók felderítése, akkor a particionált mappák adatoszlopként való olvasásához a gyökér elérési útját kell megadni.<br/><br/>Ha nincs megadva, a rendszer alapértelmezés szerint<br/>– Ha a fájl elérési útját használja az adatkészletben vagy a forrásban található fájlok listáján, a partíció gyökerének elérési útja az adatkészletben konfigurált útvonal.<br/>– Ha helyettesítő mappa szűrőt használ, a partíció gyökerének elérési útja az első helyettesítő karakter előtti Alútvonal.<br/><br/>Tegyük fel például, hogy az adatkészletben az elérési utat "root/Folder/Year = 2020/hónap = 08/Day = 27" értékre konfigurálja:<br/>– Ha a partíció gyökerének elérési útját "gyökér/mappa/év = 2020" értékre állítja, a másolási tevékenység két további oszlopot fog előállítani, `month` és a `day` "08" és "27" értéket is kijelöli a fájlokban lévő oszlopokon kívül.<br/>– Ha nincs megadva a partíció gyökerének elérési útja, nem jön létre további oszlop. | No                                            |
| maxConcurrentConnections | A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No                                            |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 fogadó típusként

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

A következő tulajdonságok támogatottak Data Lake Storage Gen2 a `storeSettings` Format-alapú másolási fogadó beállításaiban:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| típus                     | A Type tulajdonságot a `storeSettings` **AzureBlobFSWriteSettings** értékre kell állítani. | Yes      |
| copyBehavior             | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | No       |
| blockSizeInMB | A blokk méretének megadása MEGABÁJTban az adatADLS Gen2ba való íráshoz. További [információ a blokkos blobokról](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Az engedélyezett érték **4 MB és 100 MB között** van. <br/>Alapértelmezés szerint az ADF automatikusan meghatározza a blokk méretét a forrás-áruház típusa és adatai alapján. A nem bináris másolási ADLS Gen2 az alapértelmezett blokk mérete 100 MB, hogy a legtöbb 4,95-TB adathoz illeszkedjen. Nem lehet optimális, ha az adatai nem nagyméretűak, különösen akkor, ha olyan helyi Integration Runtime használ, amely gyenge hálózattal működik, ami működési időtúllépést vagy teljesítménnyel kapcsolatos problémát okoz. Explicit módon megadhatja a blokk méretét, miközben biztosítja, hogy a blockSizeInMB * 50000 elég nagy legyen az adattároláshoz, máskülönben a másolási tevékenység futtatása sikertelen lesz. | No |
| maxConcurrentConnections | A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No       |

**Példa**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Üres, alapértelmezett használata) | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Üres, alapértelmezett használata) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Példák a fájllista

Ez a szakasz a fájllista elérési útjának a másolási tevékenység forrásában való használatának eredményét írja le.

Feltételezve, hogy rendelkezik a következő forrás-mappa struktúrájával, és félkövéren szeretné átmásolni a fájlokat:

| Példa a forrás struktúrájára                                      | Tartalom FileListToCopy.txt                             | ADF-konfiguráció                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| fájlrendszer<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metaadatok<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Az adatkészletben:**<br>-Fájlrendszer: `filesystem`<br>-Mappa elérési útja: `FolderA`<br><br>**A másolási tevékenység forrása:**<br>-Fájllista elérési útja: `filesystem/Metadata/FileListToCopy.txt` <br><br>A fájl-lista elérési útja ugyanazon az adattárban található szövegfájlra mutat, amely tartalmazza a másolni kívánt fájlok listáját, és soronként egy fájlt az adatkészletben konfigurált útvonal relatív elérési útjával. |


### <a name="some-recursive-and-copybehavior-examples"></a>Néhány rekurzív és copyBehavior példa

Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredményét írja le.

| rekurzív | copyBehavior | Forrás mappa szerkezete | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a fájl3 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File4 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File5 automatikusan generált neve |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 + fájl3 + File4 + File5 tartalom egyetlen fájlba van egyesítve, amely egy automatikusan létrehozott fájlnevet tartalmaz. |
| hamis |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 tartalma egyetlen fájlba van egyesítve, amely egy automatikusan létrehozott fájlnevet tartalmaz. a file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |

## <a name="preserve-metadata-during-copy"></a>Metaadatok megőrzése a másolás során

Ha fájlokat másol az Amazon S3/Azure Blob/Azure Data Lake Storage Gen2ról Azure Data Lake Storage Gen2/Azure Blobba, megtarthatja a fájl metaadatainak megőrzését az adatokkal együtt. További információ: a [metaadatok megőrzése](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> ACL-ek megőrzése Data Lake Storage Gen1/Gen2

Ha Azure Data Lake Storage Gen1/Gen2 másolja a fájlokat a Gen2-be, dönthet úgy, hogy megőrzi a POSIX hozzáférés-vezérlési listákat (ACL-eket) az adatokkal együtt. További információ az [ACL-ek megőrzése Data Lake Storage Gen1/Gen2 és a Gen2](copy-activity-preserve-metadata.md#preserve-acls)között.

>[!TIP]
>Az adatok Azure Data Lake Storage Gen1ból a Gen2-be való másolásához lásd: [adatok másolása Azure Data Lake Storage Gen1ról a Gen2](load-azure-data-lake-storage-gen2-from-gen1.md) -be a Azure Data Factory használatával a lépésenkénti és ajánlott eljárásokhoz.

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatok leképezési folyamatokban való átalakításakor a következő formátumokban olvashat és írhat fájlokat Azure Data Lake Storage Gen2ról:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Common adatmodell (előzetes verzió)](format-common-data-model.md#mapping-data-flow-properties)
* [Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

A megadott beállítások formázása az adott formátum dokumentációjában található. További információ: [forrás-átalakítás a](data-flow-source.md) leképezési adatfolyamban és a fogadó [átalakítás a leképezési folyamatokban](data-flow-sink.md).

### <a name="source-transformation"></a>Forrás-átalakítás

A forrás-átalakításban egy tárolóból, mappából vagy különálló fájlból is elolvasható Azure Data Lake Storage Gen2. A **forrás beállításai** lapon kezelheti a fájlok olvasásának módját. 

![Forrás beállításai](media/data-flow/sourceOptions1.png "Forrás beállításai")

**Helyettesítő karakter elérési útja:** A helyettesítő karakterek használatakor a rendszer az ADF-et az egyes egyező mappákon és fájlokon keresztül, egyetlen forrás-átalakításon keresztüli hurokra utasítja. Ez egy hatékony módszer több fájl feldolgozására egyetlen folyamaton belül. Több helyettesítő karakterből álló mintázatot is hozzáadhat, ha a meglévő helyettesítő karakter fölé viszi a kurzort.

A forrás tárolóban válasszon egy olyan fájlt, amely megfelel a mintának. Az adatkészletben csak tároló adható meg. A helyettesítő karakteres elérési útnak ezért a gyökérmappa elérési útját is tartalmaznia kell.

Helyettesítő karakteres példák:

* ```*``` A karakterek tetszőleges halmazát jelöli.
* ```**``` Rekurzív könyvtár beágyazását jelöli
* ```?``` Egy karakter cseréje
* ```[]``` A zárójelben szereplő több karakternek felel meg.

* ```/data/sales/**/*.csv``` Az összes CSV-fájl beolvasása a/Data/Sales alatt
* ```/data/sales/20??/**/``` Minden fájl beolvasása a huszadik században
* ```/data/sales/*/*/*.csv``` CSV-fájlok beolvasása két szinten a/Data/Sales alatt
* ```/data/sales/2004/*/12/[XY]1?.csv``` Minden CSV-fájl beolvasása a 2004-ben decemberben, X vagy Y előtaggal, kétjegyű számmal

**Partíció gyökerének elérési útja:** Ha a fájlformátumban particionált mappák vannak ```key=value``` (például év = 2019), akkor a partíciós mappa fájának legfelső szintjét hozzárendelheti az adatáramlási adatfolyamban található oszlop neveként.

Először állítson be egy helyettesítő karaktert, amely tartalmazza az összes olyan elérési utat, amely a particionált mappák és az elolvasni kívánt levél fájlok.

![Partíciós forrásfájl beállításai](media/data-flow/partfile2.png "Partíciós fájl beállítása")

A partíció gyökerének elérési útja beállítással határozhatja meg, hogy a mappa szerkezete milyen felső szintű legyen. Amikor az adatelőnézet használatával tekinti meg az adatai tartalmát, látni fogja, hogy az ADF hozzáadja az egyes mappák szintjein talált feloldott partíciókat.

![Partíció gyökerének elérési útja](media/data-flow/partfile1.png "Partíciós gyökér elérési útjának előnézete")

**Fájlok listája:** Ez egy beállított fájl. Hozzon létre egy szövegfájlt, amely tartalmazza a feldolgozandó relatív elérési út fájljainak listáját. Mutasson erre a szövegfájlra.

**A fájl nevét tároló oszlop:** Tárolja a forrásfájl nevét az adataiban található oszlopban. Itt adjon meg egy új oszlopnevet a fájlnév karakterláncának tárolásához.

**Befejezés után:** Ha az adatfolyam futtatása után semmit nem kíván végrehajtani a forrásfájlban, törölje a forrásfájlt, vagy helyezze át a forrásfájlt. Az áthelyezés elérési útjai relatívak.

Ha a forrásfájlokat másik helyre szeretné áthelyezni a feldolgozás után, először válassza a "áthelyezés" lehetőséget a fájl művelethez. Ezután állítsa be a "from" könyvtárat. Ha nem használ helyettesítő karaktereket az elérési úthoz, akkor a "from" beállítás lesz a forrás mappájával megegyező mappa.

Ha a forrás elérési útja helyettesítő karakterrel rendelkezik, a szintaxis az alábbihoz hasonlóan fog kinézni:

```/data/sales/20??/**/*.csv```

A "from" lehetőséget adhatja meg

```/data/sales```

És "to"

```/backup/priorSales```

Ebben az esetben a rendszer a/Data/Sales alatt forrásozott összes fájlt áthelyezi a/backup/priorSales.

> [!NOTE]
> A fájl műveletei csak akkor futnak, amikor egy folyamaton belül indítja el az adatfolyamatot (a folyamat hibakeresési vagy végrehajtási futtatása), amely az adatfolyamok végrehajtása tevékenységet használja egy folyamaton belül. A fájl műveletei *nem* futnak adatfolyam-hibakeresési módban.

**Szűrés legutóbb módosítva:** A feldolgozás alatt álló fájlokat szűrheti, ha az utolsó módosítás dátumát megadja. Az összes dátum-idő UTC. 

### <a name="sink-properties"></a>Fogadó tulajdonságai

A fogadó átalakításban megírhatja Azure Data Lake Storage Gen2 tárolóba vagy mappájába. a **Beállítások** lapon kezelheti a fájlok írásának módját.

![fogadó beállításai](media/data-flow/file-sink-settings.png "fogadó beállításai")

**Törölje a mappát:** Meghatározza, hogy a célmappa törlődik-e az adatírás előtt.

**Fájlnév beállítás:** Meghatározza, hogy a célfájl hogyan legyen elnevezve a célmappában. A fájlnév beállításai a következők:
   * **Default (alapértelmezett**): a Spark használatával a fájlok a részek alapértelmezett értékei alapján megtalálhatók.
   * **Minta**: adjon meg egy mintát, amely egy partíción lévő kimeneti fájlokat sorolja fel. A **hitelek [n]. csv** például loans1.csv, loans2.csv és így tovább fog létrejönni.
   * **/Partíció**: adjon meg egy fájlnevet partícióként.
   * **Oszlopbeli adatként**: állítsa a kimeneti fájlt egy oszlop értékére. Az elérési út az adatkészlet-tárolóhoz viszonyítva nem a célmappához képest. Ha a mappa elérési útja szerepel az adatkészletben, a rendszer felülbírálja.
   * **Kimenet egyetlen fájlba**: a particionált kimeneti fájlok egyetlen elnevezett fájlba kombinálhatók. Az elérési út az adatkészlet mappájához képest relatív. Vegye figyelembe, hogy a saját egyesítési művelete valószínűleg meghiúsulhat a csomópont méretétől függően. Ez a beállítás nagyméretű adatkészletek esetén nem ajánlott.

**Összes idézet:** Meghatározza, hogy az idézőjelek közé kell-e foglalni az összes értéket

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja a fenti szakaszban említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát **AzureBlobFSFile** értékre kell állítani. |Yes |
| folderPath | A Data Lake Storage Gen2 mappájának elérési útja. Ha nincs megadva, a gyökérre mutat. <br/><br/>A helyettesítő karakteres szűrő használata támogatott. Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (nulla vagy egyetlen karakternek felelnek meg). `^`Ha a tényleges mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter be van zárva, használja a Escape-nevet. <br/><br/>Példák: fájlrendszer/mappa/. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. |No |
| fileName | A megadott "folderPath" alatt lévő fájlok neve vagy helyettesítő karakteres szűrője. Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes fájlra mutat. <br/><br/>A Filter (szűrő) esetében a megengedett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egyetlen karakternek felel meg).<br/>– 1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>`^`Ha a tényleges fájlnév helyettesítő karakterrel rendelkezik, vagy a escape-karakter be van zárva, használja a Escape-nevet.<br/><br/>Ha nincs megadva fájlnév a kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*adat. [ tevékenység futtatási azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha konfigurálva]*", például" Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz ". Ha egy táblázatos forrásból másol egy lekérdezés helyett egy tábla nevét, a név minta a következő: "*[Table Name]. [ Format]. [tömörítés, ha konfigurálva]*", például" MyTable.csv ". |No |
| modifiedDatetimeStart | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a és a közötti időtartományon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| No |
| modifiedDatetimeEnd | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a és a közötti időtartományon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| No |
| formátumban | Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat** és **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a **Format** értékre a következő értékek egyikére. További információ: [szöveg formátuma](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a **gzip**, a **deflate**, a **BZip2** és a **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak**. |No |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a **folderPath** kell megadni.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** mappát, és nevezze el a **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **fájlnevet** egy helyettesítő szűrővel kell megadnia. 

**Példa**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásának modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát **AzureBlobFSSource** értékre kell állítani. |Yes |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát.<br/>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | No |
| maxConcurrentConnections | A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Örökölt másolási tevékenység fogadó modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát **AzureBlobFSSink** értékre kell állítani. |Yes |
| copyBehavior | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | No |
| maxConcurrentConnections | A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

**Példa**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések

A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).