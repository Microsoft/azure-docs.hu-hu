---
title: Arcfelismerés – fogalmak
titleSuffix: Azure Cognitive Services
description: Ez a cikk az ellenőrzés, a hasonló, a csoport és az arcfelismerő műveletek, valamint az alapul szolgáló adatstruktúrák fogalmait ismerteti.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518806"
---
# <a name="face-recognition-concepts"></a>Arcfelismerés – fogalmak

Ez a cikk az ellenőrzés, a hasonló, a csoport és az arcfelismerő műveletek, valamint az alapul szolgáló adatstruktúrák fogalmait ismerteti. Széles körben az elismerés leírja a két különböző arc összehasonlításának feltételeit annak megállapításához, hogy hasonlók-e, vagy ugyanahhoz a személyhez tartoznak-e.

## <a name="recognition-related-data-structures"></a>Az elismeréssel kapcsolatos adatstruktúrák

Az elismerési műveletek elsősorban a következő adatstruktúrákat használják. Ezek az objektumok a felhőben tárolódnak, és az azonosító sztringek hivatkozhatnak rájuk. Az azonosító sztringek mindig egyediek az előfizetésen belül. A név mezők többször is megadhatók.

|Név|Leírás|
|:--|:--|
|DetectedFace| Az egyoldalas megjelenítést a [Arcfelismerés](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) művelet kéri le. Az azonosító 24 órával a létrehozása után lejár.|
|PersistedFace| Ha a DetectedFace objektumokat egy csoportba, például FaceList vagy személyhez adja hozzá, akkor a rendszer PersistedFace-objektumokat vált ki. Ezeket bármikor [lekérhetik](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) , és nem járnak le.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) vagy [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Ez az adatstruktúra a PersistedFace objektumok egy választékát tartalmazza. A FaceList egyedi AZONOSÍTÓval, névvel és opcionálisan egy felhasználói adatkarakterlánctal rendelkeznek.|
|[Személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Ez az adatstruktúra azoknak a PersistedFace-objektumoknak a listája, amelyek ugyanahhoz a személyhez tartoznak. Egyedi AZONOSÍTÓval, névvel és opcionálisan egy felhasználói adatsztringtel rendelkezik.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) vagy [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Ez az adatstruktúra a személy objektumok egy vegyes listája. Egyedi AZONOSÍTÓval, névvel és opcionálisan egy felhasználói adatsztringtel rendelkezik. Meg kell [tanítani](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) egy PersonGroup, mielőtt az felismerhető műveletekben felhasználható lenne.|

## <a name="recognition-operations"></a>Felismerési műveletek

Ez a szakasz részletesen ismerteti, hogyan használja a négy felismerési művelet a korábban leírt adatstruktúrákat. Az egyes felismerési műveletek széles leírását az [Áttekintés](../Overview.md)című témakörben tekintheti meg.

### <a name="verify"></a>Ellenőrzés

Az [ellenőrzési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) művelet a DetectedFace vagy a PersistedFace, illetve egy másik Arcfelismerés vagy egy személy objektum azonosítóját veszi figyelembe, és meghatározza, hogy ugyanahhoz a személyhez tartoznak-e. Ha egy személy objektumot továbbít, lehetősége van egy olyan PersonGroup átadására, amelyhez az adott személy a teljesítmény javításához tartozik.

### <a name="find-similar"></a>Hasonló keresése

A [hasonló művelet megkeresése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a DetectedFace vagy a PersistedFace, illetve a FaceList vagy más Arcfelismerés-azonosítók tömbjét veszi át. A FaceList egy kisebb FaceList ad vissza, amely hasonló az adott arc eléréséhez. Az arc-azonosítók tömbje hasonlóan kisebb tömböt ad vissza.

### <a name="group"></a>Group

A [csoportosítási](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) művelet a DetectedFace vagy a PersistedFace különböző számú arc-azonosítót használ, és ugyanazokat az azonosítókat adja vissza, amelyek több kisebb tömbbe vannak csoportosítva. Az egyes "csoportok" tömb olyan Arcfelismerés-azonosítókat tartalmaz, amelyek hasonlónak látszanak. Egyetlen "messyGroup" tömb tartalmaz olyan Arcfelismerés-azonosítókat, amelyekhez nem találhatók hasonlóságok.

### <a name="identify"></a>Azonosítani

Az [azonosítási](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) művelet egy vagy több Arcfelismerés a DetectedFace vagy a PersistedFace, valamint egy PersonGroup, és visszaadja azon személy objektumok listáját, amelyekhez az egyes arcok tartozhatnak. A visszaadott személyek a jelölt objektumként vannak becsomagolva, amelyek előrejelzési megbízhatósági értékkel rendelkeznek.

## <a name="input-data"></a>Bemeneti adatok

A következő tippek segítségével biztosíthatja, hogy a bemeneti képek a legpontosabb felismerési eredményeket adjanak:

* A támogatott bemeneti képformátumok a következők: JPEG, PNG, GIF (az első keret), BMP.
* A képfájl mérete nem lehet hosszabb 6 MB-nál.
* Ha személy objektumokat hoz létre, különféle nézőpontokat és megvilágítást használó képeket használhat.
* Előfordulhat, hogy néhány arc nem ismerhető fel technikai kihívások miatt, például:
  * Rendkívüli megvilágítás, például súlyos háttérvilágítású képek.
  * Az egyik vagy mindkét szemet blokkoló akadály.
  * A haj típusától vagy az arc szőrtől származó különbségek.
  * Az arc megjelenése az életkor miatt változik.
  * Szélsőséges arc kifejezéseket.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri az Arcfelismerés fogalmait, írjon egy olyan parancsfájlt, amely azonosítja az arcokat egy betanított PersonGroup.

* [Arc ügyféloldali kódtár – rövid útmutató](../Quickstarts/client-libraries.md)