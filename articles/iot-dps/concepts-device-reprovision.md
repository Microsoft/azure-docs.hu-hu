---
title: Azure IoT Hub Device Provisioning Service – eszközökhöz kapcsolódó fogalmak
description: Az Azure IoT Hub Device Provisioning Service (DPS) eszköz újraépítési fogalmait ismerteti
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9653a584382584d982c55008a6e8547de28691b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842852"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>A IoT Hub eszköz újraépítésével kapcsolatos fogalmak

Egy IoT-megoldás életciklusa során gyakran előfordul, hogy az eszközöket az IoT-hubok között helyezi át. Az áthelyezés okai a következők lehetnek:

* **Térinformatikai/GeoLatency**: mivel az eszköz a helyszínek között mozog, a hálózati késést úgy javítja ki, hogy az eszközt áttelepíti egy szorosabb IoT-hubhoz.

* **Több-bérlő**: egy eszköz felhasználható ugyanabban a IoT-megoldásban, és új ügyfélhez vagy vásárlói helyhez rendelhető hozzá. Ezt az új ügyfelet egy másik IoT hub használatával lehet kiszolgálni.

* **Megoldás módosítása**: egy eszköz áthelyezhető egy új vagy frissített IoT-megoldásba. Az ismételt hozzárendelés miatt előfordulhat, hogy az eszköz kommunikálni fog egy olyan új IoT hubhoz, amely más háttér-összetevőkhöz van csatlakoztatva.

* **Karanténba helyezés**: hasonló a megoldás módosításához. Egy olyan eszköz, amely hibásan működik, sérült vagy elavult, újra hozzárendelhető egy IoT hubhoz, amely csak a frissítésre és a megfelelőség visszaszerzésére használható. Miután az eszköz megfelelően működik, a rendszer áttelepíti a főhubhoz.

Az eszközök kiépítési szolgáltatáson belüli támogatásának újraépítése ezen igényeknek megfelelően történik. Az eszközök automatikusan hozzárendelhetők az új IoT-hubokhoz az eszköz beléptetési bejegyzésén konfigurált újraépítési házirend alapján.

## <a name="device-state-data"></a>Eszköz állapotának adatvédelme

Az eszköz állapotával kapcsolatos adatmennyiség az [eszköz Twin](../iot-hub/iot-hub-devguide-device-twins.md) és az eszköz képességeiből tevődik össze. Ezeket az adatkészleteket az eszköz kiépítési szolgáltatásának példánya és a IoT hub tárolja, amelyhez az eszköz hozzá van rendelve.

![Diagram, amely bemutatja, hogyan működik a kiépítés az eszköz kiépítési szolgáltatásával.](./media/concepts-device-reprovisioning/dps-provisioning.png)

Ha egy eszközt először kiépít egy eszköz kiépítési szolgáltatásának példányával, a következő lépéseket kell végrehajtani:

1. Az eszköz kiépítési kérelmet küld egy eszköz kiépítési szolgáltatásának példányára. A szolgáltatási példány egy beléptetési bejegyzés alapján hitelesíti az eszköz identitását, és létrehozza az Eszközállapot-adatok kezdeti konfigurációját. A szolgáltatási példány a beléptetési konfiguráció alapján hozzárendeli az eszközt egy IoT hubhoz, és visszaadja az IoT hub-hozzárendelést az eszközhöz.

2. A kiépítési szolgáltatás példánya a kiindulási eszköz állapotával kapcsolatos összes adat másolatát tartalmazza a hozzárendelt IoT hubhoz. Az eszköz csatlakozik a hozzárendelt IoT hubhoz, és megkezdi a műveleteket.

Idővel a IoT hub eszköz állapotára vonatkozó adatok frissítése az [eszköz műveletei](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) és a [háttérbeli műveletek](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations)alapján lehetséges. Az eszköz kiépítési szolgáltatási példányában tárolt első Eszközállapot-információ érintetlen marad. Ez a nem érintett Eszközállapot-állapot a kezdeti konfiguráció.

