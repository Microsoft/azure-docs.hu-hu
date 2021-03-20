---
title: A bemeneti adatok bármilyen formátumban olvashatók a .NET-alapú egyéni deszerializálók használatával Azure Stream Analytics
description: Ez a cikk ismerteti a szerializálási formátumot, valamint azokat az interfészeket, amelyek egyéni .NET-deszerializálók definiálását Azure Stream Analytics Felhőbeli és peremhálózati feladatok számára.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 140a836882ad3abe048047120e4fe1ebc0a3067c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018156"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>A bemeneti adatok bármilyen formátumban olvashatók a .NET-alapú egyéni deszerializálók használatával

A .NET-alapú egyéni deszerializálók lehetővé teszik, hogy az Azure Stream Analytics-feladatok a három [beépített adatformátumon](stream-analytics-parsing-json.md)kívülről is olvassák az adatformátumokat. Ez a cikk ismerteti a szerializálási formátumot, valamint azokat a csatolókat, amelyek meghatározzák a .NET-alapú egyéni deszerializálók Azure Stream Analytics a felhő és a peremhálózati feladatok számára. A protokoll-és CSV-formátumhoz is vannak példa deszerializálók.

## <a name="net-custom-deserializer"></a>.NET egyéni deszerializáló

A következő kódrészletek az egyéni deszerializáló és implementált felületek `StreamDeserializer<T>` .

`UserDefinedOperator` az összes egyéni streaming operátor alaposztálya. Inicializálja a `StreamingContext` -t, amely magában foglalja a diagnosztika közzétételi mechanizmusát, amelynek a deszerializáló hibáit kell elhárítani.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

A következő kódrészlet a streaming-adatátvitelek deszerializálása. 

A kihagyható hibákat az `IStreamingDiagnostics` átadott inicializálási módszer használatával kell kiállítani `UserDefinedOperator` . A rendszer az összes kivételt hibákat fogja kezelni, és a deszerializáló újra létrejön. Bizonyos számú hiba után a feladatok sikertelen állapotba kerülnek.

`StreamDeserializer<T>` egy adatfolyamot deszerializál egy típusú objektumba `T` . A következő feltételeknek kell teljesülniük:

1. A T egy osztály vagy egy struct.
1. A T összes nyilvános mezője a következők egyike:
    1. Az egyik a [sbyte érték, a byte, a short, a ushort, az int, a uint, a Long, a DateTime, a string, a float, a Double] vagy a nullával egyenértékű.
    1. Egy másik Struct vagy osztály, amely ugyanezeket a szabályokat követi.
    1. A típusú tömb, `T2` amely ugyanezeket a szabályokat követi.
    1. IList, `T2` ahol a T2 ugyanazokat a szabályokat követi.
    1. Nem rendelkezik rekurzív típusokkal.

A paraméter a `stream` szerializált objektumot tartalmazó adatfolyam. `Deserialize` példányok gyűjteményét adja vissza `T` .

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` olyan kontextust biztosít, amely a felhasználói kezelő diagnosztika-közzétételi mechanizmusát tartalmazza.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` a felhasználó által definiált operátorok, például a szerializáló, a deszerializáló és a felhasználó által definiált függvények diagnosztikája.

`WriteError` hibaüzenetet ír az erőforrás-naplókba, és elküldi a hibát a diagnosztika számára.

`briefMessage` egy rövid hibaüzenet. Ez az üzenet a diagnosztika szolgáltatásban jelenik meg, és a termék csapata hibakeresési célokat szolgál. Ne tartalmazzon bizalmas adatokat, és tartsa meg a 200 karakternél rövidebb üzenetet

`detailedMessage` a részletes hibaüzenetet kap, amely csak a tárolóban lévő erőforrás-naplókhoz van hozzáadva. Az üzenetnek 2000 karakternél rövidebbnek kell lennie.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Deszerializáló – példák

Ez a szakasz bemutatja, hogyan írhat egyéni deszerializált a Protopuf és a CSV-hez. További példákat, például az Event hub Capture AVRO-formátumát, látogasson el [a Azure stream Analyticsre a githubon](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Protokoll-puffer (Protopuf) formátuma

Ez egy példa a protokoll pufferének formátumára.

Tegyük fel a következő protokoll-puffer definícióját.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

Fut `protoc.exe` a **Google. Protopuf. Tools** NuGet. cs fájlt hoz létre a definícióval. A létrehozott fájl itt nem jelenik meg. Győződjön meg arról, hogy a Stream Analytics-projektben használt Protopuf-verzió megegyezik a bemenet létrehozásához használt Protopuf-verzióval. 

A következő kódrészlet a deszerializáló implementációja, feltételezve, hogy a létrehozott fájl szerepel a projektben. Ez a megvalósítás csak egy vékony burkoló a generált fájlon.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

A következő kódrészlet egy egyszerű CSV-deszerializáló, amely a terjesztési hibákat is bemutatja.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>A REST API-k szerializálási formátuma

Minden Stream Analytics-bemenet **szerializálási formátummal** rendelkezik. A bemeneti beállításokkal kapcsolatos további információkért tekintse meg a [input REST API](/rest/api/streamanalytics/2016-03-01/inputs) dokumentációját.

A következő JavaScript-kód példa a .NET deszerializáló szerializálási formátumára a REST API használatakor:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` a-t implementáló osztálynak kell lennie `StreamDeserializer<T>` . Ezt a következő szakaszban ismertetjük.

## <a name="region-support"></a>Régiós támogatás

Ez a funkció a következő régiókban érhető el:

* USA nyugati középső régiója
* Észak-Európa
* USA keleti régiója
* USA nyugati régiója
* USA 2. keleti régiója
* Nyugat-Európa

További régiók [támogatását is kérheti](https://aka.ms/ccodereqregion) .

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Mikor lesz elérhető ez a szolgáltatás az összes Azure-régióban?

Ez a funkció [6 régióban](#region-support)érhető el. Ha egy másik régióban szeretné használni ezt a funkciót, [elküldhet egy kérelmet](https://aka.ms/ccodereqregion). Az összes Azure-régió támogatása az ütemterv szerint történik.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Hozzáférhetek a MetadataPropertyValue a saját bemenetekhez a GetMetadataPropertyValue függvényhez hasonló módon?

Ez a funkció nem támogatott. Ha erre a képességre van szüksége, szavazzon erre a kérelemre a [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Megoszthatom a deszerializáló implementációját a Közösséggel, hogy mások is részesülhessenek?

A deszerializáló megvalósítását követően a Közösséggel megoszthatja másokkal. Küldje be a kódot a [Azure stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)-tárházba.

### <a name="what-are-the-other-limitations-of-using-custom-deserializers-in-stream-analytics"></a>Melyek a Stream Analytics egyéni deszerializálók használatának egyéb korlátai?

Ha a bemeneti formátuma protopuf típusú sémát tartalmaz `MapField` , nem fog tudni egyéni deszerializáló megvalósítani. Emellett az egyéni deszerializálók nem támogatják a mintaadatok vagy az előnézeti adatmegjelenítést. 

## <a name="next-steps"></a>Következő lépések

* [.NET-alapú egyéni deszerializálók Azure Stream Analytics felhőalapú feladatokhoz](custom-deserializer.md)
