---
title: Cognitive Services Text Analytics-erőforrás létrehozása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre Cognitive Services Text Analytics erőforrást.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750811"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics-erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza az **Erőforrás létrehozása** lehetőséget, majd lépjen a **Mesterséges intelligencia és gépi tanulás** > **Text Analytics** területre.
   Vagy nyissa meg a következőt: [text Analytics létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Név|Adjon meg egy nevet (2–64 karakter).|
    |Előfizetés|Válassza ki a megfelelő előfizetést.|
    |Hely|Válasszon egy közeli helyet.|
    |Tarifacsomag| Adja meg az **S** betűt, amely a standard tarifacsomag jele.|
    |Erőforráscsoport|Válasszon ki egy elérhető erőforráscsoportot.|

1. Válassza a **Létrehozás** lehetőséget, és várjon, amíg az erőforrás létrejön. A böngésző automatikusan átirányítja az újonnan létrehozott erőforrásoldalra.
1. Gyűjtse össze a konfigurált `endpoint` és egy API-kulcsot:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A következőhöz hasonlóan jelenik meg: `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` .|
    |**Kulcsok**|API-kulcs|Másolja a két kulcs egyikét. Ez egy 32 karakterből álló alfanumerikus karakterlánc, szóköz vagy kötőjel nélkül: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
