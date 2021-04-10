---
title: Oktatóanyag – a Microsoft Scripts használatával x. 509 teszt-tanúsítványokat hozhat létre az Azure IoT Hubhoz | Microsoft Docs
description: Oktatóanyag – egyéni parancsfájlok használata HITELESÍTÉSSZOLGÁLTATÓ-és eszköz-tanúsítványok létrehozásához az Azure IoT Hub
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
- devx-track-azurecli
ms.openlocfilehash: fc3717436619468e2db0bf4b408059112dae24cc
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384153"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Oktatóanyag: a Microsoft által biztosított parancsfájlok használata tesztelési tanúsítványok létrehozásához

A Microsoft PowerShell-és bash-parancsfájlokkal ismerteti, hogyan hozhat létre saját X. 509 tanúsítványokat, és hogyan hitelesítheti azokat egy IoT Hub. A parancsfájlok egy GitHub- [tárházban](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)találhatók. Csak demonstrációs célokat szolgálnak. Az általuk létrehozott tanúsítványok nem használhatók éles környezetben. A tanúsítványok rögzített jelszavakat tartalmaznak ("1234"), és 30 nap elteltével lejárnak. Éles környezetben saját ajánlott eljárásokat kell használnia a tanúsítványok létrehozásához és az élettartam-kezeléshez.

## <a name="powershell-scripts"></a>PowerShell-parancsprogramok

### <a name="step-1---setup"></a>1. lépés – telepítés

A Windows rendszerhez készült OpenSSL beszerzése. <https://www.openssl.org/docs/faq.html#MISC4>A letöltéshez vagy a <https://www.openssl.org/source/> forrásból való kiépítéshez szükséges helyeket itt tekintheti meg. Ezután futtassa az előzetes parancsfájlokat:

1. Másolja a parancsfájlokat ebből a GitHub- [adattárból](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) abba a helyi könyvtárba, amelyben dolgozni szeretne. A rendszer az összes fájlt a címtár gyermekeiként hozza létre.

1. Indítsa el a PowerShellt rendszergazdaként.

1. Váltson arra a könyvtárra, ahová betöltötte a parancsfájlokat.

1. A parancssorban állítsa a környezeti változót `$ENV:OPENSSL_CONF` arra a könyvtárra, amelyben az OpenSSL konfigurációs fájlja (OpenSSL. cnf) található.

1. Futtassa `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` a parancsot, hogy a PowerShell futtatni tudja a parancsfájlokat.

1. Futtassa az `. .\ca-certs.ps1` parancsot. Ez a szkript funkcióit a PowerShell globális névtérbe helyezi.

1. Futtassa az `Test-CACertsPrerequisites` parancsot. A PowerShell a Windows tanúsítványtároló használatával kezeli a tanúsítványokat. Ez a parancs ellenőrzi, hogy a későbbiekben nem lesz-e ütközés a meglévő tanúsítványokkal, és hogy az OpenSSL megfelelően van-e beállítva.

### <a name="step-2---create-certificates"></a>2. lépés – tanúsítványok létrehozása

Futtassa az `New-CACertsCertChain [ecc|rsa]` parancsot. A CA-tanúsítványok esetében az ECC használata ajánlott, de nem kötelező. Ez a parancsfájl frissíti a címtárat és a Windows-tanúsítványtárolót a következő HITELESÍTÉSSZOLGÁLTATÓI és köztes tanúsítványokkal:

* intermediate1. PEM
* intermediate2. PEM
* intermediate3. PEM
* RootCA. cer
* RootCA. PEM

A parancsfájl futtatása után adja hozzá az új HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt (RootCA. PEM) a IoT Hubhoz:

1. Lépjen a IoT Hub, és navigáljon a tanúsítványok elemre.

1. Válassza a **Hozzáadás** lehetőséget.

1. Adja meg a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megjelenítendő nevét.

1. Töltse fel a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt.

1. Kattintson a **Mentés** gombra.

### <a name="step-3---prove-possession"></a>3. lépés – birtoklás bizonyítása

Most, hogy feltöltötte a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát a IoT Hubba, bizonyítania kell, hogy valóban maga a sajátja:

1. Válassza ki az új HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt.

1. A **tanúsítvány részletei** párbeszédpanelen válassza az **ellenőrző kód előállítása** lehetőséget. További információ: CA- [tanúsítvány birtoklásának bizonyítása](tutorial-x509-prove-possession.md).

