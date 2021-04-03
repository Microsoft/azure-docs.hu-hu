---
title: SQL felügyelt példány üzembe helyezése egy példány-készleten
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk az Azure SQL felügyelt példány-készletek (előzetes verzió) létrehozását és felügyeletét ismerteti.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: 732a9bab018103321a9a3a759b31495bd67a209e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793109"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Az Azure SQL felügyelt példányának üzembe helyezése egy példány-készleten
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk részletesen ismerteti, hogyan hozhat létre egy [példány-készletet](instance-pools-overview.md) , és hogyan telepítheti az Azure SQL felügyelt példányát. 

## <a name="instance-pool-operations"></a>Példányok készletének műveletei

A következő táblázat a példány-készletekhez kapcsolódó elérhető műveleteket, valamint azok rendelkezésre állását mutatja be a Azure Portal és a PowerShellben.

|Parancs|Azure Portal|PowerShell|
|:---|:---|:---|
|Példány-készlet létrehozása|Nem|Igen|
|Példány készletének frissítése (korlátozott számú tulajdonság)|Nem |Igen |
|Példány-készlet használatának és tulajdonságainak keresése|Nem|Igen |
|Példány-készlet törlése|Nem|Igen|
|Felügyelt példány létrehozása egy példány-készleten belül|Nem|Igen|
|Felügyelt példány erőforrás-használatának frissítése|Igen |Yes|
|Felügyelt példány használatának és tulajdonságainak keresése|Igen|Yes|
|Felügyelt példány törlése a készletből|Igen|Yes|
|Adatbázis létrehozása példányban a készleten belül|Igen|Yes|
|SQL felügyelt példányról származó adatbázis törlése|Igen|Yes|

Elérhető [PowerShell-parancsok](/powershell/module/az.sql/):

