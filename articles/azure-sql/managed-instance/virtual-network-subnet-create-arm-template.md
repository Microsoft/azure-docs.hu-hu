---
title: Virtuális hálózat létrehozása
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk azt ismerteti, hogyan hozható létre olyan virtuális hálózat, amely támogatja az Azure SQL felügyelt példányának telepítését.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 09/12/2019
ms.openlocfilehash: 2a23fc0b769727cab5a28d3d313a7791bcfa6eee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91617706"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Virtuális hálózat létrehozása felügyelt Azure SQL-példányhoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre egy érvényes virtuális hálózatot és alhálózatot, ahol üzembe helyezheti az Azure SQL felügyelt példányát.

Az Azure SQL felügyelt példányát egy Azure-beli [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni. Ez a központi telepítés a következő forgatókönyveket teszi lehetővé:

- Biztonságos magánhálózati IP-cím
- Csatlakozás az SQL felügyelt példányhoz közvetlenül egy helyszíni hálózatról
- SQL felügyelt példány csatlakoztatása egy csatolt kiszolgálóhoz vagy egy másik helyszíni adattárhoz
- SQL felügyelt példány csatlakoztatása az Azure-erőforrásokhoz  

> [!NOTE]
> Az első példány üzembe helyezése előtt meg kell [határoznia az alhálózat méretét az SQL által felügyelt példányhoz](vnet-subnet-determine-size.md) . Az alhálózatot az erőforrások belső elhelyezése után nem lehet átméretezni.
>
> Ha meglévő virtuális hálózat használatát tervezi, akkor módosítania kell a hálózati konfigurációt az SQL felügyelt példányának befogadásához. További információ: [meglévő virtuális hálózat módosítása SQL felügyelt példányhoz](vnet-existing-add-subnet.md).
>
> Felügyelt példány létrehozása után a felügyelt példány vagy a virtuális hálózat másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.  A felügyelt példány másik alhálózatra való áthelyezése szintén nem támogatott.
>

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozásának és konfigurálásának legegyszerűbb módja egy Azure Resource Manager telepítési sablon használata.

1. Jelentkezzen be az Azure Portalra.

2. Válassza az **üzembe helyezés az Azure-** ban gombot:

   [![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json)

   Ezzel a gombbal megnyithatja a hálózati környezet konfigurálásához használható űrlapot, amelyen telepítheti az SQL felügyelt példányát.

   > [!Note]
   > Ez a Azure Resource Manager-sablon két alhálózattal rendelkező virtuális hálózatot fog telepíteni. Egy **ManagedInstances** nevű alhálózat a felügyelt SQL-példány számára van fenntartva, és előre konfigurált útválasztási táblázattal rendelkezik. A másik, **alapértelmezett** nevű alhálózat más erőforrásokhoz is használatos, amelyeknek hozzá kell férniük az SQL felügyelt példányához (például Azure Virtual machines).

3. Konfigurálja a hálózati környezetet. A következő űrlapon konfigurálhatja a hálózati környezet paramétereit:

   ![Resource Manager-sablon az Azure-hálózat konfigurálásához](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   Módosíthatja a virtuális hálózat és az alhálózatok nevét, és módosíthatja a hálózati erőforrásokhoz társított IP-tartományokat. Miután kiválasztotta a **vásárlás** gombot, az űrlap létrehozza és konfigurálja a környezetet. Ha nincs szüksége két alhálózatra, törölheti az alapértelmezettet.

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Mi az SQL felügyelt példány?](sql-managed-instance-paas-overview.md).
- Ismerje meg a [kapcsolati architektúrát az SQL felügyelt példányában](connectivity-architecture-overview.md).
- Megtudhatja, hogyan [módosíthat egy meglévő virtuális hálózatot az SQL felügyelt példányain](vnet-existing-add-subnet.md).
- A virtuális hálózatok létrehozásával, felügyelt példány létrehozásával és adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért lásd: [felügyelt példány létrehozása](instance-create-quickstart.md).
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md)című témakört.
