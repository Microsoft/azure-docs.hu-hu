---
title: Azure API Management-terminológia | Microsoft Docs
description: Ez a cikk a API Managementra vonatkozó feltételek definícióit tartalmazza.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 002ae9f99865114dd8bf52b53efc9303a0706a82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491824"
---
# <a name="azure-api-management-terminology"></a>Azure API Management-terminológia

Ez a cikk a API Managementra (APIM) vonatkozó feltételek definícióit ismerteti.

## <a name="term-definitions"></a>Kifejezés-definíciók

* **Háttérbeli API** – egy http-szolgáltatás, amely megvalósítja az API-t és annak műveleteit. További információ: [háttérrendszer](backends.md).
* **FRONTEND API** / **APIM API** – az APIM API nem üzemeltet API-kat, az API-khoz homlokzatokat hoz létre. Igényei szerint testre szabhatja a homlokzatot a háttér-API érintése nélkül. További információ: [API importálása és közzététele](import-and-publish.md).
* **APIM termék** – egy termék tartalmaz egy vagy több API-t, valamint egy használati kvótát és a használati feltételeket. Több API-t is megadhat, és a fejlesztői portálon keresztül biztosíthatja őket a fejlesztőknek. További információ: [termékek létrehozása és közzététele](api-management-howto-add-products.md).
* **APIM API-művelet** – minden APIM API a fejlesztők számára elérhető műveletek készletét jelöli. Minden APIM API tartalmaz egy hivatkozást a háttér-szolgáltatásra, amely megvalósítja az API-t, valamint a háttér-szolgáltatás által megvalósított műveletekhez kapcsolódó műveleteiket. További információ: az [API-válaszok modellezése](mock-api-responses.md).
* **Verzió** – előfordulhat, hogy egyes felhasználók számára új vagy más API-szolgáltatásokat szeretne közzétenni, míg mások a jelenleg használt API-val szeretnének ragaszkodni. További információ: [az API több verziójának közzététele](api-management-get-started-publish-versions.md).
* **Változat** – ha az API készen áll a fejlesztésre, és a fejlesztők megkezdik a használatát, akkor általában ügyelnie kell arra, hogy módosítsa az API-t, és egyidejűleg ne zavarja meg az API hívóit. Emellett az is hasznos, ha a fejlesztők értesülnek az elvégzett módosításokról. További információ: [változatok használata](api-management-get-started-revise-api.md).
* **Fejlesztői portál** – ügyfelei (fejlesztői) a fejlesztői portál használatával férhetnek hozzá az API-khoz. A fejlesztői portál testreszabható. További információ: [a fejlesztői portál testreszabása](api-management-customize-styles.md).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Példány létrehozása](get-started-create-service-instance.md)

