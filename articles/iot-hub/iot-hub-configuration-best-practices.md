---
title: Eszköz-konfiguráció – ajánlott eljárások az Azure IoT Hubhoz | Microsoft Docs
description: Ismerje meg az automatikus eszközkezelés használatának ajánlott eljárásait, amelyekkel csökkenthető a IoT-eszközök nagy léptékű kezelése során felmerülő ismétlődő és összetett feladatok.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 8a39c2b06ca8a0f852891acb60ba199fc2c6db5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96024129"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Ajánlott eljárások az eszközök konfigurálásához egy IoT-megoldáson belül

Az Azure IoT Hub automatikus eszközkezelés számos ismétlődő és összetett feladatot automatizál a nagyméretű eszközök flottáinak teljes életciklusa során. Ez a cikk a IoT-megoldások fejlesztéséhez és üzemeltetéséhez szükséges különböző szerepkörökkel kapcsolatos ajánlott eljárásokat ismerteti.

* **IoT hardver gyártója/integrátora:** A IoT-hardverek gyártói, a különböző gyártóktól származó hardverek és a más szállítók által gyártott vagy integrált IoT üzembe helyezéséhez hardvert biztosító szolgáltatók. Részt vesz a belső vezérlőprogram, a beágyazott operációs rendszerek és a beágyazott szoftverek fejlesztésében és integrálásában.

* **IoT-megoldás fejlesztője:** A IoT-megoldások fejlesztését általában egy megoldás fejlesztője végzi. Ez a fejlesztő lehet egy házon belüli csapat vagy egy ebben a tevékenységben szakosodott rendszerintegrátor. A IoT-megoldás fejlesztője a IoT-megoldás különböző összetevőit fejlesztheti teljesen, integrálhatja a különböző standard vagy nyílt forráskódú összetevőket, vagy testre szabhatja az [IoT-megoldási gyorssegédeket](../iot-accelerators/index.yml).

* **IoT-megoldás operátora:** A IoT-megoldás üzembe helyezése után hosszú távú műveleteket, figyelést, frissítést és karbantartást igényel. Ezeket a feladatokat egy házon belüli csapat is elvégezheti, amely információs technológiai szakemberekből, hardveres és karbantartási csapatokból, valamint olyan tartományi szakemberekből áll, akik a teljes IoT-infrastruktúra megfelelő viselkedését figyelik.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>A IoT-eszközök nagy léptékű konfigurálásához szükséges automatikus eszközkezelés ismertetése

Az automatikus eszközkezelés számos előnnyel jár a [Device Twins](iot-hub-devguide-device-twins.md) és a [Module ikrek](iot-hub-devguide-module-twins.md) számára, hogy szinkronizálja a kívánt és jelentett állapotokat a felhő és az eszközök között. Az [automatikus eszköz-konfigurációk](./iot-hub-automatic-device-management.md) automatikusan frissítik a nagy mennyiségű ikreket, és összegzik az előrehaladást és a megfelelőséget. A következő magas szintű lépések azt írják le, hogyan fejleszthető és használható az automatikus eszközkezelés:

* A **IoT hardver gyártója/integrátora** egy beágyazott alkalmazásban az [eszközök ikrek](iot-hub-devguide-device-twins.md)használatával valósítja meg az eszközök felügyeleti funkcióit. Ezek a szolgáltatások magukban foglalhatják a belső vezérlőprogram frissítéseit, a szoftverek telepítését és frissítését, valamint a beállítások kezelését.

* A **IoT-megoldás fejlesztője** az eszközökön az [ikrek](iot-hub-devguide-device-twins.md) és az [automatikus eszköz-konfigurációk](./iot-hub-automatic-device-management.md)használatával valósítja meg a felügyeleti réteget. A megoldásnak tartalmaznia kell egy operátori felület definiálását az eszközkezelés feladatainak elvégzéséhez.

* A **IoT-megoldás kezelője** az IoT-megoldás használatával hajtja végre az eszközkezelés feladatait, különösen az eszközök csoportosítását, a konfigurációs változások kezdeményezését, például a belső vezérlőprogram frissítését, a figyelés előrehaladását és a felmerülő problémák elhárítását.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT hardver gyártója/integrátora

A következő gyakorlati tanácsok a beágyazott szoftverfejlesztés által használt hardvergyártók és integrátorok számára:

