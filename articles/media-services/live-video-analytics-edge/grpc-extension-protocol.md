---
title: gRPC-bővítményi protokoll – Azure
description: Ebből a cikkből megtudhatja, hogyan küldhet üzeneteket az élő video Analytics-modul és az AI-vagy CV egyéni bővítménye között a gRPC-bővítmény protokoll használatával.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 8d153b472e54b221b60a2b584043ffaf68e8ff82
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565809"
---
# <a name="grpc-extension-protocol"></a>gRPC-bővítmény-protokoll

A IoT Edge élő videó-elemzések lehetővé teszik a Media Graph feldolgozási képességeinek kiterjesztését egy [gráf kiterjesztésű csomóponton](./media-graph-extension-concept.md)keresztül. Ha a gRPC bővítmény processzorát használja bővítményként, akkor a Live Video Analytics modul és az AI-vagy CV-modul közötti kommunikáció a gRPC-alapú, nagy teljesítményű strukturált protokollon keresztül történik.

Ebből a cikkből megtudhatja, hogyan küldhet üzeneteket az élő video Analytics-modul és az AI-vagy CV egyéni bővítménye között a gRPC-bővítmény protokoll használatával.

a gRPC egy modern, nyílt forráskódú, nagy teljesítményű RPC-keretrendszer, amely bármilyen környezetben fut, és támogatja a platformok közötti és a többnyelvű kommunikációt. A gRPC átviteli szolgáltatás HTTP/2 kétirányú folyamatos átvitelt használ a következők között:

* a gRPC-ügyfél (élő videó Analytics IoT Edge modulon) és 
* a gRPC-kiszolgáló (az egyéni bővítmény).

A gRPC-munkamenetek a gRPC-ügyfél és a gRPC-kiszolgáló közötti egyetlen kapcsolat a TCP/TLS-porton keresztül. 

Egyetlen munkamenetben: az ügyfél egy Media stream-leírót küld a kiszolgálónak, amelyet [protopuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) üzenetként a gRPC stream-munkamenetben. A kiszolgáló érvényesíti az adatfolyam-leírót, elemzi a videó keretét, és visszaadja az eredményeket protopuf üzenetként. 

Erősen ajánlott, hogy a válaszokat érvényes JSON-dokumentumok használatával adja vissza a rendszer, amely a [következtetési metaadatok séma-objektummodell](./inference-metadata-schema.md)alapján meghatározott, előre meghatározott sémát követi. Így jobban biztosítható a más összetevőkkel való együttműködés, valamint az élő video Analytics modulhoz hozzáadott lehetséges jövőbeli képességek.

![gRPC-kiterjesztési szerződés](./media/grpc-extension-protocol/grpc.png)

## <a name="implementing-grpc-protocol"></a>GRPC protokoll implementálása

### <a name="creating-a-grpc-connection"></a>GRPC-kapcsolatok létrehozása

Az egyéni bővítménynek a következő gRPC szolgáltatást kell megvalósítania:

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

Ha a hívása megtörténik, ez egy kétirányú streamet nyit meg a gRPC-bővítmény és az élő video Analytics gráf közötti adatforgalomhoz. Az ezen a streamben mindkét fél által küldött első üzenet tartalmaz egy MediaStreamDescriptor, amely meghatározza, hogy milyen információkat küld a rendszer a következő MediaSamples.

Előfordulhat például, hogy a Graph bővítmény elküldheti az üzenetet (itt a JSON-ban), hogy jelezze, hogy a gRPC-üzenetekben beágyazott 416x416 RGB24-kódolt kereteket küld az egyéni bővítménynek.

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

Az egyéni bővítmény válaszként elküldi a következő üzenetet, amely jelzi, hogy csak a következtetéseket adja vissza.

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

Most, hogy mindkét oldalon kicserélték a média-leírókat, az élő videó Analytics elkezdi a keretek továbbítását a kiterjesztéshez.

> [!NOTE]
> A gRPC kiszolgálóoldali implementációja az Ön által választott programozási nyelven végezhető el.
### <a name="sequence-numbers"></a>Sorozatszámok

A gRPC bővítmény csomópontja és az egyéni bővítmény is külön sorszámokat tart fenn, amelyek az üzeneteiknek vannak rendelve. Ezeknek a sorszámoknak az 1-től kezdődő monoton módon kell növekedni. `ack_sequence_number` figyelmen kívül hagyható, ha a rendszer nem fogad el üzenetet, ami az első üzenet elküldésekor fordulhat elő.

Ha a megfelelő üzenetet teljes mértékben feldolgozták, a kérést igazolni kell. Megosztott memória átvitele esetén ez a visszaigazolás azt jelzi, hogy a küldő felszabadíthatja a megosztott memóriát, és a fogadó többé nem fér hozzá. A küldőnek minden megosztott memóriát meg kell tartania, amíg a felhasználó el nem ismeri.

### <a name="reading-embedded-content"></a>Beágyazott tartalom olvasása

Előfordulhat, hogy a beágyazott tartalom közvetlenül az üzenetből van kiolvasva az `MediaSample` `content_byte` s mezőn keresztül. Az adatkódolás a következő szerint történik: `MediaDescriptor` .

