---
title: Azure Migrate és Site Recovery összehasonlítása az Azure-ba való Migrálás során
description: Összefoglalja az Azure Migrate használatának előnyeit Site Recovery helyett az áttelepítéshez.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: raynew
ms.openlocfilehash: 358efaa1493aa08fb76c9bb83e0e4289950e0969
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87844320"
---
# <a name="migrating-to-azure"></a>Migrálás az Azure-ba

A Migrálás esetében javasoljuk, hogy a Azure Migrate szolgáltatás használatával telepítse át a virtuális gépeket és a kiszolgálókat az Azure-ba a Azure Site Recovery szolgáltatás helyett. [További](../migrate/migrate-services-overview.md) információ a Azure Migrateról.


## <a name="why-use-azure-migrate"></a>Miért javasolt az Azure Migrate használata?

Az áttelepítéshez Azure Migrate használata számos előnyt biztosít:
 
 
- Azure Migrate központosított hubot biztosít a felderítéshez, az értékeléshez és az Azure-ba való áttelepítéshez.
- A Azure Migrate használata lehetővé teszi a Azure Migrate eszközökkel, más Azure-szolgáltatásokkal és harmadik féltől származó eszközökkel való együttműködés és jövőbeni bővíthetőség biztosítását.
- A Azure Migrate: a kiszolgáló áttelepítési eszköze az Azure-ba történő kiszolgáló-áttelepítéshez készült. Áttelepítésre van optimalizálva. Nem kell megismernie az áttelepítéshez közvetlenül nem kapcsolódó fogalmakat és forgatókönyveket. 
- A 180 napos áttelepítéshez nem tartoznak eszköz-használati díjak a virtuális gépek replikálásának időpontjában. Ez időt biztosít az áttelepítés befejezésére. Csak a replikációhoz használt tárolási és hálózati erőforrásokért, valamint a tesztelési áttelepítések során felhasznált számítási díjakért kell fizetnie.
- A Azure Migrate a Site Recovery által támogatott összes áttelepítési forgatókönyvet támogatja. Emellett a VMware virtuális gépek esetében a Azure Migrate ügynök nélküli áttelepítési lehetőséget biztosít.
- Új áttelepítési funkciókat rangsorolunk a Azure Migrate: csak a kiszolgáló áttelepítését szolgáló eszközhöz. Ezek a szolgáltatások nem célozzák meg Site Recovery.

## <a name="when-to-use-site-recovery"></a>Mikor kell használni a Site Recovery?

Site Recovery kell használni:

- A helyszíni gépek Azure-ba való vész-helyreállításához.
- Azure-beli virtuális gépek vész-helyreállításához az Azure-régiók között.

Habár azt javasoljuk, hogy a Azure Migrate használatával telepítse át a helyszíni kiszolgálókat az Azure-ba, ha már elindította az áttelepítést a Site Recoveryval, továbbra is használhatja az áttelepítés befejezéséhez.  

## <a name="next-steps"></a>Következő lépések

> [Tekintse át](../migrate/resources-faq.md) a Azure Migrateával kapcsolatos gyakori kérdéseket.
