---
title: Az Azure igazolási jogcím szabályának nyelvtana
description: Az Azure igazolási szabályzatára vonatkozó jogcímek és jogcímek részletei.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91285418"
---
# <a name="claim-and-claim-rules"></a>Jogcím-és jogcím-szabályok

A jogcím-szabályok nyelvtanának megismeréséhez először ismernie kell az igazolási házirend jogcímeit.

## <a name="claim"></a>Jogcím

A jogcím a tulajdonságok egy halmaza, amely a releváns információk megadásához van csoportosítva. Az Azure-igazoláshoz a jogcím a következő tulajdonságokat tartalmazza:

- **Type (típus**): a jogcím típusát jelképező karakterlánc-érték.
- **Value (érték**): a jogcím értékét jelképező logikai, egész szám vagy karakterlánc érték.
- **valueType**: az érték tulajdonságban tárolt adatok adattípusa. A támogatott típusok a következők: string, integer és Boolean. Ha nincs meghatározva, az alapértelmezett érték a "string" lesz.
- **kiállító**: a jogcím kiállítójának adatai. A kiállító a következő típusok egyike lesz:
  - **AttestationService**: egyes jogcímek elérhetővé válnak a szabályzat szerzője számára az Azure-igazolás alapján, amelyet az igazolási szabályzat szerzője használhat a megfelelő szabályzat kiépítéséhez.
  - **AttestationPolicy**: a szabályzat (a rendszergazda által meghatározott módon) a feldolgozás során a bejövő bizonyítékokhoz is hozzáadhat jogcímeket. Ebben az esetben a kiállító a "AttestationPolicy" értékre van állítva.
  - **CustomClaim**: az igazoló (ügyfél) további jogcímeket is hozzáadhat az igazolási igazolásokhoz. Ebben az esetben a kiállító a "CustomClaim" értékre van állítva.

Ha nincs definiálva. az alapértelmezett érték a "CustomClaim" lesz.

## <a name="claim-rule"></a>Jogcím szabálya

A házirend-kezelő a bejövő jogcímek készletét használja a tanúsítvány eredményének kiszámításához. A jogcím-szabály a bejövő jogcímek érvényesítésére és a definiált művelet végrehajtására szolgáló feltételek összessége.

```
Conditions list => Action (Claim)
```

A jogcím-szabályok Azure-igazolási kiértékelése a következő lépéseket foglalja magában:

- Ha a feltételek listája nem található, hajtsa végre a műveletet a megadott jogcímen. 
- Ellenkező esetben értékelje ki a feltételeket a feltételek listából.
- Ha a feltételek lista hamis értéket ad vissza, állítsa le. Egyéb esetben folytassa a folytatást.

A jogcím szabályának feltételei határozzák meg, hogy kell-e végrehajtani a műveletet. A feltételek lista a "&&" operátorral elválasztott feltételek sorozata.

A feltételek lista a következőképpen van strukturálva:

```
Condition && Condition && ...
```

A feltétel a következőképpen van strukturálva:

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

A feltételek lista a jogcímek különböző tulajdonságaira vonatkozó egyedi feltételekből áll. Egy feltételhez opcionális azonosító is tartozhat, amely a feltételnek megfelelő jogcímek és a-k hivatkozására használható. Ez a hivatkozás más feltételekben vagy ugyanazon szabály műveleteiben is használható.

Példa:

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

A következő operátorok használhatók a feltételek ellenőrzési feltételéhez:

| ValueType | Támogatott műveletek |
|--|--|
| Egész szám | = = (egyenlő), \! = (nem egyenlő), <= (kisebb vagy egyenlő), < (kevesebb, mint), >= (nagyobb vagy egyenlő), > (nagyobb, mint) |
| Sztring | = = (egyenlő), \! = (nem egyenlő) |
| Logikai | = = (egyenlő), \! = (nem egyenlő) |

A feltételek kiértékelése lista:

- A "&&" operátor jelenléte azt jelenti, hogy a feltételek listája igaz, csak akkor, ha a lista összes feltétele igaz értékre van kiértékelve.
- A feltétel a jogcímek készletére vonatkozó szűrési feltételeket jelöli. Magának a feltételnek a kiértékelése igaz, ha legalább egy olyan jogcím található, amely megfelel a feltételnek.
- A feltételben szereplő szűrési feltételnek meg kell felelnie egy jogcímet, ha mindegyik tulajdonsága megfelel a feltételben lévő megfelelő jogcím-tulajdonság feltételeinek.  

A szabályzatokban engedélyezett műveletek készletét alább találja.

| Műveleti művelet | Description | A szabályzat azon részei, amelyekre ezek érvényesek |
|--|--|--|
| engedélyezés () | A bejövő jogcímek készlete a **issuancerules** számításához használható. A nem igényel jogcímet paraméterként | **engedélyezési szabályok** |
| megtagadás () | A bejövő jogcímek készlete nem használható a **issuancerules** számításához paraméterként | **engedélyezési szabályok** |
| Hozzáadás (jogcím) | Hozzáadja a jogcímet a bejövő jogcímek készletéhez. A bejövő jogcímek készletéhez hozzáadott bármely jogcím elérhető lesz a következő jogcím-szabályokhoz. |**engedélyezési szabályok**, **issuancerules** |
| probléma (jogcím) | Hozzáadja a jogcímet a bejövő és a kimenő jogcímek készletéhez. | **issuancerules** |
| issueproperty (jogcím) | Hozzáadja a jogcímet a bejövő és a tulajdonság jogcímek készletéhez. | **issuancerules**

## <a name="next-steps"></a>Következő lépések

- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)

