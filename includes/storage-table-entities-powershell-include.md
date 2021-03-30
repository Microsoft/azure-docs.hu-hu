---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67178794"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Tábla entitások kezelése

Most, hogy már rendelkezik egy táblázattal, nézzük meg, hogyan kezelheti az entitásokat vagy sorokat a táblában. 

Az entitások legfeljebb 255 tulajdonsággal rendelkezhetnek, beleértve a következő három rendszertulajdonságot: **PartitionKey**, **RowKey** és **timestamp**. A **PartitionKey** és a **RowKey** értékeinek behelyezéséhez és frissítéséhez felelős. A kiszolgáló kezeli az **időbélyeg** értékét, amely nem módosítható. A **PartitionKey** és a **RowKey** együtt egyedileg azonosítják a táblákon belüli összes entitást.

* **PartitionKey**: meghatározza azt a partíciót, amelyet az entitás tárol.
* **RowKey**: egyedileg azonosítja az entitást a partíción belül.

Egy entitáshoz legfeljebb 252 egyéni tulajdonságot adhat meg. 

### <a name="add-table-entities"></a>Tábla entitások hozzáadása

Entitások hozzáadása egy táblához a **Add-AzTableRow** használatával. Ezek a példák az értékekkel rendelkező partíciós kulcsokat használják `partition1` `partition2` , és a sorok kulcsai megegyeznek az állapot rövidítésével. Az egyes entitások tulajdonságai a következők: `username` és `userid` . 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Tábla entitások lekérdezése

Egy táblában lévő entitásokat a **Get-AzTableRow** parancs használatával kérdezheti le.

> [!NOTE]
> A Get- **AzureStorageTableRowAll**, a **Get-AzureStorageTableRowByPartitionKey**, a **Get-AzureStorageTableRowByColumnName** és a **Get-AzureStorageTableRowByCustomFilter** parancsmag elavult, és a rendszer eltávolítja a későbbi verziókban.

#### <a name="retrieve-all-entities"></a>Az összes entitás beolvasása

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Ez a parancs az alábbi táblázathoz hasonló eredményeket eredményez:

| userid | username | partíció | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Adott partíció entitásának beolvasása

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Az eredmények az alábbi táblázathoz hasonlóan jelennek meg:

| userid | username | partíció | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Adott értékhez tartozó entitások beolvasása egy adott oszlopban

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Ez a lekérdezés egy rekordot kérdez le.

|mező|érték|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Entitások beolvasása egyéni szűrő használatával 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Ez a lekérdezés egy rekordot kérdez le.

|mező|érték|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Entitások frissítése 

Az entitások frissítésének három lépése van. Először kérje le az entitást a módosításhoz. Másodszor, végezze el a módosítást. Harmadszor, véglegesítse a módosítást az **Update-AzTableRow** használatával.

Frissítse a username = ' Jessie ' nevű entitást a username = ' Jessie2 ' felhasználónévvel. Ez a példa azt is bemutatja, hogyan hozhatók létre egyéni szűrők .NET-típusok használatával.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

Az eredmények a Jessie2 rekordot mutatják.

|mező|érték|
|----|----|
| userid | 2 |
| username | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Tábla entitások törlése

Törölhet egy vagy több entitást is a táblában.

#### <a name="deleting-one-entity"></a>Egy entitás törlése

Egyetlen entitás törléséhez szerezzen be egy hivatkozást az entitásra, és húzza azt a **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>A tábla összes entitásának törlése

A táblában lévő összes entitás törléséhez lekéri őket, és az eredményeket a Remove parancsmagba kell beolvasni. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
