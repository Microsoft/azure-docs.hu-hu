---
title: Azure IoT Hub Device Provisioning Service – Eszközfogalmak
description: A Device Provisioning Service (DPS) Azure IoT Hub eszköz-újraépítési fogalmait ismerteti
author: wesmc7777
ms.author: wesmc
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: fbc83ec62c10fae00e371cd9ad95cf2860495fad
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575768"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub eszköz-újraosztás fogalmai

Az IoT-megoldások életciklusa során gyakran áthelyezik az eszközöket az IoT Hubok között. Ennek az áthelyezésnek az okai a következők lehetnek:

* **Földrajzi hely/ GeoLatency:** Az eszközök helyek közötti áthelyezése esetén a hálózati késést javítja, ha az eszközt egy közelebbi IoT Hubra migrálják.

* **Több-bérlős:** Az eszközök ugyanazon IoT-megoldáson belül használhatók, és új ügyfélhez vagy ügyfélhelyhez is hozzárendelve lehetnek. Az új ügyfél kiszolgálása egy másik IoT Hubon keresztül is előfordulhat.

* **Megoldás módosítása:** Az eszközök áthelyezhetők egy új vagy frissített IoT-megoldásba. Az ismételt hozzárendelés megkövetelheti, hogy az eszköz kommunikáljon egy olyan új IoT Hubbal, amely más háttérösszetevőkhöz csatlakozik.

* **Karantén:** Megoldásváltozáshoz hasonlóan. Előfordulhat, hogy egy hibásan működő, feltört vagy elavult eszközt olyan IoT Hubhoz lehet hozzárendelni, amely csak frissíteni tud, és csak a megfelelőséget tudja visszahozni. Miután az eszköz megfelelően működik, azt a rendszer visszatelepíti a főközpontba.

A Device Provisioning Service-beli újraépítési támogatás ezeket az igényeket kielégíti. Az eszközök automatikusan hozzárendelt új IoT Hubhoz az eszköz regisztrációs bejegyzésén konfigurált újrakiosztási szabályzat alapján.

## <a name="device-state-data"></a>Eszközállapot-adatok

Az eszközállapot-adatok az ikereszköz [és](../iot-hub/iot-hub-devguide-device-twins.md) az eszköz képességeiből állnak. Ezek az adatok a Device Provisioning Service-példányban és az IoT Hubban vannak tárolva, amelyekhez az eszköz hozzá van rendelve.

![A Device Provisioning Service-sel való kiépítés működését bemutató ábra.](./media/concepts-device-reprovisioning/dps-provisioning.png)

Amikor egy eszközt először kiépít egy Device Provisioning Service-példánnával, a következő lépéseket kell követnie:

1. Az eszköz kiépítési kérelmet küld egy Device Provisioning Service-példánynak. A szolgáltatáspéldány egy regisztrációs bejegyzés alapján hitelesíti az eszközidentitást, és létrehozza az eszközállapot-adatok kezdeti konfigurációját. A szolgáltatáspéldány a regisztrációs konfiguráció alapján hozzárendeli az eszközt egy IoT Hubhoz, és visszaadja az IoT Hub-hozzárendelést az eszköznek.

2. A kiépítési szolgáltatáspéldány másolatot ad a kezdeti eszközállapot-adatokról a hozzárendelt IoT Hubnak. Az eszköz csatlakozik a hozzárendelt IoT Hubhoz, és megkezdi a műveleteket.

Az IoT Hubon található eszközállapot-adatok idővel [](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) frissíthetők az eszközműveletekkel és a [háttérműveletekkel.](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations) A Device Provisioning Service-példányban tárolt kezdeti eszközállapot-információk változatlanok maradnak. Ezek az eszközállapot-adatok a kezdeti konfigurációk.

