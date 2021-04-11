---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c8f817ad06742e6f84c3cb87dda0c36866540267
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450530"
---
Jelenleg az ultra-lemezek további korlátozásokkal rendelkeznek, ezek a következők:

A jelenleg csak az ultra-lemezek számára elérhető infrastruktúra-redundancia lehetőség a rendelkezésre állási zónák. A más redundancia-beállításokat használó virtuális gépek nem csatolhatnak Ultra-lemezt.

Az alábbi táblázat az ultra lemezek régióit ismerteti, valamint a hozzájuk tartozó rendelkezésre állási lehetőségeket:

> [!NOTE]
> Ha az alábbi listán szereplő egyik régió nem rendelkezik a lemezre alkalmas rendelkezésre állási zónákkal, akkor az adott régióban lévő virtuális gépeket infrastruktúra-redundancia nélkül kell üzembe helyezni az ultra-lemez csatlakoztatása érdekében.

|Régiók  |Redundancia-beállítások  |
|---------|---------|
|Dél-Brazília     |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|Közép-India     |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|Kelet-Ázsia     |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|Középnyugat-Németország     |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|Dél-Korea középső régiója     |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|USA északi középső régiója    |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|USA déli középső régiója    |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|USA-beli államigazgatás – Arizona     |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|USA-beli államigazgatás – Virginia     |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|USA-beli államigazgatás – Texas     |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|USA nyugati régiója     |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)        |
|Ausztrália középső régiója    |Csak egyetlen virtuális gép (a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok nem támogatottak)|
|Kelet-Ausztrália     |Három rendelkezésre állási zóna         |
|Délkelet-Ázsia    |Három rendelkezésre állási zóna        |
|Közép-Kanada     |Három rendelkezésre állási zóna          |
|Az USA középső régiója     |Három rendelkezésre állási zóna          |
|USA keleti régiója     |Három rendelkezésre állási zóna          |
|USA 2. keleti régiója     |Három rendelkezésre állási zóna         |
|Közép-Franciaország    |Két rendelkezésre állási zóna        |
|Kelet-Japán    |Három rendelkezésre állási zóna        |
|Észak-Európa    |Három rendelkezésre állási zóna        |
|Az Egyesült Királyság déli régiója    |Három rendelkezésre állási zóna        |
|Nyugat-Európa    | Három rendelkezésre állási zóna|
|USA 2. nyugati régiója    |Három rendelkezésre állási zóna|

- Csak a következő virtuálisgép-sorozatokban támogatott:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem minden virtuálisgép-méret érhető el minden olyan támogatott régióban, amely Ultra-lemezekkel rendelkezik.
- Csak adatlemezként érhetők el. 
- A 4k-os fizikai szektor méretének támogatása alapértelmezés szerint. a 512E szektor mérete általánosan elérhető ajánlatként érhető el (nincs szükség regisztrációra). A legtöbb alkalmazás kompatibilis a 4k-os szektor méretével, de egyesek 512 bájtos szektort igényelnek. Az egyik példa Oracle Database, amely a 12,2-as vagy újabb kiadást igényli a 4k natív lemezek támogatásához. A Oracle DB régebbi verzióihoz a 512 bájtos szektor mérete szükséges.
- Csak üres lemezként hozható létre.
- Jelenleg nem támogatja a lemezes pillanatképeket, a virtuálisgép-lemezképeket, a rendelkezésre állási csoportokat, az Azure dedikált gazdagépeket vagy az Azure Disk encryptiont.
- Jelenleg nem támogatja a Azure Backup-vagy Azure Site Recovery-integrációt.
- Csak a nem gyorsítótárazott olvasási és nem gyorsítótárazott írásokat támogatja.
- A GA virtuális gépek IOPS jelenlegi maximális korlátja 80 000.

Az Azure Ultra Disks szolgáltatás alapértelmezés szerint akár 32 TiB-t is kínál, de az ultra-lemezek igény szerint nagyobb kapacitást biztosítanak. A kapacitás növelésének igényléséhez kvótát kell megemelni, vagy kapcsolatba kell lépnie az Azure ügyfélszolgálatával.
