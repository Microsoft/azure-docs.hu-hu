---
title: Azure-eszköz kódolása Media Encoder Standard használatával | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Media Encoder Standard a médiatartalom Azure Media Services való kódolásához. A kód minták a REST APIt használják.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4b7a188a9509c3510de2843b232f57e3437177b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009827"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Eszköz kódolása Media Encoder Standard használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portál](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Áttekintés

Az interneten keresztüli digitális videó továbbításához tömöríteni kell az adathordozót. A digitális videofájlok nagy méretűek, és előfordulhat, hogy túl nagy méretűek az interneten keresztül, vagy ha az ügyfelek eszközei megfelelően jelennek meg. A kódolás a videó és hang tömörítésének folyamata, így az ügyfelek megtekinthetik az adathordozót.

A kódolási feladatok a Azure Media Services leggyakoribb feldolgozási műveleteinek egyike. Kódolási feladatokat hozhat létre a médiafájlok egyik kódolásból a másikba való konvertálásához. Kódoláskor használhatja a Media Services beépített kódolót (Media Encoder Standard). Media Services-Partner által biztosított kódolót is használhat. A külső gyártótól származó kódolók az Azure Piactéren keresztül érhetők el. A kódolási feladatok részleteit a kódolóhoz megadott előre definiált karakterláncok, illetve előre definiált konfigurációs fájlok használatával határozhatja meg. Az elérhető beállításkészletek típusairól a következő témakörben talál további információt: [Media Encoder standard](./media-services-mes-presets-overview.md).

Minden feladathoz egy vagy több feladat tartozhat, attól függően, hogy milyen típusú feldolgozást szeretne elvégezni. A REST API segítségével a feladatokat és a hozzájuk kapcsolódó feladatokat kétféleképpen hozhatja létre:

* A feladatok a feladat entitások navigációs tulajdonságán keresztül is meghatározhatók.
* Használjon OData batch-feldolgozást.

Javasoljuk, hogy mindig kódolja a forrásfájlokat egy adaptív sávszélességű MP4-készletbe, majd alakítsa át a készletet a kívánt formátumra [dinamikus csomagolás](media-services-dynamic-packaging-overview.md)használatával.

Ha a kimeneti eszköz titkosítva van, konfigurálnia kell az eszköz kézbesítési házirendjét. További információ: az [eszközök kézbesítési házirendjének konfigurálása](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Megfontolandó szempontok

A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).

A Media processors-re való hivatkozás megkezdése előtt ellenőrizze, hogy rendelkezik-e a megfelelő adathordozó-feldolgozó AZONOSÍTÓval. További információ: adathordozó- [processzorok beszerzése](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Feladat létrehozása egyetlen kódolási feladattal

> [!NOTE]
> A Media Services REST API használatakor a következő szempontokat kell figyelembe venni:
>
> A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).
>
> Ha JSON-t használ, és megadja, hogy az **__metadata** kulcsszót használja a kérelemben (például egy csatolt objektumra való hivatkozáshoz), be kell állítania az **Accept** fejlécet a [JSON részletes formátuma](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)mezőbe: elfogadás: Application/JSON; OData = verbose.
>
>

Az alábbi példa bemutatja, hogyan hozhat létre és küldhet el egy feladatot egy adott feladattal egy adott felbontás és minőség alapján. Media Encoder Standard használatával történő kódoláskor Itt adhatja meg az [itt](./media-services-mes-presets-overview.md)megadott feladat-konfigurálási beállításkészletet.

Kérés:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net

{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}
```

Válasz:

```console
HTTP/1.1 201 Created

