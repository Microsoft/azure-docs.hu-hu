---
title: Eszközök kiépítése több-bérlősként a Device Provisioning Service Azure IoT Hub szolgáltatásban
description: Eszközök kiépítése több-bérlős rendszer számára a Device Provisioning Service (DPS) példányával
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0b88923ff6447785a4ef5a7c80e1ff44d1a2b9cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777362"
---
# <a name="how-to-provision-for-multitenancy"></a>Több-bérlős modell kiépítése 

Ez a cikk bemutatja, hogyan lehet biztonságosan kiépítni több szimmetrikus kulcsú eszközt egy IoT Hub-csoport számára egy foglalási [szabályzat használatával.](concepts-service.md#allocation-policy) A kiépítési szolgáltatás által meghatározott kiosztási szabályzatok számos különböző foglalási forgatókönyvet támogatnak. Két gyakori forgatókönyv:

* **Földrajzi hely/ GeoLatency:** Az eszközök helyek közötti áthelyezése esetén a hálózati késést javítja, ha az eszközt az ioT Hubhoz legközelebbi helyen kell kiépítenünk. Ebben a forgatókönyvben az IoT Hubok régiók közötti csoportja lesz kiválasztva a regisztrációkhoz. Ezekhez **a regisztrációkhoz** a legkisebb késési kiosztási szabályzat van kiválasztva. Ez a szabályzat hatására a Device Provisioning Service kiértékeli az eszköz késését, és meghatározza, hogy az IoT Hubok csoportjából melyik IoT Hub található. 

* **Több-bérlős:** Előfordulhat, hogy az IoT-megoldásban használt eszközöket hozzá kell rendelni egy adott IoT Hubhoz vagy IoT Hub-csoporthoz. A megoldás megkövetelheti, hogy egy adott bérlő összes eszköze kommunikáljon az IoT Hubok egy adott csoportjával. Bizonyos esetekben előfordulhat, hogy egy bérlő rendelkezik IoT-központokkal, és megkövetelheti az eszközök ioT Hubhoz való hozzárendelését.

Ezt a két forgatókönyvet gyakran kombinálják. Egy több-bérlős IoT-megoldás például általában bérlőeszközöket rendel hozzá a régiók között elszórt IoT Hub-csoportok használatával. Ezek a bérlői eszközök hozzárendelhetőek a csoportban található IoT Hubhoz, amely a földrajzi helytől függően a legkisebb késéssel rendelkezik.

Ez a cikk egy szimulált eszközmintát használ az [Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) SDK-ból, amely bemutatja, hogyan lehet eszközöket kiépíteni több-bérlős forgatókönyvekben régiók között. A cikkben a következő lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure CLI használatával hozzon létre két regionális IoT-központot (az **USA nyugati** régiója és az USA **keleti régiója)**
> * Több-bérlős regisztráció létrehozása
> * Az Azure CLI használatával hozzon létre két regionális Linux rendszerű virtuális gépeket, amelyek eszközként szolgálnak ugyanabban a régióban **(az** USA nyugati régiójában és az **USA keleti régiójában)**
> * Az Azure IoT C SDK fejlesztési környezetének beállítása mindkét Linux rendszerű virtuális géphez
> * Szimulálja az eszközöket, hogy látják, hogy ugyanazon bérlőhöz vannak kiépítve a legközelebbi régióban.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

- A [Beállítási útmutató IoT Hub Device Provisioning Service befejezése Azure Portal](./quick-setup-auto-provision.md) gyorsútmutatóval.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Két regionális IoT Hub létrehozása

Ebben a szakaszban a Azure Cloud Shell hoz létre két új regionális IoT Hubot az **USA** nyugati régiójában és az **USA** keleti régiójában egy bérlő számára.


1. A Azure Cloud Shell hozzon létre egy erőforráscsoportot [az az group create paranccsal.](/cli/azure/group#az_group_create) Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

    Az alábbi példa egy *contoso-us-resource-group* nevű erőforráscsoportot hoz létre az *eastus régióban.* Javasoljuk, hogy ezt a csoportot használja a cikkben létrehozott összes erőforráshoz. Ez megkönnyíti a tisztítást a munka befejezése után.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. A Azure Cloud Shell hozzon létre egy IoT Hubot az **eastus** régióban [az az iot hub create paranccsal.](/cli/azure/iot/hub#az_iot_hub_create) Az IoT Hub a *contoso-us-resource-group csoporthoz lesz hozzáadva.*

    Az alábbi példa egy *contoso-east-hub* nevű IoT Hubot hoz létre az *eastus* helyen. A contoso-east-hub helyett a saját egyedi **hubnevét kell használnia.**

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    A parancs befejezése eltarthat néhány percig.

3. A Azure Cloud Shell hozzon létre egy IoT Hubot a **westus** régióban [az az iot hub create paranccsal.](/cli/azure/iot/hub#az_iot_hub_create) Ez az IoT Hub a *contoso-us-resource-group csoporthoz is hozzá lesz adva.*

    Az alábbi példa egy *contoso-west-hub* nevű IoT Hubot hoz létre a *westus helyen.* A contoso-west-hub helyett a saját egyedi **hubnevét kell használnia.**

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs befejezése eltarthat néhány percig.



## <a name="create-the-multitenant-enrollment"></a>A több-bérlős regisztráció létrehozása

Ebben a szakaszban egy új regisztrációs csoportot fog létrehozni a bérlői eszközökhöz.  

Az egyszerűség kedvéért ez a cikk szimmetrikus [kulcsú igazolást használ a](concepts-symmetric-key-attestation.md) regisztrációval. A biztonságosabb megoldás érdekében érdemes [lehet X.509-tanúsítvány](concepts-x509-attestation.md) igazolást használni megbízhatósági lánccal.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)és nyissa meg a Device Provisioning Service-példányt.

2. Válassza a **Regisztrációk kezelése** lapot, majd kattintson az oldal tetején található **Regisztrációs** csoport hozzáadása gombra. 

3. A **Regisztrációs csoport hozzáadása oldalon adja** meg a következő adatokat, majd kattintson a Mentés **gombra.**

    **Csoport neve:** Adja meg **a contoso-us-devices nevet.**

    **Igazolás típusa:** Válassza a **Szimmetrikus kulcs lehetőséget.**

    **Kulcsok automatikus létrehozása:** Ez a jelölőnégyzet már be van jelölve.

    **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a központokhoz:** Válassza a **Legkisebb késés lehetőséget.**

    ![Több-bérlős regisztrációs csoport hozzáadása szimmetrikus kulcsú igazoláshoz](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. A **Regisztrációs csoport hozzáadása oldalon kattintson** az Új **IoT Hub csatolása** elemre mindkét regionális központ csatolásához.

    **Előfizetés:** Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyben létrehozta a regionális IoT Hubokat.

    **IoT Hub:** Válassza ki az egyik létrehozott regionális központot.

    **Hozzáférési szabályzat:** Válassza az **iothubowner lehetőséget.**

    ![A regionális IoT Hubok összekapcsolása a kiépítési szolgáltatással](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Miután mindkét regionális IoT Hubot összekapcsolta, ki kell választania  őket a regisztrációs csoporthoz, majd a Mentés gombra kattintva létre kell hoznia a regionális IoT Hub-csoportot a regisztrációhoz.

    ![A regionális központi csoport létrehozása a regisztrációhoz](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. A regisztráció mentése után nyissa meg újra, és jegyezze fel az **elsődleges kulcsot.** A kulcsok generálása előtt mentenie kell a regisztrációt. A későbbiekben ezzel a kulccsal fog egyedi eszközkulcsokat létrehozni mindkét szimulált eszközhöz.


## <a name="create-regional-linux-vms"></a>Regionális Linux rendszerű virtuális gépek létrehozása

Ebben a szakaszban két regionális Linux rendszerű virtuális gépet fog létrehozni. Ezek a virtuális gépek minden régióból lefuttatnak egy eszközszimulációs mintát, amely mindkét régió bérlőeszközei számára bemutatja az eszközátépítést.

A tisztítás érdekében ezek a virtuális gépek ugyanabba az erőforráscsoportba kerülnek, amely a létrehozott IoT Hubokat tartalmazza( *contoso-us-resource-group).* A virtuális gépek azonban külön régiókban fognak futni ( az **USA** nyugati régiójában és **az USA keleti régiójában).**

1. A Azure Cloud Shell hajtsa végre **a** következő parancsot egy USA keleti régiójában található virtuális gép létrehozásához, miután végrehajtotta a következő paraméterváltozásokat a parancsban:

    **--name:** Adjon meg egy egyedi nevet az USA keleti régiója **virtuális** gépének. 

    **--admin-username:** Használja a saját rendszergazdai felhasználónevét.

    **--admin-password:** Használja a saját rendszergazdai jelszavát.

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

    A parancs feldolgozása eltarthat néhány percig. A parancs befejezése után jegyezze fel az USA keleti régiójában található virtuális gép **publicIpAddress** értékét.

1. A Azure Cloud Shell hajtsa végre **az** parancsot az USA nyugati régiójában található virtuális gép létrehozásához, miután végrehajtotta a következő paraméterváltozásokat a parancsban:

    **--name:** Adjon meg egy egyedi nevet az USA **nyugati régiója** virtuális gépének. 

    **--admin-username:** Használja a saját rendszergazdai felhasználónevét.

    **--admin-password:** Használja a saját rendszergazdai jelszavát.

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

    A parancs feldolgozása eltarthat néhány percig. A parancs befejezése után jegyezze fel az USA nyugati régiójában található virtuális gép **publicIpAddress** értékét.

1. Nyisson meg két parancssori rendszerhéjat. Csatlakozzon az egyes rendszerhéjak egyik regionális virtuális géphez SSH használatával. 

    Adja át paraméterként a rendszergazdai felhasználónevet és a virtuális géphez feljegyzett nyilvános IP-címet az SSH-nak. Amikor a rendszer kéri, adja meg a rendszergazdai jelszót.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Az Azure IoT C SDK fejlesztési környezet előkészítése

Ebben a szakaszban klónozni fogja az Azure IoT C SDK-t minden virtuális gépen. Az SDK tartalmaz egy mintát, amely szimulálja a bérlők eszközátépítését az egyes régiókból.

1. Minden virtuális géphez telepítse a **CMake,** **g++**, **gcc** és [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) adatokat a következő parancsokkal:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Keresse meg az SDK [legújabb kiadásának](https://github.com/Azure/azure-iot-sdk-c/releases/latest) címkenevét.

1. Klónozza [az Azure IoT C SDK-t](https://github.com/Azure/azure-iot-sdk-c) mindkét virtuális gépre.  Használja az előző lépésben talált címkét a paraméter `-b` értékeként:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Mindkét virtuális géphez hozzon létre egy új **cmake** mappát az adattárban, és váltsa át erre a mappára.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Mindkét virtuális gép esetében futtassa a következő parancsot, amely a fejlesztői ügyfélplatformra szabott SDK-verziót építi ki. 

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


## <a name="derive-unique-device-keys"></a>Egyedi eszközkulcsok származtatása

Ha szimmetrikus kulcsú igazolást használ a csoportos regisztrációkhoz, nem használja közvetlenül a regisztrációs csoport kulcsait. Ehelyett létre kell hoznia egy egyedi származtatott kulcsot minden eszközhöz, és szimmetrikus kulcsokkal kell megemlítenie a Csoportos [regisztrációkban.](concepts-symmetric-key-attestation.md#group-enrollments)

Az eszközkulcs létrehozásához használja a csoport főkulcsát az eszköz egyedi regisztrációs [azonosítójának HMAC-SHA256](https://wikipedia.org/wiki/HMAC) kiszámításához, és alakítsa át az eredményt Base64 formátumra.

Ne foglalja bele a csoport főkulcsát az eszközkódba.

A Bash-rendszerhéj példáját használva hozzon létre egy származtatott eszközkulcsot minden eszközhöz **az openssl használatával.**

- Cserélje le a **KEY** értékét **a** regisztrációhoz korábban feljegyzett elsődleges kulcsra.

- Cserélje le **a** REG_ID értékét a saját egyedi regisztrációs azonosítójára minden eszközhöz. A két azonosító meghatározásához használjon kisbetűs alfanumerikus karaktereket és kötőjelet ('-').

Példa eszközkulcs generációra *a contoso-simdevice-east eszközhöz:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Példa eszközkulcs generációra a *contoso-simdevice-west eszközhöz:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


A bérlői eszközök mind a származtatott eszközkulcsukat és egyedi regisztrációs azonosítójukat használják a szimmetrikus kulcsú igazolás végrehajtásához a regisztrációs csoporttal a bérlői IoT Hub-központokra való kiépítés során.




## <a name="simulate-the-devices-from-each-region"></a>Az eszközök szimulálása az egyes régiókból


Ebben a szakaszban frissíteni fog egy kiépítési mintát az Azure IoT C SDK-ban mindkét regionális virtuális géphez. 

A mintakód egy olyan eszköz rendszerindítási sorozatát szimulálja, amely elküldi a kiépítési kérelmet a Device Provisioning Service-példánynak. A rendszerindítási sorozat az eszköz felismerését és a késés alapján legközelebbi IoT Hubhoz való hozzárendelését fogja eredményezheti.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Nyissa **meg az ~/azure-iot-sdk-c/provisioning \_ client/samples/prov \_ dev client \_ \_ sample/prov \_ dev client \_ \_ sample.c** szolgáltatásokat mindkét virtuális gép szerkesztéséhez.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg arról, hogy a változó értéke a következő, hogy megfeleljen a regisztrációs csoport igazolási `hsm_type` `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` módszerének. 

    Mentse mindkét virtuális gép fájljainak módosításait.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Mindkét virtuális gép esetében keresse meg a hívását a `prov_dev_set_symmetric_key_info()` **prov \_ dev client \_ \_ sample.c** fájlban, amely megjegyzésként van megírva.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    A függvényhívások mellőzőjelének visszahívása után cserélje le a helyőrző értékeket (a szögletes zárójeleket is beleértve) az egyes eszközök egyedi regisztrációs azonosítóira és származtatott eszközkulcsaira. Az alább látható kulcsok csak példaként szolgálnak. Használja a korábban létrehozott kulcsokat.

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

1. Mindkét virtuális gép esetében keresse meg az alább látható mintamappát, és készítse el a mintát.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Ha a build sikeresen lefutott, futtassa mindkét virtuális **\_ \_ \_sample.exe** fejlesztői ügyfélprogramot, hogy szimuláljon egy bérlőeszközt az egyes régiókból. Figyelje meg, hogy minden eszköz a szimulált eszköz régióihoz legközelebbi bérlői IoT Hubhoz van lefoglalva.

    Futtassa a szimulációt:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Példakimenet az USA keleti régiója virtuális gépről:

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

    Példakimenet az USA nyugati régiója virtuális gépről:
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

Ha azt tervezi, hogy folytatja a munkát a cikkben létrehozott erőforrásokkal, elhagyhatja őket. Ha nem tervezi tovább használni az erőforrást, a következő lépésekkel törölheti a cikk által létrehozott összes erőforrást a szükségtelen költségek elkerülése érdekében.

Az itt található lépések feltételezik, hogy a cikkben található összes erőforrást a **contoso-us-resource-group** nevű erőforráscsoportban utasítás szerint hozta létre.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport név alapján való törléséhez:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. A Szűrés **név alapján...** szövegmezőbe írja be az erőforrásokat tartalmazó erőforráscsoport nevét(contoso-us-resource-group).  

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

* További információ az újraosztásról:

> [!div class="nextstepaction"]
> [IoT Hub eszközök újraosztásának fogalmai](concepts-device-reprovision.md)

* További információ a megszüntetésről:
> [!div class="nextstepaction"]
> [Korábban automatikusan kiépített eszközök megszüntetése](how-to-unprovision-devices.md)