* **[Eszköz-ikrek](iot-hub-devguide-device-twins.md)implementálása:** Az eszközök ikrek lehetővé teszik a kívánt konfiguráció szinkronizálását a felhőből, valamint a jelenlegi konfiguráció és eszköz tulajdonságainak jelentését. Az eszközökhöz tartozó ikrek a beágyazott alkalmazásokon belüli megvalósításának legjobb módja az [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)-k használata. Az eszközök ikrek a legmegfelelőbbek a konfigurációhoz, mert:

    * Kétirányú kommunikáció támogatása.
    * A csatlakoztatott és a leválasztott eszközök állapotának engedélyezése.
    * Kövesse a végleges konzisztencia elvét.
    * Teljesen queriable a felhőben.

* **A Device Twin eszköz strukturálása az eszközök felügyeletéhez:** Az eszköz ikerét strukturálni kell, hogy az eszközkezelés tulajdonságai logikai csoportokba legyenek csoportosítva. Ez lehetővé teszi a konfigurációs változások elkülönítését anélkül, hogy ez hatással lenne a Twin további részeire. Hozzon létre például egy szakaszt a belső vezérlőprogram, a szoftver egy másik szakasza és a hálózati beállítások harmadik szakasza között. 

* **Az eszközök kezeléséhez hasznos eszköz-attribútumok jelentése:** Az olyan attribútumok, mint például a fizikai eszközök gyártmánya és modellje, a belső vezérlőprogram, az operációs rendszer, a sorozatszám és az egyéb azonosítók hasznosak a jelentéskészítéshez és a konfigurációs változások célzásához.

* **A jelentések állapotának és előrehaladásának fő állapotának meghatározása:** A legfelső szintű állapotokat fel kell sorolni, hogy jelenteni tudják a kezelőnek. A belső vezérlőprogram frissítése például az aktuális, a letöltés, a alkalmazás, a folyamatban és a hiba állapotot jelenti. További mezők megadása az egyes állapotokkal kapcsolatban.

## <a name="iot-solution-developer"></a>IoT-megoldás fejlesztője

Az alábbi ajánlott eljárások az Azure-alapú rendszerek IoT megoldás-fejlesztői számára:

* **[Eszköz-ikrek](iot-hub-devguide-device-twins.md)implementálása:** Az eszközök ikrek lehetővé teszik a kívánt konfiguráció szinkronizálását a felhőből, valamint a jelenlegi konfiguráció és eszköz tulajdonságainak jelentését. Az [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)-k segítségével a legjobb módszer az eszközön futó ikrek megvalósítására a Cloud Solutions-alkalmazásokon belül. Az eszközök ikrek a legmegfelelőbbek a konfigurációhoz, mert:

    * Kétirányú kommunikáció támogatása.
    * A csatlakoztatott és a leválasztott eszközök állapotának engedélyezése.
    * Kövesse a végleges konzisztencia elvét.
    * Teljesen queriable a felhőben.

* **Eszközök rendszerezése az eszköz kettős címkékkel:** A megoldásnak lehetővé kell tennie, hogy az operátor minőségi gyűrűket vagy más eszközöket határozzon meg különböző központi telepítési stratégiák, például a Kanári alapján. Az eszköz szervezete a megoldáson belül valósítható meg az eszközök Twin-címkék és a [lekérdezések](iot-hub-devguide-query-language.md)használatával. Az eszköz szervezete számára szükséges, hogy biztonságosan és pontosan engedélyezze a konfigurációt.

* **[Automatikus eszköz-konfigurációk](./iot-hub-automatic-device-management.md)implementálása:** Az eszközök automatikus konfigurálásával a IoT-eszközök nagy készletei számára is telepítheti és figyelheti a konfigurációs módosításokat.

   Az eszközök automatikus beállítása az eszközökön az ikrek számára a **célként megadott feltételek alapján,** amely az eszköz Twin-címkék vagy a jelentett tulajdonságok lekérdezése. A **célként megadott tartalom** a kívánt tulajdonságok halmaza, amely a megcélzott eszközön található ikreken belül lesz meghatározva. A célként megadott tartalomnak a IoT hardver gyártója/integrátora által meghatározott eszköz kettős szerkezettel kell igazodnia. A **metrikák** az eszköz Twin jelentett tulajdonságaiban található lekérdezések, és a IoT hardver gyártója/integrátora által definiált eszköz kettős struktúrával is összhangban kell lenniük.

   Az automatikus eszköz-konfigurációk a konfiguráció létrehozása után első alkalommal futnak, majd öt percenként. Emellett a IoT Hub az eszköz két műveletét is kihasználják olyan sebességgel, amely soha nem lépi túl az eszköz kettős olvasási és frissítési [korlátait](iot-hub-devguide-quotas-throttling.md) .

