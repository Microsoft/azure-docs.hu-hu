---
title: Meglévő végrehajtható fájl becsomagolása az Azure Service Fabricba
description: További információ a meglévő alkalmazások vendég végrehajtható fájlként való csomagolásáról, így Service Fabric-fürtön is üzembe helyezhető.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 8b808d092001196a4d2150e44d508e031db95554
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017746"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Meglévő végrehajtható fájl üzembe helyezése Service Fabric
Az Azure Service Fabric szolgáltatásban bármilyen típusú kódot futtathat, például Node.js, Java vagy C++. A Service Fabric vendég végrehajtható fájlokként hivatkozik az ilyen típusú szolgáltatásokra.

A vendég végrehajtható fájlokat a Service Fabric, például az állapot nélküli szolgáltatások kezelik. Ennek eredményeképpen a fürt csomópontjain vannak elhelyezve a rendelkezésre állás és az egyéb mérőszámok alapján. Ez a cikk azt ismerteti, hogyan lehet a Visual Studióval vagy egy parancssori segédprogrammal becsomagolni és üzembe helyezni egy vendég végrehajtható fájlt egy Service Fabric-fürtön.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>A vendég végrehajtható fájl futtatásának előnyei Service Fabric
A Service Fabric-fürtben több előnye van a vendég végrehajtható fájl futtatására:

* Magas rendelkezésre állás. A Service Fabricon futó alkalmazások nagyon elérhetővé válnak. Service Fabric biztosítja, hogy egy alkalmazás példányai futnak.
* Állapotfigyelés. Service Fabric állapot-figyelés észleli, ha egy alkalmazás fut, és diagnosztikai adatokat biztosít, ha hiba történt.   
* Az alkalmazások életciklusának kezelése. A leállás nélküli verziófrissítések mellett a Service Fabric automatikus visszaállítást biztosít az előző verzióra, ha a frissítés során helytelen állapotú esemény jelent meg.    
* Sűrűségű. Több alkalmazást is futtathat egy fürtben, ami szükségtelenné teszi az egyes alkalmazások saját hardveren való futtatásának szükségességét.
* Felderíthetőség: a REST használatával meghívhatja a Service Fabric Naming Service-t, hogy a fürt más szolgáltatásait is megkeresse. 

## <a name="samples"></a>Példák
* [Minta a vendég végrehajtható fájlok csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két vendég végrehajtható fájlról (C# és NodeJS) az elnevezési szolgáltatáson keresztül a REST használatával](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Az alkalmazások és szolgáltatások jegyzékfájljának áttekintése
A vendég végrehajtható fájlok üzembe helyezésének részeként érdemes megismerni a Service Fabric csomagolási és telepítési modellt az [Application modelben](service-fabric-application-model.md)leírtak szerint. A Service Fabric csomagolási modell két XML-fájlra támaszkodik: az alkalmazásra és a szolgáltatásokra vonatkozó jegyzékfájlokra. A ApplicationManifest.xml és ServiceManifest.xml fájlok sémájának definíciója telepítve van a Service Fabric SDK-ba a *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Alkalmazás jegyzékfájlja** Az alkalmazás jegyzékfájlja az alkalmazás leírására szolgál. Felsorolja az azt alkotó szolgáltatásokat, valamint azokat a paramétereket, amelyek segítségével meghatározható egy vagy több szolgáltatás üzembe helyezése, például a példányok száma.

  Service Fabric az alkalmazás az üzembe helyezés és a frissítés egysége. Egy alkalmazás frissíthető egyetlen egységként, ahol a lehetséges hibák és a lehetséges visszaállítások kezelhetők. Service Fabric garantálja, hogy a frissítési folyamat sikeres, vagy ha a frissítés sikertelen, az alkalmazás ismeretlen vagy instabil állapotban marad.
* **Szolgáltatás jegyzékfájlja** A szolgáltatás jegyzékfájlja a szolgáltatás összetevőit ismerteti. Ide tartoznak az olyan adatmennyiségek, mint a név és a szolgáltatás típusa, valamint a kód és a konfiguráció. A szolgáltatás jegyzékfájlja Emellett néhány további paramétert is tartalmaz, amelyek segítségével konfigurálhatja a szolgáltatást az üzembe helyezés után.

## <a name="application-package-file-structure"></a>Alkalmazáscsomag-fájl szerkezete
Egy alkalmazás Service Fabricba való telepítéséhez az alkalmazásnak egy előre meghatározott címtár-struktúrát kell követnie. A következő példa erre a szerkezetre mutat.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

A ApplicationPackageRoot tartalmazza az alkalmazást definiáló ApplicationManifest.xml fájlt. Az alkalmazásban található összes szolgáltatás alkönyvtára a szolgáltatás által igényelt összes összetevőt tartalmazza. Ezek az alkönyvtárak a ServiceManifest.xml és jellemzően a következők:

* *Kód*. Ez a könyvtár tartalmazza a szolgáltatási kódot.
* *Konfiguráció*. Ez a könyvtár tartalmaz egy Settings.xml fájlt (és szükség esetén más fájlokat is), amelyekkel a szolgáltatás futásidőben elérheti az adott konfigurációs beállításokat.
* *Az adathalmazt*. Ez egy további könyvtár a szolgáltatás által igényelt további helyi információk tárolására. Csak az időszakos adattárolást kell használni. Service Fabric nem másolja vagy replikálja az adatkönyvtár módosításait, ha a szolgáltatást át kell helyezni (például a feladatátvétel során).

> [!NOTE]
> Ha nincs szüksége rájuk, nem kell létrehoznia a `config` és a `data` címtárakat.
>
>

## <a name="next-steps"></a>Következő lépések
A kapcsolódó információkkal és feladatokkal kapcsolatban tekintse meg a következő cikkeket.
* [Futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-existing-app.md)
* [Több futtatható vendégalkalmazás üzembe helyezése](./service-fabric-deploy-existing-app.md)
* [Az első vendég végrehajtható alkalmazás létrehozása a Visual Studio használatával](quickstart-guest-app.md)
* [Minta egy vendég végrehajtható fájl csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), beleértve a csomagoló eszköz előzetes kiadására mutató hivatkozást is.
* [Minta két vendég végrehajtható fájlról (C# és NodeJS) az elnevezési szolgáltatáson keresztül a REST használatával](https://github.com/Azure-Samples/service-fabric-containers)