|Parancsmag |Leírás |
|:---|:---|
|[Új – AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Létrehoz egy SQL-alapú felügyelt példány-készletet. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Egy példány-készlet adatait adja vissza. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Egy példány tulajdonságainak beállítása az SQL felügyelt példányában. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Eltávolít egy példány-készletet a felügyelt SQL-példányban. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Az SQL felügyelt példány-készlet használatára vonatkozó adatokat adja vissza. |


A PowerShell használatához [telepítse a PowerShell Core legújabb verzióját](/powershell/scripting/install/installing-powershell#powershell), és kövesse az utasításokat az [Azure PowerShell modul telepítéséhez](/powershell/azure/install-az-ps).

A készleteken és az önálló példányokon belüli példányokkal kapcsolatos műveletekhez használja a szabványos [felügyelt példányok parancsait](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances), de a *példány neve* tulajdonságot fel kell tölteni, ha egy készlet egy példányához használja ezeket a parancsokat.

## <a name="deployment-process"></a>Üzembehelyezési folyamat

Ha felügyelt példányt szeretne üzembe helyezni egy példány-készletben, először telepítenie kell a példány-készletet, amely egy egyszeri, hosszan futó művelet, amelyben az időtartam ugyanaz, mint egy [üres alhálózaton létrehozott egyetlen példány](sql-managed-instance-paas-overview.md#management-operations)üzembe helyezése. Ezután üzembe helyezhet egy felügyelt példányt a készletben, amely egy viszonylag gyors művelet, amely általában akár öt percet is igénybe vehet. A példány-készlet paraméterét explicit módon meg kell adni a művelet részeként.

Nyilvános előzetes verzióban mindkét művelet csak a PowerShell és a Azure Resource Manager sablonok használatával támogatott. A Azure Portal-élmény jelenleg nem érhető el.

A felügyelt példányok készletre való telepítése után a Azure Portal *segítségével módosíthatja* a tulajdonságait a díjszabási csomag lapon.

## <a name="create-a-virtual-network-with-a-subnet"></a>Virtuális hálózat létrehozása alhálózattal 

Ha több példányra vonatkozó készletet szeretne elhelyezni ugyanazon a virtuális hálózaton belül, tekintse meg a következő cikkeket:

- [Az Azure SQL felügyelt példányának VNet-alhálózati méretének meghatározása](vnet-subnet-determine-size.md).
- Hozzon létre új virtuális hálózatot és alhálózatot a [Azure Portal sablonnal](virtual-network-subnet-create-arm-template.md) , vagy kövesse a [meglévő virtuális hálózat előkészítésének](vnet-existing-add-subnet.md)utasításait.
 

## <a name="create-an-instance-pool"></a>Példány-készlet létrehozása 

Az előző lépések elvégzése után készen áll egy példány-készlet létrehozására.

A következő korlátozások vonatkoznak a példány-készletekre:

- Nyilvános előzetes verzióban csak általános célú és Gen5 érhető el.
- A készlet neve csak kisbetűket, számokat és kötőjeleket tartalmazhat, és nem kezdődhet kötőjellel.
- Ha Azure Hybrid Benefitt szeretne használni, a rendszer a példány-készlet szintjén alkalmazza. A licenc típusát a készlet létrehozása során állíthatja be, vagy a létrehozás után bármikor frissítheti.

> [!IMPORTANT]
> A példányok üzembe helyezése hosszú ideig futó művelet, amely körülbelül 4,5 órát vesz igénybe.

Hálózati paraméterek beolvasása:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Példány készletének létrehozása:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Mivel egy példány-készlet üzembe helyezése hosszú ideig futó művelet, meg kell várnia, amíg be nem fejeződik a jelen cikkben ismertetett lépések bármelyikének futtatása.

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A példány-készlet sikeres üzembe helyezése után itt az ideje, hogy felügyelt példányt hozzon létre benne.

Felügyelt példány létrehozásához hajtsa végre a következő parancsot:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

Egy példány egy készleten belüli üzembe helyezése néhány percet vesz igénybe. Az első példány létrehozása után további példányok is létrehozhatók:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Adatbázis létrehozása 

Egy készleten belüli felügyelt példányban lévő adatbázisok létrehozásához és kezeléséhez használja az Egypéldányos parancsokat.

Adatbázis létrehozása felügyelt példányon belül:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Készlet használatának beolvasása 
 
A készleten belüli példányok listájának lekérése:

```powershell
$instancePool | Get-AzSqlInstance
```


A készlet erőforrásai használatának beolvasása:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


A készlet és a benne található példányok részletes használatának áttekintése:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Példányban lévő adatbázisok listázása:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> A készletben legfeljebb 100 adatbázis lehet (nem példány).


## <a name="scale"></a>Méretezés 


A felügyelt példány adatbázisokkal való feltöltése után a tárolóra vagy a teljesítményre vonatkozó korlátozásokat is elérheti. Ebben az esetben, ha nem lépték túl a készlet használatát, méretezheti a példányt.
A felügyelt példányok készleten belüli skálázása egy művelet, amely néhány percet vesz igénybe. A skálázás előfeltétele, hogy a virtuális mag és a tárterület elérhető legyen a példányok készletének szintjén.

A virtuális mag és a tárterület méretének frissítése:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Csak a tárolási méret frissítése:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Csatlakozás 

A készletben lévő felügyelt példányokhoz való kapcsolódáshoz a következő két lépés szükséges:

1. [Engedélyezze a példány nyilvános végpontját](#enable-the-public-endpoint).
2. [Adjon hozzá egy bejövő szabályt a hálózati biztonsági csoporthoz (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Mindkét lépés befejezése után csatlakozhat a példányhoz a példány létrehozásakor megadott nyilvános végponti címen, porton és hitelesítő adatokon keresztül. 

### <a name="enable-the-public-endpoint"></a>Nyilvános végpont engedélyezése

Egy példány nyilvános végpontjának engedélyezése a Azure Portalon vagy a következő PowerShell-parancs használatával végezhető el:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Ez a paraméter a példány létrehozásakor is beállítható.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Bejövő szabály hozzáadása a hálózati biztonsági csoporthoz 

Ez a lépés a Azure Portalon vagy a PowerShell-parancsok használatával végezhető el, és bármikor elvégezhető az alhálózat felügyelt példányra való előkészítése után.

Részletekért lásd: [nyilvános végponti forgalom engedélyezése a hálózati biztonsági csoportban](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-to-a-pool"></a>Meglévő önálló példány áthelyezése egy készletbe
 
A készleten belüli és kívüli példányok áthelyezése a nyilvános előzetes verzióra vonatkozó korlátozások egyike. A megkerülő megoldás a készleten kívüli példányok adatbázisainak adott időpontra történő visszaállítására támaszkodik egy már egy készletben lévő példányra. 

Mindkét példánynak ugyanabban az előfizetésben és régióban kell lennie. A régiók közötti és az előfizetések közötti visszaállítás jelenleg nem támogatott.

Ez a folyamat leállási idővel rendelkezik.

Meglévő adatbázisok áthelyezése:

1. A munkaterhelések felfüggesztése az áttelepíteni kívánt felügyelt példányon.
2. Parancsfájlok létrehozásával rendszeradatbázisokat hozhat létre, és végrehajthatja azokat a példányon belül.
3. Végezze el az egyes adatbázisok adott időponthoz való visszaállítását a készletben lévő példányok között.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Irányítsa az alkalmazást az új példányra, és folytassa a számítási feladatokat.

Ha több adatbázis van, ismételje meg a folyamatot az egyes adatbázisokhoz.


## <a name="next-steps"></a>Következő lépések

- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-szolgáltatások](../database/features-comparison.md).
- További információ a VNet konfigurálásáról: [SQL felügyelt példány VNet konfigurációja](connectivity-architecture-overview.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](instance-create-quickstart.md).
- A Azure Database Migration Service áttelepítésre való használatával kapcsolatos oktatóanyagért lásd: [SQL felügyelt példányok áttelepítése Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md).
- Az SQL felügyelt példány-adatbázis teljesítményének speciális monitorozásához a beépített hibaelhárítási intelligenciával kapcsolatban lásd: az [Azure SQL felügyelt példány figyelése Azure SQL Analytics használatával](../../azure-monitor/insights/azure-sql.md).
- A díjszabással kapcsolatos információkért lásd: az [SQL felügyelt példányának díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).