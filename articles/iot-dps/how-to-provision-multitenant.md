---
title: Eszközök kiépítése az Azure-beli bérlős-IoT Hub Device Provisioning Service
description: Eszközök kiépítése a bérlős az eszköz kiépítési szolgáltatásával (DPS) rendelkező példánnyal
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: eebcfb98d4e155ca965b9e4c68a862afa08a46ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727430"
---
# <a name="how-to-provision-for-multitenancy"></a>Több-bérlős modell kiépítése 

Ez a cikk azt mutatja be, hogyan lehet biztonságosan kiosztani több szimmetrikus kulcsú eszközt a IoT hubok egy csoportjára a [kiosztási szabályzat](concepts-service.md#allocation-policy)használatával. A kiépítési szolgáltatás által meghatározott kiosztási szabályzatok számos különböző foglalási forgatókönyvet támogatnak. Két gyakori forgatókönyv:

* **Térinformatikai/GeoLatency**: mivel az eszköz a helyek között mozog, a hálózati késést úgy javítja ki, hogy az eszköz az adott helyhez legközelebb eső IoT hub-ra van kiépítve. Ebben a forgatókönyvben a IoT hubok egy csoportja lesz kiválasztva a beléptetésekhez. Ezekhez a regisztrációhoz a **legalacsonyabb késési** kiosztási szabályzat van kiválasztva. A házirend hatására az eszköz kiépítési szolgáltatása kiértékeli az eszköz késését, és meghatározza a beépített IoT hub-t az IoT hubok csoportjából. 

* **Több-bérlő**: Előfordulhat, hogy egy IoT-megoldáson belül használt eszközöket hozzá kell rendelni egy adott IoT hubhoz vagy IoT-hubok csoportjához. A megoldáshoz szükség lehet arra, hogy egy adott bérlő minden eszköze kommunikáljon egy adott IoT-hubhoz. Bizonyos esetekben előfordulhat, hogy a bérlő saját IoT-hubokat használ, és az eszközöket hozzá kell rendelni az IoT-hubokhoz.

Ez a két forgatókönyv összevonása gyakori. Egy több-bérlős IoT megoldás például a bérlői eszközöket általában olyan IoT-hubok használatával rendeli hozzá, amelyek a régiók között elszórtan helyezkednek el. Ezek a bérlői eszközök hozzárendelhetők a csoport IoT hubhoz, amely a legalacsonyabb késéssel rendelkezik a földrajzi hely alapján.

Ez a cikk egy szimulált eszköz mintát használ az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -ból, amely bemutatja, hogyan lehet eszközöket kiépíteni egy több-bérlős forgatókönyvben a régiók között. A cikkben a következő lépéseket kell végrehajtania:

* Az Azure CLI használatával két regionális IoT hub hozható létre (az USA **nyugati** régiója és az **USA keleti** régiója)
* Több-bérlős regisztráció létrehozása
* Az Azure CLI-vel két regionális linuxos virtuális gépet hozhat létre, amelyek ugyanabban a régióban (az **USA nyugati** régiójában és az **USA keleti** régiójában) lévő eszközökként működnek.
* Az Azure IoT C SDK Fejlesztői környezetének beállítása Linux rendszerű virtuális gépeken
* Szimulálja az eszközöket, hogy azok kiépítve legyenek ugyanahhoz a bérlőhöz a legközelebbi régióban.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

- A [beállított IoT hub Device Provisioning Service befejezése a Azure Portal](./quick-setup-auto-provision.md) rövid útmutatóval.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Két regionális IoT hub létrehozása

Ebben a szakaszban a Azure Cloud Shell fogja használni két új regionális IoT-központ létrehozására az **USA nyugati** régiójában és az **USA keleti** régiójában a bérlők számára.


1. A Azure Cloud Shell használatával hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

    A következő példában létrehozunk egy *contoso-US-Resource-Group* nevű erőforráscsoportot a *eastus* régióban. Ezt a csoportot az ebben a cikkben létrehozott összes erőforráshoz ajánlott használni. Ezzel a művelettel könnyebben megtisztítást végezhet.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. A Azure Cloud Shell használatával hozzon létre egy IoT hubot a **eastus** régióban az az [IoT hub Create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal. Az IoT hub hozzá lesz adva a *contoso-US-Resource-Group*-hoz.

    Az alábbi példa egy *contoso-East-hub* nevű IoT-hubot hoz létre a *eastus* helyen. A **contoso-East-hub** helyett saját egyedi hub-nevet kell használnia.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    A parancs végrehajtása több percet is igénybe vehet.

3. A Azure Cloud Shell használatával hozzon létre egy IoT hubot a **westus** régióban az az [IoT hub Create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal. Ez az IoT hub a *contoso-US-Resource-Group*-hoz is hozzá lesz adva.

    A következő példa egy *contoso-West-hub* nevű IoT hubot hoz létre a *westus* helyen. A **contoso-West-hub** helyett saját egyedi hub-nevet kell használnia.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs végrehajtása több percet is igénybe vehet.



## <a name="create-the-multitenant-enrollment"></a>A több-bérlős regisztráció létrehozása

Ebben a szakaszban egy új beléptetési csoportot fog létrehozni a bérlői eszközökhöz.  

Az egyszerűség kedvéért ez a cikk [szimmetrikus kulcsú tanúsítványokat](concepts-symmetric-key-attestation.md) használ a beléptetéshez. A biztonságosabb megoldás érdekében érdemes lehet az [X. 509 tanúsítvány-igazolást](concepts-x509-attestation.md) használni egy megbízhatósági lánc használatával.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg az eszköz kiépítési szolgáltatásának példányát.

2. Válassza a **regisztrációk kezelése** fület, majd kattintson a **regisztrációs csoport hozzáadása** gombra az oldal tetején. 

3. A **regisztrációs csoport hozzáadása** párbeszédpanelen adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

    **Csoportnév**: írja be a **contoso-US-Devices** nevet.

    **Igazolás típusa**: válassza a **szimmetrikus kulcs** lehetőséget.

    **Kulcsok automatikus generálása**: ezt a jelölőnégyzetet már be kell jelölni.

    **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a hubokhoz**: válassza a **legalacsonyabb késleltetés** lehetőséget.

    ![Több-bérlős beléptetési csoport hozzáadása a szimmetrikus kulcs igazolásához](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. A **beléptetési csoport hozzáadása** területen kattintson az **új IoT hub csatolása** lehetőségre a regionális hubok összekapcsolásához.

    **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyben létrehozta a regionális IoT hubokat.

    **IoT hub**: válassza ki a létrehozott regionális hubok egyikét.

    **Hozzáférési szabályzat**: válassza a **iothubowner** lehetőséget.

    ![A regionális IoT hubok összekapcsolása a kiépítési szolgáltatással](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Ha mind a regionális IoT hubok össze lettek kapcsolva, ki kell választania azokat a beléptetési csoport számára, és a **Mentés** gombra kattintva létre kell hoznia a regionális IoT hub csoportot a beléptetéshez.

    ![A beléptetéshez tartozó regionális központ csoport létrehozása](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Miután mentette a beléptetést, nyissa meg újra, és jegyezze fel az **elsődleges kulcsot**. Először mentenie kell a beléptetést, hogy a kulcsok létrejöttek legyenek. Ezt a kulcsot a rendszer a szimulált eszközökhöz tartozó, később létrehozott egyedi eszköz-kulcsok létrehozásához használja.


## <a name="create-regional-linux-vms"></a>Regionális Linux rendszerű virtuális gépek létrehozása

Ebben a szakaszban két regionális linuxos virtuális gépet (VM) fog létrehozni. Ezek a virtuális gépek minden régióból futtatnak egy eszköz-szimulációs mintát, hogy az eszköz kiépítés után mindkét régióból bemutassa a bérlői eszközöket.

A könnyebb tisztítás érdekében ezek a virtuális gépek ugyanahhoz az erőforráscsoporthoz lesznek hozzáadva, amely tartalmazza az IoT-t, a *contoso-US-Resource-Group-* ot. A virtuális gépek azonban különálló régiókban (az USA **nyugati** régiójában és az **USA keleti** régiójában) futnak.

1. A Azure Cloud Shell futtassa a következő parancsot az **USA keleti** régiójában lévő virtuális gép létrehozásához, miután a következő paramétert módosította a parancsban:

    **--Name (név**): adjon meg egy egyedi nevet az **USA keleti** régiójában lévő, regionális eszköz virtuális gépe számára. 

    **--Admin-username**: használja a saját rendszergazdai felhasználónevét.

    **--rendszergazda-jelszó**: használja a saját rendszergazdai jelszavát.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    A parancs feldolgozása eltarthat néhány percig. Ha a parancs befejeződött, jegyezze fel az USA keleti régiójában lévő virtuális gép **publicIpAddress** értékét.

1. A Azure Cloud Shell futtassa a parancsot az **USA nyugati** régiójában lévő virtuális gép létrehozásához, miután a következő paramétert módosította a parancsban:

    **--Name (név**): adjon meg egy egyedi nevet az **USA nyugati** régiójának regionális eszközének virtuális gépe számára. 

    **--Admin-username**: használja a saját rendszergazdai felhasználónevét.

    **--rendszergazda-jelszó**: használja a saját rendszergazdai jelszavát.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    A parancs feldolgozása eltarthat néhány percig. Ha a parancs befejeződött, jegyezze fel az USA nyugati régiójának **publicIpAddress** értékét.

1. Nyisson meg két parancssori rendszerhéjt. Csatlakozzon az egyes rendszerhéjok egyik regionális virtuális géphez az SSH használatával. 

    Adja át a rendszergazdai felhasználónevét és a virtuális gép számára feljegyzett nyilvános IP-címet az SSH paraméterként. Ha a rendszer kéri, adja meg a rendszergazdai jelszót.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Az Azure IoT C SDK fejlesztési környezetének előkészítése

Ebben a szakaszban az Azure IoT C SDK-t minden egyes virtuális gépen klónozotta. Az SDK tartalmaz egy mintát, amely szimulálja a bérlő eszközének kiépítését az egyes régiókban.

1. Minden virtuális gép esetében telepítse a következő parancsokat a **CMAK**, a **g + +**, a **GCC** és a [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) használatával:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Keresse meg az SDK [legújabb kiadásához](https://github.com/Azure/azure-iot-sdk-c/releases/latest) tartozó címke nevét.

1. Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) klónozása mindkét virtuális gépen.  Használja az előző lépésben megtalált címkét a paraméter értékeként `-b` :

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Mindkét virtuális gép esetében hozzon létre egy új **CMAK** -mappát az adattáron belül, és váltson erre a mappára.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Mindkét virtuális gép esetében futtassa a következő parancsot, amely a fejlesztői ügyféloldali platformhoz tartozó SDK egy verzióját építi fel. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Egyedi eszköz kulcsának származtatása

Ha szimmetrikus kulcsú igazolást használ csoportos regisztrációkkal, nem használja közvetlenül a beléptetési csoport kulcsait. Ehelyett hozzon létre egy egyedi származtatott kulcsot minden eszközhöz, és a [csoportos Beléptetésekben szerepel a szimmetrikus kulcsokkal](concepts-symmetric-key-attestation.md#group-enrollments).

Az eszköz kulcsának létrehozásához használja a csoport főkulcsát az eszköz egyedi regisztrációs AZONOSÍTÓjának [HMAC](https://wikipedia.org/wiki/HMAC) számításához, és Base64 formátumra alakítsa át az eredményt.

Ne foglalja bele a csoport főkulcsát az eszköz kódjába.

A bash rendszerhéj-példa használatával hozzon létre egy származtatott eszközt az összes eszközhöz az **OpenSSL** használatával.

- Cserélje le a **kulcs** értékét a regisztrációhoz korábban feljegyzett **elsődleges kulcsra** .

- Cserélje le a **REG_ID** értékét az egyes eszközökhöz tartozó saját egyedi regisztrációs azonosítóra. Használjon kisbetűs alfanumerikus és kötőjel ("-") karaktereket mindkét azonosító definiálásához.

Példa a *contoso-simdevice-East* eszköz kulcsának generálására:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Példa a *contoso-simdevice-West* eszköz kulcsának generálására:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


A bérlői eszközök mindegyike a származtatott eszköz kulcsát és egyedi regisztrációs AZONOSÍTÓját használja a szimmetrikus kulcs igazolására a regisztrációs csoporttal a bérlői IoT hubok kiosztása során.




## <a name="simulate-the-devices-from-each-region"></a>Az eszközök szimulálása az egyes régiókból


Ebben a szakaszban egy kiépítési mintát fog frissíteni az Azure IoT C SDK-ban mindkét regionális virtuális gépen. 

A mintakód szimulál egy eszköz rendszerindítási sorozatot, amely elküldi az üzembe helyezési kérelmet az eszköz kiépítési szolgáltatásának példányára. A rendszerindítási folyamat hatására az eszköz felismerhetővé válik, és hozzá lesz rendelve az IoT hubhoz, amely a legközelebbi késésen alapul.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Nyissa meg a **~/Azure-IOT-SDK-c/Provisioning \_ Client/Samples/prov \_ dev \_ Client \_ Sample/prov \_ dev \_ Client \_ sample. c-** t mindkét virtuális gépen való szerkesztéshez.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg arról, hogy a `hsm_type` változó az `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` alább látható módon van beállítva, hogy megfeleljen a regisztrációs csoport igazolási módszerének. 

    Mentse a fájlokat mindkét virtuális gépen.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Mindkét virtuális gépen keresse meg a " `prov_dev_set_symmetric_key_info()` **prov \_ dev \_ Client \_ sample. c** " hívást, amely megjegyzésként szerepel.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    A függvény meghívásának visszaírása és a helyőrző értékek (beleértve a szögletes zárójeleket is) és az egyes eszközök egyedi regisztrációs azonosítóinak és származtatott kulcsának cseréje. Az alább látható kulcsok csak példaként szolgálnak. Használja a korábban létrehozott kulcsokat.

    USA keleti régiója:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    USA nyugati régiója:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Mentse a fájlokat.

1. Mindkét virtuális gépen navigáljon az alább látható minta mappájához, és hozza létre a mintát.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Ha a létrehozás sikeres, futtassa a **prov \_ dev \_ Client \_sample.exet** mindkét virtuális gépen a bérlői eszköz minden egyes régióból való szimulálása érdekében. Figyelje meg, hogy minden eszköz a szimulált eszköz régióihoz legközelebb eső bérlői IoT hubhoz van lefoglalva.

    Futtassa a szimulációt:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Példa az USA keleti virtuális gépe kimenetére:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Példa az USA nyugati részén található virtuális gép kimenetére:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a jelen cikkben létrehozott erőforrásokkal való munkát, meghagyhatja őket. Ha nem tervezi tovább használni az erőforrást, a következő lépésekkel törölheti a cikkben létrehozott összes erőforrást a szükségtelen költségek elkerülése érdekében.

Az itt leírt lépések azt feltételezik, hogy a cikkben szereplő összes erőforrást a **contoso-US-Resource-Group** nevű erőforráscsoport utasításai szerint hozta létre.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport törlése név szerint:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. A **szűrés név szerint...** szövegmezőbe írja be az erőforrásokat tartalmazó erőforráscsoport nevét, a **contoso-US-Resource-Group** nevet. 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> Az újratelepítéssel kapcsolatos további tudnivalókért tekintse meg az [eszközök újraépítése IoT hub](concepts-device-reprovision.md)

> [!div class="nextstepaction"]
> További információ a megszüntetésről: [a korábban automatikusan kiépített eszközök kiépítése](how-to-unprovision-devices.md) .