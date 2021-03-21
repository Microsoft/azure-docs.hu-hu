---
title: Tesztelési tanúsítványok létrehozása – Azure IoT Edge | Microsoft Docs
description: Hozzon létre tesztelési tanúsítványokat, és Ismerje meg, hogyan telepítheti őket egy Azure IoT Edge eszközön az éles üzembe helyezés előkészítéséhez.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d8cf3dbe9d1dc2ad329a0b5ab8fa9554c85ae55c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199093"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Bemutató tanúsítvány létrehozása az IoT Edge-eszköz funkcióinak teszteléséhez

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge eszközökön tanúsítványokra van szükség a futtatókörnyezet, a modulok és az alsóbb rétegbeli eszközök közötti biztonságos kommunikációhoz.
Ha nem rendelkezik hitelesítésszolgáltatóval a szükséges tanúsítványok létrehozásához, a bemutató tanúsítványokkal kipróbálhatja a tesztkörnyezetben IoT Edge funkcióit.
Ez a cikk a tanúsítvány-létrehozási parancsfájlok azon funkcióit ismerteti, amelyeket IoT Edge biztosít a teszteléshez.

Ezek a tanúsítványok 30 napon belül lejárnak, és semmilyen éles környezetben nem használhatók.

Létrehozhat tanúsítványokat bármely gépen, majd átmásolhatja őket a IoT Edge eszközre.
Az elsődleges géppel könnyebben hozhatja létre a tanúsítványokat ahelyett, hogy saját maga IoT Edge eszközén kellene őket létrehoznia.
Az elsődleges gép használatával egyszer állíthatja be a parancsfájlokat, majd használhatja őket több eszközhöz tartozó tanúsítványok létrehozásához.

A következő lépésekkel hozhat létre bemutató tanúsítványokat a IoT Edge forgatókönyv teszteléséhez:

