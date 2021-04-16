---
title: Csevegési SDK áttekintése Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: További információ a Azure Communication Services SDK-val kapcsolatban.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500685"
---
# <a name="chat-sdk-overview"></a>Csevegési SDK – áttekintés 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Azure Communication Services csevegési SDK-k segítségével gazdag, valós idejű csevegést adhat az alkalmazásokhoz.
    
## <a name="chat-sdk-capabilities"></a>Csevegési SDK-képességek    

Az alábbi lista azokat a funkciókat mutatja be, amelyek jelenleg elérhetők a Communication Services-SDK-kban.  

| Funkciók csoportja | Képesség | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Alapvető képességek | Csevegési szál létrehozása 2 vagy több felhasználó között                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Csevegési szál témakörének frissítése                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Résztvevők hozzáadása vagy eltávolítása egy csevegési szálból                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Annak kiválasztása, hogy megossa-e a csevegési üzenetek előzményeit a hozzáadott résztvevővel                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Egy csevegés résztvevőinek listájának lekért listája                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Csevegés törlése                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Egy kommunikációs felhasználónak meg kell kapnia azon csevegési szálak listáját, amelyekhez a felhasználó a tagja                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Egy adott csevegési szálra vonatkozó információk lekérte                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Üzenetek küldése és fogadása egy csevegésben                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Az elküldött üzenet tartalmának frissítése                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Korábban elküldött üzenet törlése                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | A csevegés más résztvevői által olvasott üzenetek nyugtáinak olvasása                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Értesítést kap, ha a résztvevők aktívan begépelnek egy üzenetet egy csevegőszálba                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Az összes üzenet lekérte egy csevegési szálon                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Unicode-hangulatjelek küldése az üzenettartalom részeként                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Valós idejű értesítések (saját jelcsomag által engedélyezve**)|  A csevegési ügyfelek feliratkozva valós idejű frissítéseket kaphatnak a bejövő üzenetekhez és a csevegési szálon keresztüli egyéb műveletekhez. A valós idejű értesítések támogatott frissítésének listájáért lásd: [Csevegési fogalmak](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Integráció a Azure Event Grid             | A Azure Event Grid elérhető csevegési eseményekkel egyéni értesítési szolgáltatásokat csatlakoztat, vagy az eseményt egy webhookon közzéteve üzleti logikát hajt végre, például a CRM-rekordok frissítését egy csevegés befejezése után   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Jelentéskészítés </br>(Ez az információ a Figyelés lapon érhető el a Communication Services erőforráshoz a Azure Portal)      | A csevegőalkalmazásból származó API-forgalommal kapcsolatos további adatok az Azure Metrikaböngésző közzétett metrikák monitorozása és riasztások beállítása a rendellenességek észleléséhez     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Az erőforrás diagnosztikai naplózásának engedélyezésével figyelheti és Communication Services megoldás hibakeresését    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


**A saját jelátviteli csomag webes szoftvercsatornák használatával van megvalósítva. Ha a webes szoftvercsatornák nem támogatottak, a rendszer visszaveszi a hosszú lekérdezéseket.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>JavaScript chat SDK-támogatás operációs rendszer és böngésző szerint    

Az alábbi táblázat a jelenleg elérhető támogatott böngészőket és verziókat tartalmazza.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad operációs rendszer|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Csevegési SDK** | Firefox,*Chrome,* új Edge | Firefox,*Chrome,* Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Vegye figyelembe, hogy az előző két kiadás mellett a legújabb verzió is támogatott.<br/>   

## <a name="next-steps"></a>Következő lépések   

> [!div class="nextstepaction"] 
> [Csevegés – első lépések](../../quickstarts/chat/get-started.md)    

A következő dokumentumok érdekesek lehetnek az Ön számára:  
- Ismerkedjen meg a [csevegési fogalmakkal](../chat/concepts.md)
- A csevegés [díjszabásának](../pricing.md#chat) működése
