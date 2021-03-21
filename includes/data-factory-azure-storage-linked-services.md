---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001732"
---
### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Az **Azure Storage társított szolgáltatása** lehetővé teszi, hogy egy Azure Storage-fiókot egy Azure-beli adatgyárhoz társítson a **fiók kulcsa** segítségével, amely az Azure Storage-hoz való globális hozzáféréssel rendelkező adatelőállítót biztosít. Az alábbi táblázat az Azure Storage társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |A Type tulajdonságot a következőre kell beállítani: **AzureStorage** |Yes |
| connectionString |Itt adhatja meg az Azure Storage-hoz a connectionString tulajdonsághoz való kapcsolódáshoz szükséges adatokat. |Yes |

További információ a Storage-fiók hozzáférési kulcsainak beolvasásáról: a [Storage-fiók hozzáférési kulcsainak kezelése](../articles/storage/common/storage-account-keys-manage.md).

**Példa**  

```json
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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage sas társított szolgáltatás
A közös hozzáférésű jogosultságkód (SAS) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz. Lehetővé teszi, hogy az ügyfél korlátozott engedélyeket biztosítson a Storage-fiókban lévő objektumoknak egy adott időtartamra és egy meghatározott engedélyekkel, anélkül, hogy meg kellene osztania a fiók hozzáférési kulcsait. Az SAS egy URI, amely magában foglalja a lekérdezési paramétereit, a tárolási erőforrásokhoz való hitelesített hozzáféréshez szükséges összes információt. A tároló erőforrásainak SAS-vel való eléréséhez az ügyfélnek csak az SAS-t kell átadnia a megfelelő konstruktorba vagy metódusba. Az SAS-vel kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférési aláírások (SAS) használatával](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> A Azure Data Factory mostantól csak a **Service sas** -t támogatja, de nem a fiók sas-t. Vegye figyelembe, hogy a SAS URL-generable Azure Portal vagy Storage Explorer egy fiók SAS, amely nem támogatott.

> [!TIP]
> Az alábbi PowerShell-parancsok futtatásával létrehozhat egy Service SAS-t a Storage-fiókjához (cserélje le a tulajdonosokat, és adja meg a szükséges engedélyt): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Az Azure Storage SAS társított szolgáltatása lehetővé teszi egy Azure Storage-fiók összekapcsolását egy Azure-beli adatgyárhoz egy közös hozzáférési aláírás (SAS) használatával. Az adat-előállítót a tárolóban lévő összes/meghatározott erőforrás (blob/tároló) számára korlátozott/időhöz kötött hozzáféréssel biztosítja. Az alábbi táblázat az Azure Storage SAS társított szolgáltatásához tartozó JSON-elemek leírását tartalmazza. 

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |A Type tulajdonságot a következőre kell beállítani: **AzureStorageSas** |Yes |
| sasUri |Az Azure Storage-erőforrásokhoz, például a blobhoz, a tárolóhoz vagy a táblához válassza a közös hozzáférési aláírás URI-JÁT.  |Yes |

**Példa**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

**Sas URI** létrehozásakor a következőket kell figyelembe venni:  

* Állítsa be a megfelelő olvasási/írási **engedélyeket** az objektumokon attól függően, hogy a társított szolgáltatás (olvasás, írás, olvasás/írás) hogyan legyen használatban az adatelőállítóban.
* Megfelelő **lejárati idő** beállítása. Győződjön meg arról, hogy az Azure Storage-objektumokhoz való hozzáférés nem jár le a folyamat aktív időszakán belül.
* Az URI-t a megfelelő tárolóban, blobban vagy táblázatban kell létrehozni a szükséges érték alapján. Az Azure-Blobok SAS URI-ja lehetővé teszi, hogy az Data Factory szolgáltatás hozzáférjen az adott blobhoz. Az Azure Blob-tároló SAS URI-ja lehetővé teszi, hogy az Data Factory szolgáltatás megismételje az adott tárolóban lévő blobokat. Ha később több vagy kevesebb objektumot kell megadnia, vagy frissítenie kell az SAS URI-t, ne felejtse el frissíteni a társított szolgáltatást az új URI-val.   

