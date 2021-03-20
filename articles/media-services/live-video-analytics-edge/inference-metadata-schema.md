---
title: Metaadat-séma következtetése – Azure
description: Ebből a cikkből megtudhatja, milyen viszonyítási metaadatok sémája.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 2de437577dc00692fb98c46fec32bfaa6612dc99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92019494"
---
# <a name="inference-metadata-schema"></a>Következtetéses metaadatok sémája 

Minden olyan következtetési objektum, amely HTTP-alapú vagy gRPC-alapú szerződés használatával történik, a jelen témakörben ismertetett objektummodell követi.

## <a name="object-model"></a>Objektummodell

![Objektummodell](./media/inference-metadata-schema/object-model.png)
 
|Típusdefiníció|Description|
|---|---|
|Címke|Az eredményhez társított címke vagy címke. A címkézéssel való Alng a címkéhez társított megbízhatósági értéket is megkapja.|
|Attribútum|Az eredményhez tartozó további attribútumok. Hozzáadhat új attribútumokat, amelyeket a rendszer a következtetési motortól kapott, a megbízhatósági értékkel együtt.|
|Attribútumok listája|Választható attribútumok listája.|
|Téglalap|Téglalap alakú terület a bal felső sarokban. A kötelező tulajdonságok a "Length", a "width", a Height "és a" legfelső szél távolsága a forrástól "lesz.|
|Osztályozás|Az osztályozó címkéje gyakran alkalmazható a teljes mintára. A "címke" segítségével osztályozhatja az eredményt.|
|Entitás|A mintában észlelt vagy azonosított entitás. Ha a következtetést kiterjesztő motor észleli egy entitást, a rendszer "címkét" kap, további attribútumokat is kikövetkeztetett, és a rendszer visszaadja az észlelt entitás körüli téglalap alakú doboz összehangolása értékét.|
|Esemény|A mintában észlelt esemény. Ha a mintában egy esemény észlelhető, az esemény neve és az esemény-specifikus tulajdonságok lesznek visszaadva.|
|Mozgásban lévő|A rendszer mozgást észlelt a mintában. Ha a rendszer mozgást észlel a mintában, a rendszer egy, a mozgást észlelő, téglalap alakú határolókeret koordinátáit adja vissza.|
|Szöveg|A rendszer a mintához tartozó szöveget adja vissza a szöveg kezdő és záró időbélyegével együtt.|
|Egyéb|Egyéb általános hasznos adatokat ad vissza.|

Az alábbi példa egyetlen eseményt tartalmaz, néhány támogatott következtetési típussal:

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>Következő lépések

- [gRPC-adategyezmény](./grpc-extension-protocol.md)
- [HTTP-adategyezmény](./http-extension-protocol.md)