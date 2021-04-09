---
title: Gyakori kérdések az Azure-beli VM-méretekről helyi ideiglenes lemez nélkül
description: Ez a cikk az olyan Microsoft Azure VM-méretekkel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza, amelyek nem rendelkeznek helyi ideiglenes lemezzel.
author: brbell
ms.service: virtual-machines
ms.topic: conceptual
ms.subservice: sizes
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: bd4dcbdc7ab13d18ef7f2d7102c56d1bd8d8758d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582102"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Azure-beli virtuálisgép-méretek helyi ideiglenes lemezzel nem 
Ez a cikk olyan Azure-beli virtuálisgép-méretekkel kapcsolatos gyakori kérdésekre adott válaszokat tartalmaz, amelyek nem rendelkeznek helyi ideiglenes lemezzel (azaz nincs helyi Temp lemez). A virtuálisgép-méretekkel kapcsolatos további információkért tekintse meg a [DV4 és Dsv4-sorozat (általános célú munkaterhelések)](dv4-dsv4-series.md) , illetve [a Ev4 és a Esv4-sorozat (memória-optimalizált számítási feladatok)](ev4-esv4-series.md)specifikációit ismertető témakört.

## <a name="what-does-no-local-temp-disk-mean"></a>Mit jelent a helyi Temp lemez? 
Hagyományosan rendelkezünk olyan virtuálisgép-méretekkel (pl. Standard_D2s_v3, Standard_E48_v3), amelyek kis helyi lemezt tartalmaznak (például D: meghajtó). Most, hogy ezek az új virtuálisgép-méretek már nem léteznek, a kis helyi lemez már nem létezik; azonban továbbra is csatolhat standard HDD, prémium SSD vagy ultra SSD.

## <a name="what-if-i-still-want-a-local-temp-disk"></a>Mi a teendő, ha továbbra is szeretnék egy helyi Temp-lemezt?
Ha a számítási feladathoz helyi ideiglenes lemezre van szükség, akkor az új [Ddv4 és Ddsv4](ddv4-ddsv4-series.md) , illetve a [Edv4 és a Edsv4](edv4-edsv4-series.md) virtuálisgép-méretek is elérhetők. Ezek a méretek 50%-kal nagyobb ideiglenes lemezt biztosítanak az előző v3 méretekhez képest.

> [!NOTE]
> A helyi ideiglenes lemez nem állandó; Ha biztosítani szeretné, hogy az adatai állandóak legyenek, használja standard HDD, prémium SSD vagy ultra SSD beállításokat. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>Mi a különbség az új virtuálisgép-méretek és a általános célú Dv3/Dsv3, illetve a felhasznált Ev3/Esv3 virtuálisgép-méretek között? 
| v3 VM-családok helyi Temp lemezzel   | Új v4-családok helyi Temp lemezzel | Helyi Temp-lemezzel nem rendelkező új v4-családok |
|---|---|---|
| Dv3   | Ddv4 | DV4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Átméretezhetim a virtuálisgép-méretet, amely helyi Temp lemezzel rendelkezik a virtuális gép méretéhez, helyi Temp lemez nélkül?  
Nem. Az átméretezéshez csak a következő kombinációk engedélyezettek: 

1. Virtuális gép (helyi Temp lemezzel) – > virtuális gép (helyi Temp lemezzel); és 
2. Virtuális gép (helyi Temp lemez nélkül) – > virtuális gép (helyi Temp lemez nélkül). 

Ha érdekli a megoldás, tekintse meg a következő kérdést.

> [!NOTE]
> Ha egy rendszerkép az erőforrás-lemeztől függ, vagy a helyi ideiglenes lemezen található egy lapozófájl vagy swapfile, a lemez nélküli lemezképek nem fognak működni – helyette a "lemez" alternatívát használja. 

## <a name="how-do-i-migrate-from-a-vm-size-with-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Hogyan Migrálás virtuális gépről a helyi Temp lemezrel rendelkező virtuálisgép-méretre, helyi Temp lemez nélkül?  
A lépéseket a következő lépésekkel telepítheti át: 

1. Kapcsolódjon a virtuális géphez, amely helyi rendszergazdaként (például a D: meghajtón) található helyi ideiglenes lemezzel.
2. Kövesse a [d: meghajtó használata a Windows rendszerű virtuális gépen](./windows/change-drive-letter.md) az "ideiglenes áthelyezés pagefile.sys a c meghajtóra" című szakaszt, amely a lapozófájlt a helyi ideiglenes lemezről (D: meghajtó) a C: meghajtóra helyezi át.

   > [!NOTE]
   > Kövesse a D: meghajtó használata a Windows rendszerű virtuális gépen adatmeghajtóként a (z) "ideiglenes áthelyezés pagefile.sys a C meghajtóra" című szakasz útmutatását, és helyezze át a lapozófájlt a helyi ideiglenes lemezről (D: meghajtó) a C: meghajtóra. **Az ismertetett lépésektől való eltérés a következő hibaüzenetet eredményezi: "nem lehet átméretezni a virtuális gépet, mert az erőforrás lemezről nem az erőforrás-lemezről nem a virtuálisgép-méretre változik, és fordítva nem engedélyezett.**

3. Készítse el a virtuális gép pillanatképét a [pillanatkép létrehozása a portál vagy az Azure CLI használatával](./linux/snapshot-copy-managed-disk.md)című rész lépéseit követve. 
4. A pillanatkép használatával hozzon létre egy új lemez nélküli virtuális gépet (például:, DV4, Dsv4, Ev4, Esv4 sorozat) a [virtuális gép létrehozása pillanatképből a CLI-vel](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot)című rész lépéseit követve. 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Ezek a virtuálisgép-méretek támogatják a Linux és a Windows operációs rendszer (OS) használatát?
Igen.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Megszakítja az egyéni parancsfájlok, egyéni lemezképek vagy operációsrendszer-lemezképek, amelyek egy helyi Temp-lemezen találhatók a fájlok vagy a lapozófájlok?
Ha az egyéni operációsrendszer-lemezkép a helyi ideiglenes lemezre mutat, lehet, hogy a rendszerkép nem fog megfelelően működni a lemez nélküli mérettel.

## <a name="have-questions-or-feedback"></a>Kérdése van, vagy visszajelzést küldene?
Töltse ki a [visszajelzési űrlapot]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u). 

## <a name="next-steps"></a>Következő lépések 
Ebben a dokumentumban többet tudhat meg az Azure-beli virtuális gépekkel kapcsolatos leggyakoribb kérdésekre, amelyek helyi Temp-lemez nélkül találhatók. További információt ezekről a virtuálisgép-méretekről a következő cikkekben talál:

- [A DV4 és a Dsv4 sorozat specifikációi (általános célú munkaterhelés)](dv4-dsv4-series.md)
- [A Ev4 és a Esv4 sorozat specifikációi (memória-optimalizált számítási feladatok)](ev4-esv4-series.md)