---
title: SQL Server kiadás helyben történő módosítása
description: Megtudhatja, hogyan módosíthatja a SQL Server virtuális gép kiadását az Azure-ban, hogy csökkentse a költségeket vagy a frissítést a további funkciók engedélyezéséhez.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b6ea6f947b2cd8d7d9c38a0e5e440ce5fd273bb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358826"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Az Azure-beli virtuális gépen futó SQL Server kiadásának módosítása helyben
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan módosítható a SQL Server kiadása egy Windows rendszerű virtuális gépen az Azure-ban. 

A SQL Server kiadása a termékkulcs alapján van meghatározva, és a telepítési folyamat során a telepítési adathordozó használatával van megadva. A kiadás azt szabja meg, hogy mely [funkciók](/sql/sql-server/editions-and-components-of-sql-server-2017) érhetők el a SQL Server termékben. A SQL Server kiadása a telepítési adathordozóval módosítható, és a további funkciók engedélyezéséhez csökkentheti a költségeket vagy a frissítést.

Ha a SQL Server kiadását belsőleg módosították a SQL Server VMre, akkor a Azure Portal SQL Server kiadás tulajdonságát a számlázáshoz kell frissítenie. 

## <a name="prerequisites"></a>Előfeltételek

A SQL Server kiadásának helyben történő megváltoztatásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Az [SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md)regisztrált [Windows-SQL Server VM](./create-sql-vm-portal.md) .
- Állítsa be az adathordozót SQL Server **kívánt kiadásával** . A [Frissítési garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) rendelkező ügyfelek a [Mennyiségilicenc-szolgáltatási központból](https://www.microsoft.com/Licensing/servicecenter/default.aspx) szerezhetik be a telepítési adathordozót. Azok az ügyfelek, akik nem rendelkeznek frissítési garanciával, a kívánt kiadással rendelkező Azure Marketplace SQL Server VM-rendszerkép telepítési adathordozóját használhatják (általában a következő helyen található `C:\SQLServerFull` ). 


## <a name="upgrade-an-edition"></a>Kiadás frissítése

> [!WARNING]
> A SQL Server kiadásának verziófrissítése újraindul a szolgáltatás SQL Serverhoz, valamint a hozzájuk kapcsolódó szolgáltatásokhoz, például a Analysis Serviceshoz és az R szolgáltatásokhoz. 

SQL Server kiadásának frissítéséhez szerezze be a SQL Server kívánt kiadásához tartozó SQL Server telepítési adathordozót, majd tegye a következőket:

1. Nyissa meg Setup.exe a SQL Server telepítési adathordozóról. 
1. Lépjen a **karbantartás** menüpontra, és válassza a **kiadás frissítése** lehetőséget. 

   ![SQL Server kiadásának frissítésére szolgáló kijelölés](./media/change-sql-server-edition/edition-upgrade.png)

1. Kattintson a **Next (tovább** ) gombra, amíg el nem éri a **Verziófrissítési kiadás** oldalát, majd válassza a **frissítés** lehetőséget. Előfordulhat, hogy a beállítási időszak néhány percen belül leáll, amíg a módosítás érvénybe lép. A **teljes** oldal megerősíti, hogy a kiadás frissítése befejeződött. 

A SQL Server kiadás frissítése után módosítsa a Azure Portal SQL Server virtuális gép kiadás tulajdonságát. Ezzel frissíti a virtuális géphez társított metaadatokat és számlázást.

## <a name="downgrade-an-edition"></a>Kiadás visszaminősítése

SQL Server kiadásának visszalépéséhez teljesen el kell távolítania SQL Server, majd újra újra kell telepítenie a kívánt kiadás telepítési adathordozóján. 

> [!WARNING]
> A SQL Server eltávolítása további állásidőt eredményezhet. 

A SQL Server kiadásának visszalépéséhez kövesse az alábbi lépéseket:

1. Az összes adatbázis biztonsági mentése, beleértve a rendszeradatbázisokat is. 
1. Rendszeradatbázisok (Master, Model és msdb) áthelyezése egy új helyre. 
1. Távolítsa el teljesen SQL Server és az összes társított szolgáltatást. 
1. Indítsa újra a virtuális gépet. 
1. Telepítse SQL Server a SQL Server kívánt kiadásával az adathordozón.
1. Telepítse a legújabb szervizcsomagokat és összesítő frissítéseket.  
1. Cserélje le a telepítés során létrehozott új rendszeradatbázisokat azokra a rendszeradatbázisokra, amelyeket korábban más helyre helyezett át. 

A SQL Server kiadásának visszaminősítése után módosítsa a Azure Portal SQL Server virtuális gép kiadás tulajdonságát. Ezzel frissíti a virtuális géphez társított metaadatokat és számlázást.

## <a name="change-edition-in-portal"></a>Kiadás módosítása a portálon 

Miután módosította SQL Server kiadását a telepítési adathordozó használatával, és regisztrálta az SQL Server VM az [SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md), a Azure Portal használatával módosíthatja a SQL Server VM kiadási tulajdonságát a számlázási célokra. Ehhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Nyissa meg a SQL Server virtuális gép erőforrását. 
1. A **Beállítások** területen válassza a **Konfigurálás** lehetőséget. Ezután válassza ki az SQL Server kívánt kiadását a **kiadás** alatti legördülő listából. 

   ![Kiadási metaadatok módosítása](./media/change-sql-server-edition/edition-change-in-portal.png)

1. Tekintse át azt a figyelmeztetést, amely szerint először módosítania kell a SQL Server kiadást, és hogy a kiadás tulajdonságnak meg kell egyeznie a SQL Server kiadással. 
1. Válassza az **alkalmaz** elemet a kiadás metaadatainak módosításainak alkalmazásához. 


## <a name="remarks"></a>Megjegyzések

- A SQL Server VM kiadási tulajdonságának meg kell egyeznie az összes SQL Server virtuális géphez telepített SQL Server példány kiadásával, beleértve az utólagos elszámolású és a saját licences licencek típusát is.
- Ha eldobja a SQL Server VM-erőforrást, visszatérhet a rendszerkép rögzített változatára.
- A kiadás módosításának lehetősége az SQL IaaS-ügynök bővítmény egyik funkciója. Egy Azure Marketplace-rendszerkép üzembe helyezése a Azure Portal automatikusan regisztrálja az SQL IaaS-ügynök bővítménnyel rendelkező SQL Server VM. A SQL Server önálló telepítését végző ügyfeleknek azonban manuálisan kell [regisztrálniuk SQL Server VM](sql-agent-extension-manually-register-single-vm.md).
- SQL Server VM hozzáadása egy rendelkezésre állási csoporthoz újra kell létrehozni a virtuális gépet. A rendelkezésre állási csoportba felvett virtuális gépek vissza lesznek állítva az alapértelmezett kiadásra, és a kiadást újra módosítani kell.

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](doc-changes-updates-release-notes.md)