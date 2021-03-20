---
title: Anomália-visszajelzés küldése a metrikai tanácsadó szolgáltatásnak
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan küldhet visszajelzést a metrikai tanácsadó példány által észlelt rendellenességekről, és hogyan hangolhatja be az eredményeket.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184969"
---
# <a name="provide-anomaly-feedback"></a>Anomália-visszajelzés megadása

A felhasználói visszajelzések az egyik legfontosabb módszer a hibák felderítésére az anomáliák észlelése rendszeren belül. Itt biztosítjuk, hogy a felhasználók a helytelen észlelési eredményeket közvetlenül egy idősorozaton jelölhetik meg, és azonnal alkalmazza a visszajelzést. Ily módon a felhasználó megtaníthatja az anomáliák észlelési rendszerét, hogyan lehet az aktív interakciók révén egy adott idősorozathoz tartozó anomáliát észlelni. 

> [!NOTE]
> A visszajelzések jelenleg csak az **intelligens észlelési** eredményekre, de a **küszöbértékek** és a **határértékek változására** is hatással vannak.

## <a name="how-to-give-time-series-feedback"></a>Idősorozat-visszajelzés küldése

A metrikák részletei lapon bármilyen adatsorozatban visszajelzést adhat. Csak válasszon ki egy pontot, és az alábbi visszajelzési párbeszédablak jelenik meg. Megjeleníti a kiválasztott sorozat dimenzióit. Kiválaszthatja a dimenzió értékeit, vagy eltávolíthat néhányat, hogy lekérje az idősoros adatok kötegét. Az idősorozatok kiválasztása után a **Hozzáadás** gombra kattintva adja hozzá a visszajelzést, négyféle visszajelzést adhat. Több visszajelzési elem hozzáfűzéséhez válassza a **Mentés** gombot a jegyzetek befejezése után.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Diagram idősoros adatsorozattal, kék vonallal és piros ponttal, különböző pontokon. A piros mező, amely egy pontot tartalmaz a szöveggel: válasszon ki egy pontot":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="Visszajelzés hozzáadása párbeszédpanel két dimenzióval, valamint a dimenziók kiválasztásának és eltávolításának lehetőségével, valamint visszajelzések hozzáadásával.":::

### <a name="mark-the-anomaly-point-type"></a>A rendellenességi pont típusának megjelölése

Ahogy az alábbi képen is látható, a visszajelzés párbeszédablak automatikusan kitölti a kiválasztott pont időbélyegét, de szerkesztheti ezt az értéket. Ezután kiválaszthatja, hogy szeretné-e azonosítani ezt az elemet:, `Anomaly` `NotAnomaly` vagy `AutoDetect` .

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Legördülő menü a anomália, a NotAnomaly és az automatikus észlelés lehetőségeivel":::

A kijelölés az azonos adatsorozat jövőbeli rendellenesség-észlelési feldolgozására vonatkozó visszajelzéseket fogja alkalmazni. A feldolgozott pontok nem lesznek újraszámítva. Ez azt jelenti, hogy ha NotAnomaly-ként jelölt ki egy anomáliát, akkor a jövőben a hasonló anomáliák is megmaradnak, és ha a pontot jelölte meg `NotAnomaly` `Anomaly` , a későbbiekben hasonló pontokat fogunk észlelni `Anomaly` . Ha a `AutoDetect` választást választja, a jövőben a rendszer figyelmen kívül hagyja a korábbi visszajelzéseket.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Visszajelzés küldése több folyamatos pontról 

Ha egyszerre több folyamatos pontra vonatkozóan is szeretne anomáliát adni, válassza ki a jegyzetként használni kívánt pontok csoportját. A rendszer automatikusan kitölti a kiválasztott időtartományt, ha anomália-visszajelzést ad.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Anomália visszajelzési menü kiválasztott rendellenességgel és adott időtartománysal":::

Ha szeretné megtekinteni, hogy egy adott pontra hatással van-e az anomália, akkor az idősorozatok böngészése során jelöljön ki egy pontot. Ha a rendszer a rendellenességek észlelésének eredményét visszajelzéssel módosította, az elemleírás a következő **visszajelzéseket fogja megjeleníteni: true (igaz**). Ha a **visszajelzések szerint** a következő üzenet jelenik meg: false (hamis), ez azt jelenti, hogy a rendszer anomália-visszajelzési számítást hajtott végre ehhez a ponthoz, de a rendellenesség észlelésének eredménye nem

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Az elemleírás az alábbiak szerint jelenik meg: visszajelzés: igaz, vörös színnel kijelölve":::

Vannak olyan helyzetek, amikor nem javasoljuk, hogy visszajelzést kapjon:

- A rendellenességet egy szünnap okozza. Azt javasoljuk, hogy az ilyen típusú hamis riasztások megoldásához egy előre beállított eseményt használjon, mivel pontosabban fog megjelenni.
- Az anomáliát egy ismert adatforrás-változás okozza. Például egy felsőbb rétegbeli rendszerváltozás történt az adott időpontban. Ebben az esetben egy anomália-riasztást kell megadni, mivel a rendszer nem tudta, hogy mi okozta az érték változását, és ha a hasonló értékek változása újra megtörténik. Ezért nem javasoljuk, hogy ilyen típusú problémát jegyezzen fel `NotAnomaly` .

## <a name="change-points"></a>Pontok módosítása