. . .
```

### <a name="set-the-output-assets-name"></a>A kimeneti eszköz nevének beállítása
Az alábbi példa bemutatja, hogyan állíthatja be a assetName attribútumot:

```console
{ "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}`
```

### <a name="considerations"></a>Megfontolandó szempontok
* A TaskBody tulajdonságainak literál XML-t kell használniuk a feladat által használt bemeneti vagy kimeneti eszközök definiálásához. A feladat cikke tartalmazza az XML-séma XML-sémájának definícióját.
* A TaskBody-definícióban minden belső értéknek `<inputAsset>` `<outputAsset>` JobInputAsset (Value) vagy JobOutputAsset (Value) értékűnek kell lennie.
* Egy feladat több kimeneti eszközzel is rendelkezhet. Egy JobOutputAsset (x) csak egyszer használható egy feladat tevékenységének kimenetében.
* A JobInputAsset vagy a JobOutputAsset megadható egy tevékenység bemeneti eszközeként.
* A feladatok nem lehetnek ciklusok.
* Az JobInputAsset vagy JobOutputAsset-re átadott Value paraméter az adott eszköz indexének értékét jelöli. A tényleges eszközök a InputMediaAssets és a OutputMediaAssets navigációs tulajdonságaiban vannak definiálva a feladatok entitásának definíciójában.
* Mivel Media Services a OData v3-ra épül, a InputMediaAssets és a OutputMediaAssets navigációs tulajdonság gyűjteményében lévő egyedi eszközökre a "__metadata: URI" név-érték párok hivatkoznak.
* A InputMediaAssets egy vagy több olyan eszközre mutat, amelyet a Media Services hozott létre. A OutputMediaAssets a rendszer hozza létre. Nem hivatkoznak meglévő eszközre.
* A OutputMediaAssets a assetName attribútummal lehet elnevezni. Ha ez az attribútum nem található, akkor a OutputMediaAsset neve megegyezik az elem belső szöveges értékével, vagy a feladatnév értékének `<outputAsset>` vagy a feladattípus értékének (abban az esetben, ha nincs megadva a Name tulajdonság) utótagja. Ha például a assetName értéket állítja be a "Sample" értékre, akkor a OutputMediaAsset Name tulajdonság értéke "Sample". Ha azonban nem állított be értéket a assetName, de a feladatot "NewJob" értékre állította, akkor a OutputMediaAsset neve "JobOutputAsset (Value) _NewJob."

## <a name="create-a-job-with-chained-tasks"></a>Feladat létrehozása láncolt feladatokkal
Számos alkalmazási helyzetben a fejlesztők több feldolgozási feladatot kívánnak létrehozni. Media Services több láncolt feladat is létrehozható. Mindegyik feladat különböző feldolgozási lépéseket hajt végre, és különböző adathordozó-processzorokat használhat. A láncolt feladatok kikapcsolhatják az adategységeket az egyik feladatból a másikba, és a feladatok lineáris sorát hajtják végre az adategységen. Egy adott feladatban végrehajtott feladatok azonban nem szükségesek a sorozatban. Láncolt feladat létrehozásakor a láncolt **ITask** objektumok egyetlen **IJob** -objektumban jönnek létre.

> [!NOTE]
> Egy feladat jelenleg legfeljebb 30 feladatot hajt végre. Ha több mint 30 feladatot szeretne láncba felvenni, hozzon létre több feladatot a feladatok tárolására.
>
>

```console
POST https://media.windows.net/api/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      },
      {  
         "Configuration":"H264 Smooth Streaming 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
      }
   ]
}
```

### <a name="considerations"></a>Megfontolandó szempontok
A feladatok láncolásának engedélyezése:

* Egy feladatnak legalább két feladattal kell rendelkeznie.
* Legalább egy olyan feladatnak kell lennie, amelynek bemenete egy másik feladat kimenete a feladatban.

## <a name="use-odata-batch-processing"></a>OData batch-feldolgozás használata
Az alábbi példa azt szemlélteti, hogyan használható a OData batch-feldolgozás feladatok és feladatok létrehozásához. További információ a Batch-feldolgozásról: [Open adatprotokoll (OData) batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

```console
POST https://media.windows.net/api/$batch HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Accept: multipart/mixed
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
Host: media.windows.net


--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/Jobs HTTP/1.1
Content-ID: 1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/$1/Tasks HTTP/1.1
Content-ID: 2
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
   "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
```


## <a name="create-a-job-by-using-a-jobtemplate"></a>Feladatok létrehozása JobTemplate használatával
Ha több objektumot dolgoz fel a feladatok közös készletével, egy JobTemplate segítségével megadhatja az alapértelmezett feladatait, vagy beállíthatja a feladatok sorrendjét.

Az alábbi példa bemutatja, hogyan hozhat létre egy beágyazott TaskTemplate rendelkező JobTemplate. A TaskTemplate a Media Encoder Standard használja a MediaProcessor az adatfájl kódolásához. Azonban más MediaProcessors is használhatók.

```console
POST https://media.windows.net/API/JobTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
```

> [!NOTE]
> Más Media Services entitásokkal ellentétben minden TaskTemplate új GUID azonosítót kell megadnia, és a kérés törzsében a taskTemplateId és az id tulajdonságban kell elhelyeznie. A tartalmi azonosító sémának követnie kell a Azure Media Services entitások azonosítása című témakörben leírt sémát. Emellett a JobTemplates nem frissíthető. Ehelyett létre kell hoznia egy újat a frissített módosításaival.
>
>

Ha a művelet sikeres, a rendszer a következő választ adja vissza:

```console
HTTP/1.1 201 Created

. . .
```

Az alábbi példa bemutatja, hogyan hozhat létre olyan feladatot, amely egy JobTemplate-azonosítóra hivatkozik:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
```

Ha a művelet sikeres, a rendszer a következő választ adja vissza:

```console
HTTP/1.1 201 Created

. . .
```


## <a name="advanced-encoding-features-to-explore"></a>Speciális kódolási funkciók a felderítéshez
* [Miniatűrök készítése](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniatűrök létrehozása a kódolás során](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Videók körülvágása a kódolás során](media-services-crop-video.md)
* [Kódolási beállításkészletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Videó befedése vagy vízjele képpel](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy már tudja, hogyan hozhat létre egy adategység kódolására szolgáló feladatot, tekintse meg a következő témakört: [a feladatok előrehaladásának ellenőrzéséhez Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Lásd még
[Adathordozó-processzorok beolvasása](media-services-rest-get-media-processor.md)