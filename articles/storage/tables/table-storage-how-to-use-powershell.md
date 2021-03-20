---
title: Azure Table Storage-műveletek végrehajtása a PowerShell használatával | Microsoft Docs
description: Ismerje meg, hogyan futtathat olyan gyakori feladatokat, mint például az Azure Table Storage-fiókból származó adatok létrehozása, lekérdezése és törlése a PowerShell használatával.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a7502d48095fe18b983c1971d5145f51e766c95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93306911"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Azure Table Storage-műveletek végrehajtása a Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Az Azure Table Storage egy NoSQL-adattár, amellyel nagy mennyiségű strukturált, nem összehasonlítható adat tárolható és kérdezhető le. A szolgáltatás fő összetevői a táblák, az entitások és a tulajdonságok. A tábla entitások gyűjteménye. Az entitások tulajdonságok halmazai. Minden entitás legfeljebb 252 tulajdonsággal rendelkezhet, amelyek mindegyike név-érték párokat tartalmaz. Ez a cikk azt feltételezi, hogy már ismeri az Azure Table Storage szolgáltatással kapcsolatos fogalmakat. Részletes információkért lásd: [a Table Service adatmodell megismerése](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) és az [Azure Table Storage használatának első lépései a .NET használatával](../../cosmos-db/tutorial-develop-table-dotnet.md).

Ez a cikk a közös Azure Table Storage-műveletekre vonatkozik. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Tábla lekérése
> * Tábla entitások hozzáadása
> * Tábla lekérdezése
> * Tábla entitások törlése
> * Tábla törlése

Ez a cikk bemutatja, hogyan hozhat létre új Azure Storage-fiókot egy új erőforráscsoporthoz, így egyszerűen eltávolíthatja azt, ha elkészült. Ha inkább meglévő Storage-fiókot használ, ezt megteheti.

A példákhoz a PowerShell-modulok és a szükségesek `Az.Storage (1.1.0 or greater)` `Az.Resources (1.2.0 or greater)` . Egy PowerShell-ablakban futtassa a parancsot a `Get-Module -ListAvailable Az*` verzió megkereséséhez. Ha semmi sem jelenik meg, vagy frissítenie kell, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört.

> [!IMPORTANT]
> Ha ezt az Azure-szolgáltatást a PowerShell használatával szeretné használni, `Az` telepítenie kell a modult. A jelenlegi verziója `AzTable` nem kompatibilis a régebbi AzureRM modullal.
> Szükség esetén kövesse az az [modul telepítéséhez szükséges legújabb telepítési útmutatót](/powershell/azure/install-az-ps) .

Azure PowerShell telepítését vagy frissítését követően telepítenie kell a **AzTable** modult, amely az entitások kezelésére szolgáló parancsokat tartalmaz. A modul telepítéséhez futtassa a PowerShellt rendszergazdaként, és használja az **install-Module** parancsot.

> [!IMPORTANT]
> A modul neve kompatibilitási okokból továbbra is ugyanezt a modult tesszük közzé a régi néven `AzureRmStorageTables` PowerShell-Galéria. Ez a dokumentum csak az új névre hivatkozik.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Add-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Helyszínek listájának beolvasása

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ezek a példák a **eastus**-t használják. Tárolja ezt az értéket a változó **helyén** későbbi használatra.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport nevét egy változóban tárolja későbbi használatra. Ebben a példában egy *pshtablesrg* nevű erőforráscsoportot hoznak létre a *eastus* régióban.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Hozzon létre egy szabványos általános célú Storage-fiókot a helyileg redundáns tárolással (LRS) a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)használatával. Ügyeljen arra, hogy egyedi nevet adjon a Storage-fióknak. Ezután szerezze be a Storage-fiókot jelképező környezetet. Storage-fiók esetén a hitelesítő adatok ismételt megadása helyett hivatkozhat a környezetre.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Új tábla létrehozása

Tábla létrehozásához használja a [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) parancsmagot. Ebben a példában a tábla neve `pshtesttable` .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>A Storage-fiókban található táblák listájának beolvasása

Kérje le a Storage-fiókban található táblák listáját a [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)használatával.

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Egy adott táblára mutató hivatkozás beolvasása

A táblákon végrehajtandó műveletek végrehajtásához az adott táblára kell hivatkoznia. Útmutató a [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)használatával.

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Egy adott tábla CloudTable tulajdonsága

> [!IMPORTANT]
> A CloudTable használata kötelező, ha a **AzTable** PowerShell-modullal dolgozik. Hívja meg a **Get-AzStorageTable** parancsot az objektumra mutató hivatkozás beszerzéséhez. Ez a parancs a táblát is létrehozza, ha még nem létezik.

Ha a **AzTable**-t használó táblán műveleteket szeretne végrehajtani, akkor egy adott tábla CloudTable tulajdonságára kell hivatkoznia.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Tábla törlése

Egy tábla törléséhez használja a [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Ez a parancsmag eltávolítja a táblázatot, beleértve az összes adattal.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozta az új erőforráscsoportot és a Storage-fiókot a jelen útmutató elején, eltávolíthatja az ebben a gyakorlatban létrehozott összes eszközt az erőforráscsoport eltávolításával. Ez a parancs törli a csoporton belül található összes erőforrást, valamint magát az erőforráscsoportot is.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megismertük a közös Azure Table Storage-műveleteket a PowerShell-lel, beleértve a következőket: 

> [!div class="checklist"]
> * Tábla létrehozása
> * Tábla lekérése
> * Tábla entitások hozzáadása
> * Tábla lekérdezése
> * Tábla entitások törlése
> * Tábla törlése

További információt a következő cikkekben talál.

* [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage#storage)

* [Azure-táblák használata a PowerShell-AzureRmStorageTable/AzTable PS modul 2.0-s verziójában](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.