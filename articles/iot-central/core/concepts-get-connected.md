---
title: Eszköz csatlakoztatása az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk bemutatja az Azure-beli eszközök csatlakoztatásával kapcsolatos főbb fogalmakat IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: 828f0ff81048ca0b6f07b7fdee9ed29c87991db4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032636"
---
# <a name="get-connected-to-azure-iot-central"></a>Csatlakozás az Azure IoT Centralhoz

*Ez a cikk az operátorokra és az eszközök fejlesztőire vonatkozik.*

Ez a cikk azt ismerteti, hogyan csatlakozhatnak az eszközök egy Azure IoT Central-alkalmazáshoz. Ahhoz, hogy egy eszköz az IoT Central-mel tudja cserélni az adatcserét, a következőket kell tennie:

- *Hitelesítés*. A IoT Central alkalmazással történő hitelesítés a _közös hozzáférésű aláírás (SAS) tokenjét_ vagy egy _X. 509 tanúsítványt_ használ. Az X. 509 tanúsítványokat éles környezetekben ajánlott használni.
- *Regisztráljon*. Az eszközöknek regisztrálva kell lenniük a IoT Central alkalmazásban. A regisztrált eszközök az alkalmazás **eszközök** lapján tekinthetők meg.
- *Társítson egy eszköz sablonnal*. IoT Central alkalmazásban az eszközök sablonjai határozzák meg azt a felhasználói felületet, amelyet a kezelő a csatlakoztatott eszközök megtekintéséhez és kezeléséhez használ.

A IoT Central a következő két eszköz regisztrációs forgatókönyveket támogatja:

