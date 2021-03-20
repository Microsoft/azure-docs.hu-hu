---
title: SQL Server VM futtatása egy dedikált Azure-gazdagépen
description: Megtudhatja, hogyan futtathat SQL Server VM egy dedikált Azure-gazdagépen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 957e458bf21eb4a95b1faba1daf0459271648310
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358690"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server VM futtatása egy dedikált Azure-gazdagépen 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk a SQL Server virtuális gép (VM) [Azure dedikált gazdagépen](../../../virtual-machines/dedicated-hosts.md)való használatának részleteit részletezi. Az Azure dedikált gazdagép további információit az [Azure dedikált gazdagép bevezetését](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)ismertető blogbejegyzésben találja. 

## <a name="overview"></a>Áttekintés
Az [Azure dedikált gazdagép](../../../virtual-machines/dedicated-hosts.md) olyan szolgáltatás, amely fizikai kiszolgálókat biztosít, amelyek egy vagy több virtuális gép üzemeltetésére alkalmasak egyetlen Azure-előfizetéshez. A dedikált gazdagépek ugyanazok a fizikai kiszolgálók, amelyeket a Microsoft adatközpontjai használnak erőforrásként. Dedikált gazdagépeket a régión, a rendelkezésre állási zónán és a tartalék tartományon belül is kiépítheti. Ezután elhelyezheti a virtuális gépeket közvetlenül a kiépített gazdagépeken, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.

## <a name="limitations"></a>Korlátozások

- Nem minden virtuálisgép-sorozat támogatott a dedikált gazdagépeken, a virtuálisgép-sorozat elérhetősége pedig régiónként változik. További információ: [Az Azure dedikált gazdagépek áttekintése](../../../virtual-machines/dedicated-hosts.md).

## <a name="licensing"></a>Licencek

Két különböző licencelési lehetőség közül választhat, ha a SQL Server VM egy Azure dedikált gazdagépen helyezi el. 

  - **SQL virtuális gép licencelése**: Ez a meglévő licencelési lehetőség, ahol egyenként kell fizetnie az egyes SQL Server VM licencekhez. 
  - **Dedikált gazdagép licencelése**: az Azure dedikált gazdagéphez elérhető új licencelési modell, ahol a SQL Server licencek a gazdagép szintjén vannak csomagolva és fizetve. 


A meglévő SQL Server licencek használatára vonatkozó gazdagép-szintű beállítások: 
  - SQL Server Enterprise Edition Azure Hybrid Benefit (AHB)
    - Elérhető az SA vagy előfizetéssel rendelkező ügyfelek számára.
    - Licenc az összes rendelkezésre álló fizikai magot és korlátlan virtualizációt élvez (a gazdagép által támogatott maximális vCPU).
        - További információ a AHB Azure dedikált gazdagépre való alkalmazásáról: [Azure Hybrid BENEFIT GYIK](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - SQL Server az október 1. előtt beszerzett licencek
      - A SQL Server Enterprise kiadásban a gazdagép-és a virtuális gépek licencelési lehetőségei is megadhatók. 
      - A SQL Server Standard Edition rendszerhez csak a virtuális gépekre vonatkozó licencelési lehetőség áll rendelkezésre. 
          - Részletekért lásd: [Microsoft-termékek használati feltételei](https://www.microsoft.com/licensing/product-licensing/products). 
  - Ha nincs kiválasztva SQL Server dedikált gazdagép-szintű beállítás, akkor a több-bérlős virtuális gépekhez hasonlóan a SQL Server AHB is kiválaszthatja az egyes virtuális gépek szintjén.



## <a name="provisioning"></a>Kiépítés  
A SQL Server VM dedikált gazdagépre való kiépítés nem különbözik az Azure-beli virtuális gépektől. Ezt a [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), a [Azure Portal](../../../virtual-machines/dedicated-hosts-portal.md)és az [Azure CLI](../../../virtual-machines/linux/dedicated-hosts-cli.md)használatával teheti meg.

Egy meglévő SQL Server VM a dedikált gazdagéphez való hozzáadásának folyamata állásidőt igényel, de nem befolyásolja az adatvesztést, és nem fog adatvesztést okozni. Ennek ellenére az áthelyezés előtt biztonsági mentést kell készíteni az összes adatbázisról, beleértve a rendszeradatbázisokat is.

## <a name="virtualization"></a>Virtualizáció 

A dedikált gazdagépek egyik előnye, hogy korlátlan a virtualizáció. Rendelkezhet például 64 virtuális mag-licenccel, de úgy is beállíthatja, hogy a gazdagép 128-virtuális mag rendelkezzen, így megduplázhatja a virtuális mag, de csak a felet kell fizetnie a SQL Server licencekhez. 

Mivel mivel ez a gazdagép, jogosult a virtualizáció 1:2 arányú beállítására. 

## <a name="faq"></a>GYIK

**K: Hogyan működik a Azure Hybrid Benefit a Windows Server/SQL Server-licencek esetében az Azure dedikált gazdagépen?**

A: a meglévő Windows Server-és SQL Server-licencek értékét frissítési garanciával vagy előfizetési licencekkel vehetik igénybe, hogy a Azure Hybrid Benefit használatával kedvezményes díjszabást fizessenek az Azure dedikált gazdagépen. A Windows Server Datacenter és a SQL Server Enterprise Edition rendszerű ügyfelek korlátlan virtualizációt kapnak (több Windows Server rendszerű virtuális gépet helyezhet üzembe a gazdagépen, a mögöttes kiszolgáló fizikai kapacitása alapján), amikor a teljes gazdagépet licencbe helyezik, és Azure Hybrid Benefit használják.  Az Azure dedikált gazdagép összes Windows Server-és SQL Server-munkaterhelése külön díj nélkül jogosult a Windows Server és a SQL Server 2008/R2 további biztonsági frissítéseire. 

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](doc-changes-updates-release-notes.md)