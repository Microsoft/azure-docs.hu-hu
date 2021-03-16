---
title: Átjárók alsóbb rétegbeli eszközökhöz – Azure IoT Edge | Microsoft Docs
description: A Azure IoT Edge használatával olyan transzparens, átlátszatlan vagy proxy átjárót hozhat létre, amely több alsóbb rétegbeli eszközről küld adatokat a felhőbe, vagy helyileg dolgozza fel azokat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ca8f02b375420590bcf1cc732c067a165e22b3fa
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492710"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge-eszköz használata átjáróként

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge-eszközök átjáróként működhetnek, és kapcsolatot biztosítanak a hálózaton lévő más eszközök és a IoT Hub között.

Az IoT Edge hub-modul úgy viselkedik, mint IoT Hub, így képes kezelni az olyan eszközök kapcsolatait, amelyeknek identitása IoT Hub, beleértve az egyéb IoT Edge eszközöket is. Az ilyen típusú átjárók mintázatát *transzparensnek* nevezzük, mivel az üzenetek átadhatók az alsóbb rétegbeli eszközökről IoT hubnak, mintha nem voltak közöttük átjárók.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
A IoT Edge 1,2-es verziójától kezdve az átlátszó átjárók más IoT Edge-eszközökről is kezelhetik az alsóbb rétegbeli kapcsolatokat.
::: moniker-end

Azon eszközök esetében, amelyek nem tudnak saját IoT Hub csatlakozni, IoT Edge átjárók biztosíthatják ezt a kapcsolatot. Az ilyen típusú átjárók mintáját *fordításnak* nevezzük, mivel a IoT Edge eszköznek feldolgozást kell végeznie a bejövő alsóbb szintű eszközön lévő üzenetekben, mielőtt továbbítani lehetne őket a IoT hubba. Ezeknek a forgatókönyveknek további modulokra van szükségük a IoT Edge átjárón a feldolgozási lépések kezeléséhez.

Az átlátható és a fordítási átjárók mintái nem zárják ki egymást. Egyetlen IoT Edge eszköz is működhet egy transzparens átjáróként és egy fordítási átjáróként.

Az átjáró összes mintázata a következő előnyöket biztosítja:

* **Elemzések az Edge** -ben – a mesterséges intelligenciát használó eszközök használatával helyileg dolgozhat fel adatokat, anélkül, hogy teljes hűségű telemetria küldene a felhőbe. Megkeresheti és reagálhat helyi elemzésekre, és csak az adathalmazt küldheti IoT Hubba.
* **Alsóbb rétegbeli eszközök elkülönítése** – az átjáró eszköz az összes alsóbb rétegbeli eszközt megvédi az internet felé. Egy olyan operatív technológiai (OT) hálózat között tud részt venni, amely nem rendelkezik kapcsolattal és olyan informatikai (IT) hálózattal, amely hozzáférést biztosít a webhelyhez. Hasonlóképpen, azok az eszközök, amelyek nem képesek csatlakozni a IoT Hubhoz a saját maguk csatlakozhatnak egy átjáró-eszközhöz.
* **Kapcsolat – többszörös** használat – az IoT Hub egy IoT Edge átjárón keresztül csatlakozó összes eszköz ugyanazt az alapul szolgáló kapcsolatot használhatja. Ehhez a multiplexeri képességhez az IoT Edge-átjáró a AMQP-t használja felsőbb rétegbeli protokollként.
* **Forgalom simítása** – a IoT Edge eszköz automatikusan végrehajtja az exponenciális leállítási, ha IoT hub szabályozza a forgalmat, miközben az üzeneteket helyileg tartja fenn. Ezzel a megoldással rugalmasan teheti meg a megoldást a forgalomban lévő csúcsokra.
* **Offline támogatás** – az átjáró eszköz olyan üzeneteket és kettős frissítéseket tárol, amelyek nem továbbíthatók IoT hubba.

## <a name="transparent-gateways"></a>Transzparens átjárók

Az átlátszó átjáró mintájában az eszközök, amelyek elméletileg csatlakozhatnak IoT Hub csatlakozhatnak egy átjáró-eszközhöz. Az alsóbb rétegbeli eszközök saját IoT Hub identitásokkal rendelkeznek, és a MQTT vagy AMQP protokollok használatával kapcsolódnak egymáshoz. Az átjáró csupán továbbítja az eszközök és az IoT Hub közötti kommunikációt. Az eszközök és a velük kommunikáló felhasználók IoT Hub nem biztos, hogy az átjáró közvetíti a kommunikációt. Ez a tájékoztatás hiánya azt jelenti, hogy az átjáró *transzparensnek* tekintendő.

