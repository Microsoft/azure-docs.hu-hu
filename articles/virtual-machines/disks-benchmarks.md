---
title: Az alkalmazás teljesítményértékelése Azure Disk Storage
description: Ismerje meg az Azure-beli alkalmazás teljesítménytesztének folyamatát.
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094596"
---
# <a name="benchmark-a-disk"></a>Lemez teljesítménytesztelése

A teljesítményértékelés a különböző számítási feladatok szimulálása az alkalmazáson, és az egyes számítási feladatok teljesítményének mérése. A [nagy teljesítményű cikk tervezésekor](premium-storage-performance.md)leírt lépések használatával összegyűjtöttük az alkalmazás teljesítményének követelményeit. Az alkalmazást futtató virtuális gépekre vonatkozó teljesítményteszt-eszközök futtatásával meghatározhatja, hogy az alkalmazás milyen teljesítményszint mellett érhet el prémium SSD-k használatával. Ebben a cikkben példát mutatunk az Azure Premium SSD-k által kiépített Standard_D8ds_v4 virtuális gépek összehasonlítására.

A Windows és a Linux rendszerhez készült közös teljesítményteszt-eszközöket DiskSpd és FIO használjuk. Ezek az eszközök több szálat szimulálnak, mint például a számítási feladatok, és mérik a rendszer teljesítményét. Az eszközök használatával olyan paramétereket is konfigurálhat, mint például a blokk mérete és a várólista mélysége, amelyet általában nem lehet módosítani egy alkalmazás esetében. Ez nagyobb rugalmasságot biztosít, hogy a maximális teljesítményt egy prémium SSD-vel kiépített, nagy léptékű virtuális gépen, különböző típusú alkalmazás-munkaterhelések esetén lehessen vezetni. Ha többet szeretne megtudni az egyes teljesítményértékelési eszközökről, látogasson el a [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) és a [Fio](http://freecode.com/projects/fio)webhelyre.

Az alábbi példák követéséhez hozzon létre egy Standard_D8ds_v4, és csatoljon négy prémium SSD-t a virtuális géphez. A négy lemez esetében a "None" értékkel állítsa be a háromat a gazdagépek gyorsítótárazásával, majd egy NoCacheWrites nevű kötetre. Konfigurálja a gazdagép gyorsítótárazását "ReadOnly" néven a fennmaradó lemezen, és hozzon létre egy CacheReads nevű kötetet a lemezzel. Ezzel a beállítással megtekintheti egy Standard_D8ds_v4 virtuális gép maximális olvasási és írási teljesítményét. A prémium SSD-ket tartalmazó Standard_D8ds_v4 létrehozásával kapcsolatos részletes útmutatásért lásd: [a nagy teljesítmény kialakítása](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Következő lépések

Folytassa a cikket a [nagy teljesítmény kialakításához](premium-storage-performance.md).

Ebben a cikkben a prototípushoz tartozó meglévő alkalmazáshoz hasonló ellenőrzőlistát hoz létre. A teljesítményértékelési eszközök használatával szimulálhatja a számítási feladatokat, és mérhetővé teheti a teljesítményt a prototípus alkalmazásban. Ezzel meghatározhatja, hogy melyik lemez-ajánlat megfelel vagy meghaladja az alkalmazás teljesítményére vonatkozó követelményeket. Ezután megadhatja az éles alkalmazásra vonatkozó irányelveket.
