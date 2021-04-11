---
title: Azure HPC gyorsítótár-forgatókönyvek
description: Útmutatás arról, hogy a számítástechnikai feladatok megfelelően működnek-e az Azure HPC cache-vel
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259959"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>Megfelelő-e a feladata az Azure HPC cache számára?

Az Azure HPC cache számos különböző tudományágban képes a nagy teljesítményű számítástechnikai feladatok adataihoz való hozzáférésre. Ez azonban nem tökéletes a munkafolyamatok összes típusához. Ez a cikk útmutatást nyújt arról, hogyan döntheti el, hogy a HPC cache jó megoldás-e az igényeihez.

Az [áttekintő](hpc-cache-overview.md) cikk rövid áttekintést nyújt arról, hogy mikor érdemes használni az Azure HPC cache-t és néhány példát a használati esetekre.

Olvassa el [ezt a cikket](nfs-blob-considerations.md) is, amely bemutatja, hogyan lehet hatékonyan használni az [NFS-hez csatlakoztatott blob Storage-](../storage/blobs/network-file-system-protocol-support.md)t, amely előzetes verzióban érhető el.

## <a name="nfs-version-30-applications"></a>NFS 3,0-es verziójú alkalmazások

Az Azure HPC cache csak az NFS 3,0-ügyfeleket támogatja.

## <a name="high-read-to-write-ratio"></a>Magas olvasási és írási arány

Azok a munkaterhelések, amelyekben a számítási ügyfelek az írásnál nagyobb olvasási műveleteket végeznek, általában jó választás a gyorsítótárban. Ha például az írási és olvasási arány értéke 80/20 vagy 70/30, az Azure HPC cache a gyakran kért fájlok a gyorsítótárból való kiszolgálásával is segíthet, ahelyett, hogy le kellene kérni őket a távoli tárterületről.

Egy fájl beolvasása és a gyorsítótárban való tárolása az első alkalommal kis késleltetéssel jár egy normál ügyfél-kérelem közvetlen tárolásakor, így a hatékonyság növelése a következő alkalommal történik, amikor egy ügyfél ugyanazt a fájlt kéri. Ez különösen igaz a nagyméretű fájlokra. Ha minden ügyfél-kérelem egyedi, a HPC gyorsítótárának hatása korlátozott. Minél nagyobb a fájl, annál jobb teljesítményt érhet el az első hozzáférés után.

## <a name="file-based-analytic-workload"></a>Fájl alapú elemzési munkaterhelés

Az Azure HPC cache ideális egy olyan folyamathoz, amely fájl-alapú adatmennyiséget használ, és nagy számú számítási ügyfélen fut, különösen akkor, ha a számítási ügyfelek Azure-beli virtuális gépek. Segíthet a hosszú fájl-hozzáférési idők által okozott lassú vagy inkonzisztens teljesítmény javításában.

## <a name="remote-data-access"></a>Távoli adathozzáférés

Az Azure HPC cache segítségével csökkentheti a késést, ha a munkaterhelésnek olyan távoli adatokhoz kell hozzáférnie, amelyek nem helyezhetők közelebb a számítástechnikai erőforrásokhoz. Előfordulhat például, hogy a rekordok egy WAN-környezet, egy másik Azure-régió vagy egy ügyfél-adatközpont túlsó végén találhatók. (Ezt néha "file-burst"-nek is nevezik.)

## <a name="heavy-request-load"></a>Nagy mennyiségű kérelem betöltése

Ha nagy számú ügyfél kér le egy időben több adatforrást, az Azure HPC cache felgyorsíthatja a fájlokhoz való hozzáférést. Ha például nagy teljesítményű számítástechnikai fürttel használják, az Azure HPC cache méretezhetővé teszi a gyorsítótáron keresztüli egyidejű kérések nagy számát.

## <a name="compute-resources-are-located-in-azure"></a>A számítási erőforrások az Azure-ban találhatók

Az Azure Virtual Machines méretezhető és költséghatékony megoldást nyújt a nagy teljesítményű számítási feladatokhoz. Az Azure HPC cache segítségével a szükséges információkhoz közelebb kerülhet, különösen akkor, ha az eredeti adatokat egy távoli rendszeren tárolják.

Ha egy ügyfél az Azure Virtual Machines szolgáltatásban aktuális folyamatát szeretné futtatni, akkor az Azure HPC cache egy POSIX-alapú megosztott tárolási (vagy gyorsítótárazási) megoldást biztosít a méretezhetőséghez.

Az Azure HPC cache használatával nem kell újratervezni a munkafolyamatot, hogy natív hívásokat hajtson végre az Azure Blob Storage-ba. Az adataihoz az eredeti rendszeren férhet hozzá, vagy a HPC cache használatával helyezheti át azt egy új blob-tárolóba.

## <a name="next-steps"></a>Következő lépések

* További információ a gyorsítótár tervezéséről és konfigurálásáról az [Áttekintés](hpc-cache-overview.md) és az [Előfeltételek](hpc-cache-prerequisites.md) cikkeiben
* Olvasási megfontolások az [NFS-kompatibilis blob Storage](nfs-blob-considerations.md) (előzetes verzió) Azure HPC cache-vel való használatához
