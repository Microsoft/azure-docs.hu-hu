---
title: A szolgáltatások kezelésének megismerése az Azure app Configuration használatával
description: Funkciók be-és kikapcsolása az Azure app Configuration használatával
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: cfd676092bcaede58909a3ec1eefeabb4c80f86b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930228"
---
# <a name="feature-management-overview"></a>A szolgáltatások kezelése – áttekintés

Az új alkalmazás-szolgáltatás szállítása hagyományosan az alkalmazás teljes újratelepítését igényli. Egy szolgáltatás teszteléséhez gyakran több központi telepítésre van szükség az alkalmazáshoz.  Az egyes központi telepítések megváltoztathatják a funkciót, vagy a szolgáltatást különböző ügyfeleknek teheti elérhetővé tesztelés céljából.  

A szolgáltatás-felügyelet egy modern szoftverfejlesztői gyakorlat, amely elválasztja a funkció kiadását a kód központi telepítéséről, és lehetővé teszi az igény szerinti szolgáltatás rendelkezésre állásának gyors módosítását. Ez a funkció *jelzői* (más néven *funkció-váltás*, *funkció-kapcsolók* stb.) elnevezésű technikát használja a szolgáltatások életciklusának dinamikus felügyeletéhez.

A szolgáltatások kezelése segít a fejlesztőknek a következő problémák megoldásában:

* A **kód elágazásának kezelése**: a funkció-jelzők használatával a jelenleg fejlesztés alatt álló új alkalmazás-funkciókat is becsomagolhatja. Az ilyen funkciók alapértelmezés szerint rejtettek. Biztonságosan szállíthatja a szolgáltatást, annak ellenére, hogy befejezetlen állapotban van, és az éles környezetben marad. Ha ezt a módszert használja, a *sötét üzembe helyezési* folyamattal minden egyes fejlesztési ciklus végén felszabadíthatja az összes kódot. Többé nem kell megtartania a kód ágakat több fejlesztési ciklusban, mert egy adott szolgáltatáshoz több ciklus szükséges.
* **Tesztelés éles** környezetben: a szolgáltatás-jelzők használatával korai hozzáférést biztosíthat az éles környezetben futó új funkciókhoz. Korlátozhatja például a csapattagok vagy a belső bétaverziós tesztelők elérését. Ezek a felhasználók a teljes hűségű üzemi élményt a tesztelési környezet szimulált vagy részleges felhasználói felülete helyett fogják tapasztalni.
* **Beléptetés: a** funkciók jelzői segítségével fokozatosan, a végfelhasználók számára új funkciókat érhet el. Először is megcélozhatja a felhasználói populáció kis hányadát, és az idő múlásával fokozatosan növelheti a százalékos arányt.
* **Instant kill kapcsoló**: a funkciók jelzői egy belső biztonsági hálót biztosítanak az új funkciók felszabadításához. A kódok újbóli üzembe helyezése nélkül is be-és kikapcsolhatja az alkalmazás funkcióit. Ha szükséges, az alkalmazás újraépítése és újbóli üzembe helyezése nélkül is gyorsan letilthatja a szolgáltatást.
* **Szelektív aktiválás**: a szolgáltatás-jelzők használatával szegmentálhatja a felhasználókat, és meghatározott szolgáltatásokat biztosíthat az egyes csoportoknak. Lehet, hogy egy szolgáltatás csak bizonyos webböngészőn működik. Meghatározhatja a szolgáltatás jelölőjét, így csak a böngésző felhasználói láthatják és használhatják a szolgáltatást. Ezzel a módszerrel később egyszerűen kiterjesztheti a támogatott böngészőket a kód módosítása nélkül.

## <a name="basic-concepts"></a>Alapfogalmak

A szolgáltatások felügyeletével kapcsolatos további feltételek:

* **Szolgáltatás jelzője**: a szolgáltatás jelölője a *be* -vagy *kikapcsolt* bináris állapotú változó. A szolgáltatás jelzője egy kapcsolódó kódrészletet is tartalmaz. A funkció jelző állapota elindítja, hogy a kód blokkolva van-e.
* A Feature **Manager**: a Feature Manager egy alkalmazáscsomag, amely az alkalmazás összes szolgáltatás jelzőjének életciklusát kezeli. A Feature Manager további funkciókat is biztosít, többek között a gyorsítótárazási funkciók jelzőit és az állapotuk frissítését.
* **Szűrő**: a szűrő egy olyan szabály, amely egy szolgáltatás jelzője állapotát értékeli. A lehetséges szűrők közé tartoznak a felhasználói csoportok, az eszközök és a böngészők típusai, a földrajzi helyszínek és az időablakok.

A szolgáltatások felügyeletének hatékony megvalósítása legalább két, a koncerten dolgozó összetevőből áll:

* Egy alkalmazás, amely a funkciók jelzőit használja.
* Egy különálló adattár, amely a szolgáltatás jelölőit és aktuális állapotait tárolja.

## <a name="using-feature-flags-in-your-code"></a>Szolgáltatás-jelzők használata a kódban

A szolgáltatás-jelzők alkalmazásban való megvalósításának alapvető mintája egyszerű. A szolgáltatás jelölője a kódban szereplő feltételes utasítást vezérlő logikai állapot változó:

```csharp
if (featureFlag) {
    // Run the following code
}
```

A statikus értéket is beállíthatja `featureFlag` .

```csharp
bool featureFlag = true;
```

Bizonyos szabályok alapján kiértékelheti a jelző állapotát:

```csharp
bool featureFlag = isBetaUser();
```

Kiterjesztheti a feltételes beállítást az alkalmazás viselkedésének megadásához bármelyik állapot esetén:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Szolgáltatások jelző tárháza

Ahhoz, hogy hatékonyan használhassa a szolgáltatás jelzőit, meg kell Externalize az alkalmazásban használt összes szolgáltatás jelölőjét. Ez lehetővé teszi, hogy az alkalmazás módosítása és újbóli üzembe helyezése nélkül módosítsa a szolgáltatás jelölőjét.

Az Azure app Configuration egy központi tárházat biztosít a funkciók jelzői számára. A használatával különböző típusú szolgáltatás-jelzőket adhat meg, és gyorsan és magabiztosan kezelheti az állapotukat. Ezután az alkalmazás konfigurációs kódtárai különböző programozási nyelvi keretrendszerekhez használhatók, így egyszerűen hozzáférhetnek ezekhez a szolgáltatásokhoz.

A [ASP.net Core alkalmazásban látható funkció-jelzők](./use-feature-flags-dotnet-core.md) azt mutatják be, hogyan használhatók a .net Core alkalmazás-konfiguráló szolgáltató és a szolgáltatás-felügyeleti kódtárak a ASP.net-webalkalmazáshoz tartozó szolgáltatási jelzők megvalósításához.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Szolgáltatás-jelzők hozzáadása egy ASP.NET Core webalkalmazáshoz](./quickstart-feature-flag-aspnet-core.md)  
