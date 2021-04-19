---
title: VMware-migrálási lehetőség kiválasztása a Azure Migrate Server Migration lehetőséggel
description: Áttekintést nyújt a VMware virtuális gépek Azure-ba való migrálásának lehetőségeiről a Azure Migrate Server Migration használatával
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 22a0629d50ee8181ffcbfe7dad32ab76fb3e68fd
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714163"
---
# <a name="select-a-vmware-migration-option"></a>VMware-migrálási lehetőség kiválasztása

A VMware virtuális gépeket az Azure-ba a Azure Migrate Server Migration eszközzel migrálhatja. Ez az eszköz a VMware virtuális gépek migrálásának néhány beállítását kínálja:

- Migrálás ügynök nélküli replikációval. Anélkül telepíthet át virtuális gépeket, hogy bármit is telepítenie kellene rájuk.
- Migrálás egy replikációs ügynökkel. Telepítsen egy ügynököt a virtuális gépre a replikációhoz.


## <a name="compare-migration-methods"></a>Migrálási módszerek összehasonlítása

Ezekkel a kiválasztott összehasonlításokkal eldöntheti, hogy melyik módszert használja. Az ügynök nélküli és [](migrate-support-matrix-vmware-migration.md#agentless-migration) ügynökalapú áttelepítés teljes támogatási követelményeit [is áttekintheti.](migrate-support-matrix-vmware-migration.md#agent-based-migration)

**Beállítás** | **Ügynök nélküli** | **Ügynökalapú**
--- | --- | ---
**Azure-engedélyek** | Engedélyekkel kell rendelkeznie egy Azure Migrate projekt létrehozásához és az új berendezés üzembe helyezésekor létrehozott Azure AD-Azure Migrate regisztráláshoz. | Közreműködői engedélyekre van szüksége az Azure-előfizetésben. 
**Replikáció** | Egy virtuális gépről egyidejűleg legfeljebb 500 virtuális gép replikálható vCenter Server. A portálon egyszerre legfeljebb 10 gépet választhat ki a replikációhoz. További gépek replikálása 10-es kötegekkel.| A replikációs kapacitás a replikációs berendezés méretezésével növekszik.
**Berendezés üzembe helyezése** | A [Azure Migrate berendezés](migrate-appliance.md) a helyszínen van üzembe stb. | A [Azure Migrate replikációs berendezés](migrate-replication-appliance.md) a helyszínen van üzembe stb.
**Site Recovery kompatibilitás** | Kompatibilis. | A Azure Migrate Server Migration használatával nem lehet replikálni, ha a gép replikációját az Site Recovery.
**Céllemez** | Felügyelt lemezek | Felügyelt lemezek
**Lemezkorlátok** | Operációsrendszer-lemez: 2 TB<br/><br/> Adatlemez: 32 TB<br/><br/> Maximális lemez: 60 | Operációsrendszer-lemez: 2 TB<br/><br/> Adatlemez: 32 TB<br/><br/> Maximális lemezek: 63
**Áttért lemezek** | Nem támogatott | Támogatott
**UEFI-rendszerindítás** | Támogatott. | Támogatott. 
**Kapcsolódás** | Nyilvános internet <br/> ExpressRoute Microsoft társviszony-létesítéssel <br/> <br/> [Megtudhatja,](./replicate-using-expressroute.md) hogyan használhat privát végpontokat ExpressRoute privát társviszony-létesítésen vagy S2S VPN keresztüli replikációhoz. |Nyilvános internet <br/> ExpressRoute privát társviszony-létesítéssel <br/> ExpressRoute Microsoft társviszony-létesítéssel <br/> Helyek közötti VPN

## <a name="compare-deployment-steps"></a>Üzembe helyezési lépések összehasonlítása

A korlátozások áttekintése után az egyes megoldások üzembe helyezéséhez szükséges lépések megértése segíthet eldönteni, hogy melyik lehetőséget válassza.

**Feladat** | **Részletek** |**Ügynök nélküli** | **Ügynökalapú**
--- | --- | --- | ---
**Az Azure Migrate-berendezés üzembe helyezése** | VMware virtuális gépen futó egyszerűsített berendezés.<br/><br/> A berendezés a gépek felderítésére és értékelésére, valamint a gépek ügynök nélküli migrálásával való áttelepítésére használható. | Kötelező.<br/><br/> Ha már beállította a berendezést az értékeléshez, használhatja ugyanazt a berendezést az ügynök nélküli migráláshoz. | Nem szükségesek.<br/><br/> Ha beállított egy berendezést az értékeléshez, akkor a helyén hagyhatja, vagy eltávolíthatja, ha végzett az értékeléssel.
**A Server Assessment eszköz használata** | Gépek értékelése a Azure Migrate:Server Assessment eszköz használatával. | Az értékelés nem kötelező. | Az értékelés nem kötelező.
**A Server Migration eszköz használata** | Adja hozzá Azure Migrate Server Migration eszközt a Azure Migrate projekthez. | Kötelező | Kötelező
**A VMware előkészítése a migráláshoz** | Konfigurálja a VMware-kiszolgálók és virtuális gépek beállításait. | Kötelező | Kötelező
**Az Mobility szolgáltatás telepítése virtuális gépeken** | Mobility szolgáltatás a replikálni kívánt virtuális gépeken | Nem szükséges | Kötelező
**A replikációs berendezés üzembe helyezése** | A [replikációs berendezés](migrate-replication-appliance.md) ügynökalapú migráláshoz használható. A virtuális gépeken futó Mobility szolgáltatás és a Server Migration között csatlakozik. | Nem szükséges | Kötelező
**Virtuális gépek replikálása.** Virtuálisgép-replikáció engedélyezése. | Replikációs beállítások konfigurálása és a replikálni kívánt virtuális gépek kiválasztása | Kötelező | Kötelező
**Migrálási teszt futtatása** | Egy migrálási teszt futtatásával ellenőrizze, hogy minden a várt módon működik-e. | Kötelező | Kötelező
**Teljes migrálás futtatása** | A virtuális gépek áttelepítése. | Kötelező | Kötelező



## <a name="next-steps"></a>Következő lépések

[VMware virtuális gépek áttelepítése](tutorial-migrate-vmware.md) ügynök nélküli migrálással.



