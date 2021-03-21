---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582352"
---
Az eszköze társítva van a tárfiókhoz, amelyet az Azure-ban tárolt adatok célhelyeként szolgál. A tárfiók elérését az előfizetés és két 512 bites tárelérési kulcs szabályozza, amelyek társítva vannak a tárfiókkal.

Az egyik kulcs hitelesítésre használatos, amikor az Azure Stack Edge-eszköz hozzáfér a tárfiókhoz. A másik kulcs a tartalék, így a kulcsok rendszeresen lecserélhetők.

Biztonsági okokból számos adatközpont megköveteli a kulcsváltást. Javasoljuk, hogy kövesse az alábbi ajánlott eljárásokat a Key rotációhoz:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Ügyeljen a fiókja kulcsának védelmére. Ne ossza meg a jelszót más felhasználók számára, és ne jegyezze fel, vagy mentse azt a mások számára elérhető egyszerű szövegben.
- Ha úgy gondolja, hogy sérült, akkor a fiók kulcsát a Azure Portal használatával újra létrehozhatja. További információ: a [Storage-fiók elérési kulcsainak kezelése](../articles/storage/common/storage-account-keys-manage.md).
- Az Azure-rendszergazdának rendszeresen módosítania vagy újra kell generálnia az elsődleges vagy másodlagos kulcsot a Azure Portal Storage szakaszának használatával, hogy közvetlenül hozzáférhessen a Storage-fiókhoz.