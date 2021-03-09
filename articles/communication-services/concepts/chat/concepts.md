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
ms.openlocfilehash: 4fc94181b0f6369f288be129436d1659c85725c5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485927"
---
# <a name="chat-concepts"></a>Csevegéssel kapcsolatos alapfogalmak

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Az Azure kommunikációs szolgáltatások csevegési ügyfélszoftverei valós idejű SMS-beszélgetések hozzáadására használhatók az alkalmazásokhoz. Ez az oldal a legfontosabb csevegési fogalmakat és képességeket foglalja össze.

Tekintse meg a [kommunikációs szolgáltatások csevegési függvénytárának áttekintését](./sdk-features.md) , hogy többet tudjon meg az adott ügyféloldali függvénytár-nyelvekről és-képességekről.

## <a name="chat-overview"></a>Csevegés – áttekintés

Csevegési beszélgetések a csevegési szálakon belül történnek. Egy csevegési szál Számos üzenetet és számos felhasználót tartalmazhat. Minden üzenet egyetlen szálhoz tartozik, és egy felhasználó egy vagy több szál része lehet.

A csevegési szál minden felhasználójának neve tag. Egy csevegési szálban akár 250 taggal is rendelkezhet. Csak a szál tagjai küldhetnek és fogadhatnak üzeneteket, vagy hozzáadhatnak vagy eltávolíthatnak tagokat egy csevegési szálban. Az üzenet maximális megengedett mérete körülbelül 28KB. A csevegési szálban lévő összes üzenetet lekérheti a `List/Get Messages` művelettel. A kommunikációs szolgáltatások addig tárolja a csevegési előzményeket, amíg nem hajt végre törlési műveletet a csevegési szálon vagy az üzeneten, vagy amíg egyetlen tag sem marad a csevegési szálban, ahol az árva, és a törlésre van feldolgozva.

A több mint 20 taggal rendelkező csevegési szálak esetében a beolvasás és a begépelési mutatók funkció le van tiltva.

## <a name="chat-architecture"></a>Csevegési architektúra

Két fő részből áll a csevegő architektúra: 1) megbízható szolgáltatás és 2) ügyfélalkalmazás.

:::image type="content" source="../../media/chat-architecture.png" alt-text="A kommunikációs szolgáltatások csevegési architektúráját bemutató ábra.":::

 - **Megbízható szolgáltatás:** A csevegési munkamenetek megfelelő kezeléséhez olyan szolgáltatásra van szükség, amely segítséget nyújt a kommunikációs szolgáltatásokhoz való kapcsolódáshoz az erőforrás-kapcsolati karakterlánc használatával. Ez a szolgáltatás felelős a csevegési szálak létrehozásához, a szálak tagságának kezeléséhez, valamint hozzáférési jogkivonatok biztosításához a felhasználók számára. A hozzáférési jogkivonatokkal kapcsolatos további információk a [hozzáférési tokenek](../../quickstarts/access-tokens.md) rövid útmutatójában találhatók.

 - **Ügyfélalkalmazás:**  Az ügyfélalkalmazás csatlakozik a megbízható szolgáltatáshoz, és fogadja a közvetlenül a kommunikációs szolgáltatásokhoz való csatlakozáshoz használt hozzáférési jogkivonatokat. A kapcsolatfelvétel után az ügyfélalkalmazás küldhet és fogadhat üzeneteket.

Javasoljuk, hogy a megbízható szolgáltatási szinten hozza létre a hozzáférési jogkivonatokat. Ebben a forgatókönyvben a kiszolgálói oldal feladata a felhasználók létrehozása és kezelése, valamint a jogkivonatok kiállítása.

## <a name="message-types"></a>Üzenetek típusai

A kommunikációs szolgáltatások csevegés megosztja a felhasználó által létrehozott üzeneteket, valamint a szál- **tevékenységek** nevű rendszer által generált üzeneteket. A hozzászóláslánc-tevékenységek akkor jönnek létre, amikor egy csevegési szál frissül. `List Messages` `Get Messages` Egy csevegési szál hívásakor az eredmény a felhasználó által generált szöveges üzeneteket, valamint a rendszerüzeneteket is tartalmazza időrendben. Ez segít megállapítani, hogy mikor lett hozzáadva vagy eltávolítva egy tag, vagy mikor frissítették a csevegési szál témakört. A támogatott üzenetek típusai a következők:

 - `Text`: Egyszerű szöveges üzenet, amelyet egy felhasználó egy csevegési beszélgetés részeként komponál és küld.
 - `RichText/HTML`: Formázott szöveges üzenet. Vegye figyelembe, hogy a kommunikációs szolgáltatások felhasználói jelenleg nem küldhetnek RichText üzeneteket. Ezt az üzenetet a csapatok felhasználóitól a kommunikációs szolgáltatások felhasználóinak küldött üzenetek támogatják a csapatok együttműködési forgatókönyvekben.

 - `ThreadActivity/ParticipantAdded`: Olyan Rendszerüzenet, amely azt jelzi, hogy egy vagy több résztvevő hozzá lett adva a csevegési szálhoz. Például:

