---
title: A Azure Site Recovery és a Azure Backup használatának támogatása
description: Áttekintést nyújt a Azure Site Recovery és Azure Backup együttes használatáról.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135183"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>A Site Recovery és a Azure Backup használatának támogatása

Ez a cikk a [site Recovery szolgáltatás](site-recovery-overview.md) és a [Azure Backup szolgáltatás](../backup/backup-overview.md)együttes használatának támogatását foglalja össze.

**Művelet** | **Site Recovery támogatása** | **Részletek**
--- | --- | ---
**Szolgáltatások együttes üzembe helyezése** | Támogatott | A szolgáltatások együttműködésre alkalmasak, és konfigurálhatók egymással.
**Fájl biztonsági mentése/visszaállítása** | Támogatott | Ha a biztonsági mentés és a replikáció engedélyezve van egy virtuális gépen, és biztonsági mentést végez, a fájlok a forrás-és a virtuális gépeken való visszaállítása nem jelent problémát. A replikáció állapota a szokásosnál folytatódik, a replikálás állapotának változása nélkül.
**Lemez visszaállítása** | Nincs jelenlegi támogatás | Ha egy biztonsági másolattal rendelkező lemezt állít vissza, le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikálását.
**Virtuális gép visszaállítása** | Nincs jelenlegi támogatás | Ha egy virtuális gépet vagy virtuális gépek csoportját állítja vissza, le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikálását.  


