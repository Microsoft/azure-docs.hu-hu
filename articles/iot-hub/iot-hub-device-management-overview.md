---
title: Az Azure IoT Hub-vel való eszközkezelés áttekintése | Microsoft Docs
description: Az Azure IoT hu – a nagyvállalati eszközök életciklusának és az eszközkezelés mintázatának áttekintése (például, újraindítás, gyári beállítások visszaállítása, belső vezérlőprogram frissítése, konfigurálás, eszköz ikrek, lekérdezések, feladatok).
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 9ff8ebeaf7c4a219bc403c81dfea62f4657a1fd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148341"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Az IoT Hub-eszközfelügyelet áttekintése

Az Azure IoT Hub által biztosított szolgáltatások és bővíthetőségi modell segítségével az eszköz- és háttérrendszer-fejlesztők hatékony eszközfelügyeleti megoldásokat építhetnek ki. Az eszközök köre a korlátozott érzékelőktől és az egycélú mikrovezérlőktől az eszközcsoportok kommunikációjának továbbítását végző, nagyteljesítményű átjárókig terjed.  Emellett az IoT-műveletek használati módja és követelményei az egyes iparágak esetében jelentősen eltér.  Ezen eltérések ellenére az Azure IoT Hub-eszközfelügyelet az eszközök és végfelhasználók változatos együttesének igényeihez igazítva biztosítja a szükséges képességeket, mintákat és kódelemtárakat.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A vállalati IoT-megoldások sikeres létrehozásának fontos része annak a stratégiának a megalkotása, amely meghatározza a kezelők számára a folyamatos eszközfelügyelet módját. Az IoT-kezelőknek olyan egyszerű és megbízható eszközökre és alkalmazásokra van szükségük, amelyek segítségével a feladataik stratégiai szempontjaira koncentrálhatnak. Ez a cikk a következő információkat tartalmazza:

* Az Azure IoT Hub-eszközfelügyelet rövid áttekintése.
* Az általános eszközfelügyeleti alapelvek leírása.
* Az eszközök életciklusának ismertetése.
* Az általános eszközfelügyeleti minták áttekintése.

## <a name="device-management-principles"></a>Az eszközfelügyelet alapelvei

Az IoT alkalmazása egyedi eszközfelügyeleti kihívásokat hoz magával, a következő alapelveket pedig a vállalati szintű megoldások mindegyikének magában kell foglalnia:

![Az eszközfelügyelet alapelveinek ábrája](media/iot-hub-device-management-overview/image4.png)

* **Méretezés és automatizálás**: Az IoT-megoldások olyan egyszerű eszközök alkalmazását igénylik, amelyek képesek a rutinfeladatok automatizálására, valamint amelyekkel eszközök millióinak kezelése oldható meg viszonylag kis kezelői létszámmal. A kezelők napról napra azt várják, hogy távolról, kötegelten kezelhessék az eszközök működését, és a rendszer csak akkor riassza őket, ha olyan hiba történt, amelynek elhárításához a közvetlen beavatkozásukra van szükség.

* **Nyitottság és kompatibilitás**: az eszközök ökoszisztémája rendkívül szerteágazó. A felügyeleti eszközöket úgy kell kialakítani, hogy alkalmasak legyenek nagy mennyiségű eszközosztály, platform és protokoll befogadására. A kezelőknek képesnek kell lenniük számos eszköztípus kezelésére, a legkorlátozottabb, beágyazott egyutas lapkáktól kezdve a nagyteljesítményű, teljeskörű funkciókkal ellátott számítógépekig.

* **A környezet figyelése**: az IoT-környezetek dinamikusak és állandóan változnak. A szolgáltatás megbízhatósága létfontosságú. Az eszközfelügyeleti műveleteknek a következő tényezőket kell figyelembe venniük annak biztosításához, hogy a karbantartási állásidő ne befolyásolja a létfontosságú üzleti tevékenységeket, illetve ne idézzen elő veszélyes feltételeket:

    * SLA karbantartási időszakok
    * A hálózat és a tápellátás állapota
    * Használatban lévő feltételek
    * Az eszköz földrajzi helyei

