---
title: Csevegési fogalmak az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg a kommunikációs szolgáltatások csevegési fogalmait.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 389d2282812406c50cddf255be2219fa203b0895
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729660"
---
# <a name="chat-concepts"></a>Csevegéssel kapcsolatos alapfogalmak 

Az Azure kommunikációs szolgáltatások csevegési SDK-k használatával valós idejű szöveges csevegést adhat hozzá az alkalmazásaihoz. Ez az oldal a legfontosabb csevegési fogalmakat és képességeket foglalja össze.    

További információ az adott SDK-nyelvekről és-képességekről: a [kommunikációs szolgáltatások csevegési SDK áttekintése](./sdk-features.md) .  

## <a name="chat-overview"></a>Csevegés – áttekintés    

Csevegési beszélgetések a csevegési **szálakon** belül történnek. A csevegési szálak a következő tulajdonságokkal rendelkeznek:

- A csevegési szálat egyedileg azonosítják `ChatThreadId` . 
- A csevegési szálak egy vagy több felhasználóval rendelkezhetnek, akik küldhetnek üzeneteket. 
- Egy felhasználó egy vagy több csevegési szál része lehet. 
- Csak a szál résztvevői férhetnek hozzá egy adott csevegési szálhoz, és csak a csevegési szálon végezheti el a műveleteket. Ezek a műveletek magukban foglalják az üzenetek küldését és fogadását, a résztvevők hozzáadását és a résztvevők eltávolítását. 
- A felhasználókat a rendszer automatikusan felveszi a résztvevőként a létrehozott csevegési Szálakba.

### <a name="user-access"></a>Felhasználói hozzáférés
Általában a szál létrehozója és résztvevői ugyanolyan szintű hozzáféréssel rendelkeznek a szálhoz, és az SDK-ban elérhető összes kapcsolódó műveletet végrehajtják, beleértve a törlést is. A résztvevők nem rendelkeznek írási hozzáféréssel a többi résztvevő által küldött üzenetekhez, ami azt jelenti, hogy csak az üzenet küldője frissítheti vagy törölheti az elküldött üzeneteket. Ha egy másik résztvevő megpróbál erre, hibaüzenetet kap. 

Ha korlátozni szeretné a csevegési funkciókhoz való hozzáférést egy adott felhasználó számára, a hozzáférést a megbízható szolgáltatás részeként is konfigurálhatja. A megbízható szolgáltatás az a szolgáltatás, amely összehangolja a csevegés résztvevőinek hitelesítését és engedélyezését. Ezt részletesebben is megvizsgáljuk.  

### <a name="chat-data"></a>Csevegési adatgyűjtés 
A kommunikációs szolgáltatások addig tárolja a csevegési előzményeket, amíg explicit módon nem törli őket. A csevegési szál résztvevői `ListMessages` megtekinthetik egy adott szál üzeneteinek előzményeit. A csevegési szálból eltávolított felhasználók megtekinthetik az előzmények korábbi előzményeit, de nem tudnak majd új üzeneteket küldeni vagy fogadni a csevegési szál részeként. 30 nap elteltével a rendszer automatikusan törli a résztvevőket nem tartalmazó teljes mértékben tétlen szálat. Ha többet szeretne megtudni a kommunikációs szolgáltatások által tárolt adatokról, tekintse meg az [Adatvédelem](../privacy.md)dokumentációját.  

### <a name="service-limits"></a>Szolgáltatási korlátozások  
- A csevegési szálban engedélyezett résztvevők maximális száma 250.   
- Az üzenet maximális megengedett mérete körülbelül 28 KB.  
- A több mint 20 résztvevővel rendelkező csevegési szálak esetén az olvasási visszaigazolások és a beírási mutatók funkciói nem támogatottak.    

## <a name="chat-architecture"></a>Csevegési architektúra    

Két fő részből áll a csevegő architektúra: 1) megbízható szolgáltatás és 2) ügyfélalkalmazás.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="A kommunikációs szolgáltatások csevegési architektúráját bemutató ábra."::: 

 - **Megbízható szolgáltatás:** A csevegési munkamenetek megfelelő kezeléséhez olyan szolgáltatásra van szükség, amely segítséget nyújt a kommunikációs szolgáltatásokhoz való kapcsolódáshoz az erőforrás-kapcsolati karakterlánc használatával. Ez a szolgáltatás a csevegési szálak létrehozásához, a résztvevők hozzáadásához és eltávolításához, valamint a hozzáférési tokenek felhasználókhoz való kiállításához felelős. A hozzáférési jogkivonatokkal kapcsolatos további információk a [hozzáférési tokenek](../../quickstarts/access-tokens.md) rövid útmutatójában találhatók.  
 - **Ügyfélalkalmazás:**  Az ügyfélalkalmazás csatlakozik a megbízható szolgáltatáshoz, és fogadja a felhasználók által a kommunikációs szolgáltatásokhoz való közvetlen csatlakozáshoz használt hozzáférési jogkivonatokat. Miután a megbízható szolgáltatás létrehozta a csevegési szálat, és hozzáadta a felhasználókat a résztvevőkhöz, az ügyfélalkalmazás használatával csatlakozhat a csevegési szálhoz, és üzeneteket küldhet. Használja a valós idejű értesítések funkciót, amelyet az alábbiakban ismertetünk, hogy az ügyfélalkalmazás előfizessen az üzenetekre & a más résztvevőktől származó hozzászóláslánc-frissítéseket.
    
        
## <a name="message-types"></a>Üzenetek típusai    

