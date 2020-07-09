---
title: Gyakori kérdések az Azure-beli VM-méretekről helyi ideiglenes lemez nélkül
description: Ez a cikk az olyan Microsoft Azure VM-méretekkel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza, amelyek nem rendelkeznek helyi ideiglenes lemezzel.
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 413f53feedc4fee0877694e3f3a3a509c4d38001
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783609"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Azure-beli virtuálisgép-méretek helyi ideiglenes lemezzel nem 
Ez a cikk olyan Azure-beli virtuálisgép-méretekkel kapcsolatos gyakori kérdéseket (GYIK) tartalmaz, amelyek nem rendelkeznek helyi ideiglenes lemezzel (azaz nincs helyi Temp Desk). A virtuálisgép-méretekkel kapcsolatos további információkért tekintse meg a [DV4 és Dsv4-sorozat (általános célú munkaterhelések)](dv4-dsv4-series.md) , illetve [a Ev4 és a Esv4-sorozat (memória-optimalizált számítási feladatok)](ev4-esv4-series.md)specifikációit ismertető témakört.

> [!IMPORTANT]
> A DV4, a Dsv4, a Ev4 és a Esv4 sorozatú VM-méretek már nyilvános előzetes verzióban érhetők el. A nyilvános előzetes verzióra való feliratkozáshoz töltse ki ezt az [űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

## <a name="what-does-no-local-temp-disk-mean"></a>Mit jelent a helyi Temp lemez? 
Hagyományosan rendelkezünk olyan virtuálisgép-méretekkel (pl. Standard_D2s_v3, Standard_E48_v3), amelyek kis helyi lemezt tartalmaznak (például D: meghajtó). Most, hogy ezek az új virtuálisgép-méretek már nem léteznek, a kis helyi lemez már nem létezik; azonban továbbra is csatolhat standard HDD, prémium SSD vagy ultra SSD.

## <a name="what-if-i-still-want-local-temp-disk"></a>Mi a teendő, ha továbbra is szeretnék a helyi Temp lemezt?
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

> [!NOTE]
> Ha egy rendszerkép az erőforrás-lemeztől függ, vagy a helyi ideiglenes lemezen található egy lapozófájl vagy swapfile, a lemez nélküli lemezképek nem fognak működni – helyette a "lemez" alternatívát használja. 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Ezek a virtuálisgép-méretek támogatják a Linux és a Windows operációs rendszer (OS) használatát?
Igen.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Megszakítja az egyéni parancsfájlok, egyéni lemezképek vagy operációsrendszer-lemezképek, amelyek egy helyi Temp-lemezen találhatók a fájlok vagy a lapozófájlok?
Ha az egyéni operációsrendszer-lemezkép a helyi ideiglenes lemezre mutat, lehet, hogy a rendszerkép nem fog megfelelően működni a lemez nélküli mérettel.

## <a name="have-questions-or-feedback"></a>Kérdése van, vagy visszajelzést küldene?
Töltse ki a [visszajelzési űrlapot]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u). 

## <a name="next-steps"></a>További lépések 
Ebben a dokumentumban többet tudhat meg az Azure-beli virtuális gépekkel kapcsolatos leggyakoribb kérdésekről a helyi ideiglenes lemezzel. További információt ezekről a virtuálisgép-méretekről a következő cikkekben talál:

- [A DV4 és a Dsv4 sorozat specifikációi (általános célú munkaterhelés)](dv4-dsv4-series.md)
- [A Ev4 és a Esv4 sorozat specifikációi (memória-optimalizált számítási feladatok)](ev4-esv4-series.md)
