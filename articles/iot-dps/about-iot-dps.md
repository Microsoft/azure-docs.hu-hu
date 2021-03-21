---
title: Az Azure IoT Hub Device Provisioning Service áttekintése | Microsoft Docs
description: Az eszközök üzembe helyezését ismerteti az Azure-ban az eszközök kiépítési szolgáltatásával (DPS) és IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 6b33b866a10ad4a44cef14f3c86d8ca1f40c4750
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965374"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Eszközregisztráció az Azure IoT Hub Device Provisioning Service használatával
A Microsoft Azure integrált nyilvános felhőszolgáltatások széles skáláját biztosítja, az IoT-megoldásokkal kapcsolatos igények teljes körű kielégítése céljából. A IoT Hub Device Provisioning Service (DPS) egy olyan IoT Hub segítő szolgáltatása, amely lehetővé teszi, hogy az emberi beavatkozás nélkül, a megfelelő IoT hub-ra való leválasztást, valamint a jobb oldali üzembe helyezést. A DPS lehetővé teszi, hogy több millió eszköz biztonságos és skálázható módon legyen kiépítve.

## <a name="when-to-use-device-provisioning-service"></a>Mikor érdemes használni a Device Provisioning Service-t?
Számos üzembe helyezési forgatókönyv van, amelyekben a DPS kiváló választás a IoT Hubhoz csatlakoztatott és konfigurált eszközök beszerzéséhez, például:

* Érintésmentes kiépítés egyetlen IoT-megoldásra, az IoT Hub-kapcsolati adatok gyári kódolása nélkül (kezdeti telepítés)
* Eszközök terheléselosztása több hubok között
* Eszközök csatlakoztatása a tulajdonos IoT-megoldásához értékesítési tranzakciós adatforgalom (bérlős) alapján
* Eszközök csatlakoztatása egy adott IoT-megoldáshoz a használati esettől függően (megoldáselkülönítés)
* Az eszközök legkisebb mértékű késleltetéssel rendelkező IoT Hubhoz való csatlakoztatása (földrajzi horizontális skálázás)
* Ismételt regisztráció az eszközt érintő változás alapján
* Az eszköz által az IoT Hubhoz való csatlakozáshoz használt kulcsok váltása (nem X.509-tanúsítványokkal történő csatlakozás esetében)

## <a name="behind-the-scenes"></a>A színfalak mögött
Az előző szakaszban felsorolt összes forgatókönyv a DPS használatával nulla érintéses kiépítés esetén ugyanazzal a folyamattal végezhető el. A kiépítés során hagyományosan felmerülő kézi lépések számos része automatikusan együttműködik a DPS szolgáltatással, hogy csökkentse a IoT-eszközök üzembe helyezésének idejét, és csökkentse a manuális hibák kockázatát. A következő szakasz azt ismerteti, hogy mi történik a színfalak mögött az eszközök regisztrálása során. Az első lépés manuális, az összes további lépés pedig automatizált.

![Alapszintű üzembehelyezési folyamat](./media/about-iot-dps/dps-provisioning-flow.png)

