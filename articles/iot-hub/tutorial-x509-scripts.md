---
title: Oktatóanyag – Microsoft-szkriptek használata x.509 teszttanúsítványok létrehozásához Azure IoT Hub | Microsoft Docs
description: Oktatóanyag – Egyéni szkriptek használata hitelesítésszolgáltatói és eszköztanúsítványok létrehozásához Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: ff4b63f49a87dd9ca6b0ef458bdcf1c285a34a18
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378210"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Oktatóanyag: A Microsoft által biztosított szkriptek használata teszttanúsítványok létrehozásához

A Microsoft PowerShell- és Bash-szkriptekkel segít megérteni, hogyan hozhat létre saját X.509-tanúsítványokat, és hogyan hitelesítheti őket egy IoT Hub. A szkriptek egy GitHub-adattárban [találhatók.](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) Csak bemutatási célokra szolgálnak. Az által létrehozott tanúsítványokat nem szabad éles környezetben használni. A tanúsítványok nem kódolt jelszavakat ("1234") tartalmaznak, és 30 nap után lejárnak. Éles környezetben saját ajánlott eljárásokat kell használnia a tanúsítványok létrehozásához és az élettartam kezeléséhez.

## <a name="powershell-scripts"></a>PowerShell-parancsprogramok

### <a name="step-1---setup"></a>1. lépés – Beállítás

Szerezze be a Windowshoz való OpenSSL-t. A <https://www.openssl.org/docs/faq.html#MISC4> letöltési helyeket és a <https://www.openssl.org/source/> forrásból való buildet itt láthatja: . Ezután futtassa az előzetes szkripteket:

1. Másolja a szkripteket ebből [](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) a GitHub-adattárból a helyi könyvtárba, amelyben dolgozni szeretne. Minden fájl ennek a könyvtárnak a gyermekeiként jön létre.

1. Indítsa el a PowerShellt rendszergazdaként.

1. Váltsa át azt a könyvtárat, ahová a szkripteket betöltötte.

1. A parancssorban állítsa be a környezeti változót arra a könyvtárra, amelyben az openssl konfigurációs fájl `$ENV:OPENSSL_CONF` (openssl.cnf) található.

1. Futtassa `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` a parancsot, hogy a PowerShell futtatni tudja a szkripteket.

1. Futtassa az `. .\ca-certs.ps1` parancsot. Ez a parancsprogram funkcióit a PowerShell globális névterében hozza el.

1. Futtassa az `Test-CACertsPrerequisites` parancsot. A PowerShell a Windows tanúsítványtárolót használja a tanúsítványok kezelésére. Ez a parancs ellenőrzi, hogy a későbbiekben nem lesznek névütközések a meglévő tanúsítványokkal, és hogy az OpenSSL megfelelően van-e beállítva.

### <a name="step-2---create-certificates"></a>2. lépés – Tanúsítványok létrehozása

Futtassa az `New-CACertsCertChain [ecc|rsa]` parancsot. Az ECC hitelesítésszolgáltatói tanúsítványokhoz ajánlott, de nem szükséges. Ez a szkript frissíti a címtárat és a Windows tanúsítványtárolót a következő hitelesítésszolgáltatói és köztes tanúsítványokkal:

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

A szkript futtatása után adja hozzá az új hitelesítésszolgáltatói tanúsítványt (RootCA.pem) a IoT Hub:

1. Lépjen a saját IoT Hub a Tanúsítványok lapra.

1. Válassza a **Hozzáadás** lehetőséget.

1. Adja meg a CA-tanúsítvány megjelenítendő nevét.

1. Töltse fel a hitelesítésszolgáltatói tanúsítványt.

1. Kattintson a **Mentés** gombra.

### <a name="step-3---prove-possession"></a>3. lépés – A birtoklás bizonyítása

Most, hogy feltöltötte a HITELESÍTÉSszolgáltatói tanúsítványt a IoT Hub, igazolnia kell, hogy valóban ön a tanúsítvány a tulajdona:

1. Válassza ki az új hitelesítésszolgáltatói tanúsítványt.

1. A **Tanúsítvány részletei párbeszédpanelen válassza** az Ellenőrző kód **generálása** lehetőséget. További információ: [Hitelesítésszolgáltatói tanúsítvány birtoklásának igazolása.](tutorial-x509-prove-possession.md)

1. Hozzon létre egy tanúsítványt, amely tartalmazza az ellenőrző kódot. Ha például az ellenőrző kód , a következő futtatásával hozzon létre egy új tanúsítványt a munkakönyvtárban, amely a `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` tárgyat `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` tartalmazza. A szkript létrehoz egy nevű `VerifyCert4.cer` tanúsítványt.

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Töltse `VerifyCert4.cer` fel a IoT Hub a Tanúsítvány részletei **párbeszédpanelen.**

1. Válassza az **Ellenőrzés** lehetőséget.

### <a name="step-4---create-a-new-device"></a>4. lépés – Új eszköz létrehozása

Hozzon létre egy eszközt a IoT Hub:

