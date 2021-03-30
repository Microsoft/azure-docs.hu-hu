---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "82562124"
---
Az eszköze társítva van a tárfiókhoz, amelyet az Azure-ban tárolt adatok célhelyeként szolgál. A tárfiók elérését az előfizetés és két 512 bites tárelérési kulcs szabályozza, amelyek társítva vannak a tárfiókkal.

Az egyik kulcs hitelesítésre használatos, amikor az Azure Stack Edge-eszköz hozzáfér a tárfiókhoz. A másik kulcs a tartalék, így a kulcsok rendszeresen lecserélhetők.

Biztonsági okokból számos adatközpont megköveteli a kulcsváltást. Javasoljuk, hogy kövesse az alábbi ajánlott eljárásokat a Key rotációhoz:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Ügyeljen a fiókja kulcsának védelmére. Ne ossza meg a jelszót más felhasználókkal, rögzítse szoftveresen, vagy mentse el egy mások által is elérhető egyszerű szövegbe.
- Ha úgy gondolja, hogy sérült, akkor a fiók kulcsát a Azure Portal használatával újra létrehozhatja. További információ: a [Storage-fiók elérési kulcsainak kezelése](../articles/storage/common/storage-account-keys-manage.md).
- Az Azure-rendszergazdának rendszeresen módosítania vagy újra kell generálnia az elsődleges vagy másodlagos kulcsot a Azure Portal Storage szakaszának használatával, hogy közvetlenül hozzáférhessen a Storage-fiókhoz.