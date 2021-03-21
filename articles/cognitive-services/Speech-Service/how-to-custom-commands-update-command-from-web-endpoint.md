---
title: Parancsok frissítése webes végpontról
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan frissítheti egy parancs állapotát egy webes végpont hívásával.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97560270"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Parancsok frissítése webes végpontról

Ha az ügyfélalkalmazás a hangbemenet nélküli folyamatban lévő parancs állapotának frissítését igényli, a parancs futtatásával a webes végpont hívásával frissítheti a parancsot.

Ebből a cikkből megtudhatja, hogyan frissítheti a folyamatban lévő parancsokat egy webes végpontról.

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * Egy korábban [létrehozott Custom Commands-alkalmazás](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása 

Ebben a példában egy HTTP-triggert használó [Azure-függvényre](../../azure-functions/index.yml) lesz szüksége, amely a következő bemenetet (vagy a bemenet egy részhalmazát) támogatja:

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Tekintsük át a bemenet főbb attribútumait:

| Attribútum | Magyarázat |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Beszélgetésazonosító** | A beszélgetés egyedi azonosítója. Vegye figyelembe, hogy ez az azonosító az ügyfélalkalmazás alapján hozható létre. |
| **currentCommand** | A beszélgetésben jelenleg aktív parancs. |
| **név** | A parancs neve. Az `parameters` attribútum egy Térkép a paraméterek aktuális értékeivel. |
| **currentGlobalParameters** | Például a `parameters` globális paraméterekhez használt Térkép. |

Az Azure-függvény kimenetének a következő formátumot kell támogatnia:

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Ezt a formátumot felismerheti, mert ugyanaz, mint amit a parancs az [ügyfélről való frissítésekor](./how-to-custom-commands-update-command-from-client.md)használt. 

Most hozzon létre egy Azure-függvényt Node.js alapján. A kód másolása/beillesztése:

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Ha ezt az Azure-függvényt egyéni parancsokból hívja meg, akkor a beszélgetés aktuális értékeit kell elküldenie. Visszaküldi a frissíteni kívánt paramétereket, vagy ha meg szeretné szakítani az aktuális parancsot.

## <a name="update-the-existing-custom-commands-app"></a>A meglévő egyéni parancsok alkalmazás frissítése

Ismerkedjen meg az Azure-függvénysel a meglévő egyéni parancsok alkalmazással:

1. Adjon hozzá egy nevű új parancsot `IncrementCounter` .
1. Vegyen fel csak egy példa mondatot az értékkel `increment` .
1. Adja hozzá a (z) nevű új paramétert `Counter` (az Azure-függvényben megadott nevet), `Number` amelynek alapértelmezett értéke a következő: `0` .
1. Adjon hozzá egy nevű új webes végpontot `IncrementEndpoint` Az Azure-függvény URL-címével, és **engedélyezze** a **távoli frissítések** beállítást.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="A webes végpontok távoli frissítésekkel való beállítását bemutató képernyőkép.":::
1. Hozzon létre egy **IncrementRule** nevű új kapcsolatitevékenység-szabályt, és adjon hozzá egy **hívás webes végpont** műveletet.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="A kapcsolatitevékenység-szabály létrehozását bemutató képernyőkép.":::
1. A művelet konfigurációjában válassza a elemet `IncrementEndpoint` . A **sikeres** konfigurálással **elküldheti a beszédfelismerési választ a (z)** értékkel `Counter` , és egy hibaüzenettel konfigurálhatja **a** hibát.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="A webes végpontok meghívására szolgáló növekmény számláló beállítását bemutató képernyőkép.":::
1. Állítsa be a szabály végrehajtás utáni állapotát, hogy **várjon a felhasználó bemenetére**.

## <a name="test-it"></a>Tesztelje

1. Az alkalmazás mentése és betanítása.
1. Válassza a **Tesztelés** lehetőséget.
1. Küldjön `increment` néhányszor (amely a parancshoz tartozó példa mondata `IncrementCounter` ).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="A növekményes számláló példáját bemutató képernyőkép.":::

Figyelje meg, hogy az Azure-függvény az `Counter` egyes körökben a paraméter értékét növeli.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [CI-/CD-folyamat engedélyezése a Custom Commands-alkalmazás számára](./how-to-custom-commands-deploy-cicd.md)