---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 12/29/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: 7aa2fba6ef551a745ccaf5b00f36021b9d8680ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97820584"
---
1. Válassza a [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) lehetőséget a fájl GitHub-oldalának megadásához `pizza-app-for-luis.json` .
1. Kattintson a jobb gombbal a **RAW** gombra, és válassza a **hivatkozás mentése másként** lehetőséget a számítógépre való mentéshez `pizza-app-for-luis.json` .
1. Jelentkezzen be a [Luis portálra](https://www.luis.ai).
1. Válassza [a saját alkalmazások](https://www.luis.ai/applications)lehetőséget.
1. A **saját alkalmazások** lapon válassza az **+ új alkalmazás lehetőséget a beszélgetéshez**.
1. Válassza **az IMPORTÁLÁS JSON-ként** lehetőséget.
1. Az **új alkalmazás importálása** párbeszédpanelen kattintson a **fájl kiválasztása** gombra.
1. Válassza ki a `pizza-app-for-luis.json` letöltött fájlt, majd kattintson a **Megnyitás** gombra.
1. Az **új alkalmazás importálása** párbeszédpanel **név** mezőjébe írja be a pizza-alkalmazás nevét, majd kattintson a **kész** gombra.

Az alkalmazás importálva lesz.

Ha megjelenik **egy hatékony Luis-alkalmazás létrehozásának** párbeszédpanele, akkor zárjuk be a párbeszédpanelt.

## <a name="train-and-publish-the-pizza-app"></a>A pizza-alkalmazás betanítása és közzététele

Meg kell jelennie a **szándékok** oldalnak a pizza-alkalmazásban található leképezések listájával.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

A pizza-alkalmazás most már készen áll a használatra.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>A pizza-alkalmazás alkalmazás-AZONOSÍTÓjának, előrejelzési kulcsának és előrejelzési végpontjának rögzítése

Az új pizza-alkalmazás használatához szüksége lesz a pizza-alkalmazáshoz tartozó alkalmazás-AZONOSÍTÓra, előrejelzési kulcsra és előrejelzési végpontra.

A következő értékek megkeresése:

1. A **leképezések** lapon válassza a **kezelés** lehetőséget.
1. Az **Alkalmazásbeállítások** lapon jegyezze fel az **alkalmazás azonosítóját**.
1. Válassza az **Azure Resources** (Azure-erőforrások) lehetőséget.
1. Az **Azure-erőforrások** lapon jegyezze fel az **elsődleges kulcsot**. Ez az érték az előrejelzési kulcs.
1. Jegyezze fel a **végpont URL-címét**. Ez az érték az előrejelzési végpont.
