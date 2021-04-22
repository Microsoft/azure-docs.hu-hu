---
title: Csatlakoztatottgyár-megoldás – gyakori kérdések – Azure | Microsoft Docs
description: Ez a cikk a csatlakoztatottgyár-megoldásgyorsítóval kapcsolatos gyakori kérdésekre ad választ. A GitHub-adattárra mutató hivatkozásokat tartalmaz.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 038403743caf13087655066f4acbec4dcee598c7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874206"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Gyakori kérdések a csatlakoztatottgyár-gyorsítóval kapcsolatban

Lásd még az [](iot-accelerators-faq.md) IoT-megoldásgyorsítókra vonatkozó általános gyakori kérdéseket.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Hol találom a megoldásgyorsító forráskódját?

A forráskód a következő GitHub-adattárban van tárolva:

* [Csatlakoztatottgyár-megoldásgyorsító](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Mi az az OPC UA?

A 2008-ban kiadott OPC unified Architecture (UA) egy platformfüggetlen, szolgáltatásorientált együttműködési szabvány. Az OPC UA-t számos ipari rendszer és eszköz, például iparági PC-k, PLC-k és érzékelők használják. Az OPC UA a klasszikus OPC-specifikációk funkcióit integrálja egy beépített biztonsági keretrendszerbe. Ez az OPC Foundation által vezérelt szabvány. Az [OPC Foundation](https://opcfoundation.org/) egy nonprofit szervezet, több mint 440 taggal. A szervezet célja az OPC-specifikációk használata a többszállítós, többplatformos, biztonságos és megbízható együttműködés elősegítéséhez a következőn keresztül:

* Infrastruktúra
* Specifikációk
* Technológia
* Folyamatok

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Miért választotta a Microsoft az OPC UA-t a csatlakoztatottgyár-megoldásgyorsítóhoz?

A Microsoft azért az OPC UA-t választotta, mert nyílt, nem jogvédett, platformfüggetlen, iparág által elismert és bevált szabvány. Ez az Industrie 4.0 (RAMI4.0) referenciaarchitektúra-megoldások egyik követelménye, amely biztosítja a gyártási folyamatok és berendezések széles skálája közötti együttműködést. A Microsoft igényét látja az ügyfeleitől az Industrie 4.0-s megoldások kiépítése felé. Az OPC UA támogatása segít csökkenteni az ügyfelek céljaik elérésének akadályát, és azonnali üzleti értéket biztosít számukra.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hogyan nyilvános IP-címet a szimulációs virtuális géphez?

Az IP-cím hozzáadására két lehetőség közül választhat:

* Használja a `Simulation/Factory/Add-SimulationPublicIp.ps1` PowerShell-szkriptet [az adattárban.](https://github.com/Azure/azure-iot-connected-factory) Adja meg az üzemelő példány nevét paraméterként. Helyi központi telepítés esetén használja a következőt: `<your username>ConnFactoryLocal` . A szkript kiírja a virtuális gép IP-címét.

* A Azure Portal keresse meg az üzemelő példány erőforráscsoportját. A helyi üzemelő példány kivételével az erőforráscsoport neve megoldás- vagy üzembe helyezési név. A buildszk szkriptet használó helyi üzemelő példányok esetén az erőforráscsoport neve `<your username>ConnFactoryLocal` . Most adjon hozzá egy új **nyilvános IP-cím erőforrást** az erőforráscsoporthoz.

> [!NOTE]
> Mindkét esetben győződjön meg arról, hogy a legújabb javításokat telepíti az Ubuntu webhelyén található [utasításokat követve.](https://wiki.ubuntu.com/Security/Upgrades) Tartsa naprakészen a telepítést, amíg a virtuális gép nyilvános IP-címen keresztül elérhető.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hogyan a nyilvános IP-címet a szimulációs virtuális gépről?

Az IP-cím eltávolítására két lehetőség közül választhat:

* Használja az adattár Simulation/Factory/Remove-SimulationPublicIp.ps1 [PowerShell-szkriptet.](https://github.com/Azure/azure-iot-connected-factory) Adja meg az üzemelő példány nevét paraméterként. Helyi központi telepítés esetén használja a következőt: `<your username>ConnFactoryLocal` . A szkript kiírja a virtuális gép IP-címét.

* A Azure Portal keresse meg az üzemelő példány erőforráscsoportját. A helyi üzemelő példány kivételével az erőforráscsoport neve megoldás- vagy üzembe helyezési név. A buildszk szkriptet használó helyi üzemelő példányok esetén az erőforráscsoport neve `<your username>ConnFactoryLocal` . Most távolítsa el **a Nyilvános IP-cím erőforrást** az erőforráscsoportból.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hogyan a szimulációs virtuális gépre?

A szimulációs virtuális gépre való bejelentkezés csak akkor támogatott, ha a megoldást a tárházban található PowerShell-szkript `build.ps1` [használatával telepítette.](https://github.com/Azure/azure-iot-connected-factory)

Ha a megoldást a virtuális gépről www.azureiotsolutions.com, nem tud bejelentkezni a virtuális gépre. Nem tud bejelentkezni, mert a jelszó véletlenszerűen jön létre, és nem állítható vissza.

1. Adjon hozzá egy nyilvános IP-címet a virtuális géphez. [Lásd Hogyan nyilvános IP-cím hozzáadása a szimulációs virtuális géphez?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Hozzon létre egy SSH-munkamenetet a virtuális géphez a virtuális gép IP-címével.
1. A használni kívánt felhasználónév a következő: `docker` .
1. A használni szükséges jelszó az üzembe helyezéshez használt verziótól függ:
    * A 2017. június 1. build.ps1 előtt a jelszó a következő: `Passw0rd` .
    * A 2017. június 1build.ps1 utáni szkript használatával üzembe helyezett megoldások esetén a jelszót a fájlban `<name of your deployment>.config.user` találja. A jelszót a **VmAdminPassword beállítás tárolja.** A jelszó véletlenszerűen jön létre az üzembe helyezéskor, kivéve, ha a szkriptparaméterrel `build.ps1` adja meg `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hogyan le és indítja el az összes Docker-folyamatot a szimulációs virtuális gépen?

1. Jelentkezzen be a szimulációs virtuális gépre. Lásd [Hogyan bejelentkezést a szimulációs virtuális gépre?](#how-do-i-sign-in-to-the-simulation-vm)
1. Az aktív tárolók ellenőrzéshez futtassa a következőt: `docker ps` .
1. Az összes szimulációs tároló leállításhoz futtassa a következőt: `./stopsimulation` .
1. Az összes szimulációs tároló elindítani:
    * Exportálja a következő nevű **rendszerhéjváltozót: IOTHUB_CONNECTIONSTRING.** Használja az **IotHubOwnerConnectionString** beállítás értékét a `<name of your deployment>.config.user` fájlban. Például:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Futtassa az `./startsimulation` parancsot.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hogyan frissíteni a szimulációt a virtuális gépen?

Ha bármilyen módosítást végrehajtott a szimuláción, használhatja a PowerShell-szkriptet az `build.ps1` [adattárban](https://github.com/Azure/azure-iot-connected-factory) az `updatedimulation` paranccsal. Ez a szkript felépíti az összes szimulációs összetevőt, leállítja a szimulációt a virtuális gépen, feltölti, telepíti és elindítja őket.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hogyan a megoldás által használt IoT Hub kapcsolati sztringjét?

Ha a megoldást a tárházban található szkript használatával telepítette, a kapcsolati sztring az `build.ps1` **IotHubOwnerConnectionString** értéke a [](https://github.com/Azure/azure-iot-connected-factory) `<name of your deployment>.config.user` fájlban.

A kapcsolati sztringet a következő Azure Portal. Az IoT Hub erőforráscsoportban keresse meg a kapcsolati sztring beállításait.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Melyik IoT Hub használ a Csatlakoztatott gyár szimulációja?

A szimuláció ön regisztrálja a következő eszközöket:

* proxy.proxy.corp.contoso
* proxy.fok fokváros.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.electronics0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.corp.corp.contoso
* publisher.capepoint.corp.contoso
* publisher.mumbai.corp.contoso
* közzétevő.0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

A [DeviceExplorer vagy](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) az [Azure CLI IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension) eszközével ellenőrizheti, hogy mely eszközök vannak regisztrálva a megoldás által használt IoT Hubban. Az eszközkezelőhöz az üzemelő példányban az IoT Hub kapcsolati sztringjére van szükség. Az Azure CLI-hez használható IoT-bővítményhez szüksége lesz a IoT Hub nevére.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hogyan kaphatok naplóadatokat a szimulációs összetevőkből?

A szimuláció összes összetevője naplóinformációkat ad a naplófájlok számára. Ezek a fájlok a mappában találhatók a virtuális `home/docker/Logs` gépen. A naplók lekéréshez használhatja a PowerShell-szkriptet a `Simulation/Factory/Get-SimulationLogs.ps1` [adattárban.](https://github.com/Azure/azure-iot-connected-factory)

A szkriptnek be kell jelentkeznie a virtuális gépre. Előfordulhat, hogy meg kell adnia a bejelentkezéshez szükséges hitelesítő adatokat. A [hitelesítő Hogyan a szimulációs virtuális gépre való](#how-do-i-sign-in-to-the-simulation-vm) bejelentkezést?

A szkript hozzáad vagy eltávolít egy nyilvános IP-címet a virtuális géphez, ha még nem rendelkezik ip-címmel, és eltávolítja. A szkript az összes naplófájlt egy archívumba helyezi, és letölti az archívumot a fejlesztői munkaállomásra.

Másik lehetőségként jelentkezzen be a virtuális gépre SSH-n keresztül, és vizsgálja meg a naplófájlokat futásidőben.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hogyan tudom ellenőrizni, hogy a szimuláció adatokat küld-e a felhőbe?

Az [Azure IoT Explorer vagy](https://github.com/Azure/azure-iot-explorer) az [Azure IoT CLI-bővítmény monitor-események](/cli/azure/iot/hub#az_iot_hub_monitor_events) parancsával megvizsgálhatja az egyes eszközökről a IoT Hub küldött adatokat. Ezeknek az eszközöknek a használatához az üzemelő példányban szüksége lesz az IoT Hub kapcsolati sztringjére. Tekintse Hogyan a megoldás által használt IoT Hub kapcsolati [sztringjét.](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Vizsgálja meg az egyik közzétevő eszköz által küldött adatokat:

* publisher.corp.corp.contoso
* publisher.capepoint.corp.contoso
* publisher.mumbai.corp.contoso
* közzétevő.0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Ha nem lát adatokat a IoT Hub, akkor a szimulációval van probléma. Első elemzési lépésként elemeznie kell a szimulációs összetevők naplófájlját. Lásd: [Hogyan kaphatok naplóadatokat a szimulációs összetevőkből?](#how-can-i-get-log-data-from-the-simulation-components) Ezután próbálja meg leállítani és elindítani a szimulációt, és ha még nem küldött adatokat, frissítse teljesen a szimulációt. Lásd [Hogyan a szimuláció frissítését a virtuális gépen?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hogyan interaktív térképet a csatlakoztatott gyár megoldásban?

Az interaktív térkép csatlakoztatottgyár-megoldásban való engedélyezéséhez egy új fiókkal kell Azure Maps lennie.

Az üzembe helyezés [www.azureiotsolutions.com](https://www.azureiotsolutions.com)az üzembe helyezési folyamat hozzáad egy Azure Maps-fiókot a megoldásgyorsító szolgáltatásokat tartalmazó erőforráscsoporthoz.

Amikor a Connected Factory GitHub-adattárban található szkripttel telepíti az üzembe helyezést, állítsa a környezeti változót a build ablakában a Azure Maps `build.ps1` `$env:MapApiQueryKey` [kulcsára.](../azure-maps/how-to-manage-account-keys.md) Az interaktív térkép ezután automatikusan engedélyezve lesz.

Az üzembe helyezés után hozzáadhat egy Azure Maps-fiókkulcsot a megoldásgyorsítóhoz. Lépjen a Azure Portal, és nyissa meg App Service csatlakoztatott gyár üzemelő példányának erőforrását. Lépjen az **Alkalmazásbeállítások lapra,** ahol megtalálja az **Alkalmazásbeállítások szakaszt.** Állítsa a **MapApiQueryKey** kulcsot a Azure Maps [kulcsra.](../azure-maps/how-to-manage-account-keys.md) Mentse a beállításokat, majd lépjen az **Áttekintés lapra,** és indítsa újra App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Hogyan fiókot Azure Maps létrehozni?

Lásd: [A fiók és a Azure Maps kezelése.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-to-obtain-your-azure-maps-account-key"></a>A fiókkulcs Azure Maps beszerzése

Lásd: [A fiók és a Azure Maps kezelése.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hogyan engedélyezhető az interaktív térkép helyi hibakeresés közben?

Ha helyi hibakeresés közben szeretné engedélyezni az interaktív térképet, állítsa a fájlban és az üzemelő példány gyökerében található beállítás értékét a korábban kimásott `MapApiQueryKey` `local.user.config` `<yourdeploymentname>.user.config` **QueryKey** értékre.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hogyan másik képet használ az irányítópultom kezdőlapján?

Az irányítópult kezdőlapján látható statikus kép cseréjéhez cserélje le a `WebApp\Content\img\world.jpg` képet. Ezután építse újra és üzembe a webalkalmazást.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hogyan OPC UA-eszközöket használ csatlakoztatott gyárral?

Telemetriai adatok küldése nem OPC UA-eszközökről a csatlakoztatott gyárba:

1. [Konfigurálja az új állomást a fájl Csatlakoztatott gyár](iot-accelerators-connected-factory-configure.md) topológiájában. `ContosoTopologyDescription.json`

1. A telemetriai adatokat csatlakoztatott gyárral kompatibilis JSON formátumban kell betölteni:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. A `<timestamp>` formátuma: `2017-12-08T19:24:51.886753Z`

1. Indítsa újra a csatlakoztatott gyár App Service.

### <a name="next-steps"></a>Következő lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [Csatlakoztatottgyár-megoldásgyorsító üzembe helyezése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](../iot-fundamentals/iot-security-ground-up.md)