Előfordulhat, hogy az adatváltozások változásai hatással lesznek az anomáliák észlelési eredményeire. Ha döntés születik arról, hogy egy pont anomália-e, vagy sem, a rendszer figyelembe veszi az előzmények legújabb ablakát. Ha az idősorozatban változás következik be, akkor megadhatja a pontos módosítási pontot, így a későbbi elemzés során a rendszer segít a anomália-detektorban.

Az alábbi ábra azt mutatja, hogy kiválaszthatja `ChangePoint` a visszajelzés típusát, majd kiválaszthatja a `ChangePoint` , `NotChangePoint` vagy lehetőséget `AutoDetect` a legördülő listából.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="A ChangePoint, a NotChangePoint és az automatikus észlelés beállításait tartalmazó legördülő menü módosítása":::

> [!NOTE]
> Ha az adatai folyamatosan változnak, csak egy pontot kell megjelölnie `ChangePoint` , így ha a jelölést választotta, akkor `timerange` a rendszer automatikusan kitölti az utolsó pont időbélyegét és időpontját. Ebben az esetben a jegyzet csak 12 pont elteltével fogja érinteni a anomáliák észlelésének eredményét.

## <a name="seasonality"></a>Szezonalitás

A szezonális adatok esetében, ha a rendszer anomália-észlelést végez, az egyik lépés az idősorozat időszakának (szezonális) becslése, és alkalmazása a rendellenesség észlelési fázisára. Néha nehéz megállapítani a pontos időszakot, és az időszak is változhat. A helytelenül meghatározott időszaknak lehetnek mellékhatásai az anomáliák észlelési eredményein. Az aktuális időszakot egy elemleírásban találja, a neve pedig `Min Period` .

:::image type="content" source="../media/feedback/min-period.png" alt-text="Az elemleírás átfedésben van a szavak és a hét piros színnel.":::

Az ilyen típusú anomáliák észlelési hibájának kijavításához az időszakra vonatkozó visszajelzések is megadhatók. Ahogy az ábrán is látható, beállíthatja a pont értékét. Az egység `interval` egy részletességet jelent. Itt nulla intervallum azt jelenti, hogy az adatközpont nem szezonális. Azt is kiválaszthatja, `AutoDetect` hogy szeretné-e megszakítani a korábbi visszajelzést, és lehetővé teszi a folyamat automatikus észlelését. 
 
> [!NOTE]
> Ha a beállítás időtartamát nem kell időbélyeg-vagy timerange rendelni, akkor az adott időszak a visszajelzések pillanatában az egész idősor kapcsolatos jövőbeli rendellenességek észlelését fogja érinteni.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="A menü automatikus észlelési időszaka 28 értékre van állítva, és az intervallum nullára van állítva, amely nem szezonális.":::

## <a name="provide-comment-feedback"></a>Megjegyzés visszajelzésének megadása

Megjegyzéseket fűzhet a jegyzetekhez, és a kontextust is megadhatja az adataihoz. Megjegyzések hozzáadásához válasszon ki egy időtartományt, és adja meg a megjegyzés szövegét.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Menü, amely lehetővé teszi egy időtartomány és egy mező beállítását szöveges Megjegyzés hozzáadásához":::

## <a name="time-series-batch-feedback"></a>Time Series – batch – visszajelzés

Az előzőekben leírtaknak megfelelően a visszajelzési mód lehetővé teszi a dimenzió értékek kijelölését vagy eltávolítását egy dimenzió szűrő által meghatározott idősorozatok lekéréséhez. Ezt a modális elemet úgy is megnyithatja, hogy a bal oldali panelen a "+" gombra kattint, és kiválasztja a dimenziókat és a dimenzió értékeket.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="A Word-visszajelzések melletti piros színnel jelölt kék plusz jelre mutató menü":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Visszajelzési menü hozzáadása két, a Dim1 és a Dim2 által jelzett dimenzióval":::

## <a name="how-to-view-feedback-history"></a>Visszajelzési előzmények megtekintése

A visszajelzések előzményeit kétféleképpen lehet megtekinteni. A bal oldali panelen kiválaszthatja a visszajelzési előzmények gombot, és megjelenik a visszajelzési lista modális listája is. Felsorolja az egyes adatsorozat-vagy dimenzió-szűrők előtt megadott összes visszajelzést.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Visszajelzési lista menü":::

Egy másik lehetőség a visszajelzések előzményeinek egy sorozatból való megtekintésére. Az egyes adatsorozatok jobb felső sarkában több gomb jelenik meg. Jelölje be a visszajelzés megjelenítése gombot, és a sor a visszacsatolási pontok megjelenítését fogja megjeleníteni a visszajelzési bejegyzések megjelenítéséhez. A zöld jelölő egy változási pontot jelöl, a kék pontok pedig más visszajelzési pontok. Azt is kiválaszthatja, hogy a visszajelzések listája modális, amely felsorolja az ebben a pontban megadott visszajelzés részleteit.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Visszajelzési előzmények gráfja":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="Visszajelzési lista menü két dimenzióval":::

> [!NOTE]
> Bárki, aki hozzáféréssel rendelkezik a metrikához, küldhet visszajelzést, így a más datafeed-tulajdonosok által megadott visszajelzések is megjelenhetnek. Ha ugyanazokat a pontokat szerkeszti, mint valaki más, a visszajelzés felülírja az előző visszajelzési bejegyzést.       

## <a name="next-steps"></a>Következő lépések
- [Incidens diagnosztizálása](diagnose-incident.md).
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](configure-metrics.md)
- [Riasztások konfigurálása és értesítések fogadása beavatkozási pont segítségével](../how-tos/alerts.md)