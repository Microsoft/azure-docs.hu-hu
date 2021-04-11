---
title: AWS virtuális gépek migrálása az Azure-ba Azure Migrate
description: Ez a cikk az AWS-példányok Azure-ba való áttelepítésének lehetőségeit, valamint a Azure Migrate használatát ismerteti.
services: site-recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.custom: MVC
ms.openlocfilehash: 6c3f20f0fa3bc6ad41e76626d3cb02ec1c0b96e9
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581344"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services-beli (AWS) virtuális gépek áttelepítése az Azure-ba

Ez a cikk a Amazon Web Services-(AWS-) példányok Azure-ba való áttelepítésének lehetőségeit ismerteti.

## <a name="migrate-with-azure-migrate"></a>Migrálás Azure Migrate

Javasoljuk, hogy telepítse az AWS EC2-példányokat az Azure-ba a [Azure Migrate](../migrate/migrate-services-overview.md) szolgáltatás használatával. A Azure Migrate a kiszolgáló áttelepítéséhez készült. Azure Migrate központosított hubot biztosít a helyszíni gépek Azure-ba való felderítéséhez, értékeléséhez és áttelepítéséhez.

[Ismerje meg, hogyan](../migrate/tutorial-migrate-aws-virtual-machines.md) migrálhatja az AWS-példányokat Azure Migrate használatával. 


## <a name="migrate-with-site-recovery"></a>Migrálás Site Recovery

Site Recovery csak vész-helyreállításra kell használni, és nem kell áttelepítenie.

Ha már használja a Azure Site Recoveryt, és szeretné tovább használni az AWS-áttelepítéshez, kövesse a [fizikai gépek vész-helyreállításának](physical-azure-disaster-recovery.md)beállításához használt lépéseket.


> [!NOTE]
> Ha feladatátvételt futtat a vész-helyreállításhoz, a feladatátvétel utolsó lépéseként véglegesíti a tranzakciót. AWS-példányok áttelepítésekor a **véglegesítés** lehetőség nem érvényes. Ehelyett válassza a **teljes áttelepítési** lehetőséget. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tekintse át](../migrate/resources-faq.md) a Azure Migrateával kapcsolatos gyakori kérdéseket.
