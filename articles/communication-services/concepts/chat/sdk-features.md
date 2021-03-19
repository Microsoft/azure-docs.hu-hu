---
title: Csevegő ügyféloldali kódtár – áttekintés az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg az Azure kommunikációs szolgáltatások csevegési ügyfelének könyvtárát.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 705bd926c2ac6f414464254969b5c511c88891f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656107"
---
# <a name="chat-client-library-overview"></a>A csevegési ügyfélkódtár áttekintése  

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Az Azure kommunikációs szolgáltatások csevegési ügyfélszoftverei gazdag, valós idejű csevegést vehetnek igénybe az alkalmazásaihoz.
    
## <a name="chat-client-library-capabilities"></a>Csevegési ügyféloldali kódtár képességei 

A következő lista a kommunikációs szolgáltatások csevegési ügyféloldali könyvtáraiban jelenleg elérhető funkciókat mutatja be.  

| Szolgáltatások csoportja | Képesség | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Alapvető képességek | Csevegési szál létrehozása 2 vagy több felhasználó között (legfeljebb 250 felhasználó)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Csevegési szál témakörének frissítése                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Résztvevők hozzáadása vagy eltávolítása csevegési szálból                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Válassza ki, hogy meg szeretné-e osztani a csevegési előzményeket a hozzáadott résztvevővel                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Csevegési szál résztvevőinek listájának beolvasása                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Csevegési szál törlése                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Adott kommunikációs felhasználó, a csevegési szálak listájának beolvasása, amelyhez a felhasználó tartozik                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Egy adott csevegési szál adatainak beolvasása                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Üzenetek küldése és fogadása csevegési szálban                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Elküldött üzenet tartalmának szerkesztése                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Üzenet törlése                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Olvasási visszaigazolás a csevegés más résztvevőinek által olvasott üzenetekhez <br/> *Nem érhető el, ha több mint 20 résztvevő van egy csevegési szálban*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Értesítés küldése, ha a résztvevők aktívan beírják az üzenetet egy csevegési szálba <br/> *Nem érhető el, ha több mint 20 tag van egy csevegési szálban*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Csevegési szál összes üzenetének beolvasása <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Unicode-hangulatjelek küldése az üzenet tartalmának részeként                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Valós idejű jelzés (a tulajdonosi jelzési csomag * * által engedélyezett)|  Előfizetés a beérkező üzenetek és egyéb műveletek valós idejű frissítéseinek beszerzésére a csevegési alkalmazásban. A valós idejű jelzésekhez támogatott frissítések listájának megtekintéséhez lásd a [csevegési fogalmakat](concepts.md#real-time-signaling) ismertető témakört.                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Event Grid támogatás             | Integráció a Azure Event Grid és a kommunikációs szolgáltatás konfigurálása a csevegési tevékenységen alapuló üzleti logika végrehajtására vagy egy egyéni leküldéses értesítési szolgáltatás csatlakoztatására   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| Figyelés        | Az Azure Portalban kibocsátott API-kérelmek mérőszámait felhasználva irányítópultokat hozhat létre, figyelheti a csevegési alkalmazás állapotát, és riasztásokat állíthat be a rendellenességek észleléséhez      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | A kommunikációs szolgáltatások erőforrásának konfigurálása a csevegési operatív naplók fogadásához figyelési és diagnosztikai célokra          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


* * A tulajdonosi jelző csomag webes szoftvercsatornák használatával valósítható meg. Ha a webes szoftvercsatornák nem támogatottak, a rendszer visszaküldi a hosszú lekérdezést.  

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>JavaScript csevegő ügyféloldali függvénytár-támogatás az operációs rendszer és a böngésző számára 

A következő táblázat a jelenleg elérhető támogatott böngészők és verziók készletét tartalmazza.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad operációs rendszer|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Csevegési ügyféloldali kódtár** | Firefox *, Chrome*, új Edge | Firefox *, Chrome*, Safari * | Chrome  | Chrome | Chrome | Safari | Safari |

* Vegye figyelembe, hogy az előző két kiadáson felül a legújabb verzió is támogatott.<br/>   

## <a name="next-steps"></a>Következő lépések   

> [!div class="nextstepaction"] 
> [Ismerkedés a csevegéssel](../../quickstarts/chat/get-started.md)    

A következő dokumentumok érdekesek lehetnek:  
- Ismerkedjen meg a [csevegési fogalmakkal](../chat/concepts.md)