---
title: Red Hat Enterprise Linux – teljes életciklusra kiterjesztett támogatás
description: További információ a Red Hat Enterprise Extended Lifecycle támogatásának hozzáadásáról
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 703732725ae7215d3ff59ad92a4c171a86251c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677190"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) bővített életciklus-támogatás
Ez a cikk a Red Hat Enterprise-lemezképek kiterjesztett életciklus-támogatásával kapcsolatos információkat tartalmaz:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 életciklus
A 2020. november 30-án kezdődően a Red Hat Enterprise Linux 6 a karbantartási fázis végére ér. A karbantartási fázist a hosszabb élettartam fázisa követi. Mivel Red Hat Enterprise Linux 6 átmenet a teljes/karbantartási fázisokból, erősen ajánlott Red Hat Enterprise Linux 7 vagy 8 verzióra frissíteni. Ha az ügyfeleknek a Red Hat Enterprise Linux 6 címen kell maradniük, javasoljuk, hogy adja hozzá a Red Hat Enterprise Linux Extended életciklus-támogatás (ELS) bővítményt.

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>A kiterjesztett életciklus támogatásának lépései a piactéren elvégezhető utólagos elszámolású virtuális gépeken
1. Töltse ki az [itt elérhető els űrlapot](https://aka.ms/els-form) a kapcsolattartási adataival és azon virtuális gépek előfizetési adataival, amelyekhez az els támogatást szeretné hozzáadni. A Hozzáadás a díjszabásról részletek az űrlapon is elérhetők.
1. Az Azure Red Hat Enterprise Linux csapatával az ELS támogatású virtuális gépek listája 1-2 munkanapon belül elérhetővé válik. Tekintse át a listát, és válaszoljon a Hozzáadás a díjszabáshoz című megjegyzésre.
1. Az Azure Red Hat Enterprise Linux csapata megosztja azokat a lépéseket, amelyekkel hozzáadja az ELS-ügyfélszoftvert a virtuális gépekhez. Kövesse az e-mailben szereplő lépéseket, hogy továbbra is megkapja a szoftveres karbantartást (a hibák és a biztonsági javítások), valamint a Red Hat Enterprise Linux 6 támogatását.

> [!Note]
> Ne ossza meg azokat a lépéseket, amelyek segítségével a RHEL ELS Add-t használhatja a szervezeten kívüli személyekkel. AzureRedHatELS@microsoft.comKérjen támogatást vagy további kérdéseket.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Red Hat Enterprise Linux 6 rendszert futtatok, és jelenleg nem lehet áttérni egy újabb verzióra. Milyen lehetőségek vannak?
* Folytassa a Red Hat Enterprise Linux 6 futtatásával, és vásárolja meg a kiterjesztett életciklus-támogatás (ELS) Add-On adattárakat, hogy továbbra is korlátozott szoftveres karbantartási és technikai támogatást kapjon (lásd az alábbi lépéseket: a frissítés és a díjszabás részletei).
* A lehető leghamarabb telepítse át Red Hat Enterprise Linux 7 vagy 8 rendszerre.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Mi a kiegészítő díj a Red Hat Enterprise Linux kiterjesztett életciklus-támogatás (ELS) bővítmény használatához?
A kiterjesztett életciklus-támogatással kapcsolatos költségek az [els űrlap](https://aka.ms/els-form) használatával találhatók

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>Egyéni rendszerkép használatával telepítettem a virtuális gépet. Hogyan adhatok hozzá bővített életciklus-támogatást ehhez a virtuális géphez?
Közvetlenül a Red Hat-hoz kell fordulnia, és közvetlenül is kaphat támogatást.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>Egyéni rendszerkép használatával telepítettem a virtuális gépet. Át lehet alakítani a virtuális gépet egy TB virtuális gépre?
Nem. Az átalakítás jelenleg nem támogatott az Azure-ban.


## <a name="next-steps"></a>Következő lépések

* Az Azure-ban elérhető RHEL-lemezképek teljes listájának megtekintéséhez lásd: [Red Hat Enterprise Linux (RHEL) lemezképek az Azure-ban](./redhat-imagelist.md).
* További információ az Azure Red Hat frissítési infrastruktúráról: [Red Hat frissítési infrastruktúra az igény szerinti RHEL virtuális gépekhez az Azure-ban](./redhat-rhui.md).
* Ha többet szeretne megtudni a RHEL BYOS ajánlatáról, tekintse meg az Azure-beli [saját előfizetéssel rendelkező Gold-lemezképek Red Hat Enterprise Linux](./byos.md).
* További információ a RHEL összes verziójának Red Hat-támogatási szabályzatáról: [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata).