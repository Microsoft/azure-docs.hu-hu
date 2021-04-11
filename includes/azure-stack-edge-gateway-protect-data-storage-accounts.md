---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285197"
---
Az eszköze társítva van a tárfiókhoz, amelyet az Azure-ban tárolt adatok célhelyeként szolgál. A tárfiók elérését az előfizetés és két 512 bites tárelérési kulcs szabályozza, amelyek társítva vannak a tárfiókkal.

Az egyik kulcs hitelesítésre használatos, amikor az Azure Stack Edge-eszköz hozzáfér a tárfiókhoz. A másik kulcs a tartalék, így a kulcsok rendszeresen lecserélhetők.

Biztonsági okokból számos adatközpont megköveteli a kulcsváltást. Javasoljuk, hogy kövesse az alábbi ajánlott eljárásokat a Key rotációhoz:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Ügyeljen a fiókja kulcsának védelmére. Ne ossza meg a jelszót más felhasználókkal, rögzítse szoftveresen, vagy mentse el egy mások által is elérhető egyszerű szövegbe.
- Ha úgy gondolja, hogy sérült, akkor a [fiók kulcsát](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) a Azure Portal használatával újra létrehozhatja.
- Az Azure-rendszergazdának rendszeresen módosítania vagy újra kell generálnia az elsődleges vagy másodlagos kulcsot a Azure Portal Storage szakaszának használatával, hogy közvetlenül hozzáférhessen a Storage-fiókhoz.
- Saját titkosítási kulccsal is használhatja az Azure Storage-fiókban tárolt adatvédelmet. Felhasználó által kezelt kulcs megadásakor a megadott kulccsal védi és szabályozza az adatokat titkosító kulcs hozzáférését. Az adatok védelmével kapcsolatos további információkért lásd: [az ügyfél által felügyelt kulcsok engedélyezése az Azure Storage-fiókhoz](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account).