1. Hozzon létre egy tanúsítványt, amely tartalmazza az ellenőrző kódot. Ha például az ellenőrző kód `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , a következő paranccsal hozzon létre egy új tanúsítványt a munkakönyvtárában, amely tartalmazza a tárgyat `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . A szkript létrehoz egy nevű tanúsítványt `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Töltse fel a `VerifyCert4.cer` IoT hub a **tanúsítvány részletei** párbeszédpanelen.

1. Válassza az **Ellenőrzés** lehetőséget.

### <a name="step-4---create-a-new-device"></a>4. lépés – új eszköz létrehozása

Hozzon létre egy eszközt a IoT Hubhoz:

1. A IoT Hub navigáljon a IoT- **eszközök** szakaszhoz.

1. Adjon hozzá egy új eszközt AZONOSÍTÓval `mydevice` .

1. A hitelesítéshez válassza az **X. 509 hitelesítésszolgáltató aláírva** lehetőséget.

1. `New-CACertsDevice mydevice`Új eszköz tanúsítványának létrehozásához futtassa a parancsot. Ez a következő fájlokat hozza létre a munkakönyvtárában:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>5. lépés – az eszköz tanúsítványának tesztelése

A [tanúsítvány hitelesítésének tesztelése](tutorial-x509-test-certificate.md) lehetőségre kattintva megállapíthatja, hogy az eszköz tanúsítványa hitelesíthető-e a IoT hub. Szüksége lesz a tanúsítvány PFX-verziójára `mydevice.pfx` .

### <a name="step-6---cleanup"></a>6. lépés – karbantartás

A Start menüben nyissa meg a **számítógép-tanúsítványok kezelése** lehetőséget, és navigáljon a  **tanúsítványok – helyi számítógép > személyes** elemre. Távolítsa el az "Azure IoT CA TestOnly *" által kiadott tanúsítványokat. Hasonlóképpen távolítsa el a megfelelő tanúsítványokat **>megbízható legfelső szintű hitelesítésszolgáltatótól > tanúsítványokat és >köztes hitelesítésszolgáltatók > tanúsítványokat**.

## <a name="bash-scripts"></a>Bash-parancsfájlok

### <a name="step-1---setup"></a>1. lépés – telepítés

1. Indítsa el a Bashot.

1. Váltson arra a könyvtárra, amelyben dolgozni szeretne. Az összes fájl ebben a könyvtárban lesz létrehozva.

1. Másolja `*.cnf` `*.sh` a munkakönyvtárba.

### <a name="step-2---create-certificates"></a>2. lépés – tanúsítványok létrehozása

1. Futtassa az `./certGen.sh create_root_and_intermediate` parancsot. Ez a következő fájlokat hozza létre a **tanúsítványok** könyvtárban:

    * Azure-IOT-test-only. Chain. ca. CERT. PEM
    * Azure-IOT-test-only. Intermediate. CERT. PEM
    * Azure-IOT-test-only. root. ca. CERT. PEM

1. Lépjen a IoT Hub, és navigáljon a **tanúsítványok** elemre.

1. Válassza a **Hozzáadás** lehetőséget.

1. Adja meg a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megjelenítendő nevét.

1. Csak a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt töltse fel a IoT Hubba. A tanúsítvány neve `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Kattintson a **Mentés** gombra.

### <a name="step-3---prove-possession"></a>3. lépés – birtoklás bizonyítása

1. Válassza ki az előző lépésben létrehozott új HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt.

1. A **tanúsítvány részletei** párbeszédpanelen válassza az **ellenőrző kód előállítása** lehetőséget. További információ: CA- [tanúsítvány birtoklásának bizonyítása](tutorial-x509-prove-possession.md).

1. Hozzon létre egy tanúsítványt, amely tartalmazza az ellenőrző kódot. Ha például az ellenőrző kód `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , a következő paranccsal hozzon létre egy új tanúsítványt a munkakönyvtárában, `verification-code.cert.pem` amely tartalmazza a tárgyat `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Töltse fel a tanúsítványt a IoT hubhoz a **tanúsítvány részletei** párbeszédpanelen.

1. Válassza az **Ellenőrzés** lehetőséget.

### <a name="step-4---create-a-new-device"></a>4. lépés – új eszköz létrehozása

Hozzon létre egy eszközt az IoT hub számára:

1. A IoT Hub navigáljon a IoT-eszközök szakaszhoz.

1. Adjon hozzá egy új eszközt AZONOSÍTÓval `mydevice` .

1. A hitelesítéshez válassza az **X. 509 hitelesítésszolgáltató aláírva** lehetőséget.

1. `./certGen.sh create_device_certificate mydevice`Új eszköz tanúsítványának létrehozásához futtassa a parancsot. Ez két fájlt hoz létre `new-device.cert.pem` a `new-device.cert.pfx` munkakönyvtárában, és fájlokat is létrehoz.

### <a name="step-5---test-your-device-certificate"></a>5. lépés – az eszköz tanúsítványának tesztelése

A [tanúsítvány hitelesítésének tesztelése](tutorial-x509-test-certificate.md) lehetőségre kattintva megállapíthatja, hogy az eszköz tanúsítványa hitelesíthető-e a IoT hub. Szüksége lesz a tanúsítvány PFX-verziójára `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>6. lépés – karbantartás

Mivel a bash-szkript egyszerűen létrehozza a tanúsítványokat a munkakönyvtárában, csak törölje őket, ha végzett tesztelést.

## <a name="next-steps"></a>Következő lépések

A tanúsítvány teszteléséhez lépjen a [tanúsítvány hitelesítésének tesztelése](tutorial-x509-test-certificate.md) elemre annak megállapításához, hogy a tanúsítvány képes-e hitelesíteni az eszközt az IoT hub.