- *Automatikus regisztráció*. A rendszer automatikusan regisztrálja az eszközt az első csatlakozáskor. Ez a forgatókönyv lehetővé teszi, hogy a számítógépgyártók tömeges előállítsák az első regisztráció nélkül csatlakoztatható eszközöket. Az OEM a megfelelő eszköz hitelesítő adatait hozza létre, és konfigurálja az eszközöket a gyárban. Megkövetelheti, hogy az operátor az adatok küldésének megkezdése előtt jóváhagyja az eszközt. Ehhez a forgatókönyvhöz egy X. 509 vagy SAS _csoportos regisztrációt_ kell konfigurálnia az alkalmazásban.
- *Manuális regisztráció*. A kezelők vagy az eszközök lapon regisztrálják az egyes eszközöket, vagy [importálnak egy CSV-fájlt](howto-manage-devices.md#import-devices) **az eszközök** tömeges regisztrálásához. Ebben a forgatókönyvben X. 509 vagy SAS _csoportos regisztrációt_, illetve X. 509 vagy sas _Egyéni regisztrációt_ használhat.

A IoT Centralhoz csatlakozó eszközöknek a *IoT Plug and Play konvenciókat* kell követniük. Ezen konvenciók egyike az, hogy az eszköznek el kell küldenie az eszköz modell- _azonosítóját_ , amelyet a csatlakozáskor implementál. A modell azonosítója lehetővé teszi, hogy a IoT Central alkalmazás társítsa az eszközt a megfelelő eszköz sablonnal.

A IoT Central az [Azure IoT hub Device Provisioning szolgáltatást (DPS)](../../iot-dps/about-iot-dps.md) használja a kapcsolódási folyamat kezeléséhez. Az eszköz először csatlakozik egy DPS-végponthoz, hogy lekérje az alkalmazáshoz való csatlakozáshoz szükséges információkat. Belsőleg a IoT Central-alkalmazás egy IoT hub használatával kezeli az eszköz kapcsolatát. A DPS használata lehetővé teszi a következőket:

- IoT Central az eszközök nagy léptékű bevezetésének és csatlakoztatásának támogatásához.
- Az eszköz hitelesítő adatainak előállításához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztül történő regisztrálása nélkül.
- A saját eszközök azonosítóinak használatával regisztrálhatja az eszközöket a IoT Centralban. A saját eszköz-azonosítók használatával egyszerűbbé válik a meglévő Back-Office rendszerekkel való integráció.
- Egyetlen, egységes módszer az eszközök IoT Centralhoz való csatlakoztatására.

Ez a cikk a következő eszköz-csatlakoztatási lépéseket ismerteti:

- [X. 509 csoportos regisztráció](#x509-group-enrollment)
- [SAS-csoport beléptetése](#sas-group-enrollment)
- [Egyéni regisztráció](#individual-enrollment)
- [Eszközregisztráció](#device-registration)
- [Eszköz hozzárendelése eszköz sablonnal](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>X. 509 csoportos regisztráció

Éles környezetben az X. 509 tanúsítványok használata az ajánlott eszköz hitelesítési mechanizmusa IoT Central számára. További információért lásd: [az eszközök hitelesítése X. 509 hitelesítésszolgáltatói tanúsítványokkal](../../iot-hub/iot-hub-x509ca-overview.md).

X. 509 tanúsítvánnyal rendelkező eszköz csatlakoztatása az alkalmazáshoz:

1. Hozzon létre egy *regisztrációs csoportot* , amely a **tanúsítványok (X. 509)** igazolási típust használja.
1. Adjon hozzá és ellenőrizzen egy közbenső vagy gyökérszintű X. 509 tanúsítványt a beléptetési csoportban.
1. Egy levélből álló tanúsítvány létrehozása a beléptetési csoportban lévő legfelső szintű vagy köztes tanúsítványból. Küldje el a levél tanúsítványát az eszközről az alkalmazáshoz való csatlakozáskor.

További információ: [eszközök csatlakoztatása X. 509 tanúsítványokkal](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>Csak tesztelési célokra

Csak tesztelés esetén a következő segédprogramok segítségével hozhatja ki a gyökér-, köztes és eszköz-tanúsítványokat:

- [Eszközök az Azure IoT Device kiépítési eszköz SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)-hoz: Node.js eszközök gyűjteménye, amelyek X. 509 tanúsítványok és kulcsok létrehozásához és ellenőrzéséhez használhatók.
- Ha fejlesztői készlet eszközt használ, a [parancssori eszköz](https://aka.ms/iotcentral-docs-dicetool) létrehoz egy hitelesítésszolgáltatói tanúsítványt, amelyet hozzáadhat a IoT Central alkalmazáshoz a tanúsítványok ellenőrzéséhez.
- [Tesztelési hitelesítésszolgáltatói tanúsítványok kezelése mintákhoz és oktatóanyagokhoz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): PowerShell-és bash-parancsfájlok gyűjteménye a következőhöz:
  - Hozzon létre egy tanúsítványláncot.
  - Mentse a tanúsítványokat. cer fájlként a IoT Central alkalmazásba való feltöltéshez.
  - Az ellenőrző tanúsítvány létrehozásához használja a IoT Central alkalmazásban található ellenőrző kódot.
  - Hozzon létre Leaf-tanúsítványokat az eszközökhöz az eszköz azonosítói alapján az eszköz paraméterének megfelelően.

## <a name="sas-group-enrollment"></a>SAS-csoport beléptetése

Eszköz SAS-kulccsal való csatlakoztatása az alkalmazáshoz:

1. Hozzon létre egy *regisztrációs csoportot* , amely a **Shared Access Signature (SAS)** igazolási típust használja.
1. Másolja a csoport elsődleges vagy másodlagos kulcsát a beléptetési csoportból.
1. Használja az Azure CLI-t egy eszköz kulcs létrehozásához a csoport kulcsból:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Használja a generált eszköz kulcsát, amikor az eszköz csatlakozik a IoT Central alkalmazáshoz.

## <a name="individual-enrollment"></a>Egyéni regisztráció

A saját hitelesítő adataival rendelkező eszközöket összekötő ügyfelek egyéni regisztrációkat használhatnak. Az egyéni regisztráció egyetlen, a csatlakozáshoz engedélyezett eszközre vonatkozó bejegyzés. Az egyéni regisztrációk X. 509 levél-tanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális platformmegbízhatósági modulból) is használhatnak igazolási mechanizmusként. Az eszköz AZONOSÍTÓi betűket, számokat és karaktert tartalmazhatnak `-` . További információ: [DPS egyéni regisztráció](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Amikor egyéni regisztrációt hoz létre egy eszközhöz, az a IoT Central alkalmazás alapértelmezett csoportos regisztrálási lehetőségeivel szemben elsőbbséget élvez.

### <a name="create-individual-enrollments"></a>Egyéni regisztrációk létrehozása

IoT Central a következő igazolási mechanizmusokat támogatja az egyéni regisztrációk esetén:

- **Szimmetrikus kulcs igazolása:** A szimmetrikus kulcs igazolása egyszerű módszer egy eszköz a DPS-példánnyal való hitelesítésére. A szimmetrikus kulcsokat használó egyéni regisztráció létrehozásához nyissa meg az eszközhöz tartozó **eszköz kapcsolat** lapját, válassza az **Egyéni regisztráció** lehetőséget a kapcsolódási módszerként, valamint a **közös hozzáférésű aláírást (SAS)** mechanizmusként. Adja meg a Base64 kódolású elsődleges és másodlagos kulcsot, és mentse a módosításokat. Az eszköz csatlakoztatásához használja az **azonosító hatókörét**, az **eszköz azonosítóját** és az elsődleges vagy a másodlagos kulcsot.

    > [!TIP]
    > A teszteléshez az **OpenSSL** használatával Base64 kódolású kulcsokat hozhatja elő: `openssl rand -base64 64`

- **X. 509 tanúsítványok:** X. 509 tanúsítvánnyal rendelkező egyéni regisztráció létrehozásához nyissa meg az **eszköz kapcsolódása** lapot, válassza az **Egyéni regisztráció** lehetőséget a kapcsolódási módszerként, valamint a **tanúsítványok (X. 509)** módszert. Az egyéni beléptetési bejegyzésekhez használt eszközök tanúsítványainak követelménye, hogy a kiállító és a tulajdonos CN az eszköz AZONOSÍTÓját adja meg.

    > [!TIP]
    > A teszteléshez használhatja [Az Azure IoT Device kiépítési eszköz SDK ](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) -t a Node.jsszámára egy önaláírt tanúsítvány létrehozásához: `node create_test_cert.js device "mytestdevice"`

- **Platformmegbízhatósági modul (TPM) igazolása:** A [TPM](../../iot-dps/concepts-tpm-attestation.md) a hardveres biztonsági modul típusa. Az eszköz csatlakoztatásának egyik legbiztonságosabb módja a TPM használata. Ez a cikk feltételezi, hogy diszkrét, belső vezérlőprogramot vagy integrált TPM-t használ. A szoftveresen emulált TPM a prototípus-készítéshez és a teszteléshez megfelelőek, de nem biztosítják ugyanazt a biztonsági szintet, mint a diszkrét, belső vezérlőprogram vagy integrált TPM. Ne használja éles környezetben a szoftver TPM. TPM-t használó egyéni regisztráció létrehozásához nyissa meg az **eszköz kapcsolódása** lapot, válassza az **Egyéni regisztráció** a kapcsolódási módszerként és a **TPM** mechanizmusként lehetőséget. Adja meg a TPM-záradék kulcsát, és mentse az eszköz csatlakoztatási adatait.

## <a name="device-registration"></a>Eszközregisztráció

Ahhoz, hogy egy eszköz csatlakozhasson IoT Central alkalmazáshoz, regisztrálnia kell az alkalmazásban:

- Az eszközök automatikusan regisztrálhatják magukat, amikor először csatlakoznak. Ennek a lehetőségnek a használatához [X. 509 csoportos regisztráció](#x509-group-enrollment) vagy [sas-csoport beléptetését](#sas-group-enrollment)kell használnia.
- Egy operátor importálhat egy CSV-fájlt az alkalmazásban lévő eszközök listájának tömeges regisztrálásához.
- Egy operátor manuálisan is regisztrálhat egy adott eszközt az alkalmazás **eszközök** lapján.

IoT Central lehetővé teszi a számítógépgyártók számára, hogy tömeges előállítsák a magukat automatikusan regisztrálni képes eszközöket. Az OEM a megfelelő eszköz hitelesítő adatait hozza létre, és konfigurálja az eszközöket a gyárban. Amikor az ügyfél első alkalommal bekapcsol egy eszközt, az csatlakozik a DPS-hez, amely ezután automatikusan összekapcsolja az eszközt a helyes IoT Central alkalmazással. Megkövetelheti, hogy az operátor jóváhagyja az eszközt, mielőtt megkezdi az adatok küldését az alkalmazásba.

> [!TIP]
> Az **adminisztráció > eszköz kapcsolatai** lapon az **automatikus jóváhagyás** beállítás azt határozza meg, hogy az operátornak manuálisan kell-e jóváhagynia az eszközt, mielőtt az adatok küldését el tudja kezdeni.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>X. 509 tanúsítványokat használó eszközök automatikus regisztrálása

1. Az [X. 509 beléptetési csoportba](#x509-group-enrollment)felvett legfelső szintű vagy köztes tanúsítvány használatával hozhatja elő a levelek tanúsítványait az eszközökhöz. Használja az eszköz azonosítóit a `CNAME` levélben lévő tanúsítványokban. Az eszköz AZONOSÍTÓi betűket, számokat és karaktert tartalmazhatnak `-` .

1. OEM-ként minden eszközön egy eszköz azonosítója, egy generált X. 509 levél-tanúsítvány és az alkalmazás- **azonosító hatóköre** érték jelenik meg. Az eszköz kódjának is el kell küldenie az általa implementált eszköz modell-AZONOSÍTÓját.

1. Amikor bekapcsol egy eszközt, először csatlakozik a DPS-hez, és lekéri a IoT Central kapcsolati adatait.

1. Az eszköz a DPS használatával csatlakozik a szolgáltatáshoz, és regisztrálja az alkalmazást IoT Central alkalmazásával.

A IoT Central alkalmazás az eszköz által eljuttatott modell AZONOSÍTÓját használja a [regisztrált eszköz egy eszköz sablonnal való hozzárendeléséhez](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>SAS-tokeneket használó eszközök automatikus regisztrálása

1. Másolja a csoport elsődleges kulcsát az **sas-IoT-Devices** beléptetési csoportból:

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Elsődleges kulcs csoportosítása SAS-IoT-Devices beléptetési csoportból":::

1. Az `az iot central device compute-device-key` eszköz sas-kulcsainak létrehozásához használja az parancsot. Használja az előző lépésben a csoport elsődleges kulcsát. Az eszköz azonosítója betűket, számokat és a karaktert is tartalmazhatja `-` :

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. OEM-ként a Flash minden eszközt az eszköz AZONOSÍTÓjának, a generált eszköz SAS-kulcsának és az alkalmazás- **azonosító hatókörének** értékének megfelelően. Az eszköz kódjának is el kell küldenie az általa implementált eszköz modell-AZONOSÍTÓját.

1. Amikor bekapcsol egy eszközt, először csatlakozik a DPS-hez, hogy beolvassa a IoT Central regisztrációs adatait.

1. Az eszköz a DPS használatával csatlakozik a szolgáltatáshoz, és regisztrálja az alkalmazást IoT Central alkalmazásával.

A IoT Central alkalmazás az eszköz által eljuttatott modell AZONOSÍTÓját használja a [regisztrált eszköz egy eszköz sablonnal való hozzárendeléséhez](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Eszközök tömeges regisztrálása előre

Ha nagy számú eszközt szeretne regisztrálni a IoT Central alkalmazással, használjon egy CSV-fájlt az eszközök [azonosítóinak és az eszközök nevének importálásához](howto-manage-devices.md#import-devices).

Ha az eszköze SAS-tokeneket használ a hitelesítéshez, [exportáljon egy CSV-fájlt a IoT Central alkalmazásból](howto-manage-devices.md#export-devices). Az exportált CSV-fájl tartalmazza az eszközök azonosítóit és a SAS-kulcsokat.

Ha az eszközök X. 509 tanúsítványokat használnak a hitelesítéshez, az x. 509-es levél-tanúsítványokat az X. 509 regisztrációs csoportba feltöltött legfelső szintű vagy köztes tanúsítvány használatával hozhatja ki az eszközökön. Használja az importált eszközök azonosítóit a `CNAME` levél tanúsítványainak értékeként.

Az eszközöknek az alkalmazás **azonosító hatókörének** értékét kell használniuk, és a csatlakozáskor meg kell küldeni a modell azonosítóját.

> [!TIP]
> Az **azonosító hatókörének** értékét az **Adminisztráció > eszköz kapcsolataiban** találja.

### <a name="register-a-single-device-in-advance"></a>Egyetlen eszköz regisztrálása előre

Ez a megközelítés akkor lehet hasznos, ha IoT Central vagy tesztelési eszközökkel kísérletezik. Az **eszközök** lapon válassza az **+ új** lehetőséget az egyéni eszközök regisztrálásához. Az eszköz csatlakoztatására szolgáló SAS-kulcsok használatával csatlakoztathatja az eszközt a IoT Central alkalmazáshoz. Másolja az _eszköz sas-kulcsát_ a regisztrált eszközhöz tartozó kapcsolatok adataiból:

![SAS-kulcsok egy adott eszközhöz](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Eszköz hozzárendelése eszköz sablonnal

IoT Central automatikusan társít egy eszközt egy eszközhöz, amikor az eszköz csatlakozik. Az eszköz a csatlakozáskor a [modell azonosítóját](../../iot-pnp/iot-plug-and-play-glossary.md#model-id) küldi el. IoT Central a modell AZONOSÍTÓját használja az adott eszközhöz tartozó sablon azonosítására. A felderítési folyamat a következőképpen működik:

1. Ha az eszköz sablonja már közzé van téve a IoT Central alkalmazásban, az eszköz társítva van az eszköz sablonnal.
1. Ha az eszköz sablonja még nincs közzétéve a IoT Central alkalmazásban, IoT Central az eszköz modelljét keresi a [nyilvános modell adattárában](https://github.com/Azure/iot-plugandplay-models). Ha IoT Central megkeresi a modellt, azt használja egy alapszintű eszköz sablonjának létrehozásához.
1. Ha IoT Central nem találja a modellt a nyilvános modell adattárában, az eszköz nem **társítottként** van megjelölve. Az operátor létrehozhat egy sablont az eszközhöz, majd áttelepítheti a nem társított eszközt az új eszköz sablonba.

Az alábbi képernyőfelvételen megtekintheti, hogyan tekintheti meg az IoT Centralban lévő eszközök modell-AZONOSÍTÓját. Egy eszköz sablonjában válasszon ki egy összetevőt, majd válassza az **identitás megtekintése** elemet:

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="A modell AZONOSÍTÓját ábrázoló képernyőfelvétel a termosztátos eszköz sablonjában.":::

A [termosztát modellt](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json) a nyilvános modell adattárában tekintheti meg. A modell AZONOSÍTÓjának definíciója a következőképpen néz ki:

```json
"@id": "dtmi:com:example:Thermostat;1"
```

## <a name="device-status-values"></a>Eszköz állapotának értékei

Ha egy valós eszköz csatlakozik a IoT Central alkalmazáshoz, az eszköz állapota a következőképpen változik:

1. Az eszköz állapota először **regisztrálva** van. Ez az állapot azt jelenti, hogy az eszköz a IoT Centralban jön létre, és rendelkezik egy eszköz azonosítójával. Az eszköz regisztrálása az alábbiak szerint történik:
    - A rendszer új valódi eszközt ad hozzá az **eszközök** lapon.
    - Az **eszközök** lapon az **Importálás** használatával adhat hozzá eszközöket.

1. Az **Eszközállapot akkor változik, ha a** IoT Central alkalmazáshoz az érvényes hitelesítő adatokkal csatlakozó eszköz befejezi a kiépítési lépést. Ebben a lépésben az eszköz a DPS használatával automatikusan lekéri a IoT Central alkalmazás által használt IoT Hub egy kapcsolódási karakterláncot. Az eszköz most már csatlakozhat IoT Centralhoz, és megkezdheti az adatok küldését.

1. Az operátor letilthat egy eszközt. Ha egy eszköz le van tiltva, nem tud adatküldést küldeni a IoT Central alkalmazásnak. A letiltott eszközök állapota **Letiltva**. Az operátornak vissza kell állítania az eszközt az adatok küldésének folytatásához. Ha egy operátor feloldja egy eszköz zárolását, az állapot visszatér az előző értékre, **regisztrálva** vagy **kiépítve**.

1. Ha az eszköz **jóváhagyásra vár**, azt jelenti, hogy az **automatikus jóváhagyás** lehetőség le van tiltva. Az operátornak explicit módon jóvá kell hagynia az eszközt az adatok küldésének megkezdése előtt. Az **Eszközök lapon nem** regisztrált eszközök, de az érvényes hitelesítő adatokkal való kapcsolat az eszköz állapota **jóváhagyásra vár**. Az operátorok a **jóváhagyás** gomb használatával hagyhatják jóvá ezeket az eszközöket az **eszközök** lapról.

1. Ha az eszköz állapota nincs **társítva**, az azt jelenti, hogy a IoT Centralhoz csatlakozó eszközhöz nincs társítva eszköz sablonja. Ez a helyzet általában a következő esetekben fordul elő:

    - A Devices ( **eszközök** ) lapon található **Importálás** használatával az eszközök egy készlete lesz hozzáadva az eszköz sablonjának megadása nélkül.
    - Egy eszköz regisztrálása manuálisan megtörténjen az **eszközök** lapon az eszköz sablonjának meghatározása nélkül. Az eszköz ezután érvényes hitelesítő adatokkal csatlakozik.  

    Az operátor az **eszközök** lapon az **áttelepítés** gomb használatával rendelheti hozzá az eszközt az eszközhöz.

## <a name="sdk-support"></a>SDK-támogatás

Az Azure-eszközök SDK-k az eszköz kódjának megvalósítására szolgáló legegyszerűbb megoldást nyújtanak. A következő eszköz-SDK-k érhetők el:

- [C nyelvhez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Pythonhoz készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node.js-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Javához készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Az SDK szolgáltatásai és IoT Hub kapcsolat

A IoT Hub az összes eszköz kommunikációja a következő IoT Hub kapcsolódási lehetőségeket használja:

- [Eszközről a felhőbe irányuló üzenetkezelés](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [A felhőből az eszközre irányuló üzenetküldés](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [Eszköz ikrek](../../iot-hub/iot-hub-devguide-device-twins.md)

Az alábbi táblázat összefoglalja, hogy az Azure IoT Central-eszköz funkciói hogyan képezhetők le IoT Hub funkciókra:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetria | Eszközről a felhőbe irányuló üzenetkezelés |
| Offline parancsok | A felhőből az eszközre irányuló üzenetküldés |
| Tulajdonság | Eszköz kettős jelentett tulajdonságai |
| Tulajdonság (írható) | Az eszköz Twin kívánt és jelentett tulajdonságai |
| Parancs | Közvetlen metódusok |

### <a name="protocols"></a>Protokollok

Az eszköz SDK-k a következő hálózati protokollokat támogatják az IoT hubhoz való csatlakozáshoz:

- MQTT
- AMQP
- HTTPS

További információ ezekről a különbségi protokollokról és az első kiválasztásáról: [kommunikációs protokoll kiválasztása](../../iot-hub/iot-hub-devguide-protocols.md).

Ha az eszköz nem tudja használni a támogatott protokollokat, használja a Azure IoT Edge a protokoll átalakításához. A IoT Edge az Azure IoT Central alkalmazásból történő feldolgozás kiszervezését is lehetővé teszi az egyéb hírszerzési helyzetekben.

## <a name="security"></a>Biztonság

Az eszközök és az Azure-IoT Central között kicserélt összes adatforgalom titkosítva van. IoT Hub minden olyan eszközről hitelesíti a kérelmet, amely az eszközre irányuló IoT Hub végpontokhoz csatlakozik. A hitelesítő adatok vezetéken keresztüli cseréjének elkerüléséhez az eszköz aláírt jogkivonatokat használ a hitelesítéshez. További információ: [IoT hub hozzáférésének szabályozása](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, néhány javasolt lépés a következő:

- Tekintse át az eszközök fejlesztésére vonatkozó [ajánlott eljárásokat](concepts-best-practices.md) .
- Tekintse át a következő kódrészletet, amely bemutatja, hogyan használhatók az SAS-tokenek az [oktatóanyagban: ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central alkalmazással](tutorial-connect-device.md)
- Ismerje meg, hogyan [csatlakoztathatók az X. 509 tanúsítvánnyal rendelkező eszközök a IoT Central alkalmazáshoz készült Node.js ESZKÖZOLDALI SDK-val](how-to-connect-devices-x509.md)
- Ismerje meg, hogyan [figyelheti az eszközök kapcsolatát az Azure CLI használatával](./howto-monitor-devices-azure-cli.md)
- További információ a [Azure IoT Edge eszközökről és az Azure IoT Central](./concepts-iot-edge.md)