### <a name="reading-content-from-shared-memory"></a>Tartalom olvasása megosztott memóriából

Ha megosztott memórián keresztül viszi át a tartalmat, a feladó belefoglalja a-t, `SharedMemoryBufferTransferProperties` `MediaStreamDescriptor` Amikor először létrehozza a munkamenetet. Ez a következőképpen jelenhet meg (JSON-ban kifejezve):

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

Ekkor megnyílik a fogadó fájl `/dev/shm/inference_client_share_memory_2146989006636459346` . A küldő `MediaSample` üzenetet küld, amely a `ContentReference` fájl egy adott helyére hivatkozik.

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

A fogadó ezután beolvassa az adatokat erről a helyről a fájlból.

Ahhoz, hogy az élő videó elemzési tárolója megosztott memórián keresztül kommunikáljon, a tároló IPC-módját megfelelően kell konfigurálni. Ez többféleképpen is elvégezhető, de íme néhány ajánlott konfiguráció.

* A gazdagépen futó gRPC-következtetési motorral folytatott kommunikáció során az IPC módot a gazdagépre kell beállítani.
* Egy másik IoT Edge modulban futó gRPC-kiszolgálóval folytatott kommunikáció során az IPC-módot úgy kell beállítani, hogy megosszák az élő videó elemzési moduljának és `container:liveVideoAnalytics` az egyéni bővítménynek, ahol az az `liveVideoAnalytics` élő videó elemzési moduljának neve.

A fentiekben látható módon úgy nézheti ki az eszközt, ahogy az a fenti első lehetőséget használja.

```
"liveVideoAnalytics": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "host"
      }
  }
}
```

Az IPC-módokról további információt a következő témakörben talál: https://docs.docker.com/engine/reference/run/#ipc-settings---ipc .

## <a name="mediagraph-grpc-extension-contract-definitions"></a>MediaGraph gRPC-bővítmények szerződésének definíciói

Ez a szakasz a gRPC-szerződést határozza meg, amely meghatározza az adatfolyamot.

### <a name="protocol-messages"></a>Protokoll üzenetei

![Protokoll üzenetei](./media/grpc-extension-protocol/grpc2.png)
 
### <a name="client-authentication"></a>Ügyfél-hitelesítés

Az egyéni bővítmények implementálása érvényesítheti a bejövő gRPC-kapcsolatok hitelességét, és meggyőződhet arról, hogy a gRPC-bővítmény csomópontról érkeznek. A csomópont megadja a kérések fejlécében szereplő bejegyzést, amelyet a rendszer az ellen érvényesít.

A Felhasználónév/jelszó hitelesítő adatai használhatók ennek elvégzéséhez. A gRPC-bővítmények csomópontjának létrehozásakor a hitelesítő adatok az alábbiakhoz hasonló módon érhetők el:

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

A gRPC-kérelem elküldésekor a rendszer a következő fejlécet fogja tartalmazni a kérés metaadataiban, amely utánozza a HTTP alapszintű hitelesítést.

`x-ms-authentication: Basic (Base64 Encoded username:password)`


## <a name="configuring-inference-server-for-each-mediagraph-over-grpc-extension"></a>A következtetési kiszolgáló konfigurálása minden MediaGraph gRPC-bővítményen keresztül
A következtetési kiszolgáló konfigurálásakor nem kell kiadnia a csomópontot minden olyan AI-modell számára, amely a következtetési kiszolgálón van csomagolva. Ehelyett egy Graph-példány esetében használhatja a `extensionConfiguration` csomópont tulajdonságát, `MediaGraphGrpcExtension` és megadhatja, hogyan válassza ki az AI-modell (eke) t. A végrehajtás során a LVA átadja ezt a karakterláncot a következtetésben lévő kiszolgálónak, amely a kívánt AI-modell meghívására használható. Ez a `extensionConfiguration` tulajdonság egy opcionális tulajdonság, és a kiszolgáló-specifikus. A tulajdonság az alábbihoz hasonló módon használható:
```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "5"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>GRPC használata a TLS protokollon keresztül

A gRPC használt kapcsolat a TLS protokollal is biztonságossá válhat. Ez olyan helyzetekben hasznos, amikor a hálózat biztonsága az élő videó-elemzés és a viszonyítási motor között nem garantálható. A TLS a gRPC-üzenetbe ágyazott bármilyen tartalmat titkosít, ami további CPU-terhelést okoz a képkockák nagy sebességű továbbításakor.

A gRPC nem támogatja a IgnoreHostname és a IgnoreSignature ellenőrzési lehetőségeit, ezért a kiszolgálói tanúsítványnak, amelyre a következtetést biztosító motor mutat, tartalmaznia kell egy olyan CN-t, amely pontosan megegyezik az IP-címmel/állomásnévvel a gRPC-bővítmény csomópontjának végponti URL-címében.

## <a name="next-steps"></a>Következő lépések

[Tudnivalók a viszonyítási metaadatok sémáról](inference-metadata-schema.md)