---
title: Helyszíni gépek migrálása Azure Migrate
description: Ez a cikk összefoglalja, hogyan telepítheti át a helyszíni gépeket az Azure-ba, és hogyan ajánlja Azure Migrate.
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: e0e60ee346d20113b2ec7970390d9874522cc770
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87847312"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Helyszíni gépek áttelepítése az Azure-ba

Ez a cikk a helyszíni gépek Azure-ba történő áttelepítésének lehetőségeit ismerteti. 

## <a name="migrate-with-azure-migrate"></a>Migrálás Azure Migrate

Javasoljuk, hogy telepítse át a gépeket az Azure-ba a [Azure Migrate](../migrate/migrate-services-overview.md) szolgáltatás használatával. A Azure Migrate a kiszolgáló áttelepítéséhez készült. Azure Migrate központosított hubot biztosít a helyszíni gépek Azure-ba való felderítéséhez, értékeléséhez és áttelepítéséhez.

Az alábbi hivatkozásokat követve telepítse át Azure Migrate:

- [Ismerje meg](../migrate/server-migrate-overview.md) a VMWare virtuális gépek áttelepítési lehetőségeit, majd telepítse át a virtuális gépeket az Azure-ba [ügynök](../migrate/tutorial-migrate-vmware.md) [nélküli vagy ügynök alapú](../migrate/tutorial-migrate-vmware-agent.md) áttelepítéssel.
- [Telepítse át a Hyper-V virtuális gépeket az Azure-ba](../migrate/tutorial-migrate-hyper-v.md) .
- Áttelepíthet [fizikai kiszolgálókat vagy más virtuális gépeket](../migrate/tutorial-migrate-physical-virtual-machines.md), beleértve az [AWS-példányokat](../migrate/tutorial-migrate-aws-virtual-machines.md) az Azure-ba.

## <a name="migrate-with-site-recovery"></a>Migrálás Site Recovery
Site Recovery csak vész-helyreállításra kell használni, és nem kell áttelepítenie.

Ha már használja a Azure Site Recoveryt, és továbbra is szeretné használni az áttelepítéshez, kövesse a vész-helyreállításhoz használt lépéseket.

- VMware virtuális gépek: [készítse elő az Azure](tutorial-prepare-azure.md) -t és a [VMware](vmware-azure-tutorial-prepare-on-premises.md)-et, indítsa el a [replikálást](vmware-azure-tutorial.md), [Győződjön](tutorial-dr-drill-azure.md) meg róla, hogy minden működik, és [futtasson feladatátvételt](vmware-azure-tutorial-failover-failback.md)
- Hyper-V virtuális gépek: [készítse elő az Azure](tutorial-prepare-azure-for-hyperv.md) -t és a [Hyper-v-](hyper-v-prepare-on-premises-tutorial.md)t, indítsa el a [gépek replikálását](hyper-v-azure-tutorial.md), [Győződjön](tutorial-dr-drill-azure.md) meg róla, hogy minden működik, és [futtasson feladatátvételt](hyper-v-azure-failover-failback-tutorial.md)
- Fizikai kiszolgálók: [kövesse az](physical-azure-disaster-recovery.md) Azure előkészítésére szolgáló útmutatót, készítse elő a gépeket a vész-helyreállításhoz, és állítsa be a replikációt.

> [!NOTE]
> Ha feladatátvételt futtat a vész-helyreállításhoz, a feladatátvétel utolsó lépéseként véglegesíti a tranzakciót. A helyszíni gépek áttelepítésekor a **véglegesítés** lehetőség nem érvényes. Ehelyett válassza a **teljes áttelepítési** lehetőséget. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tekintse át](../migrate/resources-faq.md) a Azure Migrateával kapcsolatos gyakori kérdéseket.

  
