---
title: Transzparens átjáró-eszköz létrehozása – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-eszköz használata transzparens átjáróként, amely adatokat dolgozhat fel az alsóbb rétegbeli eszközökről
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f7f05fb84ff6cbe320e8f479912bdcdefdc41021
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201659"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ez a cikk részletesen ismerteti, hogyan konfigurálhat egy IoT Edge eszközt úgy, hogy az más eszközök számára transzparens átjáróként működjön, hogy az IoT Hub kommunikáljon. Ez a cikk az *IoT Edge átjáró* kifejezést használja az átlátszó átjáróként konfigurált IoT Edge eszközre való hivatkozáshoz. További tudnivalókért tekintse meg a [IoT Edge-eszköz átjáróként való használatát](./iot-edge-as-gateway.md)ismertető témakört.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

>[!NOTE]
>A IoT Edge 1,1-es és régebbi verzióiban egy IoT Edge eszköz nem lehet egy IoT Edge átjárón.
>
>Az alsóbb rétegbeli eszközök nem használhatják a fájl feltöltését.

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Az alsóbb rétegbeli eszközök nem használhatják a fájl feltöltését.

::: moniker-end

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk az első lépést ismerteti:

1. **Konfigurálja az átjáró-eszközt kiszolgálóként, hogy az alsóbb rétegbeli eszközök biztonságosan kapcsolódjanak hozzá. Állítsa be az átjárót, hogy üzeneteket kapjon az alsóbb rétegbeli eszközökről, és irányítsa azokat a megfelelő helyre.**
2. Hozzon létre egy eszköz-identitást az alsóbb rétegbeli eszköz számára, hogy a hitelesítés a IoT Hub használatával történjen. Az alsóbb rétegbeli eszköz konfigurálásával üzeneteket küldhet az átjáró eszközön keresztül. Ezekről a lépésekről további információt a következő témakörben talál: [alárendelt eszköz hitelesítése az Azure IoT hub](how-to-authenticate-downstream-device.md).
3. Az alárendelt eszköz csatlakoztatása az átjáró eszközhöz és az üzenetek küldésének megkezdése. Ezeket a lépéseket lásd: [alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md).

Ahhoz, hogy egy eszköz átjáróként működjön, biztonságosan csatlakoznia kell az alsóbb rétegbeli eszközökhöz. Azure IoT Edge lehetővé teszi, hogy egy nyilvános kulcsokra épülő infrastruktúrát (PKI) használjon az eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben lehetővé tesszük, hogy egy alsóbb rétegbeli eszköz olyan IoT Edge-eszközhöz kapcsolódjon, amely transzparens átjáróként működik. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie az átjáró-eszköz identitását. Ez az identitás-ellenőrzési szolgáltatás megakadályozza, hogy az eszközök esetlegesen rosszindulatú átjáróhoz csatlakozzanak.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Egy alsóbb rétegbeli eszköz lehet bármely olyan alkalmazás vagy platform, amely rendelkezik az [Azure IoT hub](../iot-hub/index.yml) Cloud Service szolgáltatással létrehozott identitással. Ezek az alkalmazások gyakran az [Azure IoT-eszköz SDK](../iot-hub/iot-hub-devguide-sdks.md)-t használják. Egy alsóbb rétegbeli eszköz is lehet a IoT Edge átjáró eszközön futó alkalmazás. Egy IoT Edge eszköz azonban nem lehet egy IoT Edge átjárón.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Egy alsóbb rétegbeli eszköz lehet bármely olyan alkalmazás vagy platform, amely rendelkezik az [Azure IoT hub](../iot-hub/index.yml) Cloud Service szolgáltatással létrehozott identitással. Ezek az alkalmazások gyakran az [Azure IoT-eszköz SDK](../iot-hub/iot-hub-devguide-sdks.md)-t használják. Egy alsóbb rétegbeli eszköz is lehet a IoT Edge átjáró eszközön futó alkalmazás.
:::moniker-end
<!-- end 1.2 -->

