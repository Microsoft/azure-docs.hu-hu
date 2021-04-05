---
title: Parancs frissítése ügyfélalkalmazás alapján
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan frissítheti a parancsokat egy ügyfélalkalmazás használatával.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560287"
---
# <a name="update-a-command-from-a-client-app"></a>Parancs frissítése ügyfélalkalmazás alapján

Ebből a cikkből megtudhatja, hogyan frissítheti az ügyfélalkalmazások egy folyamatban lévő parancsát.

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * Egy korábban [létrehozott Custom Commands-alkalmazás](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Parancs állapotának frissítése

Ha az ügyfélalkalmazás megköveteli, hogy egy folyamatban lévő parancs állapotát a hangbemenet nélkül frissítse, küldhet egy eseményt a parancs frissítéséhez.

A forgatókönyv szemléltetéséhez küldje el a következő esemény-tevékenységet egy folyamatban lévő parancs () állapotának frissítéséhez `TurnOnOff` : 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Tekintsük át a tevékenység főbb attribútumait:

| Attribútum | Magyarázat |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **típusa** | A tevékenység típusa `"event"` , és az esemény nevének kell lennie `"RemoteUpdate"` . |
| **value** | Az attribútum az `"value"` aktuális parancs frissítéséhez szükséges attribútumokat tartalmazza. |
| **updatedCommand** | Az attribútum a `"updatedCommand"` parancs nevét tartalmazza. Az attribútumon belül `"updatedParameters"` egy Térkép a paraméterek nevével és a frissített értékekkel. |
| **Mégse** | Ha a folyamatban lévő parancsot meg kell szüntetni, állítsa az attribútumot a következőre: `"cancel"` `true` . |
| **updatedGlobalParameters** | Az attribútum `"updatedGlobalParameters"` egy Térkép, hasonlóan a `"updatedParameters"` globális paraméterekhez. |
| **processTurn** | Ha a turnt fel kell dolgozni a tevékenység elküldése után, állítsa az attribútumot a következőre: `"processTurn"` `true` . |

Ezt a forgatókönyvet az egyéni parancsok portálon ellenőrizheti:

1. Nyissa meg a korábban létrehozott egyéni parancsok alkalmazást. 
1. Válassza ki a **betanítás** lehetőséget, majd **tesztelje**.
1. Küldés `turn` .
1. Nyissa meg az oldalsó panelt, és válassza a **tevékenység-szerkesztő** elemet.
1. Írja be és küldje `RemoteCommand` el az előző szakaszban megadott eseményt.
    > [!div class="mx-imgBorder"]
    > ![Képernyőkép, amely egy távoli parancs eseményét jeleníti meg.](media/custom-commands/send-remote-command-activity.png)

Figyelje meg, hogy a paraméter értéke nem `"OnOff"` `"on"` beszéd vagy szöveg helyett az ügyfél által végzett tevékenységre lett beállítva.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Egy parancs paraméterének katalógusának frissítése

Egy paraméter érvényes beállításainak listájának konfigurálásakor a paraméter értékei globálisan vannak meghatározva az alkalmazáshoz. 

A példánkban a (z `SubjectDevice` ) paraméter a támogatott értékek rögzített listáját fogja tartalmazni a beszélgetéstől függetlenül.

Ha új bejegyzéseket szeretne hozzáadni a paraméter katalógusához, a következő tevékenység elküldésére van lehetősége:

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Ezzel a tevékenységgel hozzáadta a bejegyzést a `"stereo"` parancshoz tartozó paraméter katalógusához `"SubjectDevice"` `"TurnOnOff"` .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="A katalógus frissítését bemutató képernyőkép.":::

Vegye figyelembe, hogy néhány dolog:
- Ezt a tevékenységet csak egyszer kell elküldeni (ideális esetben közvetlenül a kapcsolatok megkezdése után).
- A tevékenység elküldése után meg kell várnia, hogy az eseményt `ParameterCatalogsUpdated` vissza lehessen küldeni az ügyfélnek.

## <a name="add-more-context-from-the-client-application"></a>További környezet hozzáadása az ügyfélalkalmazás számára

Az ügyfélalkalmazás további kontextusát is beállíthatja, amely később az egyéni parancsok alkalmazásban használható. 

Gondoljon például arra a forgatókönyvre, amelyben el szeretné küldeni az egyéni parancsok alkalmazáshoz csatlakoztatott eszköz AZONOSÍTÓját és nevét.

A forgatókönyv teszteléséhez hozzon létre egy új parancsot az aktuális alkalmazásban:
1. Hozzon létre egy nevű új parancsot `GetDeviceInfo` .
1. Adjon meg egy példát a mondatra `get device info` .
1. A befejezési szabályban vegyen fel egy **beszédfelismerési válasz küldése** **műveletet, amely** tartalmazza a attribútumait `clientContext` .
   ![Képernyőkép, amely a beszéd kontextusban való küldésére vonatkozó választ jeleníti meg.](media/custom-commands/send-speech-response-context.png)
1. Az alkalmazás mentése, betanítása és tesztelése.
1. A tesztelési ablakban küldjön egy tevékenységet az ügyfél környezetének frissítéséhez.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. A szöveg elküldése `get device info` .
   ![Képernyőfelvétel: az ügyfél-környezet küldését bemutató tevékenység.](media/custom-commands/send-client-context-activity.png)

Vegye figyelembe, hogy néhány dolog:
- Ezt a tevékenységet csak egyszer kell elküldeni (ideális esetben közvetlenül a kapcsolatok megkezdése után).
- Összetett objektumokat használhat a következőhöz: `clientContext` .
- `clientContext`A beszédfelismerési válaszokat, a tevékenységek küldését és a webes végpontok meghívását is használhatja.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Parancsok frissítése webes végpontról](./how-to-custom-commands-update-command-from-web-endpoint.md)
