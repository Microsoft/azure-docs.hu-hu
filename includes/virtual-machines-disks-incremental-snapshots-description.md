---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39750a86ccf781a10109e299e27a55a03173acb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900961"
---
A növekményes Pillanatképek olyan felügyelt lemezek időpontos biztonsági mentései, amelyekben a rendszer csak a legutóbbi pillanatkép óta történt változásokat tartalmazza. Ha növekményes pillanatképből állít vissza lemezt, a rendszer újraépíti a teljes lemezt, amely a növekményes pillanatkép készítésekor a lemez időpontra történő biztonsági mentését jelöli. Ez az új képesség a felügyelt lemezes Pillanatképek számára potenciálisan költséghatékony lehet, mivel, ha nem választja ki, nem kell a teljes lemezt az egyes pillanatképekkel együtt tárolnia. A teljes pillanatképekhez hasonlóan a növekményes Pillanatképek is használhatók teljes felügyelt lemez vagy teljes pillanatkép létrehozásához.

A növekményes pillanatkép és a teljes pillanatkép közötti különbségek vannak. A növekményes Pillanatképek mindig a standard HDD-tárolót használják, a lemez tárolási típusától függetlenül, míg a teljes Pillanatképek a prémium SSD-ket használhatják. Ha Premium Storage teljes pillanatképeket használ a virtuálisgép-telepítések vertikális felskálázásához, javasoljuk, hogy egyéni rendszerképeket használjon a [megosztott lemezképek](../articles/virtual-machines/shared-image-galleries.md)katalógusában a standard szintű tárolóban. Ezzel a megoldással sokkal nagyobb léptékben érhet el alacsonyabb költségeket. Emellett a növekményes Pillanatképek is jobb megbízhatóságot biztosítanak a [Zone-redundáns tárolással](../articles/storage/common/storage-redundancy.md) (ZRS). Ha a ZRS elérhető a kiválasztott régióban, a növekményes pillanatkép automatikusan a ZRS-t fogja használni. Ha a ZRS nem érhető el a régióban, akkor a pillanatkép alapértelmezett értéke [helyileg redundáns tárolás](../articles/storage/common/storage-redundancy.md) (LRS). Felülbírálhatja ezt a viselkedést, és kiválaszthat egyet manuálisan, de nem ajánlott.

A növekményes Pillanatképek szintén különbözeti képességet biztosítanak, csak a felügyelt lemezek számára. Lehetővé teszik az azonos felügyelt lemezek két növekményes pillanatképének változását a blokk szintjére. Ezzel a képességgel csökkentheti az adatlábnyomot a pillanatképek régiók közötti másolásakor.  Például letöltheti az első növekményes pillanatképet egy másik régióban lévő alapblobként. A későbbi növekményes Pillanatképek esetében csak az alap blob utolsó pillanatképének változásait másolja át. A módosítások másolása után pillanatképeket készíthet az alap blobon, amely egy másik régióban lévő lemez időpontjának biztonsági mentését jelképezi. A lemezt visszaállíthatja az alap blobból, vagy egy másik régióban lévő alap blobon lévő pillanatképből is.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="A régiók között másolt növekményes pillanatképeket ábrázoló diagram. A pillanatképek különféle API-hívásokat tesznek elérhetővé, amíg az egyes Pillanatképek során a Blobok egy részét nem":::

A növekményes Pillanatképek számlázása csak a felhasznált mérethez szükséges. A pillanatképek használt méretét az [Azure használati jelentésében](../articles/cost-management-billing/understand/review-individual-bill.md)találja. Ha például egy pillanatkép által használt adatméret 10 GiB, a **napi** használati jelentés 10 GIB/(31 nap) = 0,3226-et jelenít meg a felhasznált mennyiségnek megfelelően.