---
title: Megbízható gyűjtemények biztonsági mentése helyileg olvasása és frissítése
description: Az Azure Service Fabric Backup Explorer szolgáltatásával olvashatja és frissítheti a helyi megbízható gyűjtemények biztonsági mentését.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: a0131960d356e4862d1379c308e240e19e76205c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048068"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Megbízható gyűjtemények biztonsági mentésének olvasása és frissítése a Backup Explorer használatával

Az Azure Service Fabric Backup Explorer segít az adatjavításban, ha Service Fabric megbízható gyűjtemények adatai sérültek. Az adatok aktuális állapotát megsérült az alkalmazásban bemutatott hibák, illetve az élő fürtön végrehajtott helytelen bejegyzések.

A Backup Explorer segítségével a következő feladatokat végezheti el:
-   A gyűjtemény metaadatait kérdezi le.
-   Megtekintheti az aktuális állapotot és annak bejegyzéseit a betöltött biztonsági mentés gyűjteményében.
-   Az utolsó ellenőrzőpont óta végrehajtott tranzakciók listázása.
-   A gyűjtemény frissítése a gyűjtemény bejegyzéseinek hozzáadásával, frissítésével vagy törlésével.
-   Készítsen friss biztonsági mentést a frissített állapot használatával.

> [!NOTE]
> Service Fabric a Backup Explorer jelenleg csak megbízható gyűjtemények megtekintését és szerkesztését támogatja a biztonsági mentésben.
>

## <a name="access-the-backup"></a>A biztonsági mentés elérése

Service Fabric Backup Explorer a következő módokon használható a megbízható gyűjtemények megtekintésére vagy frissítésére a biztonsági mentésben:
-   **Bináris**: a megbízható gyűjtemények megtekintéséhez és módosításához használjon NuGet-csomagot.
-   **Http/Rest**: használjon HTTP-alapú Rest-kiszolgálót a megbízható gyűjtemények megtekintéséhez és módosításához.
-   **bkpctl**: használja a Service Fabric Backup Explorer parancssori felületét (CLI) a megbízható gyűjtemények biztonsági mentésének megtekintéséhez és módosításához.

A Backup Explorer C# könyvtárral rendelkezik a haladó felhasználók számára. Az alkalmazásban található függvénytárat közvetlenül is használhatja a megbízható gyűjteményekkel való együttműködéshez, hasonlóan ahhoz, ahogyan az ügyfelek a meglévő állapot-nyilvántartó szolgáltatásokban működnek a State Managerrel. Az alapszintű felhasználók és az alapszintű használati esetekben a Explorer egy önálló REST-kiszolgálóval is rendelkezik, amely az API-k használatával teszi lehetővé a biztonsági másolatok vizsgálatát. A bkpctl CLI-eszköz a REST API-kkal működik, és Pythonon alapul. A CLI eszközzel elolvashatja és frissítheti a biztonsági mentéseket, valamint új biztonsági mentést készíthet a parancssorból.

További információ: [Service Fabric Backup Explorer](https://github.com/microsoft/service-fabric-backup-explorer) GitHub-tárháza. Az adattár a forrás-és kiadási információkat, valamint a beállítási utasításokat tartalmazza.

A tárházat helyileg is létrehozhatja, és biztonsági másolatokat is dolgozhat.
 
A Backup Explorer NuGet (Microsoft. ServiceFabric. ReliableCollectionBackup. Parser) a [nuget.org](https://www.nuget.org/)-on lesz elérhető. 

## <a name="next-steps"></a>Következő lépések

* Tudjon meg többet az [Azure Service Fabric állapot-nyilvántartó szolgáltatásokkal kapcsolatos megbízható gyűjteményekről](service-fabric-reliable-services-reliable-collections.md).
* Tekintse át [Service Fabric ajánlott eljárásokat](./service-fabric-best-practices-security.md).