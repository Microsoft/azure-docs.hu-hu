---
title: Üzemi készültség és ajánlott eljárások – Azure
description: Ez a cikk útmutatást nyújt az éles környezetekben IoT Edge modul élő videós elemzésének konfigurálásához és üzembe helyezéséhez.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56982d84b7ffac718072683076657d56a2691d6c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97400556"
---
# <a name="production-readiness-and-best-practices"></a>Termelési készenlét és ajánlott eljárások

Ez a cikk útmutatást nyújt az éles környezetekben IoT Edge modul élő videós elemzésének konfigurálásához és üzembe helyezéséhez. Tekintse át a [felkészülés a IoT Edge megoldás éles környezetben való üzembe helyezését](../../iot-edge/production-checklist.md) ismertető cikket is a IoT Edge megoldás előkészítéséhez. 

> [!NOTE]
> A biztonsággal kapcsolatos szempontokért tekintse meg a szervezet IT-részlegeit.

## <a name="running-the-module-as-a-local-user"></a>A modul futtatása helyi felhasználóként

Ha IoT Edge modulon helyezi üzembe az élő videó-elemzést egy peremhálózati eszközre, alapértelmezés szerint emelt szintű jogosultságokkal fut. Ha ezt teszi, a modul () naplóinak ellenőrzésekor a következő jelenik meg `sudo iotedge logs {name-of-module}` :

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

Az alábbi szakaszban megtudhatja, hogyan kezelheti a fenti figyelmeztetést.

### <a name="creating-and-using-a-local-user-account"></a>Helyi felhasználói fiók létrehozása és használata

Az élő videó-elemzést IoT Edge modulban is futtathatja éles környezetben, ha a lehető legkevesebb jogosultsággal rendelkező fiókot használja. A következő parancsok például megmutatják, hogyan hozhat létre helyi felhasználói fiókot Linux rendszerű virtuális gépen:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

Ezután az üzembe helyezési jegyzékben beállíthatja a LOCAL_USER_ID és LOCAL_GROUP_ID környezeti változókat a nem gyökérszintű felhasználók és csoportok számára:

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Engedélyek megadása az eszköz tárterületéhez

Az élő videó Analytics IoT Edge moduljában a következő esetekben képes fájlokat írni a helyi fájlrendszerbe:

* A modul Twin tulajdonságának használatával [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties) , ahol meg kell adnia egy könyvtárat a helyi fájlrendszerben a konfigurációs adatai tárolásához.
* Ha egy Media Graph segítségével szeretne videót felvenni a felhőbe, a modulnak egy könyvtárat kell használnia a peremhálózati eszközön a gyorsítótárban (további információt a [folyamatos videofelvételek](continuous-video-recording-concept.md) című cikkben talál).
* [Rögzítés helyi fájlba](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources), ahol meg kell adnia a rögzített videó elérési útját.

Ha a fentiek bármelyikét szeretné használni, győződjön meg arról, hogy a fenti felhasználói fiók rendelkezik hozzáféréssel a megfelelő címtárhoz. Vegyük például a applicationDataDirectory. Létrehozhat egy könyvtárat a peremhálózati eszközön, és csatolhatja az eszköz tárterületét a modul Storage-hoz. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

Ezután a központi telepítési jegyzékfájlban az Edge-modul létrehozási beállításaiban hozzáadhat egy kötést, amely a fenti könyvtárat ("var/local/Mediaservices/") társítja a modul egyik könyvtárához (például "/var/lib/azuremediaservices/"). És az utóbbi könyvtárat fogja használni a applicationDataDirectory értékeként.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

Ha a rövid útmutatóhoz és az oktatóanyagokhoz, például a [folyamatos videofelvételek](continuous-video-recording-tutorial.md)megjelenítéséhez tekinti meg a minta adathordozó-diagramokat, vegye figyelembe, hogy a Media cache Directory (localMediaCachePath) a applicationDataDirectory alatti alkönyvtárat használja. Ez az ajánlott módszer, mivel a gyorsítótár átmeneti adattartalomot tartalmaz.

### <a name="naming-video-assets-or-files"></a>Videóobjektumok vagy -fájlok elnevezése

Az adathordozó-diagramok lehetővé teszik az eszközök Felhőbeli vagy MP4-fájlokban való létrehozását az Edge-ben. A média eszközei a [folyamatos videorögzítés](continuous-video-recording-tutorial.md) vagy az [eseményvezérelt videofelvételek](event-based-video-recording-tutorial.md)segítségével hozhatók létre. Habár ezek az eszközök és fájlok megtekinthetők a kívánt névvel, a folyamatos videofelvétel-alapú adathordozó javasolt elnevezési struktúrája a következő: " &lt; anytext &gt; -$ {System. GraphTopologyName}-$ {System. GraphInstanceName}".   

A helyettesítési mintát a $ Sign határozza meg, amelyet kapcsos zárójelek követnek: **$ {variableName}**.  

Például a következő módon állíthatja be a assetNamePattern az eszköz fogadóján:

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

