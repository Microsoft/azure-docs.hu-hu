---
title: A késés csökkentése a Face szolgáltatás használata esetén
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan csökkentheti a késést a Face szolgáltatás használata esetén.
services: cognitive-services
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.openlocfilehash: a306883573387a2a5c20a53c7015c6dbd3eddf65
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878667"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>How to: mitigate latency when using the Face service (A késés csökkentése a Face szolgáltatás használata esetén)

Késést okozhat a Face szolgáltatás használata során. A késés bármilyen késést jelenti, amely a hálózaton keresztüli kommunikáció során következik be. A késés lehetséges okai általában a következők:
- Az egyes csomagok fizikai távolsága a forrástól a célig.
- Az átviteli adathordozóval kapcsolatos problémák.
- Hibák az útválasztókban vagy kapcsolókban az átviteli útvonalon.
- A víruskereső alkalmazások, tűzfalak és más biztonsági mechanizmusok által a csomagok vizsgálatához szükséges idő.
- Hibás az ügyfél- vagy kiszolgálóalkalmazások működése.

Ez a témakör a késés lehetséges okait tárgyalja a Azure Cognitive Services használatának lehetséges okairól, és hogy hogyan mérsékelheti ezeket az okokat.

> [!NOTE]
> Azure Cognitive Services nem biztosít semmilyen szolgáltatói szerződés (SLA) a késéssel kapcsolatban.

## <a name="possible-causes-of-latency"></a>A késés lehetséges okai

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Lassú kapcsolat a Cognitive Services-szolgáltatás és egy távoli URL-cím között

Egyes Azure Cognitive Services olyan metódusokat biztosítanak, amelyek az Ön által megadott távoli URL-címről szereznek be adatokat. Ha például a Face szolgáltatás [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) metódusát hívja meg, megadhatja annak a képnek az URL-címét, amelyen a szolgáltatás arcokat próbál felismerni.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

A Face szolgáltatásnak ezután le kell töltenie a képet a távoli kiszolgálóról. Ha a Face szolgáltatás és a távoli kiszolgáló kapcsolata lassú, az hatással lesz a Detect metódus válaszidejére.

Ennek enyhítése érdekében fontolja meg a rendszerkép [Azure Premium](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)Blob Storage. Például:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Nagy feltöltési méret

Egyes Azure Cognitive Services olyan metódusokat biztosítanak, amelyek egy feltöltött fájlból szereznek be adatokat. Ha például a Face szolgáltatás [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) metódusát hívja meg, feltölthet egy képet, amelyben a szolgáltatás arcokat próbál felismerni.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Ha a feltölteni kívánt fájl nagy, az hatással lesz a metódus válaszidere `DetectWithStreamAsync` a következő okok miatt:
- A fájl feltöltése hosszabb időt vesz igénybe.
- A fájl feldolgozása a fájl méretétől függően hosszabb időt vesz igénybe a szolgáltatás számára.

Enyhítése:
- Fontolja [meg a rendszerkép Azure Premium Blob Storage.](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet) Például:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Érdemes lehet egy kisebb fájlt feltölteni.
    - Lásd az [arcfelismeréshez szükséges bemeneti](../concepts/face-detection.md#input-data) adatokra és az arcfelismeréshez [szükséges bemeneti adatokra vonatkozó irányelveket.](../concepts/face-recognition.md#input-data)
    - Az arcfelismeréshez az észlelési modell használata esetén a képfájl méretének csökkentése `DetectionModel.Detection01` növeli a feldolgozási sebességet. Észlelési modell használata esetén a képfájl méretének csökkentése csak akkor növeli a feldolgozási sebességet, ha a képfájl kisebb, mint `DetectionModel.Detection02` 1920x1080.
    - Arcfelismerés esetén az arcméret 200x200 képpontra csökkentése nem befolyásolja a felismerési modell pontosságát.
    - A és a metódus teljesítménye attól is függ, hogy hány arc látható `DetectWithUrlAsync` egy `DetectWithStreamAsync` képen. A Face szolgáltatás legfeljebb 100 arcot képes visszaadni egy képhez. Az arcok az arc téglalapmérete szerint vannak rangsorolva a nagytól a kis méretűek felé.
    - Ha több szolgáltatási metódust kell hívnia, fontolja meg azok párhuzamos hívását, ha az alkalmazás kialakítása ezt lehetővé teszi. Ha például arcokat kell észlelni két képről az arcok összehasonlításához:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Lassú kapcsolat a számítási erőforrás és a Face szolgáltatás között

Ha a számítógép lassú kapcsolattal rendelkezik a Face szolgáltatáshoz, az hatással lesz a szolgáltatási metódusok válaszidejére.

Enyhítése:
- A Face-előfizetés létrehozásakor ügyeljen arra, hogy az alkalmazáshoz legközelebb eső régiót válassza.
- Ha több szolgáltatás metódust kell hívnia, fontolja meg azok párhuzamos hívását, ha az alkalmazás kialakítása ezt lehetővé teszi. Erre az előző szakaszban láthat példát.
- Ha a hosszabb késések hatással vannak a felhasználói élményre, az API-hívás újrapróbálása előtt válasszon ki egy időtúllépési küszöbértéket (pl. maximum 5 másodperc).

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan csökkentheti a késést a Face szolgáltatás használata esetén. Ezután megtudhatja, hogyan skálázható fel a meglévő PersonGroup- és FaceList-objektumokról LargePersonGroup- és LargeFaceList-objektumokra.

> [!div class="nextstepaction"]
> [Példa: A nagy léptékű funkció használata](how-to-use-large-scale.md)

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Referenciadokumentáció (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenciadokumentáció (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
