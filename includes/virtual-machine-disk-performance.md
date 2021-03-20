---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518062"
---
Ez a cikk segít a lemezek teljesítményének és működésének tisztázásában az Azure Virtual Machines és az Azure-lemezek egyesítése során. Azt is leírja, hogyan diagnosztizálhatja a lemez i/o-sebességének szűk keresztmetszeteit, és hogy milyen módosításokat végezhet a teljesítmény optimalizálása érdekében.

## <a name="how-does-disk-performance-work"></a>Hogyan működik a lemez teljesítménye?
Az Azure-beli virtuális gépek másodpercenkénti bemeneti/kimeneti műveletei (IOPS) és az átviteli teljesítmény korlátai a virtuális gép típusa és mérete alapján. Az operációsrendszer-lemezek és adatlemezek a virtuális gépekhez csatlakoztathatók. A lemezek saját IOPS és átviteli sebességre vonatkozó korlátokkal rendelkeznek.

Az alkalmazás teljesítménye túllépi a határértéket, ha a virtuális gépek vagy a csatlakoztatott lemezek számára kiosztottnál több IOPS vagy átviteli sebességet kér. Ha a határértéket meghaladóan tapasztalja, az alkalmazás az optimálisnál rosszabb teljesítményt nyújt. Ez negatív következményekhez vezethet, például nagyobb késéssel. Néhány példát mutatunk be a koncepció tisztázására. Ahhoz, hogy ezeket a példákat könnyen követni lehessen, csak a IOPS tekintjük meg. Ugyanez a logika azonban az átviteli sebességre is vonatkozik.

## <a name="disk-io-capping"></a>Lemez i/o-korlátja

**Telepítő**

- Standard_D8s_v3
  - Nem gyorsítótárazott IOPS: 12 800
- E30 operációsrendszer-lemez
  - IOPS: 500
- Két E30 adatlemez × 2
  - IOPS: 500

![A lemez szintjének maximális korlátját bemutató ábra.](media/vm-disk-performance/disk-level-throttling.jpg)

A virtuális gépen futó alkalmazás olyan kérést tesz elérhetővé, amelyhez 10 000 IOPS szükséges a virtuális géphez. A virtuális gép mindegyike számára engedélyezett, mert az Standard_D8s_v3 virtuális gép akár 12 800 IOPS-t is végrehajthat.

Az 10 000 IOPS-kérelmek három különböző kérelemre vannak bontva a különböző lemezekre:

- 1 000 a IOPS az operációs rendszer lemezére kéri.
- 4 500 a IOPS minden adatlemezre kéri.

Az összes csatlakoztatott lemez E30, és csak a 500 IOPS képes kezelni. Így reagálnak a 500 IOPS. Az alkalmazás teljesítménye a csatlakoztatott lemezek számára korlátozott, és csak az 1 500-es IOPS dolgozható fel. Az alkalmazás a maximális teljesítmény 10 000 IOPS, ha jobb teljesítményű lemezeket használ, például prémium SSD P30 lemezeket.

## <a name="virtual-machine-io-capping"></a>Virtuális gép IO-korlátja

**Telepítő**

- Standard_D8s_v3
  - Nem gyorsítótárazott IOPS: 12 800
- P30 operációsrendszer-lemez
  - IOPS: 5 000
- Két P30 adatlemez × 2
  - IOPS: 5 000

![A virtuális gépek szintjének maximalizálását ábrázoló diagram.](media/vm-disk-performance/vm-level-throttling.jpg)

A virtuális gépen futó alkalmazás 15 000 IOPS igénylő kérelmet tesz elérhetővé. Sajnos a Standard_D8s_v3 virtuális gép csak az 12 800-es IOPS kezelésére van kiépítve. Az alkalmazást a virtuálisgép-korlátok korlátozzák, és a kiosztott 12 800 IOPS kell lefoglalni.

A kért 12 800 IOPS három különböző kérelemre vannak bontva a különböző lemezekre:

- 4 267 a IOPS az operációs rendszer lemezére kéri.
- 4 266 a IOPS minden adatlemezre kéri.

Az összes csatlakoztatott lemez olyan P30-lemez, amely képes a 5 000 IOPS kezelésére. Így reagálnak a kért összegekre.
