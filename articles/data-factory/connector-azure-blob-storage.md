---
title: Az Azure Blob Storage-ban tárolt adatmásolási és-átalakítási funkciók
description: Megtudhatja, hogyan másolhat adatok a blob Storage-ba és a-ból, és hogyan alakíthatja át a blob Storage-ban lévő adatok Data Factory használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: c1e0dffafafa76e90ec57ce1a00fb8e155ff4edf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608095"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Az Azure Blob Storage-ban lévő Adatmásolás és-átalakítás Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> - [1-es verzió](v1/data-factory-azure-blob-connector.md)
> - [Aktuális verzió](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok és az Azure Blob Storage-ba történő másolásához. Azt is leírja, hogyan használható az adatfolyam tevékenység az Azure Blob Storage-beli adatátalakításhoz. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

>[!TIP]
>Az adatközpontok vagy adattárházak áttelepítési forgatókönyvének megismeréséhez lásd: a [Azure Data Factory használata adatok áttelepítéséhez az adatközpontból vagy az adattárházból az Azure-ba](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Blob Storage-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

A másolási tevékenységhez a blob Storage-összekötő a következőket támogatja:

- Blobok másolása általános célú Azure Storage-fiókokba és gyors/lassú blob Storage-tárolóba.
- Blobok másolása egy fiók kulcsával, egy szolgáltatás közös hozzáférésű aláírásával (SAS), egy egyszerű szolgáltatással vagy egy felügyelt identitással az Azure erőforrás-hitelesítésekhez.
- Blobok másolása blokkból, hozzáfűzésből vagy blobokból, és az adatok másolása csak a Blobok blokkolására.
- Blobok másolása, illetve a Blobok elemzése vagy generálása [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md).
- A [fájlok metaadatainak megőrzése a másolás során](#preserving-metadata-during-copy).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a blob Storage-hoz kapcsolódó Data Factory definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Ez a blob Storage-összekötő a következő hitelesítési típusokat támogatja. A részletekért tekintse meg a megfelelő szakaszt.

- [Fiók kulcsának hitelesítése](#account-key-authentication)
- [Közös hozzáférésű aláírások hitelesítése](#shared-access-signature-authentication)
- [Egyszerű szolgáltatásnév hitelesítése](#service-principal-authentication)
- [Felügyelt identitások az Azure erőforrás-hitelesítéshez](#managed-identity)

>[!NOTE]
>- Ha a nyilvános Azure integrációs modult szeretné használni a blob Storage-hoz való csatlakozáshoz, azzal, hogy a **megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiók** számára engedélyezve van az Azure Storage-tűzfalon, a [felügyelt identitások hitelesítését](#managed-identity)kell használnia.
>- Ha a Base vagy a COPY utasítást használja az adatok Azure szinapszis-elemzésbe való betöltéséhez, ha a forrás-vagy az előkészítési blob Storage egy Azure Virtual Network-végponttal van konfigurálva, akkor a Szinapszishoz szükséges felügyelt identitás-hitelesítést kell használnia. További konfigurálási előfeltételekért tekintse meg a [felügyelt identitás hitelesítése](#managed-identity) szakaszt.

>[!NOTE]
>Az Azure HDInsight és a Azure Machine Learning tevékenységek csak az Azure Blob Storage-fiók kulcsait használó hitelesítést támogatják.

### <a name="account-key-authentication"></a>Fiók kulcsának hitelesítése

A Data Factory a következő tulajdonságokat támogatja a Storage-fiók kulcsos hitelesítéséhez:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A `type` tulajdonságot `AzureBlobStorage` (javasolt) vagy `AzureStorage` (lásd a következő megjegyzéseket) kell megadni. | Yes |
| connectionString | Határozza meg a tulajdonsághoz való kapcsolódáshoz szükséges adatokat `connectionString` . <br/> Azt is megteheti, hogy Azure Key Vault a fiók kulcsát, és lekéri a `accountKey` konfigurációt a kapcsolatok sztringből. További információkért tekintse meg a következő mintákat és az [áruház hitelesítő adatait Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure Integration Runtime vagy a saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, a szolgáltatás az alapértelmezett Azure Integration Runtime-t használja. | No |

>[!NOTE]
>A fiók kulcsos hitelesítésének használata esetén a másodlagos Blob service végpont nem támogatott. Más hitelesítési típusokat is használhat.

>[!NOTE]
>Ha a `AzureStorage` Type társított szolgáltatást használja, azt a rendszer továbbra is támogatja. Azt javasoljuk azonban, hogy az új `AzureBlobStorage` társított szolgáltatás típusát használja tovább.

**Példa**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
          "referenceName": "<name of Integration Runtime>",
          "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: a fiók kulcsának tárolása Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="shared-access-signature-authentication"></a>Közös hozzáférésű aláírások hitelesítése

A közös hozzáférésű aláírások delegált hozzáférést biztosítanak a Storage-fiók erőforrásaihoz. A megosztott elérési aláírás használatával a megadott időpontra korlátozhatja az ügyfél számára a Storage-fiókban lévő objektumokra vonatkozó engedélyek megadását. 

Nem kell megosztania a fiók hozzáférési kulcsait. A közös hozzáférésű aláírás egy URI, amely magában foglalja a lekérdezési paramétereit. a tárolási erőforrásokhoz való hitelesített hozzáféréshez szükséges összes információ. A megosztott hozzáférési aláírással rendelkező tárolási erőforrások eléréséhez az ügyfélnek csak a közös hozzáférési aláírásban kell átadnia a megfelelő konstruktort vagy metódust.

A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: közös hozzáférésű aláírások [: a közös hozzáférésű aláírás modelljének megismerése](../storage/common/storage-sas-overview.md).

> [!NOTE]
>- A Data Factory mostantól a *szolgáltatás közös hozzáférési aláírásait* és a *fiók közös hozzáférési aláírásait* is támogatja. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások használatával](../storage/common/storage-sas-overview.md).
>- A későbbi adatkészlet-konfigurációkban a mappa elérési útja a tároló szintjétől kezdődő abszolút elérési út. Konfigurálnia kell egy igazítást a SAS URI elérési útjával.

A Data Factory a következő tulajdonságokat támogatja a közös hozzáférésű aláírás-hitelesítés használatához:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A `type` tulajdonságot `AzureBlobStorage` (javasolt) vagy `AzureStorage` (lásd a következő megjegyzést) kell megadni. | Yes |
| sasUri | A Storage-erőforrásokhoz, például a blobhoz vagy a tárolóhoz tartozó megosztott elérési aláírás URI azonosítójának megadása. <br/>A mező megjelölése úgy, `SecureString` hogy biztonságosan tárolja Data Factoryban. A SAS-tokent Azure Key Vault is elhelyezheti az automatikus elforgatás és a jogkivonat-rész eltávolításához. További információkért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vaultban](store-credentials-in-key-vault.md). | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure Integration Runtime vagy a saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, a szolgáltatás az alapértelmezett Azure Integration Runtime-t használja. | No |

>[!NOTE]
>Ha a `AzureStorage` Type társított szolgáltatást használja, azt a rendszer továbbra is támogatja. Azt javasoljuk azonban, hogy az új `AzureBlobStorage` társított szolgáltatás típusát használja tovább.

**Példa**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: a fiók kulcsának tárolása Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Közös hozzáférésű aláírási URI létrehozásakor vegye figyelembe a következő szempontokat:

- Állítsa be a megfelelő olvasási/írási engedélyeket az objektumokon attól függően, hogy a társított szolgáltatás (olvasás, írás, olvasás/írás) hogyan legyen használatban az adatelőállítóban.
- Megfelelő **lejárati idő** beállítása. Győződjön meg arról, hogy a tárolási objektumokhoz való hozzáférés a folyamat aktív időszakán belül nem jár le.
- Az URI-t a megfelelő tárolóban vagy blobban kell létrehozni a szükséges igények alapján. A Blobok közös hozzáférésű aláírási URI-ja lehetővé teszi az Data Factory számára az adott blob elérését. A blob Storage-tárolók közös hozzáférésű aláírási URI-ja lehetővé teszi, hogy Data Factory megismételje a tárolóban lévő blobokat. Ha később több vagy kevesebb objektumhoz szeretne hozzáférést biztosítani, vagy a közös hozzáférésű aláírás URI-JÁT szeretné frissíteni, ne felejtse el frissíteni a társított szolgáltatást az új URI-val.

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az Azure Storage szolgáltatás egyszerű hitelesítésével kapcsolatos általános információkért lásd: az [Azure Storage-hoz való hozzáférés hitelesítése Azure Active Directory használatával](../storage/common/storage-auth-aad.md).

Az egyszerű szolgáltatás hitelesítésének használatához kövesse az alábbi lépéseket:

1. Az alkalmazás [Azure ad-Bérlővel való regisztrálását](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)követően regisztráljon egy Azure Active Directory (Azure ad) alkalmazásbeli entitást. Jegyezze fel ezeket az értékeket, amelyeket a társított szolgáltatás definiálásához használ:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

2. Adja meg az egyszerű szolgáltatás megfelelő engedélyét az Azure Blob Storage-ban. További információ a szerepkörökről: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adatokhoz való hozzáféréshez](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Forrásként** a **hozzáférés-vezérlés (iam)** mezőben adja meg legalább a **Storage blob-Adatolvasó** szerepkört.
    - A **fogadó, a** hozzáférés- **vezérlés (iam)** mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

Ezek a tulajdonságok egy Azure Blob Storage-beli társított szolgáltatás esetén támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **AzureBlobStorage** értékre kell beállítani. | Yes |
| serviceEndpoint | Határozza meg az Azure Blob Storage szolgáltatás végpontját a mintázatával `https://<accountName>.blob.core.windows.net/` . | Yes |
| accountKind | Adja meg a Storage-fiók típusát. Az engedélyezett értékek a következők: **Storage** (általános célú v1), **StorageV2** (általános célú v2), **BlobStorage** vagy **BlockBlobStorage**. <br/><br/>Ha az Azure Blob társított szolgáltatást adatforgalomban használja, a felügyelt identitás vagy egyszerű szolgáltatásnév hitelesítése nem támogatott, ha a fiók típusa üres vagy "Storage". Adja meg a megfelelő fiók típusát, válasszon másik hitelesítést, vagy frissítse a Storage-fiókját az általános célú v2-re. | No |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Yes |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. Ezt a mezőt **SecureString** kell megjelölni, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Yes |
| Bérlő | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekéri a Azure Portal jobb felső sarkában látható fölé. | Yes |
| azureCloudType | Az egyszerű szolgáltatás hitelesítéséhez adja meg az Azure Cloud Environment típusát, amelyre a Azure Active Directory alkalmazás regisztrálva van. <br/> Az engedélyezett értékek a következők: **AzurePublic**, **AzureChina**, **AzureUsGovernment** és **AzureGermany**. Alapértelmezés szerint a rendszer az adatfeldolgozó felhőalapú környezetét használja. | No |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure Integration Runtime vagy a saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, a szolgáltatás az alapértelmezett Azure Integration Runtime-t használja. | No |

>[!NOTE]
>
>- Ha a blob-fiókja lehetővé teszi a [Soft delete](../storage/blobs/soft-delete-blob-overview.md)használatát, az egyszerű szolgáltatás hitelesítése nem támogatott az adatfolyamban.
>- Ha a blob Storage-t a privát végponton keresztül éri el az adatfolyam használatával, akkor vegye figyelembe, hogy az egyszerű szolgáltatás hitelesítésének használatakor az adatfolyam a blob-végpont helyett a ADLS Gen2 végponthoz csatlakozik. Győződjön meg arról, hogy a megfelelő privát végpontot hozza létre az ADF-ben a hozzáférés engedélyezéséhez.

>[!NOTE]
>Az egyszerű szolgáltatás hitelesítését csak a "AzureBlobStorage" típusú társított szolgáltatás támogatja, nem az előző "AzureStorage" type társított szolgáltatás.

**Példa**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Felügyelt identitások az Azure erőforrás-hitelesítéshez

Az adatok előállítója [felügyelt identitással társítható az Azure-erőforrásokhoz](data-factory-service-identity.md), amely az adott adatelőállítót képviseli. Ezt a felügyelt identitást közvetlenül használhatja a blob Storage-hitelesítéshez, amely hasonló a saját egyszerű szolgáltatásnév használatához. Ez lehetővé teszi, hogy ez a kijelölt gyár hozzáférjen a blob Storage-ból, és másolja azokat.

Az Azure Storage-hitelesítéssel kapcsolatos általános információkért lásd: az [Azure Storage-hoz való hozzáférés hitelesítése Azure Active Directory használatával](../storage/common/storage-auth-aad.md). Az Azure erőforrás-hitelesítéshez használt felügyelt identitások használatához kövesse az alábbi lépéseket:

1. [Data Factory felügyelt identitás információinak beolvasása](data-factory-service-identity.md#retrieve-managed-identity) a gyári azonosítóval ellátott felügyelt identitási objektum értékének másolásával.

2. Adja meg a felügyelt identitás engedélyt az Azure Blob Storage-ban. További információ a szerepkörökről: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adatokhoz való hozzáféréshez](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Forrásként** a **hozzáférés-vezérlés (iam)** mezőben adja meg legalább a **Storage blob-Adatolvasó** szerepkört.
    - A **fogadó, a** hozzáférés- **vezérlés (iam)** mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

>[!IMPORTANT]
>Ha Base vagy COPY utasítást használ a blob Storage-ból (forrásként vagy átmenetiként) való adatok betöltéséhez az Azure szinapszis Analytics szolgáltatásban, akkor a blob Storage-hoz tartozó felügyelt identitásos hitelesítés használata esetén győződjön meg arról, hogy az [útmutatóban](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)az 1 – 3. lépést is végrehajtja. Ezek a lépések regisztrálják a kiszolgálót az Azure AD-ben, és hozzárendelik a Storage blob adatközreműködői szerepkört a kiszolgálóhoz. Data Factory kezeli a többit. Ha egy Azure Virtual Network-végponttal konfigurálja a blob Storage-t, akkor azt is engedélyeznie kell, hogy a **megbízható Microsoft-szolgáltatások hozzáférjenek ehhez a Storage-fiókhoz** az Azure Storage **-fiók tűzfala és a virtuális hálózatok** beállítások menüjében, a szinapszisok által megkövetelt módon.

Ezek a tulajdonságok egy Azure Blob Storage-beli társított szolgáltatás esetén támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **AzureBlobStorage** értékre kell beállítani. | Yes |
| serviceEndpoint | Határozza meg az Azure Blob Storage szolgáltatás végpontját a mintázatával `https://<accountName>.blob.core.windows.net/` . | Yes |
| accountKind | Adja meg a Storage-fiók típusát. Az engedélyezett értékek a következők: **Storage** (általános célú v1), **StorageV2** (általános célú v2), **BlobStorage** vagy **BlockBlobStorage**. <br/><br/>Ha az Azure Blob társított szolgáltatást adatforgalomban használja, a felügyelt identitás vagy egyszerű szolgáltatásnév hitelesítése nem támogatott, ha a fiók típusa üres vagy "Storage". Adja meg a megfelelő fiók típusát, válasszon másik hitelesítést, vagy frissítse a Storage-fiókját az általános célú v2-re. | No |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure Integration Runtime vagy a saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, a szolgáltatás az alapértelmezett Azure Integration Runtime-t használja. | No |

> [!NOTE]
>
> - Ha a blob-fiókja lehetővé teszi a [Soft delete](../storage/blobs/soft-delete-blob-overview.md)használatát, a felügyelt identitások hitelesítése nem támogatott az adatfolyamban.
> - Ha a blob Storage-t a privát végponton keresztül éri el az adatfolyam használatával, vegye figyelembe, hogy ha a felügyelt identitások hitelesítését használják, az adatfolyam a blob-végpont helyett a ADLS Gen2 végponthoz csatlakozik. Győződjön meg arról, hogy a megfelelő privát végpontot hozza létre az ADF-ben a hozzáférés engedélyezéséhez.

> [!NOTE]
> Az Azure erőforrás-hitelesítés felügyelt identitásait csak az "AzureBlobStorage" típusú társított szolgáltatás támogatja, nem az előző "AzureStorage" type társított szolgáltatás.

**Példa**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az Azure Blob Storage `location` -ban a Format-alapú adatkészlet beállításai alatt:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| típus       | Az adatkészlet helyének **Type** tulajdonságát **AzureBlobStorageLocation** értékre kell állítani. | Yes      |
| tároló  | A blob-tároló.                                          | Yes      |
| folderPath | A mappa elérési útja az adott tárolóban. Ha helyettesítő karaktert szeretne használni a mappa szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításainál válassza ki azt. | No       |
| fileName   | A fájl neve a megadott tárolóban és a mappa elérési útja alatt. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és határozza meg, hogy a tevékenység forrásának beállításaiban. | No       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a blob Storage forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="blob-storage-as-a-source-type"></a>BLOB Storage forrás típusaként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az Azure Blob Storage `storeSettings` -ban a Format-alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| típus                     | A **Type** tulajdonságot a `storeSettings` **AzureBlobStorageReadSettings** értékre kell állítani. | Yes                                           |
| ***Keresse meg a másolandó fájlokat:*** |  |  |
| 1. lehetőség: statikus elérési út<br> | Másolás az adatkészletben megadott tárolóból vagy mappából vagy fájlból. Ha az összes blobot egy tárolóból vagy mappából kívánja átmásolni, a következőképpen adhatja meg: `wildcardFileName` `*` . |  |
| 2. lehetőség: blob-előtag<br>-előtag | A blob nevének előtagja a forrás Blobok szűréséhez az adatkészletben konfigurált megadott tárolóban. Azok a Blobok vannak kiválasztva, amelyeknél a nevek kezdődnek `container_in_dataset/this_prefix` . A szolgáltatás kiszolgálóoldali szűrőjét használja a blob Storage-hoz, ami jobb teljesítményt nyújt, mint a helyettesítő karakteres szűrő.<br><br>Ha előtagot használ, és úgy dönt, hogy a fájl alapú fogadóba másol egy megőrzött hierarchiát, a rendszer megőrzi az utolsó "/" előtag utáni alútvonalat. Tegyük fel például, hogy rendelkezik forrással  `container/folder/subfolder/file.txt` , és konfigurálja az előtagot `folder/sub` , majd a megőrzött fájl elérési útja `subfolder/file.txt` . | No                                                          |
| 3. lehetőség: helyettesítő karakter<br>- wildcardFolderPath | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott tárolóban helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg). A (z `^` ) használatával elkerülheti, hogy a Mappanév helyettesítő karakterrel vagy a Escape-karakterrel rendelkezik. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | No                                            |
| 3. lehetőség: helyettesítő karakter<br>- wildcardFileName | Az adott tároló és mappa elérési útjának (vagy helyettesítő mappa elérési útjának) a forrásfájlok szűrésére szolgáló helyettesítő karakterrel rendelkező fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg). `^`Ha a fájl neve helyettesítő karakter vagy a escape-karakter található, akkor a (z) használatával elkerülheti a menekülési karaktert. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Yes |
| 4. lehetőség: a fájlok listája<br>- fileListPath | Egy adott fájl másolását jelzi. Mutasson egy szövegfájlra, amely tartalmazza a másolni kívánt fájlok listáját, soronként egy fájlt, amely az adatkészletben konfigurált útvonal relatív elérési útja.<br/>Ha ezt a beállítást használja, ne adjon meg fájlnevet az adatkészletben. További példákat a [fájllista példákban](#file-list-examples)talál. | No |
| ***További beállítások:*** |  | |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a **rekurzív** értéke **true (igaz** ), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. <br>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**).<br>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | No |
| deleteFilesAfterCompletion | Azt jelzi, hogy a rendszer törli-e a bináris fájlokat a forrás-áruházból, miután sikeresen áthelyezte a célhelyre. A fájl törlése fájl alapján történik, így ha a másolási tevékenység meghiúsul, néhány fájl már át lett másolva a célhelyre, és törlődik a forrásból, míg mások továbbra is a forrás-áruházban maradnak. <br/>Ez a tulajdonság csak bináris fájlok másolási forgatókönyv esetén érvényes. Az alapértelmezett érték: false. | No |
| modifiedDatetimeStart    | A fájlok a következő attribútum alapján vannak szűrve: utoljára módosítva. <br>A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek **Null értékűek**, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de **Null** értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de **Null** értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.<br/>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Lásd fentebb.                                               | No                                            |
| enablePartitionDiscovery | A particionált fájlok esetében adja meg, hogy szeretné-e elemezni a partíciókat a fájl elérési útján, majd adja hozzá őket további forrásként szolgáló oszlopként.<br/>Az engedélyezett értékek: **false** (alapértelmezett) és **true (igaz**). | No                                            |
| partitionRootPath | Ha engedélyezve van a partíciók felderítése, akkor a particionált mappák adatoszlopként való olvasásához a gyökér elérési útját kell megadni.<br/><br/>Ha nincs megadva, a rendszer alapértelmezés szerint<br/>– Ha a fájl elérési útját használja az adatkészletben vagy a forrásban található fájlok listáján, a partíció gyökerének elérési útja az adatkészletben konfigurált útvonal.<br/>– Ha helyettesítő mappa szűrőt használ, a partíció gyökerének elérési útja az első helyettesítő karakter előtti Alútvonal.<br/>– Ha előtagot használ, a partíció gyökerének elérési útja az utolsó "/" előtti Alútvonal. <br/><br/>Tegyük fel például, hogy az adatkészletben az elérési utat "root/Folder/Year = 2020/hónap = 08/Day = 27" értékre konfigurálja:<br/>– Ha a partíció gyökerének elérési útját "gyökér/mappa/év = 2020" értékre állítja, a másolási tevékenység két további oszlopot fog előállítani, `month` és a `day` "08" és "27" értéket is kijelöli a fájlokban lévő oszlopokon kívül.<br/>– Ha nincs megadva a partíció gyökerének elérési útja, nem jön létre további oszlop. | No                                            |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No                                            |

> [!NOTE]
> A Parquet/tagolt szöveges formátum esetében a következő szakaszban említett másolási tevékenység forrásának **BlobSource** -típusa továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy az új modellt addig használja, amíg a Data Factory authoring felhasználói felülete be nem vált az új típusok létrehozásához.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

> [!NOTE]
> A tároló `$logs` , amely automatikusan létrejön, amikor a Storage Analytics engedélyezve van egy Storage-fiókhoz, nem jelenik meg, ha a tárolók listázása művelet a Data Factory felhasználói felületen keresztül történik. A fájl elérési útját közvetlenül meg kell adni a Data Factory számára a tárolóból származó fájlok felhasználásához `$logs` .

### <a name="blob-storage-as-a-sink-type"></a>BLOB Storage fogadó típusként

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

Az Azure Blob Storage-ban a következő tulajdonságok támogatottak a `storeSettings` Format-alapú másolási fogadó beállításaiban:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| típus                     | A tulajdonságot a következőre kell `type` `storeSettings` beállítani: `AzureBlobStorageWriteSettings` . | Yes      |
| copyBehavior             | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl vagy a blob neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | No       |
| blockSizeInMB | Határozza meg a blokk méretét (megabájtban), amely a Blobok adatírására szolgál. További [információ a blokkos blobokról](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Az engedélyezett érték *4 MB és 100 MB között* van. <br/>Alapértelmezés szerint a Data Factory automatikusan meghatározza a blokk méretét a forrás-áruház típusa és adatai alapján. A blob Storage-ba való nem bináris másolás esetén az alapértelmezett blokkolási méret 100 MB, így a mérete (legfeljebb) 4,95 TB adat lehet. Nem lehet optimális, ha az adatai nem nagyméretűak, különösen akkor, ha a saját üzemeltetésű integrációs modult olyan gyenge hálózati kapcsolatokkal használja, amelyek működési időtúllépést vagy teljesítménnyel kapcsolatos problémákat okoznak. Explicit módon megadhatja a blokk méretét, miközben biztosítja, hogy `blockSizeInMB*50000` az adattároláshoz elég nagy legyen. Ellenkező esetben a másolási tevékenység futtatása sikertelen lesz. | No |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No       |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
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
| `container/Folder*` | (üres, alapértelmezett használata) | hamis | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (üres, alapértelmezett használata) | true | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | hamis | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Példák a fájllista

Ez a szakasz a másolási tevékenység forrásában lévő fájllista elérési útja alapján létrejövő viselkedést ismerteti.

Tegyük fel, hogy rendelkezik a következő forrás-mappa struktúrával, és félkövéren szeretné átmásolni a fájlokat:

| Példa a forrás struktúrájára                                      | Tartalom FileListToCopy.txt                             | Data Factory konfiguráció                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metaadatok<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Az adatkészletben:**<br>Tároló `container`<br>-Mappa elérési útja: `FolderA`<br><br>**A másolási tevékenység forrása:**<br>-Fájllista elérési útja: `container/Metadata/FileListToCopy.txt` <br><br>A fájllista elérési útja ugyanazon az adattárban található szövegfájlra mutat, amely tartalmazza a másolni kívánt fájlok listáját, a soronként egy fájlt, valamint az adatkészletben konfigurált útvonal relatív elérési útját. |

### <a name="some-recursive-and-copybehavior-examples"></a>Néhány rekurzív és copyBehavior példa

Ez a szakasz a **rekurzív** és **copyBehavior** értékek különböző kombinációinak másolási műveletének eredményét írja le.

| rekurzív | copyBehavior | Forrás mappa szerkezete | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A Mappa1 a forrással megegyező struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A Mappa1 nevű célmappa a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a fájl3 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File4 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File5 automatikusan generált neve |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A Mappa1 nevű célmappa a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 + fájl3 + File4 + File5 tartalom egyetlen fájlba van egyesítve, amely egy automatikusan létrehozott fájlnevet tartalmaz. |
| hamis |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A Mappa1 nevű célmappa a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A Mappa1 nevű célmappa a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A Mappa1 nevű célmappa a következő szerkezettel jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 tartalma egyetlen fájlba van egyesítve, amely egy automatikusan létrehozott fájlnevet tartalmaz. a file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |

## <a name="preserving-metadata-during-copy"></a>Metaadatok megőrzése a másolás során

Amikor fájlokat másol az Amazon S3-ból, az Azure Blob Storage-ból vagy Azure Data Lake Storage Gen2 Azure Data Lake Storage Gen2 vagy az Azure Blob Storage-ba, megtarthatja a fájl metaadatainak megőrzését az adatokkal együtt. További információ: a [metaadatok megőrzése](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatok leképezési folyamatokban való átalakításakor az alábbi formátumokban olvashat és írhat fájlokat az Azure Blob Storage-ból:

- [Avro](format-avro.md#mapping-data-flow-properties)
- [Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties)
- [Delta](format-delta.md#mapping-data-flow-properties)
- [Excel](format-excel.md#mapping-data-flow-properties)
- [JSON](format-json.md#mapping-data-flow-properties)
- [Parquet](format-parquet.md#mapping-data-flow-properties)

A megadott beállítások formázása az adott formátum dokumentációjában található. További információ: [forrás-átalakítás a](data-flow-source.md) leképezési adatfolyamban és a fogadó [átalakítás a leképezési folyamatokban](data-flow-sink.md).

### <a name="source-transformation"></a>Forrás-átalakítás

A forrás-átalakításban az Azure Blob Storage-tárolóból, mappából vagy egyedi fájlból is elolvasható. A **forrás beállításai** lapon kezelheti a fájlok olvasásának módját. 

![Forrás beállításai](media/data-flow/sourceOptions1.png "Forrás beállításai")

**Helyettesítő karakteres elérési utak:** A helyettesítő karakterek használatakor a rendszer arra utasítja Data Factory, hogy az egyes egyező mappákon és fájlokon keresztül hurkot használjon egyetlen forrás átalakításban. Ez egy hatékony módszer több fájl feldolgozására egyetlen folyamaton belül. Több helyettesítő karakterből álló mintázatot is hozzáadhat, amely akkor jelenik meg, ha a meglévő helyettesítő karakteres mintázat fölé viszi a kurzort.

A forrás tárolóban válasszon egy olyan fájlt, amely megfelel a mintának. Csak tároló adható meg az adatkészletben. A helyettesítő karakteres elérési útnak ezért a gyökérmappa elérési útját is tartalmaznia kell.

Helyettesítő karakteres példák:

- `*` A karakterek tetszőleges halmazát jelöli.
- `**` Rekurzív címtár-beágyazást jelöl.
- `?` Egy karaktert cserél le.
- `[]` A szögletes zárójelek egy vagy több karakterének felel meg.

- `/data/sales/**/*.csv` Az összes. csv fájl beolvasása a/Data/Sales. alatt
- `/data/sales/20??/**/` A 20. század összes fájljának beolvasása.
- `/data/sales/*/*/*.csv` A. csv fájlokat a/Data/Sales. alatt két szinten kéri le.
- `/data/sales/2004/*/12/[XY]1?.csv` Az összes. csv-fájl beolvasása 2004 decemberében, X vagy Y előtaggal, kétjegyű számmal.

**Partíció gyökerének elérési útja:** Ha a fájlban particionált mappák vannak `key=value` formázva (például: `year=2019` ), akkor a partíciós mappa fájának legfelső szintjét hozzárendelheti az adatfolyam adatfolyamához tartozó oszlop nevéhez.

Először állítson be egy helyettesítő karaktert, amely tartalmazza az összes olyan elérési utat, amely a particionált mappák és az olvasni kívánt levél fájlok.

![Partíciós forrásfájl beállításai](media/data-flow/partfile2.png "Partíciós fájl beállítása")

A **partíció gyökerének elérési útja** beállítással határozhatja meg, hogy a mappa szerkezete milyen felső szintű legyen. Amikor az adatelőnézet használatával tekinti meg az adatai tartalmát, látni fogja, hogy Data Factory hozzáadja az egyes mappák szintjein talált megoldott partíciókat.

![Partíció gyökerének elérési útja](media/data-flow/partfile1.png "Partíciós gyökér elérési útjának előnézete")

**Fájlok listája:** Ez egy beállított fájl. Hozzon létre egy szövegfájlt, amely tartalmazza a feldolgozandó relatív elérési út fájljainak listáját. Mutasson erre a szövegfájlra.

**A fájl nevét tároló oszlop:** Tárolja a forrásfájl nevét az adataiban található oszlopban. Itt adjon meg egy új oszlopnevet a fájlnév karakterláncának tárolásához.

**Befejezés után:** Ha az adatfolyam futtatása után semmit nem kíván végrehajtani a forrásfájlban, törölje a forrásfájlt, vagy helyezze át a forrásfájlt. Az áthelyezés elérési útjai relatívak.

Ha a forrásfájlokat másik helyre szeretné áthelyezni a feldolgozás után, először válassza a "áthelyezés" lehetőséget a fájl művelethez. Ezután állítsa be a "from" könyvtárat. Ha nem használ helyettesítő karaktereket az elérési úthoz, akkor a "from" beállítás lesz a forrás mappájával megegyező mappa.

Ha a forrás elérési útja helyettesítő karakterrel rendelkezik, a szintaxis a következőképpen fog kinézni:

`/data/sales/20??/**/*.csv`

A következőt adhatja meg:

`/data/sales`

És a következőt adhatja meg:

`/backup/priorSales`

Ebben az esetben az alatta lévő összes fájl `/data/sales` átkerül a következőre: `/backup/priorSales` .

> [!NOTE]
> A fájl műveletei csak akkor futnak, amikor egy folyamaton belül indítja el az adatfolyamatot (a folyamat hibakeresési vagy végrehajtási futtatása), amely az adatfolyamok végrehajtása tevékenységet használja egy folyamaton belül. A fájl műveletei *nem* futnak adatfolyam-hibakeresési módban.

**Szűrés legutóbb módosítva:** A feldolgozás alatt álló fájlokat szűrheti, ha az utolsó módosítás dátumát megadja.  Az összes dátum és idő UTC időzónában van megadva. 

### <a name="sink-properties"></a>Fogadó tulajdonságai

A fogadó átalakításban az Azure Blob Storage-ban egy tárolóba vagy egy mappába is írhat. A **Beállítások** lapon kezelheti a fájlok írásának módját.

![Fogadó beállításai](media/data-flow/file-sink-settings.png "fogadó beállításai")

**Törölje a mappát:** Meghatározza, hogy a célmappa törlődik-e az adatírás előtt.

**Fájlnév beállítás:** Meghatározza, hogy a célfájl hogyan legyen elnevezve a célmappában. A fájlnév beállításai a következők:
   - **Default (alapértelmezett**): a Spark használatával a fájlok a részek alapértelmezett értékei alapján megtalálhatók.
   - **Minta**: adjon meg egy mintát, amely egy partíción lévő kimeneti fájlokat sorolja fel. Például a következőt `loans[n].csv` fogja létrehozni:, `loans1.csv` `loans2.csv` és így tovább.
   - **/Partíció**: adjon meg egy fájlnevet partícióként.
   - **Oszlopbeli adatként**: állítsa a kimeneti fájlt egy oszlop értékére. Az elérési út az adatkészlet-tárolóhoz viszonyítva nem a célmappához képest. Ha a mappa elérési útja szerepel az adatkészletben, a rendszer felülbírálja.
   - **Kimenet egyetlen fájlba**: a particionált kimeneti fájlok egyetlen elnevezett fájlba kombinálhatók. Az elérési út az adatkészlet mappájához képest relatív. Vegye figyelembe, hogy az egyesítési művelet valószínűleg sikertelen lehet a csomópont méretétől függően. A nagyméretű adatkészletek esetében ez a beállítás nem ajánlott.

**Összes idézet:** Meghatározza, hogy az összes értéket idézőjelek közé kell-e foglalni.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [törlési tevékenységet](delete-activity.md).

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy a korábban említett új modellt használja. Az Data Factory szerzői felhasználói felülete átváltott az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az `type` adatkészlet tulajdonságát be kell állítani `AzureBlob` . | Yes |
| folderPath | A tároló és a mappa elérési útja a blob Storage-ban. <br/><br/>Az elérési út a tároló neve nélkül használható helyettesítő szűrőt. Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg). `^`Ha a mappa neve helyettesítő karakter vagy a escape-karakter található, akkor a (z) használatával elkerülheti a menekülési karaktert. <br/><br/>Példa: `myblobcontainer/myblobfolder/` . További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, a másolási vagy keresési tevékenység esetében nem a GetMetadata tevékenységhez |
| fileName | A Blobok neve vagy helyettesítő szűrője a megadott `folderPath` érték alatt. Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes blobra mutat. <br/><br/>A szűrő esetében az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg).<br/>– 1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>`^`Ha a fájl neve helyettesítő karakter vagy a escape-karakter található, akkor a (z) használatával elkerülheti a menekülési karaktert.<br/><br/>Ha `fileName` nincs megadva a kimeneti adatkészlethez `preserveHierarchy` , és nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a blob nevét a következő mintával: "*adat. [ tevékenység futtatási azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha konfigurálva]*". Például: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz". <br/><br/>Ha egy táblázatos forrásból másol egy lekérdezés helyett egy Táblanév használatával, a név minta `[table name].[format].[compression if configured]` . Például: "MyTable.csv". | No |
| modifiedDatetimeStart | A fájlok a következő attribútum alapján vannak szűrve: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Vegye figyelembe, hogy a beállítás engedélyezése hatással lesz az adatáthelyezés általános teljesítményére, ha nagy mennyiségű fájlt szeretne szűrni. <br/><br/> A tulajdonságok lehetnek `NULL` , ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel `modifiedDatetimeEnd` rendelkezik `NULL` , de az, hogy a legutóbb módosított attribútum nagyobb vagy egyenlő, mint a DateTime érték, akkor a rendszer kijelöli.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de az `modifiedDatetimeStart` , hogy `NULL` a legutóbb módosított attribútum kevesebb legyen, mint a DateTime érték, a rendszer kijelöli.| No |
| modifiedDatetimeEnd | A fájlok a következő attribútum alapján vannak szűrve: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Vegye figyelembe, hogy a beállítás engedélyezése hatással lesz az adatáthelyezés általános teljesítményére, ha nagy mennyiségű fájlt szeretne szűrni. <br/><br/> A tulajdonságok lehetnek `NULL` , ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel `modifiedDatetimeEnd` rendelkezik `NULL` , de az, hogy a legutóbb módosított attribútum nagyobb vagy egyenlő, mint a DateTime érték, akkor a rendszer kijelöli.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de az `modifiedDatetimeStart` , hogy `NULL` a legutóbb módosított attribútum kevesebb legyen, mint a DateTime érték, a rendszer kijelöli.| No |
| formátumban | Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat** és **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a **Format** értékre a következő értékek egyikére. További információ: [szöveg formátuma](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . | Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a **gzip**, a **deflate**, a **BZip2** és a **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak**. | No |

>[!TIP]
>A mappában található összes blob másolásához csak a **folderPath** kell megadni.<br>Ha egyetlen blobot szeretne másolni egy adott névvel, adja meg a **folderPath** és a **fájlnévhez tartozó fájlnevet** .<br>Ha egy mappában lévő Blobok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **fájlnevet** helyettesítő karakteres szűrővel kell megadnia. 

**Példa**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>A másolási tevékenységhez használt örökölt forrásoldali modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A `type` másolási tevékenység forrásának tulajdonságát a következőre kell állítani: `BlobSource` . | Yes |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a (z) értékre `recursive` van állítva `true` , a fogadó egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát.<br/>Az engedélyezett értékek: `true` (alapértelmezett) és `false` . | No |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-sink-model-for-the-copy-activity"></a>Örökölt fogadó modell a másolási tevékenységhez

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A `type` másolási tevékenység fogadójának tulajdonságát be kell állítani `BlobSink` . | Yes |
| copyBehavior | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl vagy a blob neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | No |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

**Példa**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések

A Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
