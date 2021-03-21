---
title: Adatok másolása Azure Data Lake Storage Gen1
description: Megtudhatja, hogyan másolhat adatok Data Lake Storeba és onnan a Azure Data Factory használatával
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 8bdfceff562ae2501a9d95774f7134669e929b7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379403"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Adatok másolása Data Lake Storage Gen1ba és onnan a Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-datalake-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Azure Data Lake Storage Gen1 Connector v2-ben](../connector-azure-data-lake-store.md)című témakört.

Ez a cikk azt ismerteti, hogyan használhatók a másolási tevékenységek a Azure Data Factoryban az adatok Azure Data Lake Storage Gen1ba és onnan való áthelyezésére (korábbi nevén: Azure Data Lake Store). Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben a másolási tevékenységgel végzett adatáthelyezés áttekintése szerepel.

## <a name="supported-scenarios"></a>Támogatott esetek
**Azure Data Lake Store** adatait a következő adattárakba másolhatja:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Az adatok a következő adattárakból másolhatók **Azure Data Lake Storeba**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Hozzon létre egy Data Lake Store fiókot a másolási tevékenységgel rendelkező folyamat létrehozása előtt. További információ: [Bevezetés a Azure Data Lake Store használatába](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Támogatott hitelesítési típusok
Az Data Lake Store-összekötő a következő hitelesítési típusokat támogatja:
* Egyszerű szolgáltatásnév hitelesítése
* Felhasználói hitelesítő adatok (OAuth) hitelesítése

Javasoljuk, hogy az egyszerű szolgáltatás hitelesítését használja, különösen az ütemezett adatmásoláshoz. A jogkivonat lejárati viselkedése a felhasználói hitelesítő adatok hitelesítésével történhet. A konfiguráció részleteiért tekintse meg a [társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.

## <a name="get-started"></a>Bevezetés
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely a különböző eszközök/API-k segítségével áthelyezi az adatokra egy Azure Data Lake Store.

Az adatmásolási folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. A folyamatnak a másolás varázslóval történő létrehozásával kapcsolatos oktatóanyagért lásd [: oktatóanyag: folyamat létrehozása a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Hozzon létre egy **adatelőállítót**. Egy adatelőállító egy vagy több folyamatot is tartalmazhat.
2. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához. Ha például egy Azure Blob Storage-ból másol egy Azure Data Lake Store, két társított szolgáltatást hoz létre, amely összekapcsolja az Azure Storage-fiókját, és Azure Data Lake tárolót az adatok előállítójának. A Azure Data Lake Storera jellemző társított szolgáltatás tulajdonságairól a [társított szolgáltatás tulajdonságai](#linked-service-properties) című részben olvashat.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. Az utolsó lépésben említett példában létrehoz egy adatkészletet, amely megadja a bemeneti adatokat tartalmazó BLOB-tárolót és mappát. Emellett létrehoz egy másik adatkészletet is, amely megadja a mappa és a fájl elérési útját az Data Lake tárolóban, amely a blob-tárolóból másolt adatokat tárolja. A Azure Data Lake Storera jellemző adatkészlet-tulajdonságokért lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. A korábban említett példában a BlobSource forrásként és AzureDataLakeStoreSinkként használja a másolási tevékenységhez. Hasonlóképpen, ha Azure Data Lake Storeról az Azure Blob Storagera másol, a másolási tevékenységben a AzureDataLakeStoreSource és a BlobSink is használja. A Azure Data Lake Storera jellemző másolási tevékenység tulajdonságairól a [másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban olvashat. Az adattár forrásként vagy fogadóként való használatával kapcsolatos részletekért kattintson az adattár előző szakaszában található hivatkozásra.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. Az adatok egy Azure Data Lake Storeba való másolásához használt Data Factory JSON-definíciókkal rendelkező minták esetében tekintse meg a jelen cikk [JSON-példák](#json-examples-for-copying-data-to-and-from-data-lake-store) című szakaszát.

A következő szakaszokban részletesen ismertetjük azokat a JSON-tulajdonságokat, amelyek a Data Lake Store specifikus entitások definiálásához használhatók Data Factory.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A társított szolgáltatás egy adattárhoz csatol egy adattárolót egy adatgyárhoz. Hozzon létre egy **AzureDataLakeStore** típusú társított szolgáltatást, amely összekapcsolja a Data Lake Store adatait az adatelőállítóval. A következő táblázat a Data Lake Store társított szolgáltatásokra vonatkozó JSON-elemeket ismerteti. Az egyszerű szolgáltatásnév és a felhasználói hitelesítő adatok hitelesítése lehetőség közül választhat.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **típusa** | A Type tulajdonságot **AzureDataLakeStore** értékre kell beállítani. | Yes |
| **dataLakeStoreUri** | Információ a Azure Data Lake Store fiókról. Ez az információ a következő formátumok egyikét veszi figyelembe: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/` . | Yes |
| **subscriptionId** | Az Azure-előfizetés azonosítója, amelyhez a Data Lake Store fiók tartozik. | A fogadóhoz szükséges |
| **resourceGroupName** | Az Azure-erőforráscsoport neve, amelyhez a Data Lake Store fiók tartozik. | A fogadóhoz szükséges |

### <a name="service-principal-authentication-recommended"></a>Egyszerű szolgáltatásnév hitelesítése (ajánlott)
Az egyszerű szolgáltatás hitelesítésének használatához regisztráljon egy alkalmazás-entitást Azure Active Directory (Azure AD), és adja meg a Data Lake Store elérését. A részletes lépésekért lásd: [szolgáltatások közötti hitelesítés](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:
* Alkalmazásazonosító
* Alkalmazás kulcsa
* Bérlőazonosító

> [!IMPORTANT]
> Győződjön meg arról, hogy az egyszerű szolgáltatásnév megfelelő engedélyt ad a Azure Data Lake Store:
>- **Ha forrásként szeretné használni a Data Lake Store**, adjon meg legalább **olvasási és végrehajtási** adathozzáférési engedélyt a mappa tartalmának listázásához és másolásához, vagy **olvassa el** az engedélyt egyetlen fájl másolásához. Nincs követelmény a fiók szintű hozzáférés-vezérléshez.
>- Ha a Data Lake Store a fogadóként **szeretné használni**, adjon meg legalább **írási és végrehajtási** adathozzáférési engedélyt a gyermek elemek létrehozásához a mappában. Ha pedig Azure IRt használ a másolás engedélyezésére (a forrás és a fogadó is a felhőben van), hogy Data Factory érzékelje Data Lake Store régióját, adjon meg legalább **olvasó** szerepkört a fiók hozzáférés-vezérlésében (iam). Ha el szeretné kerülni ezt a IAM-szerepkört, [adja meg a executionlocation használata](data-factory-data-movement-activities.md#global) helyét a másolási tevékenységben lévő Data Lake Store helyével.
>- Ha **a másolás varázslót használja a folyamatok létrehozásához**, adjon meg legalább **olvasó** szerepkört a fiók hozzáférés-vezérlésében (iam). Továbbá adjon meg legalább **olvasási és végrehajtási** engedélyt a Data Lake Store root ("/") és annak gyermekei számára. Ellenkező esetben előfordulhat, hogy a "megadott hitelesítő adatok érvénytelenek" üzenet jelenik meg.

Az egyszerű szolgáltatás hitelesítését a következő tulajdonságok megadásával használhatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **servicePrincipalId** | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Yes |
| **servicePrincipalKey** | Az alkalmazás kulcsának meghatározása. | Yes |
| **bérlő** | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Yes |

**Példa: egyszerű szolgáltatásnév hitelesítése**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Felhasználói hitelesítő adatok hitelesítése
Azt is megteheti, hogy a következő tulajdonságok megadásával felhasználói hitelesítő adatokat használ a vagy a rendszerből való másoláshoz Data Lake Store:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **engedély** | Kattintson az **Engedélyezés** gombra a Data Factory szerkesztőben, és adja meg a hitelesítő adatait, amely hozzárendeli az automatikusan létrehozott engedélyezési URL-címet ehhez a tulajdonsághoz. | Yes |
| **sessionId** | OAuth munkamenet-azonosító a OAuth-engedélyezési munkamenetből. Az egyes munkamenet-AZONOSÍTÓk egyediek, és csak egyszer használhatók fel. Ez a beállítás automatikusan létrejön a Data Factory-szerkesztő használatakor. | Yes |

> [!IMPORTANT]
> Győződjön meg arról, hogy a felhasználó megfelelő jogosultságot ad a Azure Data Lake Storeban:
>- **Ha forrásként szeretné használni a Data Lake Store**, adjon meg legalább **olvasási és végrehajtási** adathozzáférési engedélyt a mappa tartalmának listázásához és másolásához, vagy **olvassa el** az engedélyt egyetlen fájl másolásához. Nincs követelmény a fiók szintű hozzáférés-vezérléshez.
>- Ha a Data Lake Store a fogadóként **szeretné használni**, adjon meg legalább **írási és végrehajtási** adathozzáférési engedélyt a gyermek elemek létrehozásához a mappában. Ha pedig Azure IRt használ a másolás engedélyezésére (a forrás és a fogadó is a felhőben van), hogy Data Factory érzékelje Data Lake Store régióját, adjon meg legalább **olvasó** szerepkört a fiók hozzáférés-vezérlésében (iam). Ha el szeretné kerülni ezt a IAM-szerepkört, [adja meg a executionlocation használata](data-factory-data-movement-activities.md#global) helyét a másolási tevékenységben lévő Data Lake Store helyével.
>- Ha **a másolás varázslót használja a folyamatok létrehozásához**, adjon meg legalább **olvasó** szerepkört a fiók hozzáférés-vezérlésében (iam). Továbbá adjon meg legalább **olvasási és végrehajtási** engedélyt a Data Lake Store root ("/") és annak gyermekei számára. Ellenkező esetben előfordulhat, hogy a "megadott hitelesítő adatok érvénytelenek" üzenet jelenik meg.

**Példa: felhasználói hitelesítő adatok hitelesítése**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Jogkivonat lejárata
Az **Engedélyezés** gomb használatával létrehozott engedélyezési kód meghatározott idő után lejár. A következő üzenet azt jelenti, hogy a hitelesítési jogkivonat lejárt:

Hitelesítő adatok műveleti hibája: invalid_grant-AADSTS70002: hiba történt a hitelesítő adatok érvényesítése során. AADSTS70008: a megadott hozzáférési engedély lejárt vagy visszavont. Nyomkövetési azonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 korrelációs azonosítója: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 timestamp: 2015-12-15 21-09-31Z.

A következő táblázat a különböző típusú felhasználói fiókok lejárati idejét mutatja be:

| Felhasználó típusa | Lejárat után lejár |
|:--- |:--- |
| A Azure Active Directory által *nem* felügyelt felhasználói fiókok (például @hotmail.com vagy @live.com ) |12 óra |
| A Azure Active Directory által kezelt felhasználói fiókok |14 nappal az utolsó szelet futtatása után <br/><br/>90 nap, ha egy OAuth-alapú társított szolgáltatáson alapuló szelet 14 naponta legalább egyszer fut |

Ha a jogkivonat lejárati ideje előtt módosítja a jelszavát, a jogkivonat azonnal lejár. Ekkor megjelenik a szakasz korábbi részében említett üzenet.

A fiók újbóli engedélyezéséhez használja az **Engedélyezés** gombot, ha a jogkivonat lejár a társított szolgáltatás újbóli üzembe helyezéséhez. A **munkamenet** -azonosító és az **engedélyezési** tulajdonságok értékeit programozott módon is létrehozhatja a következő kód használatával:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
A kódban használt Data Factory osztályok részleteiért tekintse meg a [AzureDataLakeStoreLinkedService osztály](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), a [AzureDataLakeAnalyticsLinkedService osztály](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)és a [AuthorizationSessionGetResponse osztály](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) témakört. Adja hozzá a-verzióra mutató hivatkozást a `2.9.10826.1824` `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` `WindowsFormsWebAuthenticationDialog` kódban használt osztályhoz.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

**Tünet:** Ha a másolási tevékenység **a Azure Data Lake Storeba** másol adatokba, ha a másolási művelet a következő hibával meghiúsul:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Alapvető ok:** 2 lehetséges ok:

1. A `resourceGroupName` `subscriptionId` Azure Data Lake Store társított szolgáltatásban szereplő és/vagy megadott érték helytelen;
2. A felhasználó vagy az egyszerű szolgáltatásnév nem rendelkezik a szükséges engedélyekkel.

**Megoldás:**

1. Győződjön meg arról, hogy a `subscriptionId` és a `resourceGroupName` társított szolgáltatásban megadott érték `typeProperties` valóban az, hogy a saját adatközpont-fiókja tartozik hozzá.

2. Ügyeljen arra, hogy legalább **olvasói** szerepkört adjon a felhasználónak vagy az egyszerű szolgáltatásnak a (z) adat-Lake-fiókban. A következőképpen teheti meg:

    1. Nyissa meg a Data Lake Store-fiókját a Azure Portal >
    2. A Data Lake Store paneljén kattintson a **hozzáférés-vezérlés (iam)** elemre.
    3. Kattintson a **szerepkör-hozzárendelés hozzáadása** elemre.
    4. Adja meg a **szerepkört** **olvasóként**, és válassza ki azt a felhasználót vagy szolgáltatásnevet, amelyet a másoláshoz használ a hozzáférés biztosításához.

3. Ha nem szeretne **olvasói** szerepkört adni a felhasználónak vagy az egyszerű szolgáltatásnak, Alternatív megoldásként [explicit módon meg kell adnia egy végrehajtási helyet](data-factory-data-movement-activities.md#global) a másolási tevékenységben a Data Lake Store helyével. Példa:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Ha meg szeretne adni egy adatkészletet, amely a Data Lake Store bemeneti adatokat jelöli, akkor az adatkészlet **Type (típus** ) tulajdonságát **AzureDataLakeStore** értékre kell állítani. Az adatkészlet **linkedServiceName** tulajdonságát állítsa a Data Lake Store társított szolgáltatás nevére. Az adatkészletek definiálásához rendelkezésre álló JSON-csoportok és-tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. A JSON-adatkészletek (például a **struktúra**, a **rendelkezésre állás** és a **szabályzat**) részei hasonlóak az összes ADATKÉSZLET típusához (például Azure SQL Database, Azure Blob és Azure Table). A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és olyan információkat biztosít, mint például az adattárban található adatok helye és formátuma.

A **AzureDataLakeStore** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **folderPath** |Data Lake Store tárolójának és mappájának elérési útja. |Yes |
| **fileName** |A fájl neve Azure Data Lake Storeban. A **filename** tulajdonság nem kötelező, és megkülönbözteti a kis-és nagybetűket. <br/><br/>Ha megadja a **fájlnevet**, a tevékenység (beleértve a másolást is) az adott fájlon működik.<br/><br/>Ha nincs megadva a **fájlnév** , a másolás a bemeneti adatkészletben a **folderPath** összes fájlját tartalmazza.<br/><br/>Ha a **fájlnév** nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység-fogadóban, a generált fájl neve a következő formátumban jelenik meg: `Data._Guid_.txt` . Például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |No |
| **partitionedBy** |A **partitionedBy** tulajdonság nem kötelező. Ezzel a beállítással megadhatja a dinamikus elérési utat és a fájlnevet az idősorozat-értékekhez. A **folderPath** például minden egyes órányi adatértékhez paraméterként lehet megadni. További részletekért és példákért tekintse meg a partitionedBy tulajdonságot. |No |
| **formátumban** | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat** és **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a **Format** értékre a következő értékek egyikére. További információ: a [szöveg formátuma](data-factory-supported-file-and-compression-formats.md#text-format), a [JSON formátuma](data-factory-supported-file-and-compression-formats.md#json-format), a [Avro formátuma](data-factory-supported-file-and-compression-formats.md#avro-format), az [ork formátuma](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta](data-factory-supported-file-and-compression-formats.md#parquet-format) formátuma [Azure Data Factory cikk által támogatott fájl-és tömörítési](data-factory-supported-file-and-compression-formats.md) formátumokban. <br><br> Ha a fájlokat a fájl alapú tárolók között (bináris másolás) szeretné másolni, ugorja át a `format` bemeneti és a kimeneti adatkészlet-definíciók szakaszát. |No |
| **tömörítés** | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a **gzip**, a **deflate**, a **BZip2** és a **ZipDeflate**. A támogatott szintek **optimálisak** és **leggyorsabbak**. További információ: [Azure Data Factory által támogatott fájl-és Tömörítési formátumok](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

### <a name="the-partitionedby-property"></a>A partitionedBy tulajdonság
Megadhatja az idősoros adatok dinamikus **folderPath** és **fájlnevének** tulajdonságait a **partitionedBy** tulajdonsággal, Data Factory függvényekkel és a rendszerváltozókkal. További részletekért tekintse meg a [Azure Data Factory-functions és a rendszerváltozók](data-factory-functions-variables.md) című cikket.


A következő példában a `{Slice}` rendszer a Data Factory rendszerváltozó értékével helyettesíti a `SliceStart` megadott formátumban ( `yyyyMMddHH` ). A név a `SliceStart` szelet kezdő időpontjára hivatkozik. A `folderPath` tulajdonság az egyes szeletekhez hasonlóan eltérő, mint a vagy a esetében `wikidatagateway/wikisampledataout/2014100103` `wikidatagateway/wikisampledataout/2014100104` .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

A következő példában a (z) és a (z) és a (z) és a (z) tulajdonságban a (z) és a (z) és a (z) `SliceStart` Tulajdonságok által használt `folderPath` `fileName`
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Az idősorozat-adatkészletek, az ütemezés és a szeletek további részleteiért tekintse meg az [Adatkészleteket Azure Data Factory](data-factory-create-datasets.md) és [Data Factory az ütemezési és végrehajtási](data-factory-scheduling-and-execution.md) cikkeket.


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

A tevékenységek **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. A másolási tevékenységek esetében a források és a nyelők típusaitól függően változnak.

A **AzureDataLakeStoreSource** a következő tulajdonságot támogatja a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| **rekurzív** |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. |True (alapértelmezett érték), hamis |No |

A **AzureDataLakeStoreSink** a következő tulajdonságokat támogatja a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| **copyBehavior** |Megadja a másolási viselkedést. |<b>PreserveHierarchy</b>: megőrzi a fájl hierarchiáját a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><br/><b>FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén jön létre. A célfájl automatikusan létrehozott névvel lett létrehozva.<br/><br/><b>MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl vagy a blob neve, az egyesített fájl neve a megadott név. Ellenkező esetben a fájl neve automatikusan létrejön. |No |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák
Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredményét írja le.

| rekurzív | copyBehavior | Eredményül kapott viselkedés |
| --- | --- | --- |
| true |preserveHierarchy |A Mappa1 a következő szerkezettel rendelkezik: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |A Mappa1 a következő szerkezettel rendelkezik: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a fájl3 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File4 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File5 automatikusan generált neve |
| true |mergeFiles |A Mappa1 a következő szerkezettel rendelkezik: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 + fájl3 + File4 + file 5 tartalom egyetlen fájlba van egyesítve automatikusan generált fájlnévvel |
| hamis |preserveHierarchy |A Mappa1 a következő szerkezettel rendelkezik: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |flattenHierarchy |A Mappa1 a következő szerkezettel rendelkezik:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/><br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |mergeFiles |A Mappa1 a következő szerkezettel rendelkezik:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 tartalma egyetlen fájlba van egyesítve, amely automatikusan létrehozott fájlnevet tartalmaz. a file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
Részletekért tekintse [meg a fájl-és tömörítési formátumokat Azure Data Factory](data-factory-supported-file-and-compression-formats.md) cikkben.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>JSON-példák az adatok Data Lake Storeba való másolásához
Az alábbi példák a JSON-definíciókat tartalmazzák. Ezekkel a definíciókkal létrehozhat egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. A példák azt mutatják be, hogyan másolhatók az adatok Data Lake Store és az Azure Blob Storage szolgáltatásba. Az adatok azonban _közvetlenül_ a forrásokból is átmásolhatók bármelyik támogatott mosogatóba. További információ: "támogatott adattárak és-formátumok" című rész, az [adatok áthelyezése a másolási tevékenység használatával](data-factory-data-movement-activities.md) című cikkből.

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Példa: adatok másolása az Azure Blob Storageból a Azure Data Lake Storeba
Az ebben a szakaszban szereplő mintakód a következőket mutatja be:

* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [AzureDataLakeStore](#linked-service-properties)típusú társított szolgáltatás.
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureDataLakeStore](#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
* [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [AzureDataLakeStoreSink](#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A példák azt mutatják be, hogy az Azure Blob Storage idősoros adatai hogyan legyenek átmásolva Data Lake Store óránként.

**Azure Storage társított szolgáltatás**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Társított szolgáltatás Azure Data Lake Store**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> A konfiguráció részleteiért tekintse meg a [társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.
>

**Azure blobbemeneti adatkészlet**

A következő példában az adatok minden órában () egy új blobból kerülnek felvételre `"frequency": "Hour", "interval": 1` . A blob mappa elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja a kezdési időpont év, hónap és nap részét használja. A fájlnév a kezdési idő óra részét használja. A `"external": true` beállítás arról tájékoztatja a Data Factory szolgáltatást, hogy a tábla kívül esik az adatgyárban, és nem az adatelőállító tevékenysége.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Kimeneti adatkészlet Azure Data Lake Store**

Az alábbi példa a Data Lake Storeba másolja az adatfájlokat. Az új Adatmásolás minden órában Data Lake Store.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenység egy blob-forrást és egy Data Lake Store fogadót tartalmazó folyamatban**

A következő példában a folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva. A másolási tevékenység óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a `source` típus értékre van állítva, `BlobSource` és a `sink` típus értéke: `AzureDataLakeStoreSink` .

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Példa: adatok másolása Azure Data Lake Storeból egy Azure-blobba
Az ebben a szakaszban szereplő mintakód a következőket mutatja be:

* [AzureDataLakeStore](#linked-service-properties)típusú társított szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [AzureDataLakeStore](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureDataLakeStoreSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A kód óránként másolja az idősoros adatok Data Lake Storeről egy Azure-blobba.

**Társított szolgáltatás Azure Data Lake Store**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> A konfiguráció részleteiért tekintse meg a [társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.
>

**Azure Storage társított szolgáltatás**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Bemeneti adatkészlet Azure Data Lake**

Ebben a példában `"external"` úgy kell beállítani `true` a Data Factory szolgáltatást, hogy a tábla az adatelőállítón kívül esik, és nem az adatelőállító tevékenysége.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
**Azure-Blob kimeneti adatkészlete**

A következő példában a rendszer óránként egy új blobba írja az adatbevitelt `"frequency": "Hour", "interval": 1` . A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap, nap és óra részét használja.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Egy Azure Data Lake Store forrással és egy blob-fogadóval rendelkező folyamat másolási tevékenysége**

A következő példában a folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva. A másolási tevékenység óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a `source` típus értékre van állítva, `AzureDataLakeStoreSource` és a `sink` típus értéke: `BlobSink` .

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

A másolási tevékenység definíciójában a forrás adatkészletből származó oszlopokat is leképezheti a fogadó adatkészlet oszlopaira. Részletekért lásd: [adatkészlet-oszlopok leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
A másolási tevékenység teljesítményét befolyásoló tényezőkről és annak optimalizálásáról a [másolási tevékenység teljesítményének és finomhangolásának útmutatója](data-factory-copy-activity-performance.md) című cikkben olvashat bővebben.