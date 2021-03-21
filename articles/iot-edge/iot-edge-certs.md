---
title: Az eszközök biztonságával kapcsolatos tanúsítványok – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge tanúsítvány használatával ellenőrzi az eszközöket, a modulokat és a Leaf eszközöket, és biztonságos kapcsolatokat létesít közöttük.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: ffe2f2b7f94d546cdfe393170da2fd2ca6ac0149
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490993"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>A Azure IoT Edge tanúsítványok használatának ismertetése

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge a tanúsítványokat a modulok és az alsóbb rétegbeli IoT eszközök használják az [IoT Edge hub](iot-edge-runtime.md#iot-edge-hub) Runtime modul identitásának és törvényességének ellenőrzéséhez. Ezek az ellenőrzések lehetővé teszik a TLS (Transport Layer Security) biztonságos kapcsolatot a futtatókörnyezet, a modulok és a IoT eszközök között. A IoT Hubhoz hasonlóan a IoT Edgenak biztonságos és titkosított kapcsolatra van szüksége a IoT alsóbb rétegbeli (vagy levél) eszközök és IoT Edge modulok esetében. A biztonságos TLS-kapcsolat létrehozásához az IoT Edge hub modul egy kiszolgálói tanúsítványláncot jeleníti meg az ügyfelek csatlakoztatásához, hogy azok személyazonosságát ellenőrizzék.

>[!NOTE]
>Ez a cikk azokat a tanúsítványokat ismerteti, amelyek a IoT Edge eszköz különböző összetevői és egy IoT Edge eszköz és a levél eszközei közötti kapcsolatok biztonságossá tételére szolgálnak. Tanúsítványokat is használhat a IoT Edge-eszköz IoT Hub való hitelesítéséhez. Ezek a hitelesítési tanúsítványok eltérnek, és nem jelennek meg ebben a cikkben. Az eszköz tanúsítványokkal történő hitelesítésével kapcsolatos további információkért lásd: [IoT Edge-eszköz létrehozása és kiépítése X. 509 tanúsítványokkal](how-to-auto-provision-x509-certs.md).

Ez a cikk azt ismerteti, hogy IoT Edge tanúsítványok hogyan működhetnek üzemi, fejlesztési és tesztelési helyzetekben. Míg a parancsfájlok különbözőek (PowerShell vagy bash), a fogalmak ugyanazok, mint a Linux és a Windows között.

## <a name="iot-edge-certificates"></a>IoT Edge-tanúsítványok

A tanúsítványok IoT Edge eszközön való beállításának két gyakori forgatókönyve van. Előfordulhat, hogy egy eszköz végfelhasználója vagy kezelője vásárol egy, a gyártó által készített általános eszközt, majd maga kezeli a tanúsítványokat. Máskor a gyártó úgy működik, hogy létrehoz egy egyéni eszközt a kezelőhöz, és az eszköz kiosztása előtt aláír néhány kezdeti tanúsítványt. A IoT Edge-tanúsítvány kialakítása mindkét forgatókönyvet megkísérli figyelembe venni.

Az alábbi ábra a tanúsítványok IoT Edge használatát mutatja be. A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány és az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa között nulla, egy vagy több köztes aláíró tanúsítvány lehet az érintett entitások számától függően. Itt egy esetet mutatunk be.

![Jellemző tanúsítványok kapcsolatainak diagramja](./media/iot-edge-certs/edgeCerts-general.png)

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Jelenleg a libiothsm korlátozásai meggátolják a 2038 január 1-jén vagy azt követően lejáró tanúsítványok használatát. Ez a korlátozás az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványára, a megbízhatósági csomagban található összes tanúsítványra, valamint az X. 509 létesítési módszerekhez használt eszköz-azonosító tanúsítványokra vonatkozik.

:::moniker-end

### <a name="certificate-authority"></a>Hitelesítésszolgáltató

A hitelesítésszolgáltató vagy a "CA" rövid, egy olyan entitás, amely digitális tanúsítványokat bocsát ki. A hitelesítésszolgáltató megbízható harmadik félként működik a tanúsítvány tulajdonosa és fogadója között. A digitális tanúsítvány tanúsítja, hogy a tanúsítvány fogadója a nyilvános kulcs tulajdonosát. A megbízhatósági tanúsítványlánc először a főtanúsítvány kiállítását végzi, amely a hatóság által kiadott összes tanúsítvány megbízhatóságának alapja. Ezt követően a tulajdonos a főtanúsítvány használatával további köztes tanúsítványokat ("Leaf" tanúsítványokat) adhat ki.

### <a name="root-ca-certificate"></a>Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány

A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány a teljes folyamat megbízhatóságának gyökere. Éles környezetben Ez a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány általában megbízható kereskedelmi hitelesítésszolgáltatótól vásárolható, például Baltimore, VeriSign vagy DigiCert. Ha teljes körű vezérléssel rendelkezik a IoT Edge eszközökhöz csatlakozó eszközök felett, vállalati szintű hitelesítésszolgáltató is használható. Mindkét esetben a IoT Edge hub teljes tanúsítványlánc a tekercsek között, így a levél IoT-eszközeinek meg kell bízniuk a főtanúsítványban. A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a megbízható legfelső szintű hitelesítésszolgáltató tárolóban tárolhatja, vagy megadhatja a tanúsítvány részleteit az alkalmazás kódjában.

### <a name="intermediate-certificates"></a>Köztes tanúsítványok

A biztonságos eszközök létrehozására jellemző gyártási folyamat során a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat ritkán használják közvetlenül, elsősorban a szivárgás vagy a kitettség kockázata miatt. A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehoz és digitálisan aláír egy vagy több köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt. Lehet, hogy csak egy, vagy a köztes tanúsítványok lánca lehet. A köztes tanúsítványok láncát igénylő forgatókönyvek a következők:

* A gyártón belüli részlegek hierarchiája.

* Több vállalat vesz részt egy eszköz előállításában.

* Az ügyfél a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT vásárolja meg, és a gyártóhoz tartozó aláíró tanúsítványt származtatva aláírja az ügyfél nevében általuk biztosított eszközöket.

Mindenesetre a gyártó egy köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használ a lánc végén a végponti eszközön elhelyezett HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány aláírásához. Általában ezek a közbenső tanúsítványok szorosan védettek a gyártó üzemben. Szigorú folyamatokon, fizikai és elektronikus használaton keresztül működnek.

### <a name="device-ca-certificate"></a>Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa

Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát a folyamat végső közbenső HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa hozza létre és írja alá. Ez a tanúsítvány a IoT Edge eszközre van telepítve, lehetőleg biztonságos tárolóban, például hardveres biztonsági modulban (HSM). Emellett az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa egyedileg azonosít egy IoT Edge eszközt. Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa más tanúsítványokat is aláírhat.

### <a name="iot-edge-workload-ca"></a>IoT Edge munkaterhelés-CA

A [IoT Edge Security Manager](iot-edge-security-manager.md) létrehozza a munkaterhelés hitelesítésszolgáltatói tanúsítványát, amely a folyamat "operátor" oldalának első indításakor IoT Edge először elindul. Ezt a tanúsítványt az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa hozza létre és írja alá. Ez a tanúsítvány – amely csak egy másik közbenső aláíró tanúsítvány – a IoT Edge futtatókörnyezet által használt egyéb tanúsítványok előállítására és aláírására szolgál. Napjainkban ez elsősorban az IoT Edge hub-kiszolgáló tanúsítványa, amelyet a következő szakaszban tárgyaltak, de a jövőben más tanúsítványokat is tartalmazhatnak IoT Edge összetevők hitelesítéséhez.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge hub-kiszolgáló tanúsítványa

Az IoT Edge hub-kiszolgáló tanúsítványa az a tényleges tanúsítvány, amelyet a rendszer a leküldési eszközök és modulok számára biztosít a hitelesítéshez a IoT Edge által igényelt TLS-kapcsolatok létrehozása során. Ez a tanúsítvány a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványba való létrehozásához használt tanúsítványok teljes láncát mutatja be, amelyeknek a levél IoT kell megbízniuk. Ha a IoT Edge generálja, akkor ennek a IoT Edge hub-tanúsítványnak a köznapi neve (CN) a konfigurációs fájl "hostname" tulajdonságára van beállítva, miután a rendszer kisbetűvé alakítja az átalakítást. Ez a konfiguráció az összetévesztés gyakori forrása IoT Edge.

## <a name="production-implications"></a>Termelési következmények

Egy ésszerű kérdés lehet "Miért IoT Edge a munkaterhelés-HITELESÍTÉSSZOLGÁLTATÓ extra tanúsítványra van szüksége? Nem tudta használni az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát az IoT Edge hub-kiszolgáló tanúsítványának közvetlen létrehozásához? ". Technikailag lehetséges. A "munkaterhelés" közbenső tanúsítvány célja azonban az eszköz gyártója és az eszköz operátora közötti elkülönítés. Képzelje el, hogy egy IoT Edge-eszköz értékesítésére vagy az egyik ügyfélről a másikra való átadására szolgál. Valószínű, hogy a gyártó által biztosított HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nem változtatható meg. Az eszköz működéséhez tartozó "munkaterhelés" tanúsítványokat azonban törölni kell, és újból létre kell hozni az új központi telepítéshez.

Mivel a gyártási és üzemeltetési folyamatok el vannak különítve, a következő szempontokat kell figyelembe venni a termelési eszközök előkészítésekor:

* Minden tanúsítványalapú folyamat esetében a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és az összes köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a IoT Edge eszköz kivezetésének teljes folyamata során kell védeni és figyelni. A IoT Edge eszköz gyártójának erős folyamatokkal kell rendelkeznie a közbenső tanúsítványok megfelelő tárolásához és használatához. Emellett az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát a lehető legbiztonságosabb tárterületként kell tárolni az eszközön, lehetőleg hardveres biztonsági modulként.

* Az IoT Edge hub-kiszolgáló tanúsítványát IoT Edge hub mutatja be a csatlakoztatott ügyféleszközök és modulok számára. Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának köznapi neve (CN) **nem** egyezhet meg a "hostname" értékkel, amelyet a IoT Edge eszköz konfigurációs fájljában fog használni. Az ügyfelek által a IoT Edgehoz való kapcsolódáshoz használt név (például a kapcsolati karakterlánc GatewayHostName paraméterén vagy a MQTT CSATLAKOZTATÁSi parancsán keresztül) **nem egyezhet** meg az eszköz hitelesítésszolgáltatói tanúsítványában használt köznapi névvel. Ez a korlátozás azért van, mert az IoT Edge hub a teljes tanúsítványláncot mutatja be az ügyfelek általi ellenőrzéshez. Ha a IoT Edge hub-kiszolgáló tanúsítványa és az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa azonos KN-val rendelkezik, a rendszer egy ellenőrzési hurkot kap, és a tanúsítvány érvénytelenné válik.

* Mivel az IoT Edge biztonsági démon az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát használja a végső IoT Edge tanúsítványok előállításához, akkor magát aláíró tanúsítványnak kell lennie, ami azt jelenti, hogy tanúsítvány-aláírási képességekkel rendelkezik. A "v3 alapkorlátozások CA: true" alkalmazása az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa automatikusan beállítja a szükséges kulcshasználat tulajdonságokat.

>[!Tip]
> Ha már elvégezte a IoT Edge beállítását transzparens átjáróként egy fejlesztési/tesztelési forgatókönyvben a "kényelmi parancsfájlok" használatával (lásd a következő szakaszt), és ugyanazt az állomásnevet használta az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának létrehozásakor, ahogy a konfigurációs fájlban a hostname parancsban volt, lehet, hogy megértette, miért működött. A fejlesztői élmény egyszerűsítése érdekében a kényelmi szkriptek hozzáfűzik a ". ca" kifejezést a parancsfájlba átadott név végén. Tehát ha például a "mygateway" kifejezést használta a parancsfájlokban és az állomásnévben a konfigurációs fájlban, akkor az előbbi a mygateway.ca lesz bekapcsolva, mielőtt az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványához a CN-t használja.

## <a name="devtest-implications"></a>Fejlesztési/tesztelési következmények

A fejlesztési és tesztelési forgatókönyvek megkönnyítése érdekében a Microsoft számos [kényelmi parancsfájlt](https://github.com/Azure/iotedge/tree/master/tools/CACertificates) biztosít a nem éles környezetű tanúsítványok létrehozásához, amelyek alkalmasak az átlátszó átjárók IoT Edgeához. A parancsfájlok működésével kapcsolatos példákért tekintse [meg a bemutató tanúsítványok létrehozása a IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md)című témakört.

>[!Tip]
> Ahhoz, hogy az eszköz IoT a "Leaf" eszközöket és alkalmazásokat, amelyek a IoT-eszköz SDK-t használják IoT Edgeon keresztül, hozzá kell adnia az opcionális GatewayHostName paramétert az eszköz kapcsolati karakterláncának végéhez. A peremhálózati kiszolgáló tanúsítványának létrehozásakor a rendszer az állomásnévnek a konfigurációs fájlból történő, alsó betokozású változatán alapul, ezért a nevek egyeztetéséhez és a TLS-tanúsítvány ellenőrzésének sikerességéhez meg kell adnia a GatewayHostName paramétert.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Példa IoT Edge tanúsítvány-hierarchiára

A tanúsítvány elérési útjának példájának szemléltetéséhez a következő képernyőkép egy olyan működő IoT Edge eszköz, amely átlátszó átjáróként van beállítva. Az OpenSSL az IoT Edge hubhoz való kapcsolódásra, a tanúsítványok érvényesítésére és kiírására szolgál.

![Képernyőfelvétel a tanúsítvány-hierarchiáról minden szinten](./media/iot-edge-certs/iotedge-cert-chain.png)

A következő képernyőképen látható a tanúsítvány mélységének hierarchiája:

| Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány         | Csak Azure IoT Hub HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány tesztelése                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány | Csak az Azure IoT Hub köztes tanúsítvány tesztelése                                                                 |
| Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa       | a iotgateway.ca ("iotgateway") a < átjáró állomásneve > a kényelmi parancsfájlok számára)   |
| Munkaterhelés HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa     | iotedge munkaterhelés-CA                                                                                       |
| IoT Edge hub-kiszolgáló tanúsítványa | iotedgegw. local (a konfigurációs fájl "hostname" karakterének felel meg)                                            |

## <a name="next-steps"></a>Következő lépések

[Az Azure IoT Edge-modulok ismertetése](iot-edge-modules.md)

[IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
