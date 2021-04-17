---
title: Oracle-adatbázis helyreállítása az Azure BareMetal-infrastruktúrán
description: Megtudhatja, hogyan állítható helyre oracle-adatbázisa az Azure BareMetal-infrastruktúrán.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: fc7464474d01314a52a77e0f28b1df160a9f42a0
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590244"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Oracle-adatbázis helyreállítása az Azure BareMetal-infrastruktúrán

Bár egyetlen technológia sem nyújt védelmet az összes hiba esetén, a funkciók kombinálásával az adatbázisgazdák szinte bármilyen helyzetben helyreállíthatják az adatbázisukat.

## <a name="causes-of-database-failure"></a>Adatbázishibák okai

Az adatbázishibák számos okból előfordulhatnak, de általában több kategóriába sorolhatók:

- Adatkezelési hibák.
- Online újrado-naplók elvesztése.
- Adatbázis-vezérlési fájlok elvesztése.
- Adatbázis-adatfájlok elvesztése.
- Fizikai adatsérülés.

## <a name="choose-your-method-of-recovery"></a>Válassza ki a helyreállítási módszert

A helyreállítás típusa a hiba típusától függ. Tegyük fel, hogy egy objektum el lett dobva, vagy az adatok helytelenül módosultak. A leggyorsabb megoldás általában egy visszacsatolási adatbázis-művelet. Más esetekben a pillanatképen keresztüli helyreállítás Azure NetApp Files biztosíthatja a kívánt helyreállítást. Az alábbi ábra döntési fája gyakori meghibásodási és helyreállítási forgatókönyveket ábrázol, ha a fent ismertetett összes adatvédelmi beállítás meg van valósítva.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="Az adatbázis-helyreállítás döntési fájának ábrája." lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

Ne feledje, hogy ez a példa döntési fa csak egy adatbázis-rendszergazda optikáját nézi meg. Az egyes üzemelő példányok különböző követelményekkel is előfordulhatnak, amelyek megváltoztathatják a választási sorrendet. Ha például egy adatbázis-szerepkört egy másik régióra vált a Data Guardon keresztül, az kedvezőtlen hatással lehet az alkalmazás teljesítményére. Alacsonyabb RTO-t adhat a pillanatkép-helyreállítási módszernek. Az RTO/RPO követelményeinek való megfelelés biztosítása érdekében javasoljuk, hogy végezze el ezeket a műveleteket, és hozzon létre dokumentált eljárásokat a végrehajtásukhoz, amikor szükséges.

## <a name="next-steps"></a>Következő lépések

További információ az BareMetal-infrastruktúráról:

- [Mi az az BareMetal Infrastructure az Azure-ban?](../../concepts-baremetal-infrastructure-overview.md)
- [BareMetal Infrastructure-példányok csatlakoztatása az Azure-ban](../../connect-baremetal-infrastructure.md)
