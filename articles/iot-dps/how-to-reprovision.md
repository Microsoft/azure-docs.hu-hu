---
title: Eszközök újraépítése az Azure IoT Hub Device Provisioning Serviceban
description: Megtudhatja, hogyan hozhatja létre az eszközöket az eszközök kiépítési szolgáltatásával (DPS), és miért kell ezt megtennie.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0ded494debab19daa15a953715b1ab7b0b10ad18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74974904"
---
# <a name="how-to-reprovision-devices"></a>Eszközök újraépítése

Egy IoT-megoldás életciklusa során gyakori az eszközök áthelyezése a IoT-hubok között. Az áthelyezés okai a következők lehetnek:

* Földrajzi **hely: mivel**az eszköz a helyek között mozog, a hálózati késést úgy javítja ki, hogy az eszközt az egyes helyekhez közelebb helyezi át egy IoT hubhoz.

* **Több-bérlő**: egy eszköz felhasználható ugyanabban a IoT-megoldásban, de új ügyfélhez vagy vásárlói helyhez rendelhető hozzá vagy bérletbe. Ezt az új ügyfelet egy másik IoT hub használatával lehet kiszolgálni.

* **Megoldás módosítása**: egy eszköz áthelyezhető egy új vagy frissített IoT-megoldásba. Ez az ismételt hozzárendelés megkövetelheti, hogy az eszköz kommunikáljon egy olyan új IoT hubhoz, amely más háttér-összetevőkhöz van csatlakoztatva. 

* **Karanténba helyezés**: hasonló a megoldás módosításához. Olyan eszköz, amely hibásan működik, sérült vagy elavult, újra hozzárendelhető egy IoT hubhoz, ahol az összes művelet frissíthető, és visszatérhet a megfelelőséghez. Ha az eszköz megfelelően működik, a rendszer visszatelepíti a főhubhoz.