* **Sok szerepkör kiszolgálása**: az IoT-műveletekhez tartozó szerepkörök egyedi munkafolyamatainak és folyamatainak támogatása rendkívül fontos. Az üzemeltetést végző stábnak zökkenőmentesen kell együttműködnie a belső informatikai részlegek által meghatározott korlátozásokkal.  Fenntartható módot kell találniuk továbbá a valós idejű eszközművelet-információk beillesztésére a felettesi és üzletfelügyeleti szerepkörökbe.

## <a name="device-lifecycle"></a>Eszközök életciklusa
Egyes általános eszközfelügyeleti szakaszok minden vállalati IoT-projektben megjelennek. Az Azure IoT-ben az eszközök életciklusának öt szakaszát különböztetjük meg:

![Az Azure IoT-eszközök életciklusának öt fázisa: tervezés, üzembe helyezés, konfigurálás, figyelés, kivonás](./media/iot-hub-device-management-overview/image5.png)

A teljes megoldás megvalósításához számos eszközkezelői követelménynek kell teljesülnie ezen az öt szakaszon belül:

* **Tervezés**: Lehetővé teszi a kezelők számára olyan eszközmetaadat-séma létrehozását, amellyel egyszerű és pontos lekérdezés végezhető el egy adott eszközcsoporton a tömeges felügyeleteken. Az ikereszközök használatával e metaadatokat címkék és tulajdonságok formájában tárolhatja.
  
    *További olvasnivalók*: 
    * [Ikereszközök – első lépések](iot-hub-node-node-twin-getstarted.md)
    * [Az ikereszközök áttekintése](iot-hub-devguide-device-twins.md)
    * [Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)
    * [Ajánlott eljárások az eszközök konfigurálásához egy IoT-megoldáson belül](iot-hub-configuration-best-practices.md)

* **Üzembe helyezés**: Lehetővé teszi az új IoT Hub-eszközök biztonságos üzembe helyezését, valamint a kezelők számára az eszközök képességeinek azonnali elsajátítását.  Az IoT Hub-identitásjegyzék alkalmazásával rugalmas eszközidentitások és -hitelesítő adatok hozhatók létre, illetve egy feladat használatával ez a művelet tömegesen is végrehajtható. Az eszközök felépítését úgy végezze el, hogy azok képességei és állapotai az ikereszköz tulajdonságaiban nyomon követhetőek legyenek.
  
    *További olvasnivalók*: 
    * [Eszközidentitások kezelése](iot-hub-devguide-identity-registry.md)
    * [Az eszközök identitásának tömeges kezelése](iot-hub-bulk-identity-mgmt.md)
    * [Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)
    * [Ajánlott eljárások az eszközök konfigurálásához egy IoT-megoldáson belül](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)

* **Konfigurálás**: az eszközök kötegelt konfigurációmódosításainak és belső vezérlőprogramjuk frissítéseinek megkönnyítése üzemi állapotuk és biztonságuk megőrzésével. A kívánt tulajdonságok, illetve közvetlen módszerek és szórásos feladatok használatával ezek az eszközfelügyeleti műveletek tömegesen is végrehajthatók.
  
    *További olvasnivalók*:
    * [Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)
    * [IoT-eszközök konfigurálása és figyelése nagy méretekben](./iot-hub-automatic-device-management.md)
    * [Ajánlott eljárások az eszközök konfigurálásához egy IoT-megoldáson belül](iot-hub-configuration-best-practices.md)

* **Figyelés**: A teljes eszközgyűjtemény és a folyamatban lévő műveletek állapotának figyelése, valamint a kezelők riasztása az esetlegesen figyelmet érdemlő problémák kapcsán.  Az ikereszközök alkalmazásával az eszközök képesek a valós idejű működési feltételek és a frissítési műveletek állapotának jelentésére. Nagyteljesítményű irányítópulti jelentések hozhatók létre, amelyek az ikereszköz-lekérdezések használatával felszínre hozzák az azonnali intézkedést igénylő problémákat.
  
    *További olvasnivalók*: 
    * [Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)
    * [Az ikrek, a feladatok és az üzenet-útválasztás IoT Hub lekérdezési nyelve](iot-hub-devguide-query-language.md)
    * [IoT-eszközök konfigurálása és figyelése nagy méretekben](./iot-hub-automatic-device-management.md)
    * [Ajánlott eljárások az eszközök konfigurálásához egy IoT-megoldáson belül](iot-hub-configuration-best-practices.md)