Létrehozhat olyan tanúsítvány-infrastruktúrát is, amely lehetővé teszi az eszköz-átjáró topológiához szükséges megbízhatóságot. Ez a cikk azt feltételezi, hogy ugyanazt a tanúsítványt használja, amelyet az [x. 509 hitelesítésszolgáltatói biztonság](../iot-hub/iot-hub-x509ca-overview.md) engedélyezéséhez használ IoT Hubban, amely egy adott IoT hubhoz (az IoT hub legfelső szintű hitelesítésszolgáltatója) társított x. 509 hitelesítésszolgáltatói tanúsítvánnyal, a hitelesítésszolgáltatóval aláírt tanúsítványok sorozatával, valamint a IoT Edge eszközhöz tartozó hitelesítésszolgáltatóval is rendelkezik.

>[!NOTE]
>Az ezekben a cikkekben használt *legfelső szintű hitelesítésszolgáltatói tanúsítvány* kifejezés a PKI-tanúsítványlánc legfelső szintű nyilvános tanúsítványára hivatkozik, nem feltétlenül a konzorciális hitelesítésszolgáltató tanúsítványának gyökerére. Sok esetben valójában egy közbenső HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa.

A következő lépések végigvezetik a tanúsítványok létrehozásának és telepítésének folyamatán az átjáró megfelelő helyein. A tanúsítványok létrehozásához bármilyen gépet használhat, majd átmásolhatja őket a IoT Edge eszközre.

## <a name="prerequisites"></a>Előfeltételek

Linux vagy Windows rendszerű eszköz, amelyen IoT Edge telepítve van.

Ha nem áll készen az eszköz, létrehozhat egyet egy Azure-beli virtuális gépen. Kövesse az [első IoT Edge modul üzembe helyezése virtuális Linux-eszközön](quickstart-linux.md) című témakör lépéseit IoT hub létrehozásához, a virtuális gép létrehozásához és a IoT Edge futtatókörnyezet konfigurálásához.

## <a name="set-up-the-device-ca-certificate"></a>Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának beállítása

Minden IoT Edge átjáróhoz telepítve kell lennie egy eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának. A IoT Edge Security Daemon a IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát használja a számítási feladatok HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának aláírásához, ami aláírja az IoT Edge hub kiszolgálói tanúsítványát. Az átjáró a kapcsolat megkezdése során megjeleníti a kiszolgálói tanúsítványát az alárendelt eszközön. Az alsóbb rétegbeli eszköz ellenőrzi, hogy a kiszolgálói tanúsítvány egy olyan tanúsítványlánc részét képezi-e, amely a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványba összesíti. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszköz ellenőrizze, hogy az átjáró megbízható forrásból származik-e. További információ: a [Azure IoT Edge tanúsítványok használatának](iot-edge-certs.md)ismertetése.