Az üzenetek előzményeinek részeként a csevegés megosztja a felhasználó által létrehozott üzeneteket, valamint a rendszer által létrehozott üzeneteket. A rendszerüzenetek akkor jönnek létre, amikor egy csevegési szál frissül, és segít azonosítani a résztvevő hozzáadását vagy eltávolítását, vagy ha a csevegési szál témakörét frissítették. `List Messages` `Get Messages` Egy csevegési szál hívásakor az eredmény mindkét típusú üzenetet fogja tartalmazni időrendi sorrendben.

A felhasználó által generált üzenetek esetében az üzenet típusa beállítható az `SendMessageOptions` üzenet csevegési szálba küldéséhez. Ha nincs megadva érték, a kommunikációs szolgáltatások alapértelmezés szerint a `text` következőt fogják beírni:. Az érték beállítása a HTML küldésekor fontos. Ha meg `html` van adva, a kommunikációs szolgáltatások megtisztítják a tartalmat, így biztosítva a biztonságos megjelenítését az ügyféleszközök számára.
 - `text`: Egyszerű szöveges üzenet, amely egy felhasználó által egy csevegési szál részeként van elküldve. 
 - `html`: Formázott üzenet, amely HTML-t használ, és egy felhasználó a csevegési szál részeként jeleníti meg és küldi el. 

Rendszerüzenetek típusai: 
 - `participantAdded`: Az a Rendszerüzenet, amely azt jelzi, hogy egy vagy több résztvevő hozzá lett adva a csevegési szálhoz.
 - `participantRemoved`: A résztvevőt jelző Rendszerüzenet el lett távolítva a csevegési szálból.
 - `topicUpdated`: A szál témakörét jelző Rendszerüzenet frissült.

## <a name="real-time-notifications"></a>Valós idejű értesítések  

Bizonyos SDK-k (például a JavaScript csevegő SDK) támogatják a valós idejű értesítéseket. Ez a funkció lehetővé teszi, hogy az ügyfelek a kommunikációs szolgáltatásokat a valós idejű frissítések és a bejövő üzenetek beszélgetési szálra hallgassák anélkül, hogy le kellene kérdezni az API-kat. Az ügyfélalkalmazás a következő eseményekre tud előfizetni:
 - `chatMessageReceived` – Ha egy résztvevő egy új üzenetet küld egy csevegési szálnak.
 - `chatMessageEdited` – Ha egy üzenet egy csevegési szálban van szerkesztve. 
 - `chatMessageDeleted` – Ha üzenetet törölnek egy csevegési szálban.   
 - `typingIndicatorReceived` – Ha egy másik résztvevő begépelési kijelzőt küld a csevegési szálnak.    
 - `readReceiptReceived` – Ha egy másik résztvevő olvasási visszaigazolást küld az elolvasott üzenetről.  
 - `chatThreadCreated` – Ha egy kommunikációs szolgáltatás felhasználója egy csevegési szálat hoz létre.    
 - `chatThreadDeleted` – Ha egy kommunikációs szolgáltatást használó felhasználó törölte a csevegési szálat.    
 - `chatThreadPropertiesUpdated` -Ha a csevegési szál tulajdonságai frissülnek; jelenleg csak a szálra vonatkozó témakör frissítése támogatott. 
 - `participantsAdded` – Ha a felhasználó csevegési szál résztvevőként van hozzáadva.     
 - `participantsRemoved` – Ha egy meglévő résztvevőt eltávolítanak a csevegési szálból.

A valós idejű értesítések használatával valós idejű csevegést biztosíthat a felhasználóknak. Ha leküldéses értesítéseket szeretne küldeni a felhasználók által kihagyott üzenetekről, a kommunikációs szolgáltatások integrálva vannak Azure Event Gridekkel a csevegéssel kapcsolatos események (post művelet) közzétételéhez, amelyet az egyéni alkalmazás-értesítési szolgáltatáshoz lehet csatlakoztatni. További részletekért tekintse meg a [kiszolgálói események](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)című témakört.


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Intelligens, AI-alapú csevegési élmények készítése   

Az [Azure kognitív API](../../../cognitive-services/index.yml) -k segítségével a csevegési SDK-val olyan használati eseteket hozhat létre, mint például:

- Lehetővé teheti a felhasználók számára, hogy különböző nyelveken beszélgessenek egymással.  
- Egy támogatási ügynök rangsorolhatja a jegyeket úgy, hogy kideríti az ügyféltől érkező bejövő üzenetek negatív véleményét. 
- Elemezze a beérkező üzeneteket a kulcs észleléséhez és az entitások felismeréséhez, majd az üzenet tartalma alapján kérje meg az alkalmazás felhasználójának a megfelelő adatokat.

Ezt úgy érheti el, ha a megbízható szolgáltatás egy csevegési szál résztvevője. Tegyük fel, hogy szeretné engedélyezni a nyelvi fordítást. Ennek a szolgáltatásnak a feladata a többi résztvevő által cserélt üzenetek figyelése [1], a kognitív API-k meghívása a tartalom fordítására a kívánt nyelvre [2, 3], és a lefordított eredmény küldése üzenetként a csevegési szálban [4].

Így az üzenetek előzményei az eredeti és a lefordított üzeneteket is tartalmazzák. Az ügyfélalkalmazás felveheti az eredeti vagy lefordított üzenet megjelenítéséhez szükséges logikát. [Ebből](../../../cognitive-services/translator/quickstart-translator.md) a rövid útmutatóból megtudhatja, hogyan használhatja a kognitív API-kat a szövegek különböző nyelvekre való fordításához. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="A kommunikációs szolgáltatásokkal való interakciót Cognitive Services bemutató ábra."::: 

## <a name="next-steps"></a>Következő lépések   

> [!div class="nextstepaction"] 
> [Ismerkedés a csevegéssel](../../quickstarts/chat/get-started.md)    

A következő dokumentumok érdekesek lehetnek:  
- Ismerkedjen meg a [csevegési SDK](sdk-features.md) -val
