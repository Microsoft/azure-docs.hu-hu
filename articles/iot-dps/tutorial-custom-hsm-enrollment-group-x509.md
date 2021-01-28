---
title: Oktatóanyag – X. 509 eszközök kiépítése az Azure IoT Hubba egyéni hardveres biztonsági modul (HSM) használatával
description: Ez az oktatóanyag beléptetési csoportokat használ. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre X. 509-eszközöket egyéni hardveres biztonsági modul (HSM) és az Azure IoT Hub Device Provisioning Service (DPS) C Device SDK használatával.
author: wesmc7777
ms.author: wesmc
ms.date: 11/18/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 566563dde26d2dd36f4358bc8c6dcdcfb5ba8465
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954868"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>Oktatóanyag: több X. 509 eszköz kiépítése beléptetési csoportok használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan IoT-eszközök csoportjait, amelyek X. 509 tanúsítványokat használnak a hitelesítéshez. Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -ból származó mintakód használatával IoT-eszközként kiépítheti a fejlesztői gépet. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

* [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
* [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez az oktatóanyag hasonló a korábbi oktatóanyagokhoz, amelyek bemutatják, hogyan használhatók a beléptetési csoportok az eszközök készletének kiépítéséhez. Ebben az oktatóanyagban azonban a szimmetrikus kulcsok helyett X. 509 tanúsítványokat fogunk használni. Tekintse át az ebben a szakaszban található korábbi oktatóanyagokat a [szimmetrikus kulcsok](./concepts-symmetric-key-attestation.md)használatával történő egyszerű megközelítéshez.

Ez az oktatóanyag bemutatja az [Egyéni HSM-mintát](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example) , amely a hardveres biztonságos tárolással való együttműködésre szolgáló helyettes implementációt biztosít. A [hardveres biztonsági modul (HSM)](./concepts-service.md#hardware-security-module) a biztonságos, hardveres tárolásra szolgál az eszközök titkaihoz. A HSM használható szimmetrikus kulccsal, X. 509 tanúsítvánnyal vagy TPM-igazolással, hogy biztonságos tárhelyet biztosítson a titkok számára. Az eszköz titkos kulcsainak hardveres tárolása nem kötelező, de erősen ajánlott a bizalmas adatok, például az eszköz tanúsítványa titkos kulcsának védelmére.

Ha nem ismeri az kiépítés folyamatát, tekintse át a [kiépítés](about-iot-dps.md#provisioning-process) áttekintését. Győződjön meg arról is, hogy végrehajtotta a [IoT hub Device Provisioning Service beállítása a Azure Portal az](quick-setup-auto-provision.md) oktatóanyag folytatása előtt című témakör lépéseit. 


Ebben az oktatóanyagban a következő célkitűzéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy megbízhatósági láncot, amely X. 509 tanúsítványokat használó eszközök készletét rendezi.
> * A tanúsítványlánc által használt aláíró tanúsítvánnyal végzett birtoklás igazolása.
> * A tanúsítványláncot használó új csoportos regisztráció létrehozása
> * Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -ban programkódot használó eszköz kiépítésére szolgáló fejlesztői környezet beállítása
> * Hozzon létre egy eszközt a tanúsítványláncot használva az SDK-ban az egyéni hardveres biztonsági modul (HSM) mintájának használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a Windows fejlesztési környezetéhez szükségesek. Linux vagy macOS esetén tekintse meg a [fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) az SDK-ban című dokumentáció megfelelő szakaszát.

* A [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019-es verziójában engedélyezve van az ["asztali fejlesztés C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) -ban" számítási feladattal. A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Az Azure IoT C SDK fejlesztési környezetének előkészítése

Ebben a szakaszban előkészítjük az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) build készítésére szolgáló fejlesztőkörnyezetet. Az SDK tartalmazza az X. 509-eszközök által a DPS-vel való üzembe helyezéshez használt mintakód és eszközöket.

1. Töltse le a [Csatlakozáskezelő felügyeleti csomag Build-szolgáltatását](https://cmake.org/download/).

    Fontos, hogy a telepítés megkezdése **előtt** telepítse a Visual Studio előfeltételeit (a [Visual studiót](https://visualstudio.microsoft.com/vs/) és az ["asztali fejlesztés C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) -os munkaterheléssel") `CMake` . Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az Azure IoT C SDK [legújabb kiadásához](https://github.com/Azure/azure-iot-sdk-c/releases/latest) tartozó címke nevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben megtalált címkét a paraméter értékeként `-b` :

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. A `cmake` létrehozott könyvtár tartalmazni fogja az egyéni HSM mintát, valamint az egyéni HSM-et használó minta-eszköz kiépítési kódját, amely X. 509 hitelesítést biztosít. 

    Futtassa a következő parancsot a `cmake` címtárban a fejlesztői platformhoz tartozó SDK-verzió összeállításához. A Build tartalmazni fog egy hivatkozást az egyéni HSM-mintára. 

    Az alábbihoz használt elérési út megadásakor `-Dhsm_custom_lib` ügyeljen arra, hogy a `cmake` korábban létrehozott címtárhoz viszonyított elérési utat használja. Az alább látható relatív elérési út csak példa.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](/dotnet/framework/tools/developer-command-prompt-for-vs).

    Ha a létrehozás sikeres, a rendszer létrehoz egy Visual Studio-megoldást a `cmake` címtárában. Az utolsó néhány kimeneti sor a következő kimenethez hasonlóan néz ki:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>X. 509 tanúsítványlánc létrehozása

Ebben a szakaszban három tanúsítványból álló X. 509 tanúsítványláncot fog előállítani az oktatóanyag teszteléséhez. A tanúsítványok a következő hierarchiát fogják tartalmazni.

![Oktatóanyag – eszköz tanúsítványának lánca](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[Főtanúsítvány](concepts-x509-attestation.md#root-certificate): a főtanúsítvány ellenőrzéséhez teljes körű [bizonyítást](how-to-verify-certificates.md) fog végezni. Ez az ellenőrzés lehetővé teszi a DPS számára, hogy megbízzon a tanúsítványban, és ellenőrizze az általa aláírt tanúsítványokat.

[Köztes tanúsítvány](concepts-x509-attestation.md#intermediate-certificate): gyakori, hogy a köztes tanúsítványok az eszközöket a termékcsoportok, a céges részlegek vagy más feltételek alapján, logikailag csoportosítva használják. Ez az oktatóanyag egy közbenső tanúsítványból álló tanúsítványlánc használatát fogja használni. A köztes tanúsítványt a főtanúsítvány fogja aláírni. Ez a tanúsítvány a DPS-ben létrehozott beléptetési csoportban is használatos az eszközök logikai csoportosításához. Ez a konfiguráció lehetővé teszi az olyan eszközök teljes csoportjának felügyeletét, amelyeknek az eszköz-tanúsítványai ugyanazzal a közbenső tanúsítvánnyal vannak aláírva. Az eszközök egy csoportjának engedélyezéséhez vagy letiltásához létrehozhat regisztrációs csoportokat. Az eszközök egy csoportjának letiltásával kapcsolatos további információkért lásd: [X. 509 közbenső vagy legfelső szintű hitelesítésszolgáltatói tanúsítvány tiltása egy regisztrációs csoport használatával](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group)

[Eszköz tanúsítványa](concepts-x509-attestation.md#end-entity-leaf-certificate): az eszköz (levél) tanúsítványát a köztes tanúsítvány írja alá, és a titkos kulccsal együtt tárolja az eszközön. Az eszköz ezt a tanúsítványt és titkos kulcsot fogja bemutatni, valamint a tanúsítvány-láncot a kiépítés megkísérlése során. 

A tanúsítványlánc létrehozása:

1. Nyisson meg egy git bash parancssort. Hajtsa végre az 1. és a 2. lépést a [minták és oktatóanyagok tesztelési hitelesítésszolgáltatói tanúsítványainak kezelése](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials)című részében található bash rendszerhéj-utasítások használatával.

    Ez a lépés létrehoz egy munkakönyvtárat a tanúsítvány parancsfájljaihoz, és létrehozza a példában szereplő legfelső szintű és köztes tanúsítványt a tanúsítványlánc számára az OpenSSL használatával. 

    Figyelje meg az önaláírt főtanúsítvány helyét bemutató kimenetet. Ez a tanúsítvány [igazolja, hogy a](how-to-verify-certificates.md) tulajdonjog ellenőrzése később megtörténik.

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        

    Figyelje meg azt a kimenetet, amely a főtanúsítvány által aláírt/kiállított köztes tanúsítvány helyét mutatja. A rendszer ezt a tanúsítványt fogja használni a később létrehozandó beléptetési csoporttal.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    

2. Ezután futtassa a következő parancsot egy olyan új eszköz/levél tanúsítvány létrehozásához, amelynek tulajdonos neve paraméterként szolgál. Használja az oktatóanyaghoz megadott példa tulajdonos nevét `custom-hsm-device-01` . Ez a tulajdonos neve lesz a IoT eszköz azonosítója. 

    > [!WARNING]
    > Ne használjon szóközt a tulajdonos nevében. A tulajdonos neve a kiépített IoT-eszköz azonosítója. Az eszköz AZONOSÍTÓjának szabályait kell követnie. További információ: [eszköz identitásának tulajdonságai](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    Figyelje meg a következő kimenetet, amely bemutatja, hogy hol található az új eszköz tanúsítványa. Az eszköz tanúsítványát a köztes tanúsítvány aláírja (kiállította).

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
3. Futtassa a következő parancsot egy teljes tanúsítványlánc. PEM fájl létrehozásához, amely tartalmazza az új eszköz tanúsítványát.

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-full-chain.cert.pem && cd ..
    ```

    Használjon egy szövegszerkesztőt, és nyissa meg a tanúsítványlánc fájlt *./certs/New-Device-Full-Chain.CERT.PEM*. A tanúsítványlánc szövege mindhárom tanúsítvány teljes láncát tartalmazza. Az oktatóanyag későbbi részében ezt a szöveget fogja használni az egyéni HSM-kóddal rendelkező tanúsítványláncként.

    A teljes lánc szövege a következő formátumú:
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

5. Figyelje meg, hogy az új eszköz tanúsítványának titkos kulcsa a *./Private/New-Device.key.PEM*. Az eszköznek a kiépítés során szüksége lesz a kulcs szövegére. A szöveget később a rendszer hozzáadja az egyéni HSM-példához.

    > [!WARNING]
    > A tanúsítványok szövege csak a nyilvános kulcsokra vonatkozó információkat tartalmazza. 
    >
    > Az eszköznek azonban hozzá kell férnie az eszköz tanúsítványához tartozó titkos kulcshoz is. Erre azért van szükség, mert az eszköznek a kiépítés megkísérlése során az adott kulcs futtatásával kell végrehajtania a hitelesítést. Ennek a kulcsnak az érzékenysége az egyik fő oka annak, hogy a hardveres tárterületet egy valós HSM-ben érdemes használni a titkos kulcsok biztonságossá tételéhez.



## <a name="configure-the-custom-hsm-stub-code"></a>Egyéni HSM-helyettes kód konfigurálása

A tényleges biztonságos hardver-alapú tárolással való interakció sajátosságai a hardvertől függően változnak. Ennek eredményeképpen az eszköz által az oktatóanyagban használt tanúsítványlánc az egyéni HSM-hardcoded lesz. A valós forgatókönyvekben a tanúsítványlánc a tényleges HSM-hardveren lesz tárolva, hogy jobb biztonságot nyújtson a bizalmas adatok számára. Az ebben a mintában látható helyettes metódusokhoz hasonló metódusokat a rendszer úgy valósítja meg, hogy beolvassa a hardveres tárterület titkait. 

Amíg a HSM hardver nem szükséges, nem ajánlott bizalmas adatokat, például a tanúsítvány titkos kulcsát, a forráskódba bejelölve. Ez a kulcs mindenki számára elérhetővé teszi, aki megtekintheti a kódot. Ez a cikk csak a tanuláshoz nyújt segítséget.

A következő oktatóanyaghoz tartozó egyéni HSM-helyettes kód frissítése:

1. Indítsa el a Visual studiót, és nyissa meg az `cmake` Azure-IOT-SDK-c git-tárház gyökerében létrehozott könyvtárban létrehozott új megoldási fájlt. A megoldás fájljának neve `azure_iot_sdks.sln` .

2. Megoldáskezelő a Visual studióhoz navigáljon **Provisioning_Samples > custom_hsm_example > forrásfájlokat** , és nyissa meg a *Custom_hsm_example. c* fájlt.

3. Frissítse a karakterlánc-konstans karakterlánc-értékét `COMMON_NAME` az eszköz tanúsítványának létrehozásakor használt köznapi név használatával.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

4. Ugyanebben a fájlban frissítenie kell az állandó sztring sztring értékét a `CERTIFICATE` *./certs/New-Device-Full-Chain.CERT.PEM* fájlba mentett tanúsítványlánc szövege alapján a tanúsítványok létrehozása után.

    A tanúsítvány szövegének szintaxisának az alábbi mintát kell követnie, és a Visual Studio nem végez további szóközöket vagy elemzést.

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    Ebben a lépésben a karakterlánc értékének megfelelő frissítése nagyon unalmas lehet, és hiba miatt változhat. A git bash-parancssorban a megfelelő szintaxis létrehozásához másolja ki és illessze be az alábbi bash shell-parancsokat a git bash parancssorába, majd nyomja le az **ENTER** billentyűt. Ezek a parancsok a `CERTIFICATE` karakterlánc konstans értékének szintaxisát fogják eredményezni.

    ```Bash
    input="./certs/new-device-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Másolja és illessze be a kimeneti tanúsítvány szövegét az új állandó értékhez. 


5. Ugyanebben a fájlban az állandó karakterlánc értékét `PRIVATE_KEY` is frissíteni kell az eszköz tanúsítványának titkos kulcsával.

    A titkos kulcs szövegének szintaxisának az alábbi mintát kell követnie, és a Visual Studio nem végez további szóközöket vagy elemzést.

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    A karakterlánc értékének megfelelő frissítése ebben a lépésben nagyon unalmas lehet, és a hiba is fennáll. A git bash-parancssorban a megfelelő szintaxis létrehozásához másolja ki és illessze be a következő bash shell-parancsokat, majd nyomja le az **ENTER** billentyűt. Ezek a parancsok a `PRIVATE_KEY` karakterlánc konstans értékének szintaxisát fogják eredményezni.

    ```Bash
    input="./private/new-device.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Másolja és illessze be a kimeneti titkos kulcs szövegét az új állandó értékhez. 

6. Mentse a *custom_hsm_example. c*.


## <a name="verify-ownership-of-the-root-certificate"></a>A főtanúsítvány tulajdonjogának ellenőrzése

1. Az [X. 509 tanúsítvány nyilvános részének regisztrálása és az ellenőrző kód beszerzése](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code), a főtanúsítvány feltöltése ( `./certs/azure-iot-test-only.root.ca.cert.pem` ) és a DPS ellenőrző kód beszerzése.

2. Miután a főtanúsítványhoz a DPS ellenőrző kódot kapott, futtassa a következő parancsot a tanúsítvány parancsfájl munkakönyvtárában egy ellenőrző tanúsítvány létrehozásához.
 
    Az itt megadott ellenőrző kód csak példa. Használja a DPS-ből generált kódot.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    Ez a szkript létrehoz egy tanúsítványt, amelyet a főtanúsítvány aláírt a tulajdonos neve beállításnál az ellenőrző kódra. Ez a tanúsítvány lehetővé teszi, hogy a DPS ellenőrizze, hogy van-e hozzáférése a főtanúsítvány titkos kulcsához. Figyelje meg az ellenőrző tanúsítvány helyét a parancsfájl kimenetében. Ez a tanúsítvány `.pfx` formátummal jön létre.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. Az [aláírt ellenőrző tanúsítvány feltöltése](how-to-verify-certificates.md#upload-the-signed-verification-certificate)című részben leírtak szerint töltse fel az ellenőrző tanúsítványt, majd kattintson az **ellenőrzés** a DPS-ben lehetőségre a főtanúsítvány birtoklási igazolásának befejezéséhez.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>A tanúsítványtároló frissítése Windows-alapú eszközökön

A nem Windows rendszerű eszközökön a tanúsítványláncot átadhatja a kódból a tanúsítványtárolóként.

Windows-alapú eszközökön hozzá kell adnia az aláíró tanúsítványokat (root és Intermediate) egy Windows- [tanúsítványtárolóhoz](/windows/win32/secauthn/certificate-stores). Ellenkező esetben az aláíró tanúsítványok nem lesznek átirányítva a DPS-re egy biztonságos csatorna Transport Layer Security (TLS) protokollal.

> [!TIP]
> Az OpenSSL-t a biztonságos csatorna (Schannel) helyett a C SDK-val is lehet használni. Az OpenSSL használatával kapcsolatos további információkért lásd: [az OpenSSL használata az SDK-ban](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk).

Az aláíró tanúsítványok hozzáadása a tanúsítványtárolóhoz Windows-alapú eszközökön:

1. Egy git bash-parancssorban navigáljon az `certs` aláíró tanúsítványokat tartalmazó alkönyvtárhoz, és alakítsa át azokat a `.pfx` következőképpen.

    főtanúsítvány:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    köztes tanúsítvány:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Kattintson a jobb gombbal a Windows **Start** gombra. Ezután kattintson a **Futtatás** gombra. Írja be a *certmgr. MCS* parancsot, és kattintson **az OK** gombra a Tanúsítványkezelő MMC beépülő modul elindításához.

3. A Tanúsítványkezelőben a **tanúsítványok – aktuális felhasználó** területen kattintson a **megbízható legfelső szintű hitelesítésszolgáltatók** elemre. Ezután a menüben kattintson a **művelet**  >  **minden feladat** importálás elemre  >   `root.pfx` .

    * Győződjön meg arról, hogy **személyes információcsere (. pfx)** alapján keres.
    * Használja `1234` jelszóként.
    * Helyezze a tanúsítványt a **megbízható legfelső szintű hitelesítésszolgáltatók** tanúsítványtárolóba.

4. A Tanúsítványkezelőben a **tanúsítványok – aktuális felhasználó** területen kattintson a **köztes hitelesítésszolgáltatók** elemre. Ezután a menüben kattintson a **művelet**  >  **minden feladat** importálás elemre  >   `intermediate.pfx` .

    * Győződjön meg arról, hogy **személyes információcsere (. pfx)** alapján keres.
    * Használja `1234` jelszóként.
    * Helyezze a tanúsítványt a **köztes hitelesítésszolgáltatók** tanúsítványtárolóba.

Az aláíró tanúsítványok mostantól megbízhatók a Windows-alapú eszközön, és a teljes láncot a DPS-be lehet szállítani.



## <a name="create-an-enrollment-group"></a>Regisztrációs csoport létrehozása

1. Jelentkezzen be a Azure Portalba, majd a bal oldali menüben kattintson a **minden erőforrás** gombra, és nyissa meg az eszköz kiépítési szolgáltatását.

2. Válassza a **regisztrációk kezelése** fület, majd kattintson a felső **beléptetési csoport hozzáadása** gombra.

3. A **regisztrációs csoport hozzáadása** panelen adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

      ![Regisztrációs csoport hozzáadása X. 509 igazoláshoz a portálon](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | Mező        | Érték           |
    | :----------- | :-------------- |
    | **Csoport neve** | Ebben az oktatóanyagban adja meg az **Egyéni-HSM-x509-Devices** |
    | **Igazolás típusa** | **Tanúsítvány** kiválasztása |
    | **IoT Edge-eszköz** | **Hamis** kijelölése |
    | **Tanúsítvány típusa** | **Köztes tanúsítvány** kiválasztása |
    | **Elsődleges tanúsítvány. PEM vagy. cer fájl** | Navigáljon a korábban létrehozott köztes (*./certs/Azure-IOT-test-only.Intermediate.CERT.PEM*) |


## <a name="configure-the-provisioning-device-code"></a>A kiépítési eszköz kódjának konfigurálása

Ebben a szakaszban a mintakód frissítésével kiépítheti az eszközt az eszköz kiépítési szolgáltatásának példányával. Ha az eszköz hitelesítése megtörtént, akkor az eszköz kiépítési szolgáltatásának példányához társított IoT hub lesz hozzárendelve.

1. A Azure Portal válassza az eszközök kiépítési szolgáltatásának **Áttekintés** lapját, és jegyezze fel az **_azonosító hatókörének_** értékét.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Megoldáskezelő a Visual studióhoz navigáljon **Provisioning_Samples > prov_dev_client_sample > forrásfájlokat** , és nyissa meg a *Prov_dev_client_sample. c* fájlt.

3. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

4. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg arról, `hsm_type` hogy a változó a `SECURE_DEVICE_TYPE_X509` lent látható módon van beállítva.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget.

6. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a megoldás futtatásához. Amikor a rendszer rákérdez a projekt újraépítésére, válassza az **Igen** lehetőséget a projekt újraépítéséhez a futtatása előtt.

    Az alábbi kimenet egy példa arra, hogy a kiépítési eszköz ügyfél-mintája sikeresen elindult-e, és csatlakozik a kiépítési szolgáltatáshoz. Az eszköz hozzá lett rendelve egy IoT hubhoz, és regisztrálva van:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. A portálon navigáljon a kiépítési szolgáltatáshoz társított IoT hubhoz, és válassza a IoT- **eszközök** lapot. Az X. 509 eszköznek a központba való sikeres kiépítés után az eszköz azonosítója megjelenik az **IoT-eszközök** panelen, és az *állapota* **engedélyezett**. Előfordulhat, hogy a felül található **refresh (frissítés** ) gombra kell kattintania. 

    ![Az egyéni HSM-eszköz regisztrálva van az IoT hub-ban](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az eszköz ügyféloldali mintájának tesztelését és vizsgálatát, a következő lépésekkel törölheti az oktatóanyagban létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációinak kezelése** elemet, majd válassza a **regisztrációs csoportok** lapot. Jelölje be az oktatóanyagban létrehozott eszközcsoport *neve* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
1. Kattintson a **tanúsítványok** elemre a DPS-ben. Az oktatóanyagban feltöltött és ellenőrzött összes tanúsítvány esetében kattintson a tanúsítványra, és a **Törlés** gombra kattintva távolítsa el.
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközöket** a hubhoz. Jelölje be az oktatóanyagban regisztrált eszköz *azonosítója* melletti jelölőnégyzetet. Kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy X. 509 eszközt telepített egy egyéni HSM használatával az IoT hub-ra. Ha szeretné megtudni, hogyan lehet IoT-eszközöket több hubhoz kiépíteni, folytassa a következő oktatóanyaggal. 

> [!div class="nextstepaction"]
> [Oktatóanyag: eszközök kiépítése elosztott terhelésű IoT hubok között](tutorial-provision-multiple-hubs.md)