---
title: Az Azure Blockchain szolgáltatás korlátai
description: A szolgáltatás és a működési korlátok áttekintése az Azure Blockchain szolgáltatásban
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80676519"
---
# <a name="limits-in-azure-blockchain-service"></a>Korlátok az Azure Blockchain szolgáltatásban

Az Azure Blockchain szolgáltatás rendelkezik olyan szolgáltatás-és működési korlátokkal, mint például egy tag csomópontjainak száma, a konzorcium korlátozásai és a tárterület mennyisége.

## <a name="pricing-tier"></a>Tarifacsomag

A tranzakciók és az érvényesítő csomópontok maximális korlátai attól függnek, hogy az Azure Blockchain szolgáltatást alapszintű vagy standard szintű díjszabással kívánja-e kiépíteni.

| Tarifacsomag | Tranzakciós csomópontok maximális száma | Érvényesítő csomópontok maximális száma |
|:---|:---:|:---:|
| Alapszintű | 10 | 1 |
| Standard | 10 | 2 |

A konzorcium hálózatának legalább két Azure Blockchain Service standard szintű csomóponttal kell rendelkeznie. A standard szintű csomópontok két érvényesítő csomópontot tartalmaznak. Négy érvényesítő csomópontra van szükség az [isztambuli bizánci hibatűrési konszenzus](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)teljesítéséhez.

Az alapszintű csomag a fogalmak fejlesztéséhez, teszteléséhez és bizonyításához használható. Használja a standard szintű üzemi szintű üzembe helyezést. A *standard* szintet is érdemes használni, ha Blockchain Data Manager használ, vagy nagy mennyiségű privát tranzakciót küld.

Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

## <a name="storage-capacity"></a>Tárkapacitás

A Főkönyv és a naplók számára a csomópontok által használható maximális tárterület 1,8 terabájt.

A Főkönyv és a napló tárolási méretének csökkentése nem támogatott.
## <a name="consortium-limits"></a>Konzorcium korlátai

* A **konzorcium és a tagok nevének egyedinek kell lennie** az Azure Blockchain szolgáltatásban található többi konzorciumtól és tag nevétől.

* **A tag és a konzorcium neve nem módosítható**

* **A konzorcium összes tagjának ugyanabban az árképzési szinten kell lennie**

* **A konzorciumban részt vevő összes tagnak ugyanabban a régióban kell lennie**

    A konzorciumban létrehozott első tag diktálja a régiót. A konzorciumnak meghívott tagoknak ugyanabban a régióban kell lenniük, mint az első taggal. Ha egyetlen régióra korlátozza az összes tagot, azzal biztosítható, hogy a hálózati konszenzus ne legyen negatív hatással.

* **A konzorciumnak rendelkeznie kell legalább egy rendszergazdával**

    Ha a konzorciumban csak egy rendszergazda található, akkor nem távolíthatja el magukat a konzorciumból, vagy törölhetik a tagjaikat, amíg egy másik rendszergazdát nem ad hozzá vagy nem előléptet a konzorciumban.

* **A konzorciumból eltávolított tagok nem vehetők fel újra**

    Ehelyett újra kell hívni őket a konzorciumhoz való csatlakozáshoz és egy új tag létrehozásához. A korábbi tranzakciók megőrzése érdekében a meglévő tagok erőforrásai nem törlődnek.

* **A konzorcium összes tagjának ugyanazt a Főkönyv-verziót kell használnia.**

    Az Azure Blockchain szolgáltatásban elérhető javításokkal, frissítésekkel és Főkönyv verziókkal kapcsolatos további információkért lásd: [javítások, frissítések és verziók](ledger-versions.md).

## <a name="performance"></a>Teljesítmény

Ne használja az *ETH. költségbecslés* Gas függvényt az egyes tranzakciók beküldéséhez. Az *ETH. becslési* függvény a memória intenzív. A függvény többszöri meghívása jelentősen csökkenti a tranzakciók másodpercenkénti számát.

Ha lehetséges, használjon egy konzervatív gáz értéket a tranzakciók elküldéséhez, és csökkentse az *ETH. becslés* használatát.

## <a name="next-steps"></a>Következő lépések

További információ a rendszerek javításával és frissítésével kapcsolatos házirendekről – [javítások, frissítések és verziók](ledger-versions.md).