1. A saját IoT Hub lépjen az **IoT-eszközök szakaszra.**

1. Adjon hozzá egy új eszközt `mydevice` azonosítóval.

1. A hitelesítéshez válassza az **X.509 hitelesítésszolgáltató által aláírt lehetőséget.**

1. Hozzon `New-CACertsDevice mydevice` létre egy új eszköztanúsítványt az futtatásával. Ez a következő fájlokat hozza létre a munkakönyvtárban:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>5. lépés – Az eszköz tanúsítványának tesztelése

A [Tanúsítványhitelesítés tesztelése alatt](tutorial-x509-test-certificate.md) állapítsa meg, hogy az eszköz tanúsítványa hitelesíthető-e a IoT Hub. Szüksége lesz a tanúsítvány PFX-verziójára( `mydevice.pfx` ).

### <a name="step-6---cleanup"></a>6. lépés – A tisztítás

A Start menüben nyissa meg a **Számítógép-tanúsítványok** kezelése elemet, és navigáljon a Tanúsítványok – Helyi **számítógép > személyes menüpontra.** Távolítsa el az "Azure IoT CA TestOnly*" által kiadott tanúsítványokat. Hasonlóképpen távolítsa el a megfelelő tanúsítványokat>megbízható legfelső szintű hitelesítésszolgáltatótól > tanúsítványokból és >**köztes hitelesítésszolgáltatóktól > tanúsítványokból.**

## <a name="bash-scripts"></a>Bash-szkriptek

### <a name="step-1---setup"></a>1. lépés – Beállítás

1. Indítsa el a Basht.

1. Váltson arra a könyvtárra, amelyben dolgozni szeretne. Ebben a könyvtárban minden fájl létrejön.

1. Másolja `*.cnf` a és a `*.sh` et a munkakönyvtárba.

### <a name="step-2---create-certificates"></a>2. lépés – Tanúsítványok létrehozása

1. Futtassa az `./certGen.sh create_root_and_intermediate` parancsot. Ez a következő fájlokat hozza létre **a certs könyvtárban:**

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. Lépjen a IoT Hub a Tanúsítványok **lapra.**

1. Válassza a **Hozzáadás** lehetőséget.

1. Adja meg a CA-tanúsítvány megjelenítendő nevét.

1. Csak a hitelesítésszolgáltatói tanúsítványt töltse fel a IoT Hub. A tanúsítvány neve: `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Kattintson a **Mentés** gombra.

### <a name="step-3---prove-possession"></a>3. lépés – A birtoklás bizonyítása

1. Válassza ki az előző lépésben létrehozott új hitelesítésszolgáltatói tanúsítványt.

1. A **Tanúsítvány részletei párbeszédpanelen válassza** az Ellenőrző kód **generálása** lehetőséget. További információ: [Hitelesítésszolgáltatói tanúsítvány birtoklásának igazolása.](tutorial-x509-prove-possession.md)

1. Hozzon létre egy tanúsítványt, amely tartalmazza az ellenőrző kódot. Ha például az ellenőrző kód , a következő futtatásával hozzon létre egy új tanúsítványt a nevű munkakönyvtárban, amely tartalmazza `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` a `verification-code.cert.pem` `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` tárgyat.

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Töltse fel a tanúsítványt az IoT Hubra a Tanúsítvány **részletei párbeszédpanelen.**

1. Válassza az **Ellenőrzés** lehetőséget.

### <a name="step-4---create-a-new-device"></a>4. lépés – Új eszköz létrehozása

Eszköz létrehozása az IoT Hubhoz:

1. A saját IoT Hub lépjen az IoT-eszközök szakaszra.

1. Adjon hozzá egy új eszközt `mydevice` azonosítóval.

1. A hitelesítéshez válassza az **X.509 hitelesítésszolgáltató által aláírt lehetőséget.**

1. Hozzon `./certGen.sh create_device_certificate mydevice` létre egy új eszköztanúsítványt az futtatásával. Ez létrehoz két, `new-device.cert.pem` és nevű fájlt a `new-device.cert.pfx` munkakönyvtárban.

### <a name="step-5---test-your-device-certificate"></a>5. lépés – Az eszköz tanúsítványának tesztelése

A [Tanúsítványhitelesítés tesztelése alatt](tutorial-x509-test-certificate.md) állapítsa meg, hogy az eszköz tanúsítványa hitelesíthető-e a IoT Hub. Szüksége lesz a tanúsítvány PFX-verziójára( `new-device.cert.pfx` ).

### <a name="step-6---cleanup"></a>6. lépés – A tisztítás

Mivel a Bash-szkript egyszerűen tanúsítványokat hoz létre a munkakönyvtárban, csak törölje őket, amikor végzett a teszteléssel.

## <a name="next-steps"></a>Következő lépések

A tanúsítvány teszteléséhez a [Tanúsítványhitelesítés](tutorial-x509-test-certificate.md) tesztelése alatt állapítsa meg, hogy a tanúsítvány hitelesíteni tudja-e az eszközt a IoT Hub.
