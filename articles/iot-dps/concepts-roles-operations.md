---
title: IoT Hub Device Provisioning Service – szerepkörök és műveletek
description: Ez a cikk az IoT-eszközök kiépítési szolgáltatásával (DPS) kapcsolatos megoldások fejlesztésekor és IoT használt szerepkörök és műveletek fogalmi áttekintését tartalmazza.
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: 322d6b590863d8065454c0439c5d899107a6abe7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784965"
---
# <a name="roles-and-operations"></a>Szerepkörök és műveletek

A IoT-megoldások fejlesztésének fázisai hetekre vagy hónapokra terjedhetnek, az éles környezetek, például a gyártási idő, a szállítás, a vámügyi folyamat stb. miatt. Emellett több szerepkörre is kiterjedhetnek a tevékenységek a különböző érintett entitások miatt. Ez a témakör részletesen ismerteti az egyes fázisokhoz kapcsolódó különböző szerepköröket és műveleteket, majd bemutatja a folyamatot egy Sequence diagramon. 

A kiépítés az eszköz gyártójára is vonatkozik, amely az [igazolási mechanizmus](concepts-service.md#attestation-mechanism)engedélyezésével kapcsolatos. A gyártási műveletek az automatikus kiépítési fázisok ütemezéstől függetlenül is előfordulhatnak, különösen abban az esetben, ha az automatikus kiépítés után az új eszközök beszerzése megtörtént.

A bal oldali tartalomjegyzékben számos gyors útmutató áll rendelkezésére, amelyek segítenek megmagyarázni az automatikus kiépítés gyakorlati tapasztalatain keresztüli kiosztását. A tanulási folyamat megkönnyítése és egyszerűsítése érdekében a szoftver használatával szimulálható a fizikai eszközök regisztrációja és regisztrálása. Néhány rövid útmutatóban több szerepkör műveleteit kell teljesítenie, beleértve a nem létező szerepkörök műveleteit is, a rövid útmutatók szimulált természete miatt.

| Szerepkör | Művelet | Description |
|------| --------- | ------------|
| Gyártó | Azonosító és regisztrációs URL-cím kódolása | A használt igazolási mechanizmus alapján a gyártó felelős az eszköz azonosító adatainak és az eszköz kiépítési szolgáltatásának regisztrációs URL-címének kódolásában.<br><br>Gyors **útmutató: mivel** az eszköz szimulálva van, nincs gyártói szerepkör. Tekintse meg a fejlesztői szerepkört az információk beszerzéséről, amely a minta-regisztrációs alkalmazás kódolásakor használatos. |
| | Eszköz identitásának megadása | Az eszköz személyazonossági adatainak kezdeményezője a gyártó felelős azért, hogy kommunikáljon a kezelővel (vagy egy kijelölt ügynökkel), vagy közvetlenül regisztrálja azt az eszköz kiépítési szolgáltatásának API-kon keresztül.<br><br>Gyors **útmutató: mivel** az eszköz szimulálva van, nincs gyártói szerepkör. Az eszköz identitásának beszerzésével kapcsolatos részletekért tekintse meg az operátori szerepkört, amely egy szimulált eszköz regisztrálására szolgál az eszköz kiépítési szolgáltatásának példányában. |
| Operátor | Automatikus kiépítés konfigurálása | Ez a művelet megfelel az automatikus kiépítés első fázisának.<br><br>Gyors **útmutató:** végrehajtja az operátori szerepkört, konfigurálja az eszköz kiépítési szolgáltatását és IoT hub példányait az Azure-előfizetésében. |
|  | Eszköz identitásának regisztrálása | Ez a művelet megfelel az automatikus kiépítés második fázisának.<br><br>Gyors **útmutató:** végrehajtja az operátori szerepkört, és regisztrálja a szimulált eszközt az eszköz kiépítési szolgáltatásának példányában. Az eszköz identitását a rövid útmutatóban (TPM vagy X. 509) szimulált igazolási módszer határozza meg. Az igazolás részleteiért tekintse meg a fejlesztői szerepkört. |
| Eszköz kiépítési szolgáltatása,<br>IoT Hub | \<all operations\> | Mind a fizikai eszközökkel, mind a szimulált eszközökkel végzett gyors üzembe helyezés esetén ezek a szerepkörök az Azure-előfizetésében konfigurált IoT-szolgáltatásokon keresztül teljesülnek. A szerepkörök/műveletek ugyanúgy működnek, mint a IoT-szolgáltatások a fizikai és a szimulált eszközök kiépítéséhez. |
| Fejlesztő | Regisztrációs szoftver létrehozása/üzembe helyezése | Ez a művelet megfelel az automatikus kiépítés harmadik fázisának. A fejlesztő feladata a regisztrációs szoftver kiépítése és üzembe helyezése az eszközön a megfelelő SDK használatával.<br><br>Gyors **útmutató: az** Ön által létrehozott minta-regisztrációs alkalmazás szimulál egy valós eszközt, amely a munkaállomáson futó platformhoz/nyelvhez (a fizikai eszközre való üzembe helyezés helyett) fut. A regisztrációs alkalmazás ugyanazokat a műveleteket hajtja végre, mint amelyek egy fizikai eszközön vannak üzembe helyezve. Megadhatja az igazolási módszert (TPM vagy X. 509 tanúsítvány), valamint az eszköz kiépítési szolgáltatási példányának regisztrációs URL-címét és azonosító hatókörét. Az eszköz identitását az SDK igazolási logikája határozza meg futásidőben, a megadott módszer alapján: <ul><li>**TPM-igazolás** – a fejlesztési munkaállomás [TPM szimulátor alkalmazást](quick-create-simulated-device.md)futtat. A rendszer futtatása után külön alkalmazás használatával kinyerheti a TPM "záradék kulcsát" és "regisztrációs AZONOSÍTÓját", hogy regisztrálja az eszköz identitását. Az SDK-igazolási logika emellett a szimulátort is használja a regisztráció során, hogy aláírt SAS-tokent nyújtson a hitelesítéshez és a regisztráció ellenőrzéséhez.</li><li>**X509-igazolás** – [tanúsítvány létrehozásához](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain)használható eszközt használ. A létrejötte után létre kell hoznia a regisztrációhoz szükséges tanúsítványfájl-fájlt. Az SDK-igazolási logika a tanúsítványt a regisztráció során is használja a hitelesítéshez és a regisztráció ellenőrzéséhez.</li></ul> |
| Eszköz | Indítás és regisztrálás | Ez a művelet megfelel az automatikus kiépítés harmadik fázisának, amelyet a fejlesztő által készített eszköz-regisztrációs szoftver teljesít. A részletekért tekintse meg a fejlesztői szerepkört. Első rendszerindítás esetén: <ol><li>Az alkalmazás az eszköz kiépítési szolgáltatásának példányával csatlakozik, és a fejlesztés során megadott globális URL-cím és szolgáltatás "azonosító hatóköre".</li><li>A csatlakozás után az eszköz hitelesítése az igazolási módszer és a regisztráció során megadott identitás szerint történik.</li><li>A hitelesítés után az eszköz regisztrálva van a kiépítési szolgáltatás példánya által megadott IoT Hub-példányban.</li><li>A sikeres regisztráció után a rendszer egy egyedi eszköz-azonosítót és IoT Hub-végpontot ad vissza a regisztrációs alkalmazásnak a IoT Hubsal való kommunikációhoz.</li><li> Onnan az eszköz lehívhatja a kezdeti, a konfigurációhoz tartozó [különálló](~/articles/iot-hub/iot-hub-devguide-device-twins.md) állapotát, és megkezdheti a telemetria-adatok jelentéskészítési folyamatát.</li></ol>Gyors **útmutató: mivel** az eszköz szimulálva van, a regisztrációs szoftver a fejlesztői munkaállomáson fut.|

A következő ábra összefoglalja a műveletek szerepköreit és az eszközök automatikus kiépítés során történő előkészítését:
<br><br>
[![Automatikus kiépítési sorozat egy eszközhöz](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Igény szerint a gyártó a Device kiépítési szolgáltatás API-jai használatával is végrehajthatja az "eszköz-identitás regisztrálása" műveletet (a kezelőn keresztül). Az előkészítés részletes ismertetését és további tudnivalókat az [Azure IoT-videó](https://youtu.be/cSbDRNg72cU?t=2460) (a 41:00-es, a jelölőtől kezdődően)

## <a name="roles-and-azure-accounts"></a>Szerepkörök és Azure-fiókok

Az egyes szerepkörök egy Azure-fiókhoz való leképezésének módja függ a forgatókönyvtől, és van néhány olyan forgatókönyv, amely részt vehet. Az alábbi általános minták segítenek megismerni, hogy a szerepkörök általában hogyan vannak leképezve egy Azure-fiókra.

#### <a name="chip-manufacturer-provides-security-services"></a>A chip gyártója biztonsági szolgáltatásokat biztosít

Ebben az esetben a gyártó felügyeli a szintű ügyfelek biztonságát. Ezt a forgatókönyvet előnyben részesítették ezek a szintek – egy ügyfél, mivel nem kell részletesen kezelnie a biztonságot. 

A gyártó bevezeti a biztonságot a hardveres biztonsági modulokba (HSM). Ez a biztonság magában foglalhatja a gyártótól a kulcsok, tanúsítványok stb. beszerzését a lehetséges ügyfelektől, akik már rendelkeznek a DPS-példányokkal és a regisztrációs csoportok beállításával. A gyártó ezeket a biztonsági adatokat is előállíthatja ügyfelei számára.

Ebben az esetben két Azure-fiókra van szó:

- **Fiók #1**: az operátorok és a fejlesztői szerepkörök valószínűleg megosztottak bizonyos fokig. Ez a fél megvásárolhatja a HSM-zsetonokat a gyártótól. Ezek a zsetonok az #1 fiókhoz társított DPS-példányokra mutatnak. A DPS-regisztrációk segítségével ez a fél több szintű ügyfél számára is bérelhet eszközöket az eszközök regisztrációs beállításainak újrakonfigurálásával a DPS-ben. A jelen fél a végfelhasználói háttérrendszer-rendszerek számára lefoglalt IoT-hubokkal is rendelkezhet az eszköz telemetria eléréséhez. Ebben az utóbbi esetben előfordulhat, hogy nincs szükség második fiókra.

- **Fiók #2**: végfelhasználók, szint – két ügyfél rendelkezhet saját IoT-hubokkal. A fiókhoz társított fél #1 csak a bérelt eszközöket a fiók megfelelő hubhoz mutat. Ehhez a konfigurációhoz a DPS és a IoT hubok összekapcsolására van szükség az Azure-fiókok között, amely Azure Resource Manager-sablonokkal végezhető el.

#### <a name="all-in-one-oem"></a>Egy teljes körű OEM

A gyártó "all-in-One OEM" lehet, ahol csak egyetlen gyártói fiókra lenne szükség. A gyártó kezeli a biztonságot és a kiépítés végét.

A gyártó felhőalapú alkalmazást biztosíthat az olyan ügyfeleknek, akik vásárolnak eszközöket. Ez az alkalmazás a gyártó által lefoglalt IoT Hub interfésszel.

Az árusító gépek vagy az automatizált Kávégépek példákat mutatnak erre a forgatókönyvre.




## <a name="next-steps"></a>Következő lépések

Előfordulhat, hogy hasznosnak találja a cikk könyvjelzőként való megjelölését, ahogy a megfelelő automatikus kiépítési gyors útmutatókban dolgozik. 

Kezdje egy "automatikus kiépítés beállítása" rövid útmutató elvégzésével, amely a legjobban megfelel a felügyeleti eszköz preferenciájának, amely végigvezeti a "szolgáltatás konfigurációja" fázison:

- [Automatikus kiépítés beállítása az Azure CLI-vel](quick-setup-auto-provision-cli.md)
- [Automatikus kiépítés beállítása a Azure Portal használatával](quick-setup-auto-provision.md)
- [Automatikus kiépítés beállítása Resource Manager-sablon használatával](quick-setup-auto-provision-rm.md)

Ezután folytassa az "eszköz kiépítése" című rövid útmutatót, amely megfelel az eszköz igazolási mechanizmusának és az eszköz kiépítési szolgáltatásának SDK-nak/nyelvi preferenciájának. Ebben a rövid útmutatóban végigvezeti az "eszközök beléptetése" és az "eszköz regisztrálása és konfigurálása" fázisokon: 

| Eszköz-igazolási mechanizmus | Gyorsindítás SDK/nyelv | 
| ------------------------------- | -------------------- |
| Szimmetrikus kulcs | [C](quick-create-simulated-device-symm-key.md)<br>[Java](quick-create-simulated-device-symmetric-key-java.md)<br>[Python](quick-create-device-symmetric-key-python.md) |
| X. 509 tanúsítvány | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |
| Szimulált platformmegbízhatósági modul (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |




