---
title: Azure app Configuration REST API – konzisztencia
description: A valós idejű konzisztencia biztosítására szolgáló hivatkozási lapok az Azure-alkalmazás konfigurációjának használatával REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932625"
---
# <a name="real-time-consistency"></a>Valós idejű konzisztencia

Egyes elosztott rendszerek jellegéből adódóan a kérelmek közötti valós idejű konzisztencia nem bizonyulhat implicit módon. A megoldás lehetővé teszi a protokollok támogatását több szinkronizációs jogkivonat formájában. A szinkronizálási tokenek nem kötelezőek.

## <a name="initial-request"></a>Kezdeti kérelem

A különböző ügyfél-példányok és kérelmek közötti valós idejű konzisztencia biztosításához használja az opcionális `Sync-Token` kérések és válaszok fejléceit.

Szintaxis:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Paraméter|Leírás|
|--|--|
| `<id>` | Jogkivonat-azonosító (átlátszatlan) |
| `<value>` | Jogkivonat értéke (átlátszatlan). Lehetővé teszi a Base64 kódolású karakterláncok használatát. |
| `<sn>` | Jogkivonat-sorszám (verzió). A magasabb érték azt jelenti, hogy ugyanazon jogkivonat újabb verziója van. Lehetővé teszi a jobb párhuzamosságot és az ügyfél-gyorsítótárazást. Az ügyfél dönthet úgy, hogy csak a jogkivonat utolsó verzióját használja, mivel a jogkivonat-verziók tartalmazzák a szolgáltatást. Ez a paraméter nem szükséges a kérelmekhez. |

## <a name="response"></a>Reagálás

A szolgáltatás `Sync-Token` minden egyes választ tartalmaz egy fejlécet.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>További kérelmek

Minden további kérelem valós idejű konzisztens választ biztosít a megadott értékhez képest `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Ha elhagyja a `Sync-Token` fejlécet a kérelemből, akkor lehetséges, hogy a szolgáltatás a gyorsítótárazott adatokkal válaszol a rövid idő alatt (akár néhány másodpercig), mielőtt a rendszer berendezi a belsőt. Ez a viselkedés inkonzisztens olvasást okozhat, ha a módosítások azonnal megtörténnek az olvasás előtt.

## <a name="multiple-sync-tokens"></a>Több szinkronizálási token

Előfordulhat, hogy a kiszolgáló több szinkronizálási jogkivonattal válaszol egyetlen kérelemre. A következő kérelem valós idejű konzisztenciájának megőrzése érdekében az ügyfélnek az összes fogadott szinkronizálási jogkivonattal kell válaszolnia. Több fejléc értékének vesszővel elválasztva kell lennie.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
