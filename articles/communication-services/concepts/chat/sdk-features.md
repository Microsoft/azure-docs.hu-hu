---
title: A csevegő SDK áttekintése az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg az Azure kommunikációs szolgáltatások csevegési SDK-t.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 1d40d90f0f64328bbf7103015f52ea4b132d2e51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729626"
---
# <a name="chat-sdk-overview"></a>A csevegési SDK áttekintése 

Az Azure kommunikációs szolgáltatások csevegési SDK-k használatával gazdag, valós idejű csevegést adhat hozzá az alkalmazásaihoz.
    
## <a name="chat-sdk-capabilities"></a>Csevegési SDK-képességek    

A következő lista a kommunikációs szolgáltatások csevegési SDK-ban jelenleg elérhető funkciók készletét mutatja be.  

| Szolgáltatások csoportja | Képesség | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Alapvető képességek | Csevegési szál létrehozása 2 vagy több felhasználó között                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Csevegési szál témakörének frissítése                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Résztvevők hozzáadása vagy eltávolítása csevegési szálból                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Válassza ki, hogy meg szeretné-e osztani a csevegési előzményeket a hozzáadott résztvevővel                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Csevegési szál résztvevőinek listájának beolvasása                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Csevegési szál törlése                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Adott kommunikációs felhasználó, a csevegési szálak listájának beolvasása, amelyhez a felhasználó tartozik                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Egy adott csevegési szál adatainak beolvasása                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Üzenetek küldése és fogadása csevegési szálban                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Az elküldött üzenet tartalmának frissítése                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Korábban elküldött üzenet törlése                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Olvasási visszaigazolás a csevegés más résztvevőinek által olvasott üzenetekhez                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Értesítés küldése, ha a résztvevők aktívan beírják az üzenetet egy csevegési szálba                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Csevegési szál összes üzenetének beolvasása                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Unicode-hangulatjelek küldése az üzenet tartalmának részeként                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Valós idejű értesítések (a tulajdonosi jelzési csomaggal * *)|  A csevegési ügyfelek előfizethetnek a beérkező üzenetek és a csevegési szálban előforduló egyéb műveletek valós idejű frissítéseinek beszerzésére. A valós idejű értesítések támogatott frissítéseinek listáját itt tekintheti meg: [csevegési fogalmak](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  | 
| Integráció a Azure Event Grid             | Az Azure Event Gridban elérhető csevegési események használatával csatlakoztathatja az egyéni értesítési szolgáltatásokat, vagy közzéteheti az eseményt egy webhookba, hogy olyan üzleti logikát hajtson végre, mint a CRM-rekordok frissítése a csevegés befejeződése után.   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Jelentéskészítés </br>(Ez az információ a Azure Portal-on lévő kommunikációs szolgáltatások erőforrásának figyelés lapján érhető el)      | A csevegési alkalmazás API-forgalmának megismeréséhez tekintse meg az Azure Metrikaböngésző közzétett mérőszámait, és állítsa be a riasztásokat a rendellenességek észleléséhez     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | A kommunikációs szolgáltatások megoldásának monitorozása és hibakeresése az erőforrás diagnosztikai naplózásának engedélyezésével    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


* * A tulajdonosi jelző csomag webes szoftvercsatornák használatával valósítható meg. Ha a webes szoftvercsatornák nem támogatottak, a rendszer visszaküldi a hosszú lekérdezést.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>A JavaScript csevegési SDK támogatása az operációs rendszer és a böngésző használatával    

A következő táblázat a jelenleg elérhető támogatott böngészők és verziók készletét tartalmazza.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad operációs rendszer|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Csevegési SDK** | Firefox *, Chrome*, új Edge | Firefox *, Chrome*, Safari * | Chrome  | Chrome | Chrome | Safari | Safari |

* Vegye figyelembe, hogy az előző két kiadáson felül a legújabb verzió is támogatott.<br/>   

## <a name="next-steps"></a>Következő lépések   

> [!div class="nextstepaction"] 
> [Ismerkedés a csevegéssel](../../quickstarts/chat/get-started.md)    

A következő dokumentumok érdekesek lehetnek:  
- Ismerkedjen meg a [csevegési fogalmakkal](../chat/concepts.md)
- Ismerje meg, hogyan működik a [díjszabás](../pricing.md#chat) a csevegéshez