![Kiépítés az eszköz kiépítési szolgáltatásával](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

A forgatókönyvtől függően, ahogy az eszköz az IoT-hubok között mozog, előfordulhat, hogy az eszköz állapotát frissíteni kell az előző IoT-hubhoz az új IoT hubhoz. Ezt az áttelepítést a szabályzatok az eszközök kiépítési szolgáltatásban való újraépítésével lehet támogatni.

## <a name="reprovisioning-policies"></a>Szabályzatok újraépítése

A forgatókönyvtől függően az eszköz általában egy kérést küld egy kiépítési szolgáltatási példányra az újraindításkor. Emellett támogatja az igény szerinti kiépítés manuális indításának módszerét is. A beléptetési bejegyzés újraépítési szabályzata határozza meg, hogy az eszköz kiépítési szolgáltatási példánya hogyan kezeli ezeket a kiépítési kérelmeket. A házirend azt is meghatározza, hogy az eszköz állapotát át kell-e telepíteni az Újraépítés során. Ugyanazok a szabályzatok érhetők el az egyes regisztrációk és beléptetési csoportok esetében:

* **Adatok újbóli kiépítése és áttelepítése**: Ez a szabályzat az új beléptetési bejegyzések alapértelmezett értéke. Ez a szabályzat akkor lép működésbe, ha a beléptetési bejegyzéshez társított eszközök új kérelmet küldenek be (1). A beléptetési bejegyzés konfigurációjától függően előfordulhat, hogy az eszköz máshoz van rendelve egy másik IoT hubhoz. Ha az eszköz megváltoztatja a IoT hubokat, a rendszer eltávolítja az eszköz regisztrációját a kezdeti IoT hubhoz. A rendszer az adott kezdeti IoT hub frissített Eszközállapot-információit áttelepíti az új IoT hubhoz (2). Az áttelepítés során a rendszer az eszköz állapotát **hozzárendelésként** fogja jelenteni.

    ![Diagram, amely azt mutatja, hogy egy házirend akkor lép működésbe, amikor a beléptetési bejegyzéshez társított eszközök új kérelmet küldenek.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* A **kezdeti konfiguráció újbóli létrehozása és alaphelyzetbe állítása**: Ez a házirend akkor lép működésbe, ha a beléptetési bejegyzéshez társított eszközök beküldenek egy új létesítési kérelmet (1). A beléptetési bejegyzés konfigurációjától függően előfordulhat, hogy az eszköz máshoz van rendelve egy másik IoT hubhoz. Ha az eszköz megváltoztatja a IoT hubokat, a rendszer eltávolítja az eszköz regisztrációját a kezdeti IoT hubhoz. A kiépítési szolgáltatás példánya által az eszköz kiosztásakor kapott kezdeti konfigurációs információk az új IoT hub (2) számára lettek kiépítve. Az áttelepítés során a rendszer az eszköz állapotát **hozzárendelésként** fogja jelenteni.

    Ezt a házirendet gyakran használják a gyári beállítások visszaállítására az IoT-hubok módosítása nélkül.

    ![Diagram, amely bemutatja, hogyan hajt végre egy házirend a beléptetési bejegyzéshez társított eszközök új létesítési kérelem elküldésekor.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Soha ne telepítse újra**: az eszközt soha nem rendeli hozzá másik hubhoz. Ez a szabályzat a visszamenőleges kompatibilitás kezelésére szolgál.

### <a name="managing-backwards-compatibility"></a>Visszamenőleges kompatibilitás kezelése

Szeptember 2018 előtt a IoT hubokhoz tartozó eszközök hozzárendelései ragadós viselkedéssel rendelkeztek. Ha egy eszköz az üzembe helyezési folyamat során visszaállt, csak ugyanahhoz az IoT-hubhoz lesz hozzárendelve.

Azon megoldások esetében, amelyek ettől a viselkedéstől függenek, a kiépítési szolgáltatás visszamenőleges kompatibilitást is tartalmaz. Ez a viselkedés a következő feltételeknek megfelelően karbantartás alatt áll az eszközökön:

1. Az eszközök egy API-verzióval csatlakoznak, mielőtt rendelkezésre állnak a natív újraépítési támogatás a Device kiépítési szolgáltatásban. Tekintse át az alábbi API-táblázatot.

2. Az eszközök beléptetési bejegyzése nem rendelkezik a rájuk beállított újraépítési házirenddel.

Ez a kompatibilitási funkció biztosítja, hogy a korábban telepített eszközök ugyanazt a viselkedést tapasztalják, mint a kezdeti tesztelés során. Az előző viselkedés megőrzése érdekében ne mentsen egy újraépítési szabályzatot ezekre a regisztrációra. Ha be van állítva egy újraépítési szabályzat, az újraépítési szabályzat elsőbbséget élvez a viselkedéssel szemben. Azáltal, hogy az újbóli kiépítési házirend elsőbbséget élvez, az ügyfelek az eszköz működését anélkül frissíthetik, hogy el kellene végezni az eszköz rendszerképének módosítását.

A következő folyamatábra segít megmutatni, hogy mikor van jelen a viselkedés:

![visszamenőleges kompatibilitási folyamatábra](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

A következő táblázat az API-verziókat mutatja be, mielőtt elérhetővé tenné az eszköz kiépítési szolgáltatásának natív újraépítési támogatását:

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 és korábbi verziók](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 és korábbi verziók](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 és korábbi verziók](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 vagy korábbi](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 vagy korábbi](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 vagy korábbi](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Ezek az értékek és hivatkozások valószínűleg változnak. Ez csak egy helyőrző, amely meghatározza, hogy a verziók hogyan határozhatók meg egy ügyfél és a várt verziók alapján.

## <a name="next-steps"></a>Következő lépések

* [Eszközök újraépítése](how-to-reprovision.md)