1. [Hozzon létre parancsfájlokat](#set-up-scripts) a tanúsítvány létrehozásához az eszközön.
2. [Hozza létre a legfelső szintű hitelesítésszolgáltatói tanúsítványt](#create-root-ca-certificate) , amelyet a forgatókönyv összes többi tanúsítványának aláírásához használ.
3. A tesztelni kívánt forgatókönyvhöz szükséges tanúsítványok létrehozása:
   * [Hozzon létre IoT Edge eszköz-azonosító tanúsítványokat](#create-iot-edge-device-identity-certificates) az X. 509 tanúsítványalapú hitelesítéssel rendelkező eszközök kiépítési eszközeihez manuálisan vagy a IoT hub Device Provisioning Service.
   * [Hozzon létre IoT Edge eszköz hitelesítésszolgáltatói tanúsítványokat](#create-iot-edge-device-ca-certificates) IoT Edge eszközökhöz az átjáró forgatókönyvei között.
   * [Alsóbb rétegbeli eszközök tanúsítványainak létrehozása](#create-downstream-device-certificates) átjáró-forgatókönyvben lévő alsóbb rétegbeli eszközök hitelesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Egy fejlesztői gép, amelyen telepítve van a git.

## <a name="set-up-scripts"></a>Parancsfájlok beállítása

A GitHubon található IoT Edge adattár olyan tanúsítvány-létrehozási parancsfájlokat tartalmaz, amelyek segítségével bemutató tanúsítványokat hozhat létre.
Ez a szakasz útmutatást nyújt a parancsfájlok futtatásának előkészítéséhez a számítógépen, Windows vagy Linux rendszeren.
Ha Linux rendszerű gépet használ, ugorjon a [Linuxon való beállításhoz](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Beállítás Windows rendszeren

Ha bemutató tanúsítványokat szeretne létrehozni egy Windows-eszközön, telepítenie kell az OpenSSL-t, majd el kell végeznie a létrehozási parancsfájlok klónozását és a PowerShellben helyileg történő futtatását.

#### <a name="install-openssl"></a>OpenSSL telepítése

Telepítse a Windows rendszerhez készült OpenSSL-t arra a gépre, amelyet a tanúsítványok létrehozásához használ.
Ha már telepítve van az OpenSSL a Windows-eszközön, győződjön meg arról, hogy a openssl.exe elérhető a PATH környezeti változóban.

Az OpenSSL több módon is telepíthető, többek között a következő lehetőségek közül:

* **Egyszerűbb:** Töltse le és telepítse a [harmadik féltől származó OpenSSL bináris fájlokat](https://wiki.openssl.org/index.php/Binaries), például az [OpenSSL-ből a SourceForge-on](https://sourceforge.net/projects/openssl/). Adja hozzá a openssl.exe teljes elérési útját a PATH környezeti változóhoz.

* **Ajánlott:** Töltse le az OpenSSL forráskódját, és saját kezűleg vagy a [vcpkg](https://github.com/Microsoft/vcpkg)-n keresztül hozza létre a bináris fájlokat a gépen. Az alább felsorolt utasítások segítségével letöltheti a forráskódot, lefordíthatja és telepítheti az OpenSSL-t a Windows rendszerű gépén, egyszerű lépésekkel vcpkg.

   1. Navigáljon ahhoz a könyvtárhoz, amelyre telepíteni szeretné a vcpkg. A [vcpkg](https://github.com/Microsoft/vcpkg)letöltéséhez és telepítéséhez kövesse az utasításokat.

   2. Miután telepítette a vcpkg-et, futtassa a következő parancsot egy PowerShell-parancssorból a Windows x64 rendszerhez készült OpenSSL-csomag telepítéséhez. A telepítés általában körülbelül 5 percet vesz igénybe.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Adja hozzá a `<vcpkg path>\installed\x64-windows\tools\openssl` PATH környezeti változóhoz, hogy a openssl.exe fájl elérhető legyen a meghíváshoz.

#### <a name="prepare-scripts-in-powershell"></a>Parancsfájlok előkészítése a PowerShellben

A Azure IoT Edge git-tárház olyan parancsfájlokat tartalmaz, amelyek segítségével létrehozhatók tesztelési tanúsítványok.
Ebben a szakaszban a IoT Edge-tárház klónozásával és a parancsfájlok végrehajtásával foglalkozunk.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.

2. A IoT Edge git-tárház klónozása, amely parancsfájlokat tartalmaz a bemutató tanúsítványok létrehozásához. Használja a `git clone` parancsot, vagy [töltse le a ZIP-fájlt](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigáljon ahhoz a címtárhoz, amelyben dolgozni szeretne. Ebben a cikkben ezt a könyvtárat fogjuk hívni *\<WRKDIR>* . Az összes tanúsítvány és kulcs ebben a munkakönyvtárban lesz létrehozva.

4. Másolja a konfigurációs és parancsfájl-fájlokat a klónozott tárházból a munkakönyvtárba.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Ha a tárházat ZIP-fájlként töltötte le, akkor a mappa neve, `iotedge-master` az elérési út többi része pedig ugyanaz.

5. A parancsfájlok futtatásának engedélyezése a PowerShell számára.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. A parancsfájlok által használt függvények a PowerShell globális névterében használhatók.

   ```powershell
   . .\ca-certs.ps1
   ```

   A PowerShell-ablak egy figyelmeztetést jelenít meg arról, hogy a parancsfájl által generált tanúsítványok csak tesztelési célokra szolgálnak, és nem használhatók éles környezetben.

7. Győződjön meg arról, hogy az OpenSSL megfelelően van telepítve, és győződjön meg róla, hogy a meglévő tanúsítványokkal nem lesznek ütközések. Ha problémák merülnek fel, a parancsfájl kimenetének le kell írnia, hogyan kell kijavítani őket a rendszeren.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Beállítás Linuxon

Ha bemutató tanúsítványokat szeretne létrehozni egy Windows-eszközön, a létrehozási szkriptek klónozásával és a bash-ben való helyi futtatásával kell beállítania azokat.

1. A IoT Edge git-tárház klónozása, amely parancsfájlokat tartalmaz a bemutató tanúsítványok létrehozásához.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigáljon ahhoz a címtárhoz, amelyben dolgozni szeretne. Erre a könyvtárra a cikkben olvashat *\<WRKDIR>* . Az összes tanúsítvány-és kulcsfájl ebben a könyvtárban lesz létrehozva.
  
3. Másolja a config és a script fájlokat a klónozott IoT Edge-tárházból a munkakönyvtárba.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozása

A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány segítségével az összes többi bemutató tanúsítvány egy IoT Edge-forgatókönyv tesztelésére szolgál.
Továbbra is használhatja ugyanazt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, amellyel több IoT Edge vagy alsóbb rétegbeli eszközhöz is készíthet bemutató tanúsítványokat.

Ha már van egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa a munkamappájában, ne hozzon létre újat.
Az új legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány felülírja a régit, és a régiből létrehozott alsóbb rétegbeli tanúsítványok nem fognak működni.
Ha több legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt szeretne, ne felejtse el külön mappákban kezelni őket.

Az ebben a szakaszban ismertetett lépések elvégzése előtt kövesse a [parancsfájlok beállítása](#set-up-scripts) szakasz lépéseit a munkakönyvtár előkészítéséhez a bemutató tanúsítvány-létrehozási parancsfájlokkal.

### <a name="windows"></a>Windows

1. Navigáljon arra a munkakönyvtárra, ahová a tanúsítvány-létrehozási parancsfájlokat helyezte.

1. Hozza létre a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, és jelentkezzen be egy köztes tanúsítvánnyal. A tanúsítványokat a rendszer a munkakönyvtárba helyezi.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Ez a parancsfájl több tanúsítványt és kulcsot hoz létre, de ha a cikkek a **legfelső szintű hitelesítésszolgáltatói tanúsítványt** kérik, használja a következő fájlt:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navigáljon arra a munkakönyvtárra, ahová a tanúsítvány-létrehozási parancsfájlokat helyezte.

1. Hozza létre a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és egy köztes tanúsítványt.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Ez a parancsfájl több tanúsítványt és kulcsot hoz létre, de ha a cikkek a **legfelső szintű hitelesítésszolgáltatói tanúsítványt** kérik, használja a következő fájlt:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>IoT Edge eszköz-identitási tanúsítványok létrehozása

Az eszköz-identitási tanúsítványok IoT Edge eszközök kiépítésére használhatók, ha az X. 509 tanúsítványalapú hitelesítés használatát választja. Ezek a tanúsítványok a manuális kiépítés vagy az Azure IoT Hub Device Provisioning Service (DPS) használatával történő automatikus kiépítés esetén működnek.

Az eszköz azonosító tanúsítványai a IoT Edge eszköz konfigurációs fájljának **kiépítési** szakaszában találhatók.

Az ebben a szakaszban ismertetett lépések végrehajtása előtt kövesse a [parancsfájlok beállítása](#set-up-scripts) és a [legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehozása](#create-root-ca-certificate) című szakasz lépéseit.

### <a name="windows"></a>Windows

Hozza létre a IoT Edge-eszköz identitási tanúsítványát és a titkos kulcsot a következő paranccsal:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Az ehhez a parancshoz megadott név a IoT Hub IoT Edge eszközének AZONOSÍTÓja lesz.

Az új eszköz-identitás parancs több tanúsítvány-és kulcsfájl létrehozását is tartalmazza, beleértve azt is, hogy mikor kell létrehoznia egy egyéni regisztrációt a DPS-ben, és telepítenie kell a IoT Edge futtatókörnyezetet:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Hozza létre a IoT Edge-eszköz identitási tanúsítványát és a titkos kulcsot a következő paranccsal:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Az ehhez a parancshoz megadott név a IoT Hub IoT Edge eszközének AZONOSÍTÓja lesz.

A szkript több tanúsítvány-és kulcsfájl létrehozását is tartalmazza, beleértve az egyéni regisztráció létrehozásakor használni kívánt három tanúsítványt és a IoT Edge futtatókörnyezet telepítését:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak létrehozása

Az éles környezetben minden IoT Edge eszköznek szüksége van egy eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványára, amelyre a konfigurációs fájl hivatkozik.
Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa felelős az eszközön futó modulok tanúsítványainak létrehozásához.
Az átjáró-forgatókönyvek esetében is szükséges, mivel az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa az, hogy a IoT Edge eszköz ellenőrzi az identitását az alsóbb rétegbeli eszközökre.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványai a IoT Edge eszköz config. YAML fájljának **tanúsítvány** részében találhatók.
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványai a IoT Edge eszköz config. toml fájljának **PEREMHÁLÓZATI CA** szakaszában találhatók.
:::moniker-end

Az ebben a szakaszban ismertetett lépések végrehajtása előtt kövesse a [parancsfájlok beállítása](#set-up-scripts) és a [legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehozása](#create-root-ca-certificate) című szakasz lépéseit.

### <a name="windows"></a>Windows

1. Navigáljon ahhoz a munkakönyvtárhoz, amely a tanúsítvány-létrehozási parancsfájlokat és a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt tartalmaz.

2. Hozza létre a IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát és titkos kulcsát a következő paranccsal. Adja meg a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nevét.

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   Ezzel a paranccsal több tanúsítvány-és kulcsfájl is létrehozható. A következő tanúsítványt és kulcspárt át kell másolni egy IoT Edge eszközre, és hivatkozni kell rá a konfigurációs fájlban:

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

A **New-CACertsEdgeDevice** parancsnak átadott név nem egyezhet meg a konfigurációs fájlban található állomásnév paraméterrel, vagy az eszköz azonosítóját a IoT Hubban.

### <a name="linux"></a>Linux

1. Navigáljon ahhoz a munkakönyvtárhoz, amely a tanúsítvány-létrehozási parancsfájlokat és a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt tartalmaz.

2. Hozza létre a IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát és titkos kulcsát a következő paranccsal. Adja meg a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nevét.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   Ezzel a parancsfájl-paranccsal számos tanúsítvány-és kulcsfájl hozható létre. A következő tanúsítványt és kulcspárt át kell másolni egy IoT Edge eszközre, és hivatkozni kell rá a konfigurációs fájlban:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

A **create_edge_device_ca_certificate** parancsnak átadott név nem egyezhet meg a konfigurációs fájlban található állomásnév paraméterrel, vagy az eszköz azonosítóját a IoT Hubban.

## <a name="create-downstream-device-certificates"></a>Alsóbb rétegbeli eszközök tanúsítványainak létrehozása

Ha egy alsóbb rétegbeli IoT-eszközt állít be egy átjáró-forgatókönyvhöz, és X. 509 hitelesítést szeretne használni, az alárendelt eszközhöz bemutató tanúsítványokat hozhat létre.
Ha szimmetrikus kulcsos hitelesítést szeretne használni, nem kell további tanúsítványokat létrehoznia az alsóbb rétegbeli eszközhöz.
Az X. 509 tanúsítványok használatával kétféleképpen hitelesíthető egy IoT-eszköz: önaláírt tanúsítványok használata vagy hitelesítésszolgáltató (CA) által aláírt tanúsítvány használata.
Az X. 509 önaláírt hitelesítés (más néven ujjlenyomatos hitelesítés) esetében új tanúsítványokat kell létrehoznia a IoT-eszközre való elhelyezéshez.
Ezek a tanúsítványok olyan ujjlenyomattal rendelkeznek, amelyet IoT Hub a hitelesítéshez.
Az X. 509 hitelesítésszolgáltató (CA) által aláírt hitelesítéshez szükség van egy IoT Hub regisztrált legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra, amelyet a IoT-eszköz tanúsítványainak aláírásához használ.
Bármely olyan eszköz, amely a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vagy a köztes tanúsítványok bármelyikét kiállított tanúsítványt használ, a hitelesítés engedélyezve lesz.

A tanúsítvány-létrehozási parancsfájlok segítségével bemutató tanúsítványokat készíthet a hitelesítési forgatókönyvek bármelyikének teszteléséhez.

Az ebben a szakaszban ismertetett lépések végrehajtása előtt kövesse a [parancsfájlok beállítása](#set-up-scripts) és a [legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehozása](#create-root-ca-certificate) című szakasz lépéseit.

### <a name="self-signed-certificates"></a>Önaláírt tanúsítványok

Ha egy önaláírt tanúsítvánnyal rendelkező IoT-eszközt hitelesít, a megoldáshoz tartozó legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány alapján kell létrehoznia az eszköz tanúsítványait.
Ezután lekéri az "ujjlenyomat" kifejezést a tanúsítványokból, hogy IoT Hub biztosítson.
A IoT-eszköznek szüksége van az eszköz tanúsítványának egy másolatára is, hogy az IoT Hub-hitelesítéssel hitelesíthető legyen.

#### <a name="windows"></a>Windows

1. Navigáljon ahhoz a munkakönyvtárhoz, amely a tanúsítvány-létrehozási parancsfájlokat és a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt tartalmaz.

2. Hozzon létre két tanúsítványt (elsődleges és másodlagos) az alsóbb rétegbeli eszközhöz. Egy egyszerű elnevezési konvenció a használatával hozza létre a tanúsítványokat a IoT-eszköz nevével, majd az elsődleges vagy másodlagos címkét. Például:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Ezzel a parancsfájl-paranccsal számos tanúsítvány-és kulcsfájl hozható létre. A következő tanúsítvány-és kulcspár-párokat át kell másolni az alsóbb rétegbeli IoT-eszközre, és a IoT Hubhoz csatlakozó alkalmazásokban kell hivatkozni:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Minden tanúsítványból lekéri az SHA1 ujjlenyomatot (az ujjlenyomatot IoT Hub környezetekben). Az ujjlenyomat egy 40 hexadecimális karakterből álló karakterlánc. A következő OpenSSL-paranccsal tekintheti meg a tanúsítványt, és keresse meg az ujjlenyomatot:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   Futtassa kétszer ezt a parancsot az elsődleges tanúsítványhoz és egyszer a másodlagos tanúsítványhoz. Mindkét tanúsítvány ujjlenyomatát adja meg, ha új IoT-eszközt regisztrál önaláírt X. 509 tanúsítvánnyal.

#### <a name="linux"></a>Linux

1. Navigáljon ahhoz a munkakönyvtárhoz, amely a tanúsítvány-létrehozási parancsfájlokat és a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt tartalmaz.

2. Hozzon létre két tanúsítványt (elsődleges és másodlagos) az alsóbb rétegbeli eszközhöz. Egy egyszerű elnevezési konvenció a használatával hozza létre a tanúsítványokat a IoT-eszköz nevével, majd az elsődleges vagy másodlagos címkét. Például:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Ezzel a parancsfájl-paranccsal számos tanúsítvány-és kulcsfájl hozható létre. A következő tanúsítvány-és kulcspár-párokat át kell másolni az alsóbb rétegbeli IoT-eszközre, és a IoT Hubhoz csatlakozó alkalmazásokban kell hivatkozni:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Minden tanúsítványból lekéri az SHA1 ujjlenyomatot (az ujjlenyomatot IoT Hub környezetekben). Az ujjlenyomat egy 40 hexadecimális karakterből álló karakterlánc. A következő OpenSSL-paranccsal tekintheti meg a tanúsítványt, és keresse meg az ujjlenyomatot:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Az elsődleges és a másodlagos ujjlenyomatot is megadja, ha új IoT-eszközt regisztrál önaláírt X. 509 tanúsítvánnyal.

### <a name="ca-signed-certificates"></a>HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítványok

Ha önaláírt tanúsítvánnyal hitelesíti a IoT-eszközt, fel kell töltenie a megoldás legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát, hogy IoT Hub.
Ezt követően egy ellenőrzés végrehajtásával igazolhatja, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány tulajdonosa IoT Hub.
Végül ugyanazt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja a IoT-eszközre helyezett eszköz-tanúsítványok létrehozásához, hogy a hitelesítés a IoT Hub használatával történjen.

Az ebben a szakaszban szereplő tanúsítványok az [X. 509 Biztonság beállítása az Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md)-ban című témakörben találhatók.

#### <a name="windows"></a>Windows

1. Töltse fel a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványfájl fájlt a munkakönyvtárból a `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` IoT hubhoz.

2. A Azure Portalban megadott kóddal ellenőrizheti, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány tulajdonosa-e.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Hozzon létre egy tanúsítványláncot az alsóbb rétegbeli eszközhöz. Használja ugyanazt az eszköz-azonosítót, amelyet az eszköz a IoT Hubban regisztrál.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Ezzel a parancsfájl-paranccsal számos tanúsítvány-és kulcsfájl hozható létre. A következő tanúsítvány-és kulcspár-párokat át kell másolni az alsóbb rétegbeli IoT-eszközre, és a IoT Hubhoz csatlakozó alkalmazásokban kell hivatkozni:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Töltse fel a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványfájl fájlt a munkakönyvtárból a `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` IoT hubhoz.

2. A Azure Portalban megadott kóddal ellenőrizheti, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány tulajdonosa-e.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Hozzon létre egy tanúsítványláncot az alsóbb rétegbeli eszközhöz. Használja ugyanazt az eszköz-azonosítót, amelyet az eszköz a IoT Hubban regisztrál.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Ezzel a parancsfájl-paranccsal számos tanúsítvány-és kulcsfájl hozható létre. A következő tanúsítvány-és kulcspár-párokat át kell másolni az alsóbb rétegbeli IoT-eszközre, és a IoT Hubhoz csatlakozó alkalmazásokban kell hivatkozni:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