![Átjáró tanúsítványának beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak és az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának (titkos kulcsával) jelen kell lennie az IoT Edge átjáró eszközön, és konfigurálni kell a IoT Edge konfigurációs fájlban. Ne feledje, hogy ebben az esetben a *legfelső szintű hitelesítésszolgáltatói tanúsítvány* a legfelső szintű hitelesítésszolgáltatót jelenti ehhez a IoT Edge-forgatókönyvhöz. Az átjáró-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának és az alsóbb rétegbeli eszköz tanúsítványának azonos legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra van szüksége.

>[!TIP]
>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány és az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának IoT Edge-eszközre történő telepítésének folyamatát a [IoT Edge-eszközök tanúsítványainak kezelése](how-to-manage-device-certificates.md)című részben is ismertetjük.

A következő fájlok készen állnak:

* Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI titkos kulcsa

Éles környezetekben ezeket a fájlokat saját hitelesítésszolgáltatóval kell előállítani. Fejlesztési és tesztelési helyzetekben használhat bemutató tanúsítványokat.

Ha nem rendelkezik saját hitelesítésszolgáltatóval, és a bemutató tanúsítványokat szeretné használni, kövesse a bemutató tanúsítványok létrehozása a fájlok létrehozásához [IoT Edge eszköz szolgáltatásainak teszteléséhez](how-to-create-test-certificates.md) című témakör utasításait. Ezen az oldalon a következő lépéseket kell elvégeznie:

   1. Az indításhoz állítson be a parancsfájlokat a tanúsítványok létrehozásához az eszközön.
   2. Hozzon létre egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt. Ezen utasítások végén egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványfájl fog rendelkezni:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.
   3. Hozzon létre IoT Edge eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat. Ezen utasítások végén egy eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa és annak titkos kulcsa is megjelenik:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` és
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

Ha egy másik gépen hozta létre a tanúsítványokat, másolja át őket a IoT Edge eszközre, majd folytassa a következő lépésekkel.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. A IoT Edge eszközön nyissa meg a biztonsági démon konfigurációs fájlját.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. Keresse meg a fájl **tanúsítvány-beállítások** szakaszát. A következő tulajdonságok értékének megadásával adja meg a négy sort a **tanúsítványokkal** kezdődően: és adja meg a fájl URI-azonosítóit a három fájl számára:
   * **device_ca_cert**: eszköz hitelesítésszolgáltatói tanúsítványa
   * **device_ca_pk**: eszköz hitelesítésszolgáltatói titkos kulcsa
   * **trusted_ca_certs**: legfelső szintű hitelesítésszolgáltatói tanúsítvány

   Győződjön meg arról, hogy a **tanúsítványok:** sorban nincs-e a fenti szóközök, és hogy a többi sor két szóközzel van behúzva.

1. Mentse és zárja be a fájlt.

1. IoT Edge újraindítása.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`
:::moniker-end
<!-- end 1.1 -->

<!--1.2 -->
:::moniker range=">=iotedge-2020-11"

1. A IoT Edge eszközön nyissa meg a konfigurációs fájlt: `/etc/aziot/config.toml`

   >[!TIP]
   >Ha a konfigurációs fájl még nem létezik az eszközön, akkor `/etc/aziot/config.toml.edge.template` sablonként használva hozzon létre egyet.

1. Keresse meg a `trust_bundle_cert` paramétert. A sor megjegyzésének visszaadása és a fájl URI-ja megadása a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványfájl számára az eszközön.

1. Keresse meg a `[edge_ca]` fájl szakaszát. Adja meg a jelen szakasz három sorát, és adja meg a fájl URI-azonosítóit a tanúsítványhoz és a kulcs fájljaihoz a következő tulajdonságok értékeiként:
   * **tanúsítvány**: eszköz hitelesítésszolgáltatói tanúsítványa
   * **PK**: eszköz hitelesítésszolgáltatójának titkos kulcsa

1. Mentse és zárja be a fájlt.

1. Válassza az Alkalmaz gombot a módosítások alkalmazásához.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="deploy-edgehub-and-route-messages"></a>EdgeHub üzembe helyezése és az üzenetek továbbítása

Az alsóbb rétegbeli eszközök telemetria és üzeneteket küldenek az átjáró eszköznek, ahol az IoT Edge hub-modul felelős az adatok más modulokhoz való átirányításához vagy IoT Hubához. Az átjáró-eszköz ehhez a függvényhez való előkészítéséhez győződjön meg az alábbiakról:

* Az IoT Edge hub modul telepítve van az eszközön.

  Amikor először telepíti a IoT Edget egy eszközön, a rendszer csak egy rendszermodult kezd automatikusan: a IoT Edge-ügynököt. Miután létrehozta az első központi telepítést egy eszközhöz, a második rendszermodul, az IoT Edge hub is elindul. Ha a **edgeHub** modul nem fut az eszközön, hozzon létre egy központi telepítést az eszköz számára.

* Az IoT Edge hub-modul útvonalakat állított be, amelyekkel az alárendelt eszközökről érkező üzeneteket kezelhet.

  Az átjáró-eszköznek rendelkeznie kell egy olyan útvonallal, amely a alsóbb rétegbeli eszközökön lévő üzenetek kezelésére alkalmas, különben az üzenetek nem lesznek feldolgozva. Az üzeneteket elküldheti a moduloknak az átjáró eszközén, vagy közvetlenül a IoT Hub.

Az IoT Edge hub modul üzembe helyezéséhez és az alsóbb rétegbeli eszközökről bejövő üzenetek kezelésére szolgáló útvonalakkal való konfigurálásához kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg az IoT-központot.

2. Lépjen **IoT Edge** , és válassza ki az átjáróként használni kívánt IoT Edge-eszközt.

3. Válassza a **modulok beállítása** lehetőséget.

4. A **modulok** lapon hozzáadhat bármely olyan modult, amelyet központilag szeretne telepíteni az átjáró-eszközre. Ebben a cikkben a edgeHub modul konfigurálására és üzembe helyezésére koncentrálunk, amelyet nem kell explicit módon beállítani ezen az oldalon.

5. Válassza a **Tovább: útvonalak** lehetőséget.

6. Az **útvonalak** lapon ellenőrizze, hogy van-e útvonal az alárendelt eszközökről érkező üzenetek kezeléséhez. Például:

   * Egy olyan útvonal, amely minden üzenetet elküld egy modulból vagy egy alsóbb rétegbeli eszközről a IoT Hubba:
       * **Név**: `allMessagesToHub`
       * **Érték**: `FROM /messages/* INTO $upstream`

   * Az összes alsóbb rétegbeli eszközről a IoT Hubre küldött összes üzenetet küldő útvonal:
      * **Név**: `allDownstreamToHub`
      * **Érték**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      Ez az útvonal azért működik, mert a IoT Edge modulokból származó üzenetektől eltérően az alárendelt eszközöktől érkező üzenetek nem rendelkeznek a hozzájuk társított modul-AZONOSÍTÓval. Az útvonal **Where** záradékának használata lehetővé teszi, hogy az adott System tulajdonsággal rendelkező üzeneteket kiszűrje.

      Az üzenetek útválasztásával kapcsolatos további információkért lásd: [modulok üzembe helyezése és útvonalak létrehozása](./module-composition.md#declare-routes).

7. Az útvonal vagy az útvonalak létrehozása után válassza a **felülvizsgálat + létrehozás** lehetőséget.

8. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.

## <a name="open-ports-on-gateway-device"></a>Portok megnyitása átjáró eszközön

A standard IoT Edge eszközök nem igényelnek semmilyen bejövő kapcsolatot a működéshez, mert az IoT Hubkel folytatott kommunikáció kimenő kapcsolatokon keresztül történik. Az átjáró-eszközök különböznek, mert üzeneteket kell kapniuk az alsóbb rétegbeli eszközökről. Ha a tűzfal az alárendelt eszközök és az átjáró-eszköz között van, akkor a kommunikációnak a tűzfalon keresztül is elérhetőnek kell lennie.

Az átjáró-forgatókönyvek működéséhez az IoT Edge hub által támogatott protokollok közül legalább az egyiknek nyitva kell lennie az alárendelt eszközökről érkező bejövő forgalom számára. A támogatott protokollok a MQTT, a AMQP, a HTTPS, a MQTT over WebSockets és a AMQP WebSocket-en keresztül.

| Port | Protokoll |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="next-steps"></a>Következő lépések

Most, hogy rendelkezik egy IoT Edge eszközzel, amely transzparens átjáróként van beállítva, konfigurálnia kell az alsóbb rétegbeli eszközöket az átjáró megbízhatóságához és az üzenetek küldéséhez. Folytassa a következő lépéssel, hogy [hitelesítse egy alsóbb rétegbeli eszközt az Azure IoT Hubban](how-to-authenticate-downstream-device.md) az átlátszó átjáró beállításának további lépéseihez.