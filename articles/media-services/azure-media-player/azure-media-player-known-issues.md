---
title: Azure Media Player ismert problémák
description: Az aktuális kiadás a következő ismert problémákkal rendelkezik.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 9d2289f1cec0d3679b0e9400f75c75cf9fece60b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448816"
---
# <a name="known-issues"></a>Ismert problémák #

Az aktuális kiadás a következő ismert problémákkal rendelkezik:

## <a name="azure-media-player"></a>Azure Media Player ##

- A helytelenül konfigurált kódolók problémákhoz vezethetnek a lejátszás során
- A 2 ^ 53-nál nagyobb időbélyegekkel rendelkező adatfolyamok lejátszási problémákat okozhatnak.
  - Enyhítés: a 90-kHz videó és a 44,1-kHz időkeret használata
- Nincs automatikus lejátszás a mobileszközök felhasználói beavatkozása nélkül. A platform blokkolja.
- A közeli megszakítások keresése a lejátszási hibákhoz vezethet.
- A nagyméretű bemutatók letöltése a felhasználói felület lefagyását okozhatja.
- A bemutató befejezése után nem lehet automatikusan lejátszani ugyanazt a forrást.
  - A forrás visszajátszásához a befejezést követően újra be kell állítania a forrást.
- Az üres jegyzékfájlok problémákat okozhatnak a lejátszóban.
  - Ez a probléma akkor fordulhat elő, ha élő adatfolyamot indít el, és nem található elég darab a jegyzékfájlban.
- A lejátszási pozíció lehet a felhasználói felületen kívüli seekbar.
- Az események rendezése nem konzisztens az összes technológiában.
- A pufferelt tulajdonság nem konzisztens a technológiák között.
- a nativeControlsForTouch false (default) értékűnek kell lennie az "objektum nem támogatja a (z)" setControls "tulajdonságot vagy metódust.
- A plakátoknak most abszolút URL-nek kell lenniük
- A felhasználói felületen kisebb esztétikai problémák léphetnek fel, ha az eszköz nagy kontrasztú módot használ
- A teljes dekódolású karakterláncban a (z) "%" vagy "+" értéket tartalmazó URL-címeknél problémák merülhetnek fel a forrás beállításakor

## <a name="ad-insertion"></a>Ad-Beszúrás ##

- Előfordulhat, hogy a hirdetések behelyezése (igény szerint vagy élő) során problémákba ütközik, amikor egy ad-blokkoló van telepítve a böngészőben
- Előfordulhat, hogy a mobileszközök problémákat tapasztalnak a hirdetések lejátszásakor.
- A Azure Media Player jelenleg nem támogatja az MP4 Midroll-hirdetéseket.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Az élő tartalom DVR-ablakában, ha a tartalom befejezi az idővonalat, a továbbra is növekedni fog, amíg nem kívánja elérni a területét, vagy nem éri el a bemutató végét.
- Néhány probléma merült fel a Firefox-ban, az MSE-ben való élő bemutatók esetén