* **A [Device kiépítési szolgáltatás](../iot-dps/how-to-manage-enrollments.md)használata:** a megoldás-fejlesztőknek az eszköz kiépítési szolgáltatását kell használniuk az eszközhöz tartozó Twin-címkék új eszközökhöz való hozzárendeléséhez, így azok automatikusan az adott címkével ellátott, az ikreket megcélzó **automatikus eszköz-konfigurációkhoz** lesznek konfigurálva. 

## <a name="iot-solution-operator"></a>IoT-megoldás operátora

Az alábbi ajánlott eljárások az Azure-ra épülő IoT-megoldást használó IoT megoldás-kezelők számára:

* **Eszközök rendszerezése felügyeletre:** A IoT-megoldásnak meg kell határoznia vagy engedélyeznie kell a minőségi gyűrűk vagy más eszközök készletét különféle központi telepítési stratégiák (például a Kanári) alapján. A rendszer az eszközök készleteit fogja használni a konfigurációs változások kiszűréséhez, valamint az egyéb, az eszközökön elérhető felügyeleti műveletek végrehajtásához.

* **Konfigurációs módosítások elvégzése szakaszos kiépítéssel:**  A fokozatos üzembe helyezés egy átfogó folyamat, amelynek során az operátor módosításokat telepít a IoT-eszközök szélesebb körébe. A cél az, hogy fokozatosan végezze el a módosításokat, hogy csökkentse a nagy léptékű törések változásának kockázatát.  Az operátornak a megoldás felületét kell használnia az [automatikus eszköz konfigurációjának](./iot-hub-automatic-device-management.md) létrehozásához, és a célcsoport-kezelési feltételnek az eszközök kezdeti készletét (például egy Kanári-csoportot) kell megcéloznia. Az operátornak ezután ellenőriznie kell a konfigurációs változást az eszközök kezdeti készletében.

   Az ellenőrzés befejeződése után az operátor frissíti az automatikus eszköz konfigurációját, hogy az tartalmazza az eszközök nagyobb készletét. Az operátornak azt is meg kell adnia, hogy a konfiguráció prioritása nagyobb legyen, mint a jelenleg az adott eszközökre irányuló többi konfiguráció. A kivezetést az automatikus eszköz-konfiguráció által jelentett mérőszámok használatával lehet figyelni.

* **Visszaállítások végrehajtása hibák vagy helytelen konfiguráció esetén:**  A hibákat vagy helytelen konfigurációkat okozó automatikus eszköz-konfiguráció visszaállítható úgy, hogy megváltoztatja a **célzási feltételt** , így az eszközök már nem felelnek meg a célcsoport-kezelési feltételnek. Győződjön meg arról, hogy az alacsonyabb prioritású eszközök egy másik automatikus konfigurációja továbbra is megcélozza ezeket az eszközöket. Ellenőrizze, hogy a visszaállítás sikeres volt-e a metrikák megtekintésével: a visszaállított konfigurációnak többé nem kell megjelenítenie a nem megcélzott eszközök állapotát, és a második konfiguráció metrikáinak tartalmazniuk kell a továbbra is megcélzott eszközök számát.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan implementálhatja az eszközök ikreket, [és hogyan használhatja a IoT hub eszközeit](iot-hub-devguide-device-twins.md).

* Útmutató az automatikus IoT létrehozásához, frissítéséhez vagy törléséhez a nagy [léptékű eszközök konfigurálása és figyelése](./iot-hub-automatic-device-management.md).

* A belső vezérlőprogram frissítési mintájának implementálása az eszköz-ikrek és az automatikus eszköz-konfigurációk használatával az [oktatóanyagban: eszköz belső vezérlőprogram-frissítési folyamatának implementálása](tutorial-firmware-update.md).