Az újraépítéssel kapcsolatos részletesebb áttekintésért tekintse meg az eszközök újraépítésével kapcsolatos [fogalmakat IoT hub](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>A regisztráció kiosztási szabályzatának konfigurálása

A foglalási házirend határozza meg, hogy a rendszer hogyan rendeli hozzá a beléptetéshez társított eszközöket a IoT hubhoz az újbóli kiépítése után.

Az alábbi lépéseket követve konfigurálhatja egy eszköz regisztrálásának foglalási szabályzatát:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a Device kiépítési szolgáltatás példányához.

2. Kattintson a **regisztrációk kezelése**lehetőségre, majd kattintson arra a beléptetési csoportra vagy egyéni regisztrációra, amelyet be szeretne állítani az újbóli kiépítéshez. 

3. Válassza ki az alábbi kiosztási szabályzatok egyikét a **válassza ki, hogyan szeretné hozzárendelni az eszközöket**a központokhoz:

    * **Legalacsonyabb késés**: Ez a szabályzat olyan eszközöket rendel hozzá a csatolt IoT hubhoz, amelyek az eszköz és a IoT hub közötti legalacsonyabb késésű kommunikációt eredményezik. Ez a beállítás lehetővé teszi, hogy az eszköz a legközelebbi IoT-hubhoz kommunikáljon a hely alapján. 
    
    * **Egyenletesen súlyozott eloszlás**: Ez a szabályzat az egyes csatolt IoT-hubhoz rendelt foglalási súlyozás alapján osztja el az eszközöket a társított IoT-hubok között. Ez a szabályzat lehetővé teszi, hogy az adott hubokon beállított foglalási súlyok alapján terheléselosztást biztosítson az eszközök számára a társított hubok csoportján belül. Ha csak egy IoT Hub szeretné kiépíteni az eszközöket, ezt a beállítást javasoljuk. Ez az alapértelmezett beállítás. 
    
    * **Statikus konfiguráció**: ehhez a Szabályzathoz szükség van egy kívánt IoT hub a kiépíthető eszköz beléptetési bejegyzésében szereplő listára. Ez a szabályzat lehetővé teszi egy adott IoT hub kijelölését, amelyhez eszközöket szeretne hozzárendelni.

4. A **válassza ki a IoT hubok ezt a csoportot hozzá lehet rendelni**, majd válassza ki azokat a társított IoT hubokat, amelyeket a foglalási szabályzatba kíván foglalni. Opcionálisan hozzáadhat egy új csatolt IOT hubot az **új IoT hub csatolása** gomb használatával.

    A **legalacsonyabb késési** kiosztási szabályzattal a kiválasztott hubok a késés kiértékelésében lesznek felszámítva, hogy meghatározzák az eszköz hozzárendelésének legközelebbi elosztóját.

    Az **egyenletesen súlyozott terjesztési** kiosztási szabályzattal az eszközök terheléselosztása a konfigurált hubokon történik a kiválasztott kiosztási súlyok és a jelenlegi eszközök terhelése alapján.

    A **statikus konfiguráció** kiosztási házirendjével válassza ki azt az IoT hub-t, amelyhez hozzá szeretné rendelni az eszközöket.

4. Kattintson a **Save (Mentés**) gombra, vagy folytassa a következő szakasszal a kiépítési házirend beállításához.

    ![Regisztráció kiosztási szabályzatának kiválasztása](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Az újraépítési szabályzat beállítása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a Device kiépítési szolgáltatás példányához.

2. Kattintson a **regisztrációk kezelése**lehetőségre, majd kattintson arra a beléptetési csoportra vagy egyéni regisztrációra, amelyet be szeretne állítani az újbóli kiépítéshez.

3. Az **adja meg, hogyan kívánja kezelni az eszköz adatait egy másik IoT-hubhoz**, válassza az alábbi újraépítési házirendek egyikét:

    * Az **adat ismételt kiépítése és áttelepítése**: Ez a házirend akkor lép működésbe, ha a beléptetési bejegyzéshez társított eszközök új létesítési kérelmet küldenek be. A beléptetési bejegyzés konfigurációjától függően előfordulhat, hogy az eszköz máshoz van rendelve egy másik IoT hubhoz. Ha az eszköz megváltoztatja a IoT hubokat, a rendszer eltávolítja az eszköz regisztrációját a kezdeti IoT hubhoz. A rendszer az adott kezdeti IoT hub összes Eszközállapot-adatát áttelepíti az új IoT hubhoz. Az áttelepítés során a rendszer az eszköz állapotát **hozzárendelésként** fogja jelenteni.

    * A **kezdeti konfiguráció újbóli létrehozása és alaphelyzetbe állítása**: Ez a házirend akkor lép működésbe, ha a beléptetési bejegyzéshez társított eszközök új létesítési kérést küldenek. A beléptetési bejegyzés konfigurációjától függően előfordulhat, hogy az eszköz máshoz van rendelve egy másik IoT hubhoz. Ha az eszköz megváltoztatja a IoT hubokat, a rendszer eltávolítja az eszköz regisztrációját a kezdeti IoT hubhoz. A kiépítési szolgáltatás példánya által az eszköz kiosztásakor fogadott kezdeti konfigurációs információk az új IoT hub számára. Az áttelepítés során a rendszer az eszköz állapotát **hozzárendelésként**fogja jelenteni.

4. A **Mentés** gombra kattintva engedélyezheti az eszköz újraépítését a módosítások alapján.

    ![Regisztráció kiosztási szabályzatának kiválasztása](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Kiépítési kérelem küldése az eszközről

Ahhoz, hogy az eszközök újra kiálljanak az előző szakaszban megadott konfigurációs változások alapján, ezeknek az eszközöknek újra kell telepíteniük a kiépítést. 

Az, hogy az eszköz milyen gyakran küldjön üzembe helyezési kérést, a forgatókönyvtől függ. Javasoljuk azonban, hogy az eszközök kiépítési kérést küldjön a kiépítési szolgáltatási példánynak az újraindításkor, és [támogassa az igény](../iot-hub/iot-hub-devguide-direct-methods.md) szerinti kiépítés manuális indítását. A kiépítés a [kívánt tulajdonság](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example)beállításával is elindítható. 

A beléptetési bejegyzés újraépítési szabályzata határozza meg, hogy az eszköz kiépítési szolgáltatási példánya hogyan kezeli ezeket a kiépítési kérelmeket, és ha az eszköz állapotának áttelepítését át kell telepíteni az Újraépítés során. Ugyanazok a szabályzatok érhetők el az egyes regisztrációk és beléptetési csoportok esetében:

Például a kiépítési kérelmek egy eszközről történő küldésének kódja a rendszerindítási folyamat során: [szimulált eszköz automatikus kiépítés](quick-create-simulated-device.md).


## <a name="next-steps"></a>További lépések

- További információ: [IoT hub eszköz](concepts-device-reprovision.md) újraépítése 
- További részletekért lásd: [az előzőleg automatikusan kiépített eszközök](how-to-unprovision-devices.md) kiépítése. 











