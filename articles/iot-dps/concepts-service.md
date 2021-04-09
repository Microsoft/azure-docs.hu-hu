---
title: Az Azure IoT Hub Device Provisioning Service használatával használt terminológia | Microsoft Docs
description: Az eszközök kiépítési szolgáltatásával (DPS) és a IoT Hubsal használt általános terminológiát ismerteti.
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019446"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>IoT Hub Device Provisioning Service (DPS) terminológia

A IoT Hub Device Provisioning Service egy olyan IoT Hub segítő szolgáltatás, amellyel a nulla érintéses eszköz kiépíthető a megadott IoT hubhoz. A Device kiépítési szolgáltatással több millió eszköz is [kiépíthető](about-iot-dps.md#provisioning-process) biztonságos és skálázható módon.

Az eszközök kiépítési folyamata két részből áll. Az első rész a kezdeti kapcsolatot hozza létre az eszköz és a IoT megoldás között az eszköz *regisztrálásával* . A második rész a megoldás konkrét követelményei alapján alkalmazza a megfelelő *konfigurációt* az eszközre. Ha mindkét lépést végrehajtotta, az eszköz teljes mértékben *kiépítve* lett. A Device Provisioning Service a két lépés automatizálásával biztosítja az eszközök zökkenőmentes kiépítését.

Ez a cikk áttekintést nyújt a *szolgáltatás* kezeléséhez leginkább alkalmazandó kiépítési fogalmakról. Ez a cikk a [felhő telepítési lépésében](about-iot-dps.md#cloud-setup-step) érintett personák szempontjából a legfontosabb, hogy az eszköz készen álljon az üzembe helyezésre.

## <a name="service-operations-endpoint"></a>Szolgáltatási műveletek végpontja

A szolgáltatási műveletek végpontja a szolgáltatás beállításainak kezeléséhez és a beléptetési lista karbantartásához használt végpont. Ezt a végpontot csak a szolgáltatás rendszergazdája használja; az eszközök nem használják.

## <a name="device-provisioning-endpoint"></a>Eszköz kiépítési végpontja

Az eszköz kiépítési végpontja az egyetlen végpont, amelyet minden eszköz használ az automatikus kiépítés során. Az URL-cím megegyezik az összes kiépítési szolgáltatási példány esetében, így nincs szükség az eszközök új kapcsolódási információkkal való újratöltésére az ellátási lánc forgatókönyvei között. Az azonosító hatóköre biztosítja a bérlő elkülönítését.

## <a name="linked-iot-hubs"></a>Csatolt IoT hubok

A Device kiépítési szolgáltatás csak olyan IoT-hubok számára tud eszközöket kiépíteni, amelyek hozzá lettek kapcsolva. Az IoT hub és az eszköz kiépítési szolgáltatásának egy példányához való csatolásával a szolgáltatás olvasási/írási jogosultságot biztosít az IoT hub eszközének beállításjegyzékéhez; a hivatkozással egy eszköz kiépítési szolgáltatása regisztrálhat egy eszköz-azonosítót, és beállíthatja a kezdeti konfigurációt az eszköz Twin-ben. A csatolt IoT hubok bármelyik Azure-régióban lehetnek. A kiépítési szolgáltatáshoz más előfizetésekben lévő hubokat is csatolhat.


## <a name="allocation-policy"></a>Kiosztási szabályzat

A szolgáltatás szintjének beállítása, amely meghatározza, hogy az eszköz kiépítési szolgáltatása hogyan rendeljen eszközöket az IoT hubhoz. Három támogatott kiosztási szabályzat létezik:

* **Egyenletesen súlyozott eloszlás**: a csatolt IoT hubok ugyanilyen valószínűséggel rendelkeznek a számukra kiépített eszközökkel. Az alapértelmezett beállítás. Akkor érdemes megtartani, ha csak egy IoT-központban épít ki eszközöket.

* **Legalacsonyabb késés**: az eszközök egy IoT hubhoz vannak kiépítve, az eszköz legalacsonyabb késéssel. Ha több összekapcsolt IoT-hubok ugyanazt a legalacsonyabb késést nyújtják, a kiépítési szolgáltatás az adott hubokban lévő eszközöket

* **Statikus konfiguráció a beléptetési listán**: a beléptetési listán a kívánt IoT hub specifikációja elsőbbséget élvez a szolgáltatási szintű kiosztási szabályzattal szemben.

* **Egyéni (Azure-függvény használata)**: az egyéni foglalási szabályzatok segítségével jobban szabályozható, hogy az eszközök hogyan legyenek hozzárendelve az IoT hubhoz. Ezt egy Azure-függvény egyéni kódjának használatával végezheti el az eszközök IoT-hubhoz való hozzárendeléséhez. Az eszköz kiépítési szolgáltatása meghívja az Azure-függvény kódját, amely az eszközre vonatkozó összes releváns információt és a kód regisztrálását kéri. A rendszer végrehajtja a függvény kódját, és az eszköz kiépítési IoT hub-információit adja vissza.

## <a name="enrollment"></a>Beléptetés

A regisztráció az automatikus kiépítés útján regisztrálhatók eszközeinek vagy csoportjainak a nyilvántartása. A beléptetési rekord információt tartalmaz az eszközről vagy az eszközök csoportjából, beleértve a következőket:
- az eszköz által használt [igazolási mechanizmus](#attestation-mechanism)
- a választható kezdeti kívánt konfiguráció
- kívánt IoT hub
- a kívánt eszköz azonosítója

A Device kiépítési szolgáltatás két típusú regisztrációt támogat:

### <a name="enrollment-group"></a>Beléptetési csoport

A regisztrációs csoport olyan eszközök csoportja, amelyek osztoznak egy adott igazolási mechanizmuson. A regisztrációs csoportok az X. 509 és a szimmetrikus is támogatást támogatják. Az X. 509 beléptetési csoportban lévő összes eszköz X. 509 tanúsítványokat tartalmaz, amelyeket ugyanahhoz a legfelső szintű vagy köztes hitelesítésszolgáltató (CA) írta alá. A szimmetrikus kulcs beléptetése csoportban lévő minden eszköz a csoport szimmetrikus kulcsból származtatott SAS-jogkivonatokat mutat be. A regisztrációs csoport neve és a tanúsítvány neve csak alfanumerikus, kisbetűs és kötőjeleket tartalmazhat.

> [!TIP]
> Azt javasoljuk, hogy nagy számú, a kívánt kezdeti konfigurációt megosztó eszközhöz használjon beléptetési csoportot, vagy ha az összes eszköz ugyanahhoz a bérlőhöz fog csatlakozni.

### <a name="individual-enrollment"></a>Egyéni regisztráció

Az egyéni regisztráció egy olyan bejegyzés, amely regisztrálható egyetlen eszközön. Az egyéni regisztrációk X. 509 levél-tanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális TPM-ből) is használhatnak igazolási mechanizmusként. Az egyéni regisztrációban szereplő regisztrációs azonosító alfanumerikus, kisbetűs, és tartalmazhat kötőjeleket. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

> [!TIP]
> Ajánlott egyéni regisztrációkat használni olyan eszközökhöz, amelyek egyedi kezdeti konfigurációt igényelnek, vagy olyan eszközökön, amelyek csak SAS-tokenekkel tudnak hitelesíteni a TPM-igazolás segítségével.


## <a name="attestation-mechanism"></a>Igazolási mechanizmus

Az igazolási mechanizmus az eszköz identitásának megerősítésére szolgál. Az igazolási mechanizmus egy regisztrációs bejegyzésen van konfigurálva, és közli a kiépítési szolgáltatással, hogy melyik módszert kell használni az eszköz identitásának ellenőrzéséhez a regisztráció során.

> [!NOTE]
> IoT Hub a "hitelesítési séma" kifejezést használja az adott szolgáltatáshoz hasonló fogalomhoz.

A Device kiépítési szolgáltatás a következő igazolási formákat támogatja:
* **X. 509 tanúsítványok** a szabványos x. 509 tanúsítvány-hitelesítési folyamat alapján. További információ: [X. 509 igazolás](concepts-x509-attestation.md).
* **Platformmegbízhatósági modul (TPM)** egy egyszeres kihívás alapján, a kulcsokhoz tartozó TPM standard használatával egy aláírt közös hozzáférésű aláírási (SAS-) tokent mutat be. Ehhez nincs szükség fizikai TPM-re az eszközön, de a szolgáltatás a [TPM-specifikáció](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)alapján tanúsítja, hogy a hátirat kulcsát használja. További információ: TPM- [igazolás](concepts-tpm-attestation.md).
* A **szimmetrikus kulcs** a közös hozzáférésű aláírás (SAS) [biztonsági jogkivonatok](../iot-hub/iot-hub-devguide-security.md#security-tokens)alapján, amelyek egy kivonatoló aláírást és egy beágyazott lejáratot foglalnak magukban. További információ: [szimmetrikus kulcs igazolása](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A hardveres biztonsági modul, vagy a HSM biztonságos, hardveres tárolást biztosít az eszköz titkai számára, és a titkos tároló legbiztonságosabb formája. Az X. 509 tanúsítványok és az SAS-tokenek a HSM-ben is tárolhatók. A HSM a kiépítési szolgáltatás által támogatott igazolási mechanizmusokkal is használható.

> [!TIP]
> Javasoljuk, hogy használjon egy HSM-et az eszközökkel, hogy biztonságosan tárolja az eszközein a titkokat.

Az eszköz titkai a szoftverben (memóriában) is tárolhatók, de ez a tárterület kevésbé biztonságos, mint a HSM.



## <a name="id-scope"></a>AZONOSÍTÓ hatóköre

Az azonosító hatóköre egy eszköz kiépítési szolgáltatásához van hozzárendelve, amikor a felhasználó hozza létre, és az az eszköz, amelyet az adott kiépítési szolgáltatás egyedi azonosítására használ, az eszköz regisztrálva lesz. Az azonosító hatókörét a szolgáltatás hozza létre, és nem módosítható, ami garantálja az egyediséget.

> [!NOTE]
> Az egyediség fontos a hosszan futó üzembe helyezési műveletek, az egyesítési és a beszerzési forgatókönyvek esetében.


## <a name="registration"></a>Regisztráció

A regisztráció egy eszköz rekordja, amely sikeresen regisztrálja/kiépít egy IoT Hub az eszköz kiépítési szolgáltatásán keresztül. A regisztrációs rekordok automatikusan létrejönnek; törölhetik őket, de nem frissíthetők.


## <a name="registration-id"></a>Regisztrációs azonosító

A regisztrációs azonosító segítségével egyedileg azonosíthatók az eszközök regisztrációja az eszköz kiépítési szolgáltatásával. Az eszköz AZONOSÍTÓjának egyedinek kell lennie a létesítési szolgáltatás [azonosítójának hatókörében](#id-scope). Minden eszköznek regisztrációs AZONOSÍTÓval kell rendelkeznie. A regisztrációs azonosító alfanumerikus, kis-és nagybetű nem különbözik, és tartalmazhat speciális karaktereket, beleértve a kettőspontot, az időtartamot, az aláhúzást és a kötőjelet.

* TPM esetén a regisztrációs azonosítót maga a TPM is megadja.
* X. 509-alapú igazolás esetén a regisztrációs azonosító a tanúsítvány tulajdonosának neveként van megadva.

## <a name="device-id"></a>Eszközazonosító

Az eszköz azonosítója a IoT Hubban megjelenő azonosító. Előfordulhat, hogy a kívánt eszköz azonosítója be van állítva a beléptetési bejegyzésben, de nem kötelező megadni. A kívánt eszköz AZONOSÍTÓjának beállítása csak az egyéni regisztrációk esetében támogatott. Ha nincs megadva a kívánt eszköz azonosítója a beléptetési listán, a rendszer a regisztrációs azonosítót használja az eszköz AZONOSÍTÓJAként az eszköz regisztrálása során. További információ a [IoT hub eszköz-azonosítókkal](../iot-hub/iot-hub-devguide-identity-registry.md)kapcsolatban.



## <a name="operations"></a>Üzemeltetés

A műveletek az eszköz kiépítési szolgáltatásának számlázási egységei. Egy művelet egy utasítás sikeres befejezése a szolgáltatás számára. A műveletek közé tartozik az eszközök regisztrációja és az újbóli regisztráció; a műveletek olyan szolgáltatás-oldali módosításokat is tartalmaznak, mint például a regisztrációs lista bejegyzéseinek hozzáadása és a regisztrációs lista bejegyzéseinek frissítése.
