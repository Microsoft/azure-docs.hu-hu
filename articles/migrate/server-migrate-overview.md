---
title: VMware áttelepítési lehetőség kiválasztása Azure Migrate kiszolgáló áttelepítésével
description: Áttekintést nyújt a VMware virtuális gépek Azure-ba való áttelepítésének lehetőségeiről Azure Migrate Server áttelepítéssel
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 130fcaacedc8aaea7790f6aa9ca7463b4e378e02
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070711"
---
# <a name="select-a-vmware-migration-option"></a>VMware áttelepítési lehetőség kiválasztása

A VMware virtuális gépeket áttelepítheti az Azure-ba az Azure Migrate Server áttelepítési eszköz használatával. Ez az eszköz néhány lehetőséget kínál a VMware virtuális gépek áttelepítésére:

- Áttelepítés ügynök nélküli replikálással. Telepítse át a virtuális gépeket anélkül, hogy bármit telepítenie kellene rájuk.
- Áttelepítés ügynökkel a replikáláshoz. Telepítsen egy ügynököt a virtuális gépre a replikáláshoz.


## <a name="compare-migration-methods"></a>Áttelepítési módszerek összehasonlítása

A kiválasztott összehasonlítások segítségével eldöntheti, hogy melyik módszert kell használni. Az [ügynök](migrate-support-matrix-vmware-migration.md#agentless-migration) nélküli és az [ügynök-alapú](migrate-support-matrix-vmware-migration.md#agent-based-migration) áttelepítés teljes támogatási követelményeit is ellenőrizheti.

**Beállítás** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
**Azure-engedélyek** | Azure Migrate projekt létrehozásához, valamint a Azure Migrate berendezés telepítésekor létrehozott Azure AD-alkalmazások regisztrálásához engedélyekre van szükség. | Közreműködői engedélyekkel kell rendelkeznie az Azure-előfizetéshez. 
**Replikáció** | Egy vCenter Server legfeljebb 300 virtuális gépet lehet replikálni egyszerre.<br/> Ha több mint 50 virtuális gépet szeretne áttelepíteni, hozzon létre több köteget a virtuális gépekről.<br/> Ha több replikálást végez, a szolgáltatás hatással lesz a teljesítményre.<br/><br/> A portálon egyszerre legfeljebb 10 gépet választhat a replikáláshoz. Több gép replikálásához vegyen fel 10-es kötegeket.| A replikálási kapacitás a replikációs berendezés skálázásával nő.
**Berendezések üzembe helyezése** | A [Azure Migrate berendezést](migrate-appliance.md) a helyszínen helyezik üzembe. | A [Azure Migrate replikációs berendezést](migrate-replication-appliance.md) a helyszínen helyezik üzembe.
**Site Recovery kompatibilitás** | Kompatibilis. | Nem lehet replikálni Azure Migrate-kiszolgáló áttelepítésével, ha Site Recovery használatával beállította egy gép replikálását.
**Céllemez** | Felügyelt lemezek | Felügyelt lemezek
**Lemezterület-korlátok** | OPERÁCIÓSRENDSZER-lemez: 2 TB<br/><br/> Adatlemez: 32 TB<br/><br/> Maximális lemezek: 60 | OPERÁCIÓSRENDSZER-lemez: 2 TB<br/><br/> Adatlemez: 32 TB<br/><br/> Maximális lemezek: 63
**Továbbító lemezek** | Nem támogatott | Támogatott
**UEFI-rendszerindítás** | Támogatott. | Támogatott.

## <a name="compare-deployment-steps"></a>Telepítési lépések összehasonlítása

A korlátozások áttekintése után megismerheti, hogy az egyes megoldások üzembe helyezésével kapcsolatos lépések segítenek eldönteni, hogy melyik lehetőséget kell választania.

**Feladat** | **Részletek** |**Ügynök nélküli** | **Ügynök-alapú**
--- | --- | --- | ---
**Az Azure Migrate-berendezés üzembe helyezése** | Egy, a VMware virtuális gépen futó, kis méretű készülék.<br/><br/> A berendezés a gépek felderítésére és értékelésére, valamint a gépek ügynök nélküli áttelepítéssel történő áttelepítésére szolgál. | Kötelező.<br/><br/> Ha már beállította a készüléket az értékeléshez, ugyanazt a készüléket használhatja az ügynök nélküli áttelepítés számára. | Nem szükségesek.<br/><br/> Ha már beállított egy készüléket az értékeléshez, hagyja azt helyben, vagy távolítsa el, ha elkészült az értékeléssel.
**A kiszolgáló-Assessment eszköz használata** | Mérje fel a gépeket a Azure Migrate: Server Assessment Tool eszközzel. | A gépeket a Migrálás előtt is felhasználhatja, de nem kell. | Az értékelés nem kötelező.
**A kiszolgáló áttelepítési eszközének használata** | Adja hozzá a Azure Migrate Server áttelepítési eszközt a Azure Migrate projektben. | Kötelező | Kötelező
**A VMware előkészítése áttelepítésre** | Konfigurálja a beállításokat a VMware-kiszolgálókon és a virtuális gépeken. | Kötelező | Kötelező
**A mobilitási szolgáltatás telepítése virtuális gépeken** | A mobilitási szolgáltatás minden olyan virtuális gépen fut, amelyet replikálni szeretne | Nem szükséges | Kötelező
**A replikációs berendezés üzembe helyezése** | A [replikációs berendezés](migrate-replication-appliance.md) ügynök-alapú áttelepítéshez használatos. A virtuális gépeken futó mobilitási szolgáltatás és a kiszolgáló áttelepítése között csatlakozik. | Nem szükséges | Kötelező
**Virtuális gépek replikálása**. Engedélyezze a virtuális gép replikálását. | Replikációs beállítások konfigurálása és a replikálni kívánt virtuális gépek kiválasztása | Kötelező | Kötelező
**Migrálási teszt futtatása** | Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e. | Kötelező | Kötelező
**Teljes áttelepítés futtatása** | Telepítse át a virtuális gépeket. | Kötelező | Kötelező



## <a name="next-steps"></a>Következő lépések

[VMWare virtuális gépek migrálása](tutorial-migrate-vmware.md) ügynök nélküli áttelepítéssel.



