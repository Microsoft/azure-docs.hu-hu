---
title: Fájlok elrendezése átalakításhoz
description: Javaslatok a fájlok bemeneti tárolóban történő elhelyezésének legmegfelelőbb módjairól.
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 13c4b841fe2d0b62472628d9382b5f6ee3d1fa6c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91318075"
---
# <a name="laying-out-files-for-conversion"></a>Fájlok elrendezése átalakításhoz

Egy eszköz megfelelő feldolgozásához a konverziós szolgáltatásnak képesnek kell lennie az összes bemeneti fájl megkeresésére.
Ilyenek a konvertált fő adatfájlok, és általában néhány más fájl is hivatkozik az adatfájlban lévő elérési utakon.
Az eszköz átalakítására irányuló kérelem két paramétert kap, amelyek meghatározzák, hogy az átalakítási szolgáltatás hogyan találja meg ezeket a fájlokat: a `input.folderPath` (nem kötelező) és a `input.inputAssetPath` .
Ezek teljes mértékben dokumentálva vannak az [átalakítási REST API](conversion-rest-api.md) oldalon.
A fájlok megállapítása céljából fontos megjegyezni, hogy az `folderPath` eszköz feldolgozásakor az átalakítási szolgáltatás számára elérhető összes fájl teljes készletét határozza meg.

## <a name="placing-files-so-they-can-be-found"></a>Fájlok elhelyezése, hogy megtalálja őket

Ha egy forrásoldali eszköz külső fájlokat használ, a fájlok elérési útját az eszközön belül tárolja a rendszer.
A konverziós szolgáltatásnak ezeket az elérési utakat egy olyan fájlrendszerben kell értelmezni, amely eltér az objektum eredeti fájlrendszerével.
Ha az elérési utak relatív elérési utakként vannak tárolva, és a forrás és az általa hivatkozott fájl relatív helye nem változik, az átalakítási szolgáltatás egyszerűen megkeresi a hivatkozott fájlt.

> [!Note]
> Javasoljuk, hogy helyezzen el fájlokat a bemeneti tárolóba, hogy a fájlok relatív helyei ugyanazok legyenek, mint amikor az eszköz létrejött.

> [!Note]
> Inkább olyan objektumokat hozzon létre, amelyek relatív elérési utakat hordoznak.
> A [3ds Max-hoz készült anyagok beállításával](../../tutorials/modeling/3dsmax-material-setup.md) kapcsolatos oktatóanyag egy 3ds Max-példát biztosít arra vonatkozóan, hogyan biztosítható, hogy egy eszköz relatív elérési utakat használjon.

## <a name="finding-textures"></a>Textúrák keresése

Az eszközök számos módon hozhatók létre, a konverziós szolgáltatásnak rugalmasnak kell lennie.
Különösen olyan helyzetekben kell kezelni, amikor az eszköz elérési útjai és a textúrák helye nem pontosan egyezik.
Ilyen eset például, ha az eszközök abszolút elérési utakat tartalmaznak, mivel ezek az elérési utak soha nem egyeznek meg a konverziós szolgáltatás által használt fájlrendszerrel.
Ennek a helyzetnek a kezeléséhez a lehető legjobb megközelítést használjuk a textúrák kereséséhez.

A textúrák megkeresésének algoritmusa a következő: az adott eszközön tárolt elérési út alapján megkeresheti a leghosszabb Alútvonal-utótagot, amely a forrás eszköz helyének relatív elérési útjaként való használatakor a létező fájlt célozza meg.
Ha nincs ilyen alelérési út (a teljes elérési úttal együtt), a rendszer a textúrát hiányzónak tekinti.

Vegye figyelembe a következő kiagyalt fájlrendszert: 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
Ha a myAsset. FBX a relatív elérési úttal rendelkező textúrára hivatkozik `..\Textures\MyAssetTextures\myTexture.png` , akkor a konverziós szolgáltatás az E fájlt fogja használni. Valójában az A, a C és az E fájlok bármelyikét felhasználhatja, ha azok léteznek, és az E-fájl a leghosszabb utótaggal található.
A B és a D fájlokat soha nem fogja használni `Textures\myTexture.png` a rendszer, mert az nem része a tárolt útvonal egyik utótagjának sem.
Ha az eszköz Ehelyett az elérési utakat `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` vagy a `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` -t tartalmazza, akkor a konverziós szolgáltatás megkeresi az A és a C fájlt, ha léteznek (a c-re hivatkozva). Ez a módszer azonban nem található meg, és a fájlt át kell helyezni.
Ezt úgy javíthatja, ha áthelyezi a textúrák mappát az eszköz mellett.

> [!Note]
> Ha a textúrák nem találhatók, a lehetséges megoldás az, hogy az eszköz a textúrákat tartalmazó alfája egy testvére legyen.

## <a name="next-steps"></a>Következő lépések

- [Modell átalakítása](model-conversion.md)