```
{
            "id": "1613589626560",
            "type": "participantAdded",
            "sequenceId": "7",
            "version": "1613589626560",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:26Z"
        }
```

- `ThreadActivity/ParticipantRemoved`: A résztvevőt jelző Rendszerüzenet el lett távolítva a csevegési szálból. Például:

```
{
            "id": "1613589627603",
            "type": "participantRemoved",
            "sequenceId": "8",
            "version": "1613589627603",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:27Z"
        }
```

- `ThreadActivity/TopicUpdate`: A szál témakörét jelző Rendszerüzenet frissült. Például:

```
{
            "id": "1613589623037",
            "type": "topicUpdated",
            "sequenceId": "2",
            "version": "1613589623037",
            "content":
            {
                "topic": "New topic",
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:23Z"
        }
```

## <a name="real-time-signaling"></a>Valós idejű jelzés

A csevegés JavaScript-ügyféloldali könyvtára valós idejű jelzéseket tartalmaz. Ez lehetővé teszi az ügyfeleknek, hogy valós idejű frissítéseket és beérkező üzeneteket hallgassanak egy csevegési szálra anélkül, hogy le kellene kérdezni az API-kat. Az elérhető események a következők:

 - `ChatMessageReceived` – új üzenet küldése egy olyan csevegési szálnak, amelynek tagja a felhasználó. Ezt az eseményt a rendszer nem továbbítja automatikusan generált rendszerüzenetek számára, amelyeket az előző témakörben ismertetünk.
 - `ChatMessageEdited` – Ha egy üzenet olyan csevegési szálban van szerkesztve, amelynek tagja a felhasználó.
 - `ChatMessageDeleted` – Ha töröl egy üzenetet egy csevegési szálban, amelynek tagja a felhasználó.
 - `TypingIndicatorReceived` – Ha egy másik tag egy olyan csevegési szálba ír be egy üzenetet, amelyben a felhasználó tagja.
 - `ReadReceiptReceived` -Ha egy másik tag elolvasta azt az üzenetet, amelyet a felhasználó elküldött egy csevegési szálban.

## <a name="chat-events"></a>Csevegési események

A valós idejű jelzések lehetővé teszik, hogy a felhasználók valós időben beszélgessenek. A szolgáltatások a Azure Event Grid segítségével előfizethetnek a csevegéssel kapcsolatos eseményekre. További részletek: Event- [kezelés fogalma](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Cognitive Services használata a csevegő ügyféloldali kódtár használatával az intelligens funkciók engedélyezéséhez

Az [Azure kognitív API](../../../cognitive-services/index.yml) -k és a csevegési ügyféloldali kódtár segítségével intelligens funkciókat adhat az alkalmazásaihoz. Megteheti például a következőt:

- Lehetővé teheti a felhasználók számára, hogy különböző nyelveken beszélgessenek egymással.
- Egy támogatási ügynök rangsorolhatja a jegyeket úgy, hogy kideríti az ügyféltől beérkező problémák negatív véleményét.
- Elemezze a beérkező üzeneteket a kulcs észleléséhez és az entitások felismeréséhez, majd az üzenet tartalma alapján kérje meg az alkalmazás felhasználójának a megfelelő adatokat.

Ezt úgy érheti el, ha a megbízható szolgáltatás egy csevegési szál tagjaként működik. Tegyük fel, hogy szeretné engedélyezni a nyelvi fordítást. Ennek a szolgáltatásnak a feladata a többi tag által cserélt üzenetek figyelése [1], a kognitív API-k meghívása a tartalom fordítására a kívánt nyelvre [2, 3], valamint a lefordított eredmény üzenetként való elküldése a csevegési szálban [4].

Így az üzenetek előzményei az eredeti és a lefordított üzeneteket is tartalmazzák. Az ügyfélalkalmazás felveheti az eredeti vagy lefordított üzenet megjelenítéséhez szükséges logikát. [Ebből](../../../cognitive-services/translator/quickstart-translator.md) a rövid útmutatóból megtudhatja, hogyan használhatja a kognitív API-kat a szövegek különböző nyelvekre való fordításához.

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="A kommunikációs szolgáltatásokkal való interakciót Cognitive Services bemutató ábra.":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a csevegéssel](../../quickstarts/chat/get-started.md)

A következő dokumentumok érdekesek lehetnek:

- Ismerkedjen meg a [csevegési ügyfél könyvtárával](sdk-features.md)