- A csak hangalapú eszközök nem fognak visszajátszani a AzureHtml5JS-technológián keresztül.
  - Ha hang nélkül szeretné lejátszani az eszközöket, ezt megteheti, ha üres hangot szúr be a [Azure Media Services Explorer eszköz](https://aka.ms/amse) használatával
  - A csendes hang beszúrásával kapcsolatos utasítások [itt](../previous/media-services-advanced-encoding-with-mes.md#silent_audio) találhatók

## <a name="flash"></a>Flash ##

- Az AES-tartalom nem jelenik meg a flash verzió 30.0.0.134, mert hiba történik az Adobe gyorsítótárazási logikájában. Az Adobe kijavította a problémát, és kiadta azt a 30.0.0.154-ben
- Technikai és http-hibák (például 404 hálózati időtúllépés) esetén a lejátszó a többi technológiánál hosszabb időt vesz igénybe.
- Kattintson a videó lehetőségre a flashs Tech nem játssza le/szünetelteti a lejátszót.
- Ha a felhasználó rendelkezik Flash-telepítéssel, de nem ad engedélyt a hely betöltésére, a végtelen spinning megjelenhet. Ennek az az oka, hogy a lejátszó úgy gondolja, hogy a beépülő modul telepítve van és elérhető, és azt hiszi, hogy a beépülő modul fut a tartalom A JavaScript-kód elküldése megtörtént, de a böngésző beállításai letiltották a beépülő modul futtatását, amíg a felhasználó el nem fogadja a beépülő modul engedélyezését. Ez az összes böngészőben megtörténhet.  

## <a name="silverlight"></a>Silverlight ##

- Hiányzó funkciók
- Technikai és http-hibák (például 404 hálózati időtúllépés) esetén a lejátszó a többi technológiánál hosszabb időt vesz igénybe.
- A Safari és a Firefox Mac-lejátszással való lejátszásához a Silverlight explicit módon meghatározza `"http://` vagy `https://` a forrást.
- Ha egy API hiányzik ehhez a technológiához, akkor általában null értéket ad vissza.
- Ha a felhasználó rendelkezik Flash-telepítéssel, de nem ad engedélyt a hely betöltésére, a végtelen spinning megjelenhet. Ennek az az oka, hogy a lejátszó úgy gondolja, hogy a beépülő modul telepítve van és elérhető, és azt hiszi, hogy a beépülő modul fut a tartalom A JavaScript-kód elküldése megtörtént, de a böngésző beállításai letiltották a beépülő modul futtatását, amíg a felhasználó el nem fogadja a beépülő modul engedélyezését. Ez az összes böngészőben megtörténhet.  

## <a name="native-html5"></a>Natív HTML5 ##

- A Html5 Tech csak az első beállított forrás canplaythrough-eseményét küldi el.
- Ez a technológia csak a böngésző megvalósítását támogatja.  Előfordulhat, hogy néhány funkció hiányzik ebben a technológiában.  
- Ha egy API hiányzik ehhez a technológiához, akkor általában null értéket ad vissza.
- Ebben a technológiában problémák léptek fel a feliratokkal és a feliratokkal kapcsolatban. Előfordulhat, hogy ezek a technológiák nem állnak rendelkezésre vagy nem láthatók.
- A HLS/Html5 műszaki forgatókönyv korlátozott sávszélességgel rendelkezik, és videó nélküli hanglejátszást eredményez.

### <a name="microsoft"></a>Microsoft ###

- Az IE8 lejátszási folyamata jelenleg nem működik az ECMAScript 5-ös verzióval való inkompatibilitás miatt.
- Az IE-ben és az Edge egyes verzióiban a teljes képernyőt nem lehet beírni a gombhoz való bejárással, illetve az F/f billentyűparancs használatával.

## <a name="google"></a>Google ##

- Ugyanebben a jegyzékfájlban több kódolási profil is található a Chrome-ban, és nem ajánlott.
- A Chrome nem tud visszajátszani a AzureHtml5JS-mel. Kövesse a [hibák](https://bugs.chromium.org/p/chromium/issues/detail?id=534301)követésének részleteit.
- A Chrome-v58 függően a Widevine tartalmat be kell tölteni/lejátszani a https://protokollon keresztül, máskülönben a lejátszás sikertelen lesz.

## <a name="mozilla"></a>Mozilla ##

- A hangadatfolyam-kapcsolóhoz a AzureHtml5JS használatakor az adatfolyamoknak ugyanazokat a kodekeket kell megadniuk. Ezt a Firefox platformot kell megadnia.

## <a name="apple"></a>Apple ##

- A Safari a Mac gépen gyakran engedélyezi az energiatakarékos üzemmódot a "Leállítás a Power Save-ben" beállítással, amely blokkolja a beépülő modulokat, például a Flasht és a Silverlight-t, ha úgy gondolják, hogy nem a felhasználó javára van beállítva. Ez a blokk nem blokkolja a beépülő modul létezését, csak a képességeit. Az alapértelmezett techOrder miatt ez problémákat okozhat a lejátszási kísérlet során
  - 1. enyhítés: Ha a videolejátszó "elülső és középső" (a dokumentum bal felső sarkában lévő 3000 x 3000 képpont határon belül), akkor is játszania kell.
  - 2. enyhítés: módosítsa a Safari techOrder a következőre: ["azureHtml5JS", "HTML5"]. Ennek a megoldásnak a következménye, hogy ne legyenek a Flash technológiában elérhető összes funkció.
- Előfordulhat, hogy a Silverlight szolgáltatáson keresztüli PlayReady problémák léptek fel a Safariban.
- Az AES és a korlátozott jogkivonat tartalma nem játszható le az iOS és a régebbi Android-eszközök használatával.
  - Ennek a forgatókönyvnek a megvalósítása érdekében hozzá kell adni egy proxyt a szolgáltatáshoz.
- Az iOS-es telefon alapértelmezett bőre a következő módon jelenik meg:.
- az iPhone lejátszás mindig a natív lejátszó teljes képernyős területén fordul elő.
  - A funkciók, beleértve a feliratokat is, nem maradhatnak meg ebben a nem beágyazott lejátszásban.
- Ha az élő bemutató véget ér, az iOS-eszközök nem fognak megfelelően véget mutatni a bemutatónak.
- az iOS nem teszi lehetővé a DVR-képességek használatát.
- az iOS hangstream kapcsoló csak akkor hajtható végre, ha az iOS-es natív lejátszó felhasználói felülete van, és a kodeknek ugyanazokat a kodekeket kell használnia.
- Előfordulhat, hogy a Safari régebbi verzióiban problémák léptek fel az élő streamek lejátszásakor.

## <a name="older-android"></a>Régebbi Android ##

- Az AES és a korlátozott jogkivonat tartalma nem játszható le az iOS és a régebbi Android-eszközök használatával.
  - Ennek a forgatókönyvnek a megvalósítása érdekében hozzá kell adni egy proxyt a szolgáltatáshoz.

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)