További információ arról, hogy az IoT Edge hub hogyan kezeli az alsóbb rétegbeli eszközök és a felhő közötti kommunikációt: [Ismerje meg a Azure IoT Edge futtatókörnyezetet és annak architektúráját](iot-edge-runtime.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge-eszközök nem lehetnek IoT Edge átjárók alsóbb rétegében.

![Diagram – transzparens átjáró mintája](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

A 1.2.0 verziótól kezdődően IoT Edge eszközök transzparens átjárón keresztül csatlakozhatnak.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Szülő és gyermek kapcsolatok

Az átlátszó átjáró kapcsolatainak deklarálása a IoT Hubban úgy, hogy az IoT Edge átjárót egy *alárendelt* eszköz *szülője* állítja be, amely csatlakozik hozzá.

A szülő/gyermek kapcsolat az átjáró konfigurációjának három pontján van létrehozva:

#### <a name="cloud-identities"></a>Felhőbeli identitások

Az átlátszó átjárókban lévő összes eszközön Felhőbeli identitásokra van szükség, hogy a hitelesítésük IoT Hub legyen. Az eszköz identitásának létrehozásakor vagy frissítésekor beállíthatja az eszköz szülő-vagy gyermek-eszközét. Ezzel a konfigurációval engedélyezheti a szülő átjáró eszköznek, hogy kezelje az alárendelt eszközei hitelesítését.

>[!NOTE]
>A szülő eszköz beállítása IoT Hub a szimmetrikus kulcsos hitelesítést használó alsóbb rétegbeli eszközök esetében választható lépésként használható. A 1.1.0 verziótól kezdődően azonban minden alsóbb rétegbeli eszköznek hozzá kell rendelnie egy fölérendelt eszközhöz.
>
>Az IoT Edge hub úgy is beállítható, hogy visszalépjen az előző viselkedésre úgy, hogy a környezeti változót a **authenticationmode tulajdonsághoz** értékre állítja a **CloudAndScope**.

A gyermek eszközökhöz csak egy szülő tartozhat. Minden szülő akár 100 gyermeket is tartalmazhat.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge az eszközök lehetnek szülők és gyermekek is transzparens átjárói kapcsolatokban. Több IoT Edge-eszköz hierarchiája is létrehozható egymással. Az átjárók hierarchiájának legfelső csomópontja akár öt generációval is rendelkezhet. Egy IoT Edge eszköz például öt réteggel rendelkezhet az alatta lévő gyermekként IoT Edge eszközökhöz. Az ötödik generációs IoT Edge eszköznek azonban nem lehet gyermeke, IoT Edge vagy más.
::: moniker-end

#### <a name="gateway-discovery"></a>Átjáró felderítése

A gyermek eszköznek képesnek kell lennie a fölérendelt eszköz megkeresésére a helyi hálózaton. Konfigurálja az átjáró-eszközöket egy **állomásnévvel**, vagy egy teljes tartománynevet (FQDN) vagy egy IP-címet, amelyet a gyermeke eszközei a kereséséhez fognak használni.

Az alsóbb rétegbeli IoT eszközökön használja a **gatewayHostname** paramétert a kapcsolódási karakterláncban, hogy a fölérendelt eszközre mutasson.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Az alsóbb rétegbeli IoT Edge eszközökön használja a konfigurációs fájl **parent_hostname** paraméterét, hogy a szülő eszközre mutasson.
::: moniker-end

#### <a name="secure-connection"></a>Biztonságos kapcsolatok

A szülő és a gyermek eszközöknek is hitelesíteniük kell egymással a kapcsolataikat. Minden eszköznek szüksége van egy megosztott legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány másolatára, amelyet a gyermek eszközök használnak annak ellenőrzéséhez, hogy a megfelelő átjáróhoz csatlakoznak-e.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Ha több IoT Edge átjáró csatlakozik egymáshoz egy átjáró-hierarchiában, akkor a hierarchiában lévő összes eszköznek egyetlen tanúsítványláncot kell használnia.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Az eszköz képességei átlátható átjárók mögött

Az IoT Edge üzenetkezelési folyamatával dolgozó összes IoT Hub-primitív támogatja az átlátszó átjárók forgatókönyveit is. Minden IoT Edge átjáró rendelkezik az azon keresztül érkező üzenetek tárolására és továbbítására szolgáló képességekkel.

Az alábbi táblázat segítségével megtekintheti, hogy a különböző IoT Hub képességek hogyan támogatottak az eszközökhöz képest az átjárók mögötti eszközökhöz képest.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

| Képesség | IoT-eszköz | IoT az átjáró mögött |
| ---------- | ---------- | -------------------- |
| [Eszközről a felhőbe irányuló (D2C) üzenetek](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Igen – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Felhőből eszközre (C2D) vonatkozó üzenetek](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Igen – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – IoT gyermek C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [Közvetlen metódusok](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Igen – IoT Direct metódus](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT közvetlen metódus](./media/iot-edge-as-gateway/check-yes.png) |
| Az [eszközök ikrek](../iot-hub/iot-hub-devguide-device-twins.md) és [modul ikrek](../iot-hub/iot-hub-devguide-module-twins.md) | ![Igen – IoT ikrek](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT ikrek](./media/iot-edge-as-gateway/check-yes.png) |
| [Fájlfeltöltés](../iot-hub/iot-hub-devguide-file-upload.md) | ![Igen – IoT fájl feltöltése](./media/iot-edge-as-gateway/check-yes.png) | ![Nem IoT-fájl feltöltése](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Képesség | IoT-eszköz | IoT az átjáró mögött | IoT Edge-eszköz | IoT Edge átjáró mögött |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [Eszközről a felhőbe irányuló (D2C) üzenetek](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Igen – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Felhőből eszközre (C2D) vonatkozó üzenetek](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Igen – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – IoT gyermek C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Nem IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![Nem IoT Edge gyermek C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [Közvetlen metódusok](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Igen – IoT Direct metódus](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT közvetlen metódus](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – IoT Edge közvetlen metódus](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT Edge közvetlen metódus](./media/iot-edge-as-gateway/check-yes.png) |
| Az [eszközök ikrek](../iot-hub/iot-hub-devguide-device-twins.md) és [modul ikrek](../iot-hub/iot-hub-devguide-module-twins.md) | ![Igen – IoT ikrek](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT ikrek](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – IoT Edge ikrek](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT Edge ikrek](./media/iot-edge-as-gateway/check-yes.png) |
| [Fájlfeltöltés](../iot-hub/iot-hub-devguide-file-upload.md) | ![Igen – IoT fájl feltöltése](./media/iot-edge-as-gateway/check-yes.png) | ![Nem IoT-fájl feltöltése](./media/iot-edge-as-gateway/crossout-no.png) | ![Nincs IoT Edge fájlfeltöltés](./media/iot-edge-as-gateway/crossout-no.png) | ![Nem IoT Edge gyermek fájlfeltöltés](./media/iot-edge-as-gateway/crossout-no.png) |
| Tárolói képek lekérése |   |   | ![Igen – IoT Edge tároló lekérése](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT Edge tároló lekérése](./media/iot-edge-as-gateway/check-yes.png) |
| BLOB feltöltése |   |   | ![Igen – IoT Edge blob feltöltése](./media/iot-edge-as-gateway/check-yes.png) | ![Igen – gyermek IoT Edge blob feltöltése](./media/iot-edge-as-gateway/check-yes.png) |

A **tárolók lemezképeit** letöltheti, tárolhatja és elküldheti a szülői eszközökről a gyermek eszközökre.

A **Blobok**, beleértve a támogatási csomagokat és a naplókat, a gyermek eszközökről a szülő eszközökre tölthetők fel.

::: moniker-end

## <a name="translation-gateways"></a>Fordítási átjárók

Ha az alsóbb rétegbeli eszközök nem tudnak csatlakozni a IoT Hubhoz, akkor a IoT Edge átjárónak fordítóként kell működnie. Ez a minta gyakran olyan eszközök esetében szükséges, amelyek nem támogatják a MQTT, a AMQP vagy a HTTP protokollt. Mivel ezek az eszközök nem tudnak csatlakozni a IoT Hubhoz, nem tudnak előfeldolgozás nélkül csatlakozni az IoT Edge hub-modulhoz.

Azokat az egyéni vagy harmadik féltől származó modulokat, amelyek gyakran az alsóbb rétegbeli eszköz hardverére vagy protokollra vonatkoznak, telepíteni kell az IoT Edge-átjáróra. Ezek a fordítási modulok a beérkező üzeneteket a IoT Hub által értelmezhető formátumba helyezik el.

A fordítási átjárók esetében két minta létezik: a *protokollok fordítása* és az *identitások fordítása*.

![Diagram – fordítási átjáró mintái](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Protokoll fordítása

A protokoll-fordítási átjáró mintájában csak az IoT Edge átjáró rendelkezik IoT Hubval rendelkező identitással. A fordítási modul üzeneteket fogad az alárendelt eszközökről, lefordítja őket egy támogatott protokollra, majd a IoT Edge eszköz az alárendelt eszközök nevében küldi az üzeneteket. Minden információ úgy tűnik, hogy egy eszközről, az átjáróról származik. Az alsóbb réteg eszközeinek további azonosító információkat kell beágyazniuk az üzenetekbe, ha a felhőalkalmazásoknak eszközönként is elemezniük kell az adatokat. Emellett IoT Hub primitívek, például az ikrek és a közvetlen metódusok csak az átjáró-eszközön támogatottak, nem az alsóbb rétegbeli eszközökön. Az ebben a mintában lévő átjárók átlátszó átjárókkal szemben *átlátszatlannak* tekintendők, mert nem fedik le az alsóbb rétegbeli eszközök identitását.

A protokollok fordítása támogatja az erőforrás által korlátozott eszközöket. Számos meglévő eszköz olyan adatmennyiséget állít elő, amely az üzleti elemzéseket képes kiszolgálni. azonban nem a Felhőbeli kapcsolattal lettek tervezve. Az átlátszatlan átjárók lehetővé teszik, hogy ezeket az adatfájlokat egy IoT-megoldásban feloldják és használják.

### <a name="identity-translation"></a>Identitás fordítása

Az Identity Translation Gateway minta a protokoll fordítására épül, de a IoT Edge átjáró IoT Hub eszköz identitását is biztosítja az alárendelt eszközök nevében. A fordítási modul felelős az alárendelt eszközök által használt protokoll megismeréséhez, identitásuk biztosításához, valamint az üzenetek IoT Hub primitívek számára való lefordításához. Az alsóbb rétegbeli eszközök az IoT Hub felé első szintű, ikrekkel és metódusokkal rendelkező eszközökként jelennek meg. A felhasználók úgy kezelhetik az eszközöket az IoT Hubban, hogy nem tudnak a köztes átjáróeszközről.

Az Identity Translation biztosítja a protokollok fordításának előnyeit, továbbá lehetővé teszi a felhőből származó alsóbb szintű eszközök teljes kezelhetőségét. A IoT-megoldásban lévő összes eszköz megjelenik a IoT Hub az általuk használt protokolltól függetlenül.

### <a name="device-capabilities-behind-translation-gateways"></a>Eszközök képességei a fordítási átjárók mögött

Az alábbi táblázat azt ismerteti, hogyan terjednek ki IoT Hub szolgáltatások az alsóbb rétegbeli eszközökre a fordítási átjárók mintái között.

| Képesség | Protokoll fordítása | Identitás fordítása |
| ---------- | -------------------- | -------------------- |
| A IoT Hub Identity registryben tárolt identitások | Csak az átjáró eszközének identitása | Az összes csatlakoztatott eszköz identitása |
| Ikereszközök | Csak az átjáró rendelkezik egy eszköz és egy modul ikrekkel | Minden csatlakoztatott eszközhöz saját eszköz tartozik |
| Közvetlen metódusok és a felhőből az eszközre irányuló üzenetek | A felhő csak az átjáró eszközét tudja kezelni | A felhő minden egyes csatlakoztatott eszközt külön tud kezelni |
| [IoT Hub szabályozások és kvóták](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Alkalmazás az átjáró eszközre | Alkalmazás minden eszközre |

A protokoll fordítási mintájának használatakor az átjárón keresztül csatlakozó összes eszköz ugyanazzal a Felhőbeli eszköz-várólistával rendelkezik, amely legfeljebb 50 üzenetet tartalmazhat. Csak akkor használja ezt a mintát, ha kevés eszköz csatlakozik az egyes mezők átjáróján keresztül, és a felhőből az eszközre irányuló forgalom alacsony.

A IoT Edge futtatókörnyezet nem tartalmaz protokoll-vagy identitás-fordítási képességeket. Ezeknek a mintáknak olyan egyéni vagy külső modulokra van szükségük, amelyek gyakran a használt hardverre és protokollra vonatkoznak. Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) számos protokoll-fordítási modult tartalmaz, amelyek közül választhat. Az identitás-fordítási mintát használó minta: [Azure IoT Edge LoRaWAN Starter Kit](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="next-steps"></a>Következő lépések

A következő három lépésből megtudhatja, hogyan állíthat be transzparens átjárót:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon](how-to-authenticate-downstream-device.md)
* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)
