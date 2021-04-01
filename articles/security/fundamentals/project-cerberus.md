---
title: Belső vezérlőprogram integritása – Azure Security
description: A belső vezérlőprogram integritásának biztosítására szolgáló titkosítási mérések ismertetése.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557666"
---
# <a name="project-cerberus"></a>Project Cerberus

A Cerberus egy NIST 800-193-kompatibilis hardveres megbízhatósági kapcsolat, amely nem klónozásra alkalmas identitással rendelkezik. A Cerberus úgy lett kialakítva, hogy tovább növelje az Azure-infrastruktúra biztonsági állapotát azáltal, hogy erős megbízhatósági kapcsolatot biztosít a belső vezérlőprogram integritása szempontjából.

## <a name="enabling-an-anchor-of-trust"></a>Megbízhatósági kapcsolat engedélyezése
Minden Cerberus-lapka egyedi titkosítási identitással rendelkezik, amely egy Microsoft hitelesítésszolgáltató (CA) által feltört aláírt tanúsítványlánc használatával lett létrehozva. A Cerberus által beszerzett mérések az összetevők integritásának ellenőrzéséhez használhatók, például:

- Gazdagép
- Alaplapi felügyeleti vezérlő (BMC)
- Minden periféria, beleértve a hálózati adaptert és a [System-on-a-chipet](https://en.wikipedia.org/wiki/System_on_a_chip) (SoC)

Ez a megbízhatósági kapcsolat segít megvédeni a platform belső vezérlőprogramja:

- A platformon futó, feltört belső vezérlőprogram bináris fájljai
- Az operációs rendszer, az alkalmazás vagy a hypervisor hibáit kihasználó kártevők és hackerek
- Bizonyos típusú ellátási lánc elleni támadások (gyártás, szerelvény, tranzit)
- Rosszindulatú bennfentesek rendszergazdai jogosultságokkal vagy hardveres hozzáféréshez

## <a name="cerberus-attestation"></a>Cerberus-igazolás
A Cerberus a platform belső vezérlőprogram-jegyzékfájljának (PFM) használatával hitelesíti a kiszolgáló-összetevők belső vezérlőprogram-integritását. A PFM meghatározza a belső vezérlőprogram-verziók listáját, és platform-mérést biztosít az Azure [Host igazolási szolgáltatásához](measured-boot-host-attestation.md). A gazdagép-igazolási szolgáltatás ellenőrzi a méréseket, és meghatározza, hogy csak a megbízható gazdagépek csatlakozhassanak az Azure-flottához és az ügyfél-munkaterhelésekhez.

A gazda-igazolási szolgáltatással együtt a Cerberus képességei javítják és támogatják az Azure-beli biztonságos üzemi infrastruktúrát.

> [!NOTE]
> További tudnivalókért tekintse meg a [projekt Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) információit a githubon.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a platform integritásának és biztonságának elvégzéséről, olvassa el a következőt:

- [A belső vezérlőprogram biztonsága](firmware.md)
- [Biztonságos rendszerindítás](secure-boot.md)
- [Mért rendszerindítás és gazdagép-igazolás](measured-boot-host-attestation.md)
- [Titkosítás inaktív állapotban](encryption-atrest.md)
- [A hipervizor biztonsága](hypervisor.md)