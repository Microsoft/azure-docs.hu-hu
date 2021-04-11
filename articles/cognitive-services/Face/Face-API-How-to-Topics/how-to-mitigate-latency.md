---
title: Az arc szolgáltatás használatakor felhasználható késés csökkentése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan csökkentheti a késést a Face szolgáltatás használatakor.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: 412105e3262a3baf8780bd3bd1082508967ea486
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012584"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Útmutató: az arc szolgáltatás használatakor felhasználható késés csökkentése

A Face szolgáltatás használatakor késés tapasztalható. A késés a hálózaton keresztüli kommunikáció során felmerülő bármilyen késleltetésre utal. Általánosságban elmondható, hogy a késés lehetséges okai a következők:
- Az egyes csomagok fizikai távolságának a forrástól a célhelyig kell utaznia.
- Az átviteli közeggel kapcsolatos problémák.
- Az útválasztók vagy kapcsolók hibája az átviteli útvonal mentén.
- A csomagok vizsgálatára szolgáló víruskereső alkalmazások, tűzfalak és egyéb biztonsági mechanizmusok által igényelt idő.
- Az ügyfél vagy a kiszolgáló alkalmazásaiban hibásan működik.

Ez a témakör az Azure-Cognitive Services használatának késésének lehetséges okairól, valamint az ilyen okok enyhítésének módjáról beszél.

> [!NOTE]
> Az Azure Cognitive Services nem biztosít semmilyen szolgáltatói szerződés (SLA) a késéssel kapcsolatban.

## <a name="possible-causes-of-latency"></a>A késés lehetséges okai

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Lassú kapcsolat a kognitív szolgáltatás és egy távoli URL-cím között

Egyes Azure-Cognitive Services olyan metódusokat biztosítanak, amelyek az Ön által megadott távoli URL-címről szereznek be adatforrást. Például a Face szolgáltatás [DetectWithUrlAsync metódusának](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) meghívásakor megadhatja egy olyan rendszerkép URL-címét, amelyben a szolgáltatás megpróbálja felderíteni az arcokat.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

A Face szolgáltatásnak ezután le kell töltenie a rendszerképet a távoli kiszolgálóról. Ha a Face szolgáltatás és a távoli kiszolgáló közötti kapcsolatok lassúak, ez hatással lesz az észlelési módszer válaszideje.

Ennek enyhítése érdekében érdemes lehet [a rendszerképet az Azure Premium blob Storage tárolni](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Például:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Nagyméretű feltöltés mérete

Egyes Azure-Cognitive Services olyan metódusokat biztosítanak, amelyek egy feltöltött fájlból szereznek be adatforrást. Ha például a Face szolgáltatás [DetectWithStreamAsync metódusát](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) hívja meg, feltöltheti azt a rendszerképet, amelyben a szolgáltatás megpróbálja felderíteni az arcokat.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Ha a feltölteni kívánt fájl nagy méretű, az a `DetectWithStreamAsync` következő okok miatt befolyásolja a metódus válaszideje:
- A fájl feltöltése tovább tart.
- A fájl méretének arányában a szolgáltatás továbbra is feldolgozza a fájlt.

Enyhítését
- Érdemes lehet [a rendszerképet az Azure Premium blob Storage tárolni](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Például:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Érdemes lehet kisebb fájlt feltölteni.
    - Tekintse meg a [bemeneti adatokkal](../concepts/face-detection.md#input-data) kapcsolatos útmutatást a arcfelismerés és a [bemeneti adatok](../concepts/face-recognition.md#input-data)észleléséhez.
    - Az Arcfelismerés esetében az észlelési modell használatakor `DetectionModel.Detection01` a képfájl méretének csökkentése növeli a feldolgozási sebességet. Az észlelési modell használatakor `DetectionModel.Detection02` a képfájlok méretének csökkentése csak akkor növeli a feldolgozási sebességet, ha a képfájl mérete kisebb, mint 1920 × 1080.
    - Az Arcfelismerés esetében az arc méretének a 200x200 képpont értékre való csökkentése nem befolyásolja az elismerési modell pontosságát.
    - A `DetectWithUrlAsync` és a `DetectWithStreamAsync` metódusok teljesítménye attól is függ, hogy hány arc van egy képben. A Face szolgáltatás legfeljebb 100 arcot tud visszaadni egy képhez. Az arcok nagy és kis méretűre vannak rendezve.
    - Ha több szolgáltatási módszert kell meghívnia, érdemes párhuzamosan meghívni őket, ha az alkalmazás kialakítása lehetővé teszi a számára. Ha például két rendszerképben lévő arcokat kell felderíteni egy arc-összehasonlítás végrehajtásához:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Lassú kapcsolat a számítási erőforrás és a Face szolgáltatás között

Ha a számítógép lassú kapcsolatban áll a Face szolgáltatással, az hatással lesz a szolgáltatási módszerek válaszideje.

Enyhítését
- Az arc-előfizetés létrehozásakor ügyeljen arra, hogy a legközelebb eső régiót válassza ki, ahol az alkalmazás üzemeltetve van.
- Ha több szolgáltatási módszert kell meghívnia, érdemes párhuzamosan meghívni őket, ha az alkalmazás kialakítása lehetővé teszi a számára. Példaként tekintse meg az előző szakaszt.
- Ha a hosszabb késés hatással van a felhasználói élményre, válasszon időtúllépési küszöbértéket (például maximális 5 mp) az API-hívás újbóli megkísérlése előtt.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan csökkentheti a késést a Face szolgáltatás használatakor. Következő lépésként megtudhatja, hogyan méretezheti fel a meglévő PersonGroup és FaceList objektumokat a LargePersonGroup és a LargeFaceList objektumokra.

> [!div class="nextstepaction"]
> [Példa: a nagyméretű szolgáltatás használata](how-to-use-large-scale.md)

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Dokumentáció (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Hivatkozási dokumentáció (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