1. Az eszközgyártó hozzáadja az eszköz regisztrációs adatait az Azure portál regisztrációs listájához.
2. Az eszköz kapcsolatba lép a gyárban beállított DPS-végponttal. Az eszköz átadja az azonosító adatokat a DPS-nek, hogy igazolja személyazonosságát.
3. A DPS érvényesíti az eszköz identitását úgy, hogy a regisztrációs azonosítót és a kulcsot egy egyszeres ([platformmegbízhatósági modul](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) vagy egy szabványos x. 509 ellenőrzés (x. 509) használatával ellenőrzi a beléptetési lista bejegyzésén.
4. A DPS regisztrálja az eszközt egy IoT hub-ban, és feltölti az eszköz [kívánt kettős állapotát](../iot-hub/iot-hub-devguide-device-twins.md).
5. Az IoT hub az eszköz azonosító adatait adja vissza a DPS-nek.
6. A DPS a IoT hub kapcsolódási adatait adja vissza az eszköznek. Ezután az eszköz már képes adatokat küldeni közvetlenül az IoT Hubnak.
7. Az eszköz csatlakozik az IoT Hubhoz.
8. Az eszköz fogadja az IoT Hubban található ikereszköz kívánt állapotát.

## <a name="provisioning-process"></a>A regisztráció folyamata
Két különböző lépés található egy olyan eszköz telepítési folyamatában, amelyben a DPS egy olyan részt vesz igénybe, amely egymástól függetlenül végezhető el:

* A **gyártási lépés**, amely során az eszközt előállítják és előkészítik a gyárban, valamint
* A **felhőalapú telepítési lépés**, amelynek keretében a Device Provisioning Service-t automatikus üzembe helyezésre konfigurálják.

Mindkét lépés zökkenőmentesen illeszkedik a létező gyártási és üzembehelyezési folyamatokba. A DPS még egyszerűbbé teszi néhány olyan üzembe helyezési folyamatot, amely manuális munkát tartalmaz a kapcsolódási adatoknak az eszközre való beszerzéséhez.

### <a name="manufacturing-step"></a>Gyártási lépés
Ez a lépés a gyártósoron történő műveletekről szól. Az ebben a lépésben részt vevő szerepkörök közé tartozik a lapkatervező, a lapkagyártó, az integrátor és/vagy az eszköz véggyártója. Ez a lépés magának a hardvernek a gyártásáról szól.

A DPS nem vezet be új lépést a gyártási folyamatba; Ehelyett összekapcsolja a meglévő lépést, amely telepíti a kezdeti szoftvert és (ideális esetben) a HSM-t az eszközön. Az eszközazonosító ebben a lépésben történő létrehozása helyett az eszközbe az üzembehelyezési szolgáltatás adatait programozzák bele, lehetővé téve a számára az üzembehelyezési szolgáltatás meghívását bekapcsoláskor, a kapcsolatadatok/IoT-megoldáshozzárendelés lekérése céljából.

Szintén ebben a lépésben látja el a gyártó az eszköz telepítőjét/kezelőjét azonosítókulcs-adatokkal. Ezen adatok átadása lehet egyszerű, mint például annak megerősítése, hogy mindegyik eszköz a telepítője/kezelője által biztosított aláíró tanúsítványból létrehozott X.509-tanúsítvánnyal rendelkezik, illetve bonyolult, mint például a TPM-ellenőrzőkulcs nyilvános részének kinyerése mindegyik TPM-eszközből. Ezek a szolgáltatások ma már sok lapkagyártó kínálatában szerepelnek.

### <a name="cloud-setup-step"></a>Felhőalapú telepítési lépés
Ennek a lépésnek a lényege a felhő megfelelő automatikus üzembe helyezésre való konfigurálása. A felhőalapú telepítési lépésben általában kétféle felhasználó vesz részt: az, aki tudja, hogyan kell elvégezni az eszközök kezdeti beállítását (eszközkezelő), és az, aki tudja, hogyan kell elosztani az eszközöket az IoT Hubok között (megoldáskezelő).

Az üzembe helyezés kezdeti beállítását egyszer kell elvégezni, ami általában a megoldásüzemeltető feladata. Ha az üzembehelyezési szolgáltatás konfigurálása megtörtént, nem szükséges többé módosítani, kivéve, ha megváltozik a használati eset.

A szolgáltatást az automatikus üzembe helyezésre való konfigurálás után fel kell készíteni az eszközök regisztrálására. Ezt a lépést az eszközkezelő végzi el, aki ismeri az eszköz(ök) kívánt konfigurációját. Az ő feladata biztosítani, hogy az üzembehelyezési szolgáltatás képes legyen megfelelően igazolni az eszköz identitását, amikor az az IoT Hubot keresi. Az eszközkezelő hozzáadja a gyártótól kapott azonosítókulcs-adatokat a regisztrációs listához. A regisztrációs lista későbbi frissítése lehetséges, ha új bejegyzéseket adnak hozzá, vagy a létező bejegyzéseket frissítik az eszközökre vonatkozó legfrissebb adatokkal.

## <a name="registration-and-provisioning"></a>Regisztráció és üzembe helyezés
Az *üzembe helyezés* különféle jelentéseket takarhat attól függően, mely iparágban használják. Az IoT-eszközök felhőalapú megoldáson való üzembe helyezésének kontextusában az üzembe helyezés kétrészes folyamat:

1. Az első lépés a kezdeti kapcsolat létesítése az eszköz és az IoT-megoldás között az eszköz regisztrálása révén.
2. A második lépés a megfelelő konfiguráció alkalmazása az eszközre annak a megoldásnak az egyéni követelményei alapján, amelyre az eszközt regisztrálták.

Ha mindkét lépés befejeződött, az eszköz teljesen üzembe helyezettnek tekinthető. Egyes felhőszolgáltatások csak az üzembehelyezési folyamat első lépését, az eszközök IoT-megoldásvégponton való regisztrációját biztosítják, a kezdeti konfigurációt viszont nem. A DPS mindkét lépést automatizálja az eszköz zökkenőmentes kiépítési élményének biztosításához.

## <a name="features-of-the-device-provisioning-service"></a>A Device Provisioning Service funkciói
A DPS számos funkcióval rendelkezik, így ideális megoldás az eszközök kiépítési célokra.

* **Biztonságos állapotigazolás** X.509- és a TPM-alapú identitásokhoz is.
* Az esetleg regisztráló eszközök/eszközcsoportok teljes rekordját tartalmazó **regisztrációs lista**. A regisztrációs lista tartalmazza az eszköz kívánt konfigurációjára vonatkozó adatokat, amint az regisztrál, és bármikor frissíthető.
* **Több kiosztási szabályzat** annak szabályozására, hogy a DPS hogyan rendeljen eszközöket az IoT-hubokhoz a forgatókönyvek támogatása érdekében: a legalacsonyabb késés, a páros mértékben súlyozott eloszlás (alapértelmezett) és a statikus konfiguráció a beléptetési listán keresztül. A késés meghatározása a [Traffic managerével](../traffic-manager/traffic-manager-routing-methods.md#performance)megegyező módszerrel történik.
* A **Naplózás monitorozása és hibakeresése** segít biztosítani, hogy minden jól működjön.
* A **többközpontos támogatás** lehetővé teszi, hogy a DPS több IoT hubhoz rendeljen eszközöket. A DPS több Azure-előfizetésen keresztül tud kommunikálni a hubokkal.
* A régiók **közötti támogatás** lehetővé teszi, hogy a DPS más régiókban is rendeljen eszközöket az IoT-hubokhoz.
* Az inaktív **adatok titkosítása** lehetővé teszi, hogy a DPS-ben lévő adatok titkosítva legyenek, és az 256 bites AES-titkosítással, az egyik legerősebb blokk titkosítási algoritmussal, valamint az FIPS 140-2-kompatibilis módon legyenek titkosítva és visszafejtve.


Az eszközök üzembe helyezésével kapcsolatos fogalmakról és szolgáltatásokról további információt talál a [DPS terminológiával](concepts-service.md) foglalkozó témakörben, valamint az ugyanabban a szakaszban található további elméleti témakörökben.

## <a name="cross-platform-support"></a>Platformfüggetlen támogatás
Az összes Azure IoT-szolgáltatáshoz hasonlóan a DPS több különböző operációs rendszerrel is együttműködik a platformmal. Az Azure számos [nyelven](https://github.com/Azure/azure-iot-sdks) kínál nyílt forráskódú SDK-kat az eszközök csatlakoztatásához és a szolgáltatás felügyeletéhez. A DPS a következő protokollokat támogatja az eszközök csatlakoztatásához:

* HTTPS
* AMQP
* AMQP WebSocketen keresztül
* MQTT
* MQTT WebSocketen keresztül

A DPS csak a HTTPS-kapcsolatokat támogatja a szolgáltatási műveletekhez.

## <a name="regions"></a>Régiók
A DPS számos régióban elérhető. A meglévő és újonnan bejelentett régiók naprakész listája minden szolgáltatáshoz megtalálható az [Azure-régióknál](https://azure.microsoft.com/regions/). A Device Provisioning Service elérhetőségét az [Azure állapota](https://azure.microsoft.com/status/) lapon tekintheti meg.

> [!NOTE]
> A DPS globális, és nem egy helyhez van kötve. Azonban meg kell adnia egy régiót, amelyben a DPS-profilhoz tartozó metaadatok lesznek tárolva.

## <a name="availability"></a>Rendelkezésre állás
A DPS esetében 99,9% szolgáltatói szerződés van, és [elolvashatja az SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)-t. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/)-ban található.

## <a name="quotas"></a>Kvóták
Minden Azure-előfizetésre alapértelmezett kvótakorlátozások vonatkoznak, és ezek a korlátozások hatással lehetnek az IoT-megoldás hatókörére. A jelenlegi határérték előfizetésenként 10 Device Provisioning Service.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

További információ a kvótakorlátozásokról:
* [Az Azure-előfizetés szolgáltatásokra vonatkozó korlátozásai](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Kapcsolódó Azure-összetevők
A DPS automatizálja az eszköz kiépítési folyamatát az Azure IoT Hub. További információ a [IoT hubról](../iot-hub/index.yml).

## <a name="next-steps"></a>Következő lépések
Mostanra nagyjából átlátja az IoT-eszközök Azure-ban való kiépítését. A következő lépés egy teljes körű IoT-forgatókönyv kipróbálása.

[IoT Hub Device Provisioning Service beállítása a Azure Portal](quick-setup-auto-provision.md)

[Szimulált eszköz létrehozása és kiépítése](quick-create-simulated-device.md)

[Eszköz beállítása a kiépítéshez](tutorial-set-up-device.md)