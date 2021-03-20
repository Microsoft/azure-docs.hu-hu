---
title: Adattárolás – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS a kulcs által meghatározott régiónak megfelelő Azure-adattárban tárolja a titkosított adatmennyiséget.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a58bcff4e39c4a4a907cd8567b47b074ff299bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97008452"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Adattárolás és eltávolítás Language Understanding (LUIS) Cognitive Services

A LUIS a kulcs által meghatározott [régiónak](luis-reference-regions.md) megfelelő Azure-adattárban tárolja a titkosított adatmennyiséget. 

* Az alkalmazás élettartama során a modell, például az entitások, a szándékok és a hosszúságú kimondott szöveg betanításához használt adatmennyiséget a LUIS-ban menti a rendszer. Ha egy tulajdonos vagy közreműködő törli az alkalmazást, a rendszer ezeket az adattörlési művelettel törli. Ha egy alkalmazás 90 nap alatt nem volt használatban, akkor a rendszer törli. 

* Az alkalmazás szerzője dönthet úgy, hogy [engedélyezi a naplózást](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning) a közzétett alkalmazásnak eljuttatott hosszúságú kimondott szöveg. Ha engedélyezve van, a hosszúságú kimondott szöveg 30 napig lesz mentve, és az alkalmazás szerzője is megtekinthető. Ha a naplózás nincs engedélyezve az alkalmazás közzétételekor, a rendszer nem tárolja ezeket az adatfájlokat.

## <a name="export-and-delete-app"></a>Alkalmazás exportálása és törlése
A felhasználók teljes körű hozzáféréssel rendelkeznek az alkalmazás [exportálásához](luis-how-to-start-new-app.md#export-app) és [törléséhez](luis-how-to-start-new-app.md#delete-app) . 

## <a name="utterances"></a>Beszédelemek

A hosszúságú kimondott szöveg két különböző helyen is tárolhatók. 

* **A szerzői folyamat** során a rendszer létrehozza és tárolja a hosszúságú kimondott szöveg a szándékban. A sikeres LUIS-alkalmazáshoz hosszúságú kimondott szöveg szükséges. Miután közzétette az alkalmazást, és fogadja a lekérdezéseket a végponton, a végponti kérelem querystring határozza meg, `log=false` hogy a végpontok kivonása van-e tárolva. Ha a rendszer tárolja a végpontot, az a portál **összeállítás** szakaszában található aktív tanulási hosszúságú kimondott szöveg részévé válik az **Endpoint hosszúságú kimondott szöveg áttekintése** szakaszban. 
* Ha **áttekinti a végpont hosszúságú kimondott szöveg**, és felvesz egy leképezést, a rendszer már nem tárolja a megjelölést az áttekinthető végponti hosszúságú kimondott szöveg részeként. A rendszer hozzáadja az alkalmazás szándékához. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Példa hosszúságú kimondott szöveg törlése egy szándékból

A [Luis](luis-reference-regions.md)-képzéshez használt példa hosszúságú kimondott szöveg törlése. Ha töröl egy példát a LUIS-alkalmazásból, az el lesz távolítva a LUIS webszolgáltatásból, és nem érhető el exportálásra.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Hosszúságú kimondott szöveg törlése az Active learningből

A hosszúságú kimondott szöveg törölheti a felhasználói hosszúságú kimondott szöveg listájáról, amelyet a LUIS az **[Endpoint hosszúságú kimondott szöveg áttekintése lapon](luis-how-to-review-endpoint-utterances.md)** javasol. Ha törli a hosszúságú kimondott szöveg a listából, azzal megakadályozza, hogy a rendszer ne törölje őket a naplókból.

Ha nem szeretné, hogy az aktív tanulás hosszúságú kimondott szöveg, [Tiltsa le az aktív tanulást](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Az aktív tanulás letiltása szintén letiltja a naplózást.

### <a name="disable-logging-utterances"></a>Hosszúságú kimondott szöveg naplózásának letiltása
Az [aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning) letiltja a naplózást.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Fiók eltávolítása
Ha nem telepíti át a rendszert, törölheti a fiókját, és az összes alkalmazás törölve lesz a példa hosszúságú kimondott szöveg és naplóival együtt. Az adat megőrzése 90 napig tart, mielőtt a fiók és az adat véglegesen törlődik.

A fiók törlése a **Beállítások** lapon érhető el. Válassza ki a fiók nevét a jobb felső navigációs sávon a **Beállítások** lapra való lekéréshez.

## <a name="delete-an-authoring-resource"></a>Szerzői erőforrás törlése
Ha [egy szerzői erőforrásra migrált](./luis-migration-authoring.md), a Azure Portal törli az erőforráshoz társított összes alkalmazást, valamint a hozzájuk tartozó hosszúságú kimondott szöveg és naplókat is. Az adat megőrzése 90 nappal a végleges törlés előtt történik.    

Az erőforrás törléséhez nyissa meg a [Azure Portal](https://ms.portal.azure.com/#home) , és válassza ki a Luis authoring-erőforrását. Nyissa meg az **Áttekintés** lapot, és kattintson a lap tetején található **Törlés** gombra. Ezután erősítse meg, hogy az erőforrás törölve lett. 

## <a name="data-inactivity-as-an-expired-subscription"></a>Az inaktivitás lejárt előfizetésként
Az adatmegőrzés és a törlés céljából az inaktív LUIS alkalmazás a _Microsoft belátása_ szerint lejárt előfizetésként kezelhető. Az alkalmazás akkor minősül inaktívnak, ha megfelel a következő feltételeknek az elmúlt 90 napban: 

* **Nem** volt hívása.
* Nem lett módosítva.
* Nem rendelkezik a hozzá tartozó aktuális kulccsal.
* Nem volt felhasználó bejelentkezni.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók az alkalmazások exportálásáról és törléséről](luis-how-to-start-new-app.md)