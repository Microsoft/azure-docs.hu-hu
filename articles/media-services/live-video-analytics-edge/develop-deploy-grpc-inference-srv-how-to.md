---
title: GRPC-alapú következtetési kiszolgáló fejlesztése és üzembe helyezése – Azure
description: Ez a cikk útmutatást nyújt egy gRPC következtetési kiszolgáló fejlesztéséhez és üzembe helyezéséhez.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: dbf46a26626a4143d76385968d092c4f238729da
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034860"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>Útmutató: gRPC-következtetési kiszolgáló fejlesztése és üzembe helyezése

## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan teheti elérhetővé a választott AI-modelleket egy gRPC következtetési kiszolgálón belül, hogy az integrálható legyen az élő video Analytics (LVA) használatával a Graph bővítménnyel. 

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás

* [Media Graph-bővítmények](media-graph-extension-concept.md)
* [gRPC-bővítmény-protokoll](grpc-extension-protocol.md)
* [Metaadat-séma következtetése](inference-metadata-schema.md)
* [A gRPC bemutatása](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3 – nyelvi útmutató](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Előfeltételek

* A [támogatott Linux operációs rendszerek](../../iot-edge/support.md#operating-systems) vagy Windows rendszerű gépek egyikét futtató x86-64 vagy ARM64-eszköz.
* [Telepítse a Docker](https://docs.docker.com/desktop/#download-and-install) -t a gépre.
* Telepítse a [IoT Edge futtatókörnyezetet](../../iot-edge/how-to-install-iot-edge.md?tabs=linux).

## <a name="grpc-implementation-steps"></a>gRPC-implementáció lépései

A következő lépésekkel hozhat létre egy gRPC következtetési kiszolgálót, és hogyan implementálhatja azt bővítményként az élő videó Analytics használatával:

### <a name="setup"></a>Beállítás:

Hajtsa végre a szükséges lépéseket, hogy az [élő video Analytics-modul üzembe helyezése és használata IoT Edge eszközön történjen](deploy-iot-edge-device.md).

### <a name="high-level-implementation-steps"></a>Magas szintű megvalósítás lépései:

1. Válasszon a gRPC által támogatott nyelvek közül: C#, C++, Dart, go, Java, node, Objective-C, PHP, Python, Ruby.
1. Olyan gRPC-kiszolgáló implementálása, amely [az proto3-fájlok](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)használatával kommunikál az élő videók elemzésével.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="gRPC-kiszolgáló, amely a proto3-fájlok használatával kommunikál az élő videók elemzésével":::

    A szolgáltatáson belül:
    1. A kiszolgáló és az ügyfél közötti munkamenet-leíró üzenet cseréjének kezelése.
    1. Kezelheti az [adathordozó-mintákat](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) , és visszaküldheti az eredményeket.

        1. Hívja meg a következtetést, amely egy betanított modellt használ a beérkező üzenetek megjelenítéséhez.
        1. Fogadja az eredményeket a motorból, a csomagokat adathordozó-mintaként csomagolja ki, és visszaküldi az élő video Analyticsnek a [következtetést. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) fájl használatával.

            Azt is megteheti, hogy bármely média-átalakítási függvényt meghív a Media-mintára.
1. Telepítse a gRPC-kiszolgáló implementációját. Ezt kétféleképpen teheti meg:

    1. Üzembe helyezés IoT-modulként, élő videó elemzési modullal
    1. Telepítsen IoT-modulként a hálózat számára elérhető (helyszíni vagy Felhőbeli) csomópontra, amely képes az adatcserére az élő videó elemzési modullal.
1. Konfiguráljon egy élő video Analytics gráf-topológiát az élő videó elemzési modullal, és mutasson rá a gRPC-kiszolgálóra.

### <a name="recommendation"></a>Ajánlás

Ha ugyanazon a csomóponton collocating, a rendszer a közös memóriát is használhatja a legjobb teljesítmény érdekében. Ehhez a programozási nyelv/környezet által kiváltott linuxos megosztott memória-képességeket kell használnia.

1. Nyissa meg a Linux megosztott memória leíróját.
1. Egy keret fogadásakor a rendszer a megosztott memórián belüli eltoláshoz fér hozzá.
1. Nyugtázza a keret feldolgozásának befejezését, hogy a memóriája visszaigényelhető legyen az élő videó-elemzéssel.

## <a name="create-a-grpc-inference-server"></a>GRPC-következtetési kiszolgáló létrehozása

Most létrehoz egy IoT Edge modult (külső AI-t), amely a videó-képkockákat a [protopuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -üzenetek használatával, a megosztott memórián keresztül fogadja el, a kereteket "sötét" vagy "Light" értékre minősíti, és visszaküldi az eredményeket IoT hub az élő videó elemzésében a [következtetési metaadatok sémájának](inference-metadata-schema.md)használatával.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="IoT Edge modul létrehozása (külső AI)":::

Ez a gRPC következtetési kiszolgáló egy olyan .NET Core Console-alkalmazás, amely a Live Video Analytics és az egyéni AI között küldött [protopuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -üzeneteket kezeli. Az alábbi folyamat az élő videó-elemzés és a gRPC-következtetési kiszolgáló közötti üzeneteket követi:

1. Az élő videó elemzése média stream-leírót küld (lásd: [Extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)), amely a gRPC stream-munkameneten keresztül [protopuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -üzenetként adja meg a Media stream-információkat, amelyeket a rendszer a videó keretei alapján küld a kiszolgálónak. 
1. A kiszolgáló érvényesíti és visszaigazolja az adatfolyam-leírót, és beállítja a kívánt adatátviteli módszert.
1. Az élő videó-elemzések elindítják a képkockákat tartalmazó MediaSample-fájlok küldését.
1. A kiszolgáló elemzi a kapott képkockákat, és megkezdi a feldolgozását az Ön által meghatározott képfeldolgozó használatával.
1. A kiszolgáló ezután visszaadja az eredményeket [protopuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) üzenetként, amint azok elérhetővé válnak. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="GRPC-következtetési kiszolgáló létrehozása":::

A képkockák átvihetők a [megosztott memórián](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) keresztül, vagy beágyazható a protopuf üzenetbe. Az adatátviteli mód a LVA gráf topológiájában konfigurálható a keretek átadásának meghatározásához. Ezt úgy érheti el, ha a MediaGraphGrpcExtension tulajdonság **dataTransfer** elemét a lent látható módon konfigurálja:

Beágyazott

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Megosztott memória:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Megosztott memóriával folytatott kommunikáció esetén a IpcMode értékét meg kell adni a **megosztható** értékre, és a gRPC-kiszolgáló modulban állítsa be a IpcMode értékét a következő **tárolóra: {CONTAINER_NAME}**. Ezeket a beállításokat a telepítési jegyzékfájlban kell elvégezni, amelyet a modulok Azure-IoT Hub való üzembe helyezéséhez kell használni. Az alábbi példa a IoT Edge modulok beállításakor használandó tároló-beállításokat mutatja be.

Élő video Analytics-modul:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC bővítmény modulja:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> Győződjön meg arról, hogy a grpcExtension belül elérhető a "Container: lvaEdge" megosztott memória területe.

## <a name="sample-grpc-server"></a>Példa gRPC-kiszolgálóra

A gRPC-kiszolgáló fejlesztésével kapcsolatos információk megismeréséhez nézzük át a kód mintáját.

1. A tárház klónozása a GitHub-hivatkozásról [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) .
1. Indítsa el a VSCode, és navigáljon a/src/edge/modules/grpcExtension mappára.
1. Végezzük el a fájlok gyors áttekintését:

    1. **Program. cs**: ez az alkalmazás belépési pontja. A felelős a gRPC-kiszolgáló inicializálásához és kezeléséhez, amely gazdagépként fog működni. A példában a gRPC-ügyfelektől érkező bejövő gRPC (például az élő videó Analytics) figyelésére szolgáló portot a (z) AppConfig.jsgrpcBindings konfigurációs eleme határozza meg.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**: ez az osztály felelős a [protopuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) üzeneteinek kezeléséért. A rendszer beolvassa a keretet az üzenetben, meghívja a ImageProcessor, és megírja a következtetések eredményeit.
Most, hogy konfiguráltuk és inicializálta a gRPC-kiszolgáló portjának kapcsolatait, nézzük meg, hogyan lehet feldolgozni a bejövő gRPC üzeneteket.

        * A gRPC-munkamenet létrehozása után a gRPC-kiszolgáló által az ügyféltől kapott első üzenet a MediaStreamDescriptor, amely a [kiterjesztés. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) fájlban van definiálva. 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * A kiszolgáló implementációjában a metódus `ProcessMediaStreamDescriptor` érvényesíti a MediaStreamDescriptor MediaDescriptor tulajdonságát egy videofájl esetében, majd az adatátviteli módot (amely megosztott memóriát használ, vagy a beágyazott frame-átvitel üzemmódot használja) attól függően, hogy mit adott meg a topológiában, és milyen nevű telepítési sablont használ. 
        * Az üzenet fogadása és az adatátviteli mód sikeres beállítása után a gRPC-kiszolgáló visszaigazolja a MediaStreamDescriptor-üzenetet az ügyfélnek, és így létrehozza a kapcsolatot a kiszolgáló és az ügyfél között.    
        * Miután az élő videó Analytics megkapja a nyugtát, megkezdi a médiaadatfolyam-továbbítást a gRPC-kiszolgálóra. A kiszolgáló implementációjában a metódus `ProcessMediaStream` feldolgozza a bejövő MediaStreamMessage. A MediaStreamMessage a [. proto kiterjesztésben](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)is meg van adva.

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * A Appconfig.jsban lévő batchSize értékétől függően a kiszolgáló továbbra is fogadja az üzeneteket, és a listában tárolja a videó kereteit. Miután elérte a batchSize korlátot, a függvény meghívja a függvényt vagy azt a fájlt, amely feldolgozza a képet. Esetünkben a metódus egy BatchImageProcessor. cs nevű fájlt hív meg
    1. **Processors\BatchImageProcessor.cs**: ez az osztály felelős a rendszerkép (ek) feldolgozásához. Ebben a példában a rendszerkép besorolási modelljét használtuk. Minden feldolgozandó rendszerkép esetében a használt algoritmus a következő:

        1. A rendszerkép konvertálása bájtos tömbben feldolgozásra. Lásd a metódust: `GetBytes(Bitmap image)`
        
            A csak a JPG-kódolású képkockákat és a nem képpontos formátumot támogatja. Ha az egyéni processzor támogatja a különböző kódolást és/vagy formátumot, frissítse a `IsMediaFormatSupported` processzor osztály metódusát.
        1. A [ColorMatrix osztály](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1)használatával alakítsa át a képet szürkeárnyalatos méretre. Lásd: metódus: `ToGrayScale(Image source)` .
        1. Miután beolvasta a szürke méretezési képet, a rendszer kiszámítja a szürke skála bájtjainak átlagát.
        1. Ha az átlagos érték < 127, akkor a rendszerképet "Dark"-ként osztályozjuk, máskülönben "Light"-ként osztályozjuk őket a 1,0-as megbízhatósági értékkel. Lásd: metódus: `ProcessImage(List<Image> images)` .

    Saját processzor-logikát is hozzáadhat az osztály módosításával vagy új osztály hozzáadásával és a metódus megvalósításával:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    Miután hozzáadta az új osztályt, frissítenie kell a MediaGraphExtensionService. cs-t, hogy létrehozza az osztályt, és meghívja a ProcessImage metódust a feldolgozási logika futtatásához. 

## <a name="connect-with-live-video-analytics-module"></a>Kapcsolat az élő videó elemzési modullal

Most, hogy létrehozta a gRPC-bővítmény modult, most létrehozjuk és üzembe helyezzük a Media Graph-topológiát.

1. A Visual Studio Code használatával a Docker-be való bejelentkezéshez kövesse az [alábbi utasításokat](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) .
1. A Visual Studio Code-ban lépjen az src/Edge elemre. Ekkor megjelenik a. env fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz üzembe helyezési jegyzékére hivatkozik. Tartalmaz néhány helyőrző értéket. A. env fájl tartalmazza a változók értékeit.
    
    Ezután válassza a létrehozás és leküldés IoT Edge megoldást. Ehhez a lépéshez használja az src/Edge/deployment.grpc.template.js-t.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Kapcsolat az élő videó elemzési modullal":::
    
    Ez a művelet létrehozza a grpc-kiszolgáló modult, és leküldi a rendszerképet a Azure Container Registry.
    Győződjön meg arról, hogy rendelkezik a környezeti változókkal CONTAINER_REGISTRY_USERNAME_myacr és CONTAINER_REGISTRY_PASSWORD_myacr a. env fájlban.
1. Lépjen a src/Cloud-to-Device-Console-app mappába. Itt láthatja a appsettings.jsfájlt, és néhány további fájlt:

    * C2D-Console-app. csproj – a Project fájl a Visual Studio Code-hoz.
    * operations.json – a program futtatásához használni kívánt műveletek listája.
    * Program. cs – a minta programkódja. Ez a kód:

        * Betölti az alkalmazás beállításait.
        * Közvetlen metódusokat hív meg, amelyeket az élő videó Analytics IoT Edge modulban tesz elérhetővé. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md)meghívásával.
        * Szünetelteti, hogy megvizsgálhatja a program kimenetét a terminál ablakban, és megvizsgálhatja a modul által a kimeneti ablakban generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez.
1. Szerkessze a operations.jsfájlt:

    * Módosítsa a Graph-topológiára mutató hivatkozást:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * A GraphInstanceSet alatt szerkessze a gráf topológiájának nevét, hogy az megfeleljen az előző hivatkozásban szereplő értéknek:<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * A GraphTopologyDelete alatt szerkessze a nevet:<br/>`"name": "InferencingWithGrpcExtension"`

            A topológiának (például `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json` ) meg kell határoznia a kiterjesztési címeket:
    * Bővítmény címe paraméter

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Konfiguráció

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése

Az üzembe helyezési jegyzék meghatározza, hogy milyen modulok vannak üzembe helyezve egy peremhálózati eszközön és a modulok konfigurációs beállításaiban. Kövesse az alábbi lépéseket egy jegyzékfájl létrehozásához a sablonból, majd telepítse azt a peremhálózati eszközre.
Ez a lépés létrehozza a IoT Edge üzembe helyezési jegyzéket az src/Edge/config/deployment.grpc.amd64.json címen. Kattintson a jobb gombbal a fájlra, és válassza **a központi telepítés létrehozása egyetlen eszközhöz** lehetőséget.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése":::

Ezután a Visual Studio Code egy IoT Hub eszköz kiválasztását kéri. Válassza ki IoT Edge eszközét, amely LVA-minta-eszköz lehet.
Ebben a szakaszban az Edge-modulok üzembe helyezése a IoT Edge eszközön megkezdődött. Körülbelül 30 másodperc alatt frissítse az Azure IoT Hubt a Visual Studio Code bal alsó részén. Látnia kell, hogy egy új modul üzembe lett helyezve a lvaExtension néven.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="Új modul lett üzembe helyezve lvaExtension néven":::

## <a name="next-steps"></a>Következő lépések

A minta futtatásához és az eredmények értelmezéséhez kövesse a felkészülés az [élő videók elemzése a modell](use-your-model-quickstart.md) rövid útmutatója című témakörben említett **események figyelése** című szakaszát. Tekintse meg a példaként szolgáló gRPC-topológiákat is: [gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension és [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json).