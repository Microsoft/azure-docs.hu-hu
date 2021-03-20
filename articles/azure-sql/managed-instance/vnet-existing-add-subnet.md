---
title: Meglévő virtuális hálózat konfigurálása
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni egy meglévő virtuális hálózatot és alhálózatot, ahol telepítheti az Azure SQL felügyelt példányát.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 461acc07ee2217a38f7bb59805d4c7e0de4a1e22
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91617655"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Meglévő virtuális hálózat konfigurálása felügyelt Azure SQL-példányhoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányát egy Azure-beli [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md) belül kell telepíteni, és csak a felügyelt példányok számára dedikált alhálózatot kell üzembe helyezni. A meglévő virtuális hálózatot és alhálózatot akkor használhatja, ha az [SQL felügyelt példányának virtuális hálózati követelményeinek](connectivity-architecture-overview.md#network-requirements)megfelelően vannak konfigurálva.

Ha a következő esetek valamelyike vonatkozik Önre, a jelen cikkben ismertetett parancsfájllal ellenőrizheti és módosíthatja a hálózatot:

- Olyan új alhálózattal rendelkezik, amely még nincs konfigurálva.
- Nem biztos benne, hogy az alhálózat igazodik a [követelményekhez](connectivity-architecture-overview.md#network-requirements).
- Azt szeretné, hogy az alhálózat továbbra is megfeleljen a [hálózati követelményeknek](connectivity-architecture-overview.md#network-requirements) , miután végzett a módosításokat.

> [!Note]
> Felügyelt példányok csak a Azure Resource Manager üzemi modellel létrehozott virtuális hálózatokon hozhatók létre. A klasszikus üzemi modellel létrehozott Azure-beli virtuális hálózatok nem támogatottak. Az alhálózat méretének kiszámítása a [felügyelt SQL-példányhoz tartozó alhálózat méretének meghatározása](vnet-subnet-determine-size.md) című cikkben ismertetett irányelvek alapján. Az alhálózat a benne lévő erőforrások üzembe helyezése után már nem méretezhető át.
>
> A felügyelt példány létrehozása után a példány vagy a VNet másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Meglévő virtuális hálózat ellenőrzése és módosítása

Ha felügyelt példányt szeretne létrehozni egy meglévő alhálózaton belül, a következő PowerShell-parancsfájlt javasoljuk az alhálózat előkészítéséhez:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

A szkript három lépésben készíti elő az alhálózatot:

1. Ellenőrzés: ellenőrzi a kiválasztott virtuális hálózatot és alhálózatot az SQL felügyelt példányok hálózati követelményeihez.
2. Confirm (megerősítés): megjeleníti a felhasználó által az SQL felügyelt példány központi telepítésének előkészítéséhez szükséges módosításokat. Emellett beleegyezik.
3. Előkészítés: helyesen konfigurálja a virtuális hálózatot és az alhálózatot.

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Mi az SQL felügyelt példány?](sql-managed-instance-paas-overview.md).
- A virtuális hálózatok létrehozásával, felügyelt példány létrehozásával és adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért lásd: [felügyelt példány létrehozása](instance-create-quickstart.md).
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md)című témakört.