* **Kivonás: az** eszközök lecserélése vagy leszerelése meghibásodás után, frissítési ciklus vagy a szolgáltatás élettartama végén.  Az ikereszközök használatával az eszközinformációk abban az esetben is megőrizhetők, ha a fizikai eszközt kicserélik vagy a kivonás során archiválják. Az IoT Hub-identitásjegyzék alkalmazásával lehetséges az eszközidentitások és -hitelesítő adatok biztonságos visszahívása.
  
    *További olvasnivalók*: 
    * [Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)
    * [Eszközidentitások kezelése](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Eszközfelügyeleti minták

Az IoT Hub az alábbi eszközfelügyeleti mintákat engedélyezi. Az [eszközfelügyeleti oktatóanyagok](iot-hub-node-node-device-management-get-started.md) részletesebben is bemutatják e minták kiterjesztését valós helyzetekre, illetve új minták létrehozását az alapminták alapján.

* **Újraindítás**: a háttérbeli alkalmazás egy közvetlen módszerrel értesíti az eszközt arról, hogy újraindítást kezdeményezett.  Az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz újraindítási állapotát.
  
    ![Az eszközfelügyelet újraindítási mintájának ábrája](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Gyári beállítások visszaállítása**: a háttérbeli alkalmazás közvetlen módszerrel értesíti az eszközt arról, hogy a gyári beállítások visszaállítását kezdeményezte. Az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz gyári visszaállítási állapotát.
  
    ![Az eszközfelügyelet eszköz gyári alaphelyzetbe állítási mintájának ábrája](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Konfiguráció**: a háttérbeli alkalmazás a kívánt tulajdonságokat használja az eszközön futó szoftverek konfigurálásához. Az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz konfigurálási állapotát.
  
    ![Az eszközfelügyelet konfigurációs mintájának ábrája](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Belső vezérlőprogram frissítése**: a háttér-alkalmazás automatikus Eszközkezelő-konfigurációt használ a frissítés fogadására szolgáló eszközök kiválasztásához, hogy tájékoztassa azokat az eszközöket, amelyeken a frissítés megtalálhatók, valamint figyelje a frissítési folyamatot. Az eszköz többlépéses folyamatot kezdeményez a belső vezérlőprogram rendszerképének letöltéséhez, ellenőrzéséhez és alkalmazásához, majd újraindítja az eszközt, mielőtt újracsatlakozik a IoT Hub szolgáltatáshoz. A több lépésből álló folyamat során az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz előrehaladását és állapotát.
  
    ![Az eszközfelügyelet belsővezérlőprogram-frissítési mintájának ábrája](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Jelentéskészítési folyamat és állapot**: a megoldás háttérbe állítása eszköz kettős lekérdezéseket futtat az eszközökön keresztül, hogy jelentést készítsen az eszközökön futó műveletek állapotáról és előrehaladásáról.
  
    ![Az eszközfelügyelet előrehaladási és állapotmeghatározási jelentéskészítési mintájának ábrája](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Következő lépések

Az IoT Hub eszközfelügyeleti szolgáltatásai által biztosított képességek, minták és kódtárak használatával olyan IoT-alkalmazások hozhatók létre, amelyek az eszköz teljes életciklusában képesek megfelelni a vállalati IoT-kezelők követelményeinek.

Ha szeretne részletesebben is megismerkedni az IoT Hub eszközfelügyeleti funkcióival, tekintse meg az [eszközfelügyelet első lépéseit](iot-hub-node-node-device-management-get-started.md) bemutató oktatóanyagot.