![Kiépítés a Device Provisioning Service-sel](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

A forgatókönyvtől függően, amikor egy eszköz mozog az IoT Hubok között, szükség lehet az előző IoT Hubon frissített eszközállapot áttelepítésére is az új IoT Hubra. Ezt a migrálást a Device Provisioning Service-beli szabályzatok újraépítése támogatja.

## <a name="reprovisioning-policies"></a>Szabályzatok újraosztása

A forgatókönyvtől függően az eszköz általában újraindításkor kérést küld egy kiépítési szolgáltatáspéldánynak. Emellett támogatja az igény szerinti kiépítés manuális aktiválásának módszerét is. A regisztrációs bejegyzésre vonatkozó újraépítési szabályzat határozza meg, hogy az eszközregisztrációs szolgáltatás példánya hogyan kezeli ezeket a kiépítési kérelmeket. A szabályzat azt is meghatározza, hogy az eszközállapot-adatokat át kell-e migrálni az újraépítés során. Az egyes regisztrációkhoz és regisztrációs csoportokhoz ugyanazok a szabályzatok érhetők el:

* **Adatok újraépítése** és áttelepítése: Ez a szabályzat az új regisztrációs bejegyzések alapértelmezett beállítása. Ez a szabályzat akkor történik meg, ha a regisztrációs bejegyzéshez társított eszközök új kérelmet küldnek be (1). A regisztrációs bejegyzés konfigurációjának függvényében előfordulhat, hogy az eszköz egy másik IoT Hubhoz lesz hozzárendelve. Ha az eszköz módosítja az IoT Hubokat, a rendszer eltávolítja a kezdeti IoT Hubbal való eszközregisztrációt. A kezdeti IoT Hub frissített eszközállapot-információit a rendszer migrálja az új IoT Hubra (2). A migrálás során az eszköz hozzárendelési állapotúként **lesz jelentve.**

    ![Diagram, amely azt mutatja, hogy a szabályzat akkor hoz létre műveletet, ha a regisztrációs bejegyzéshez társított eszközök új kérelmet küldnek be.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Újra kiépítés és visszaállítás** a kezdeti konfigurációra: Ez a szabályzat akkor működik, amikor a regisztrációs bejegyzéshez társított eszközök új kiépítési kérelmet küldnek (1). A regisztrációs bejegyzés konfigurációjának függvényében előfordulhat, hogy az eszköz egy másik IoT Hubhoz lesz hozzárendelve. Ha az eszköz módosítja az IoT Hubokat, a rendszer eltávolítja a kezdeti IoT Hubbal való eszközregisztrációt. A kiépítési szolgáltatás példánya által az eszköz kiépítésekor kapott kezdeti konfigurációs adatok az új IoT Hubhoz (2) biztosítanak adatokat. A migrálás során az eszköz hozzárendelési állapotúként **lesz jelentve.**

    Ezt a szabályzatot gyakran használják a gyári beállítások visszaállításához az IoT Hubok módosítása nélkül.

    ![Diagram, amely bemutatja, hogyan jár el a szabályzat, amikor a regisztrációs bejegyzéshez társított eszközök új kiépítési kérelmet küldnek be.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Soha ne létesítsen** újra: Az eszköz soha nem lesz másik hubhoz hozzárendelve. Ez a szabályzat a visszamenőleges kompatibilitás kezeléséhez biztosított.

> [!NOTE]
> A DPS mindig az újrakiosztási szabályzattól függetlenül hívja meg az egyéni foglalási webhookot arra az esetre, ha az eszközhöz új [ReturnData](how-to-send-additional-data.md) adat áll rendelkezésre. Ha az újraépítési szabályzat úgy van beállítva, hogy soha ne legyen újra kiépítve, a rendszer a webhookot fogja meghívni, de az eszköz nem módosítja a hozzárendelt központot.

### <a name="managing-backwards-compatibility"></a>Visszamenőleges kompatibilitás kezelése

2018 szeptembere előtt az IoT Hubhoz való eszköz-hozzárendelések ragadós viselkedést ioT-ként viselkedtek. Amikor egy eszköz visszament a kiépítési folyamaton, az csak ugyanabba az IoT Hubba lett visszaosztva.

Az ilyen viselkedéstől függő megoldásokhoz a kiépítési szolgáltatás visszamenőleges kompatibilitást is tartalmaz. Ezt a viselkedést jelenleg az alábbi feltételeknek megfelelően tartják fenn az eszközökön:

1. Az eszközök egy API-verzióval csatlakoznak, mielőtt a Device Provisioning Service natív újraépítési támogatása elérhető lesz. Tekintse meg az alábbi API-táblázatot.

2. Az eszközök regisztrációs bejegyzéséhez nincs beállítva újraregisztrációs szabályzat.

Ez a kompatibilitás biztosítja, hogy a korábban üzembe helyezett eszközök ugyanúgy viselkednek, mint a kezdeti tesztelés során. Az előző viselkedés megőrzése érdekében ne mentsen újra kiépítő szabályzatot ezekre a regisztrációkra. Ha egy újra kiépítő szabályzat be van állítva, az újra kiépítő szabályzat elsőbbséget élvez a viselkedéssel kapcsolatban. Azáltal, hogy lehetővé teszi, hogy az újra kiépítő szabályzat elsőbbséget élvez, az ügyfelek anélkül frissítheti az eszköz viselkedését, hogy az eszköz rendszermemóióját vissza kellett volna rendszerezésre bírni.

A következő folyamatábra segít megmutatni, hogy mikor van jelen a viselkedés:

![visszamenőleges kompatibilitási folyamat diagramja](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

Az alábbi táblázat a Device Provisioning Service natív újraépítési támogatásának rendelkezésre állása előtti API-verziókat mutatja be:

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018. 04. 01. és korábbi](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 és korábbi verziók](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 és korábbi verziók](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3-as vagy korábbi verzió](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 vagy korábbi](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 vagy korábbi](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Ezek az értékek és hivatkozások valószínűleg megváltoznak. Ez csak egy helyőrző kísérlet annak meghatározására, hogy az ügyfél hol állapíthatja meg a verziókat, és milyen verziók várhatóak.

## <a name="next-steps"></a>Következő lépések

* [Eszközök újraosztása](how-to-reprovision.md)