Az esemény-alapú videók rögzítésére létrehozott eszközök esetében az ajánlott elnevezési minta a következő: " &lt; anytext &gt; -$ {System. datetime}". A rendszerváltozó biztosítja, hogy az eszközök ne legyenek felülírva, ha az események egy időben történnek. Például a következő módon állíthatja be a assetNamePattern az eszköz fogadóján:

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

Ha ugyanannak a gráfnak több példányát futtatja, a diagram topológiájának nevét és a példánynév megkülönböztetni a különbséget. Például a következő módon állíthatja be a assetNamePattern az eszköz fogadóján:

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

Az esemény-alapú videók rögzítésére létrehozott MP4 videoklipek esetében az ajánlott elnevezési mintában a DateTime értéknek kell szerepelnie, az azonos gráf több példánya pedig a GraphTopologyName és a GraphInstanceName rendszerváltozókat használja. Például a következő módon állíthatja be a filePathPattern a file mosogatón: 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

Vagy 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```
>[!NOTE]
> A fenti példában a **fileSinkOutputName** változó a Graph-topológiában definiált minta változó nevet adja meg. Ez **nem** rendszerváltozó. 

#### <a name="system-variables"></a>Rendszerváltozók
Egyes rendszer által definiált változók a következők:

|Rendszerváltozó|Leírás|Példa|
|-----------|-----------|-----------|
|System. DateTime|UTC dátum és idő ISO8601-fájlnak megfelelő formátumban (alapszintű képviseleti YYYYMMDDThhmmss).|20200222T173200Z|
|System. PreciseDateTime|UTC dátum és idő ISO8601 fájlnak megfelelő formátumban, ezredmásodpercekkel (alapszintű YYYYMMDDThhmmss. ÉER).|20200222T 173200.123 Z|
|System. GraphTopologyName|A végrehajtó gráf topológiájának felhasználó által megadott neve.|IngestAndRecord|
|System. GraphInstanceName|A végrehajtó Graph-példány felhasználó által megadott neve.|camera001|

>[!TIP]
> A System. PreciseDateTime nem használható, ha az eszközök elnevezése a " a névben
### <a name="keeping-your-vm-clean"></a>A virtuális gép tisztán tartása

A peremhálózati eszközként használt linuxos virtuális gép nem válaszol, ha nem rendszeres időközönként kezelik. Fontos, hogy a gyorsítótárak tisztán maradjanak, távolítsuk el a szükségtelen csomagokat, és távolítsa el a nem használt tárolókat a virtuális gépről. Ehhez az ajánlott parancsok egy készletét használhatja, amelyet a peremhálózati virtuális gépen használhat.

1. `sudo apt-get clean`

    Az apt-get clean parancs törli a beolvasott csomagok helyi tárházát, amely a/var/cache. marad. A megtisztított könyvtárak a/var/cache/apt/Archives/és a/var/cache/apt/Archives/Partial/. Az egyetlen fájl, amely elhagyja a/var/cache/apt/Archives, a zárolási fájl és a részleges alkönyvtár. Az apt-get clean parancs általában a lemezterület igény szerinti törlésére használatos, általában az ütemezett karbantartás részeként. További információkért lásd: az [apt-get tisztítása](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).
1. `sudo apt-get autoclean`

    Az apt-get AutoClean beállítás, például az apt-get Clean, törli a beolvasott csomagok helyi tárházát, de csak azokat a fájlokat távolítja el, amelyek már nem tölthetők le, és nem hasznosak. Segít megőrizni a gyorsítótárat a túl nagy mennyiségű növekedéssel.
1. `sudo apt-get autoremove1`

    Az automatikus eltávolítási lehetőség eltávolítja azokat a csomagokat, amelyeket a rendszer automatikusan telepített, mert más csomagok is szükségesek, de a többi csomag eltávolításával már nincs rájuk szükség.
1. `sudo docker image ls` – A peremhálózati rendszeren lévő Docker-rendszerképek listáját jeleníti meg
1. `sudo docker system prune`

    A Docker konzervatív módszert alkalmaz a fel nem használt objektumok (gyakran "Garbage Collection"), például képek, tárolók, kötetek és hálózatok tisztítására: ezeket az objektumokat általában nem távolítja el a rendszer, hacsak nem kifejezetten kéri a Docker használatát. Ennek hatására a Docker további lemezterületet is igénybe vehet. Minden objektumtípus esetében a Docker egy aszalt szilva parancsot biztosít. Emellett a Docker System aszalt szilva szolgáltatással egyszerre több típusú objektumot is kitakaríthat. További információkért lásd a nem [használt Docker-objektumok aszalt szilvát](https://docs.docker.com/config/pruning/)ismertető témakört.
1. `sudo docker rmi REPOSITORY:TAG`

    Ahogy a frissítések történnek a peremhálózati modulban, a Docker még mindig tartalmazhatja a peremhálózati modul régebbi verzióit. Ebben az esetben célszerű a Docker RMI paranccsal eltávolítani a lemezkép verziója címkével azonosított egyes lemezképeket.

## <a name="next-steps"></a>Következő lépések

[Gyors útmutató: első lépések – élő videó-elemzés IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
