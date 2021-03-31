---
title: Az Azure Kinect Sensor SDK-rendszerkép átalakításának használata
description: Ismerje meg, hogyan használhatja az Azure Kinect Sensor SDK rendszerkép-átalakítási funkcióit.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: Kinect, Azure, szenzor, SDK, koordináta rendszer, kalibrálás, projekt, nem projekt, átalakítás, RGB-d, pont felhő
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277266"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Az Azure Kinect Sensor SDK-rendszerkép átalakításának használata

A rendszerképeket az Azure Kinect DK koordinált kamerás rendszerei között követheti és alakíthatja át.

## <a name="k4a_transformation-functions"></a>k4a_transformation függvények

 Az *k4a_transformation* összes funkciója teljes lemezképeken működik. Szükségük van arra, hogy a [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) szolgáltatáson keresztül kapott transzformációs leíró [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) ne legyenek lefoglalva [k4a_transformation_destroy ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44)használatával. Tekintse át az SDK- [transzformáció példáját](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) is, amely bemutatja, hogyan használható a jelen témakörben található három függvény.

A következő függvények tartoznak ide:

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>Áttekintés

 A [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) függvény a mélységi kamera nézőpontjában átalakítja a mélységi térképet a színes kamera szempontjából. Ez a függvény úgy lett kialakítva, hogy az úgynevezett RGB-D rendszerképeket hozza létre, ahol a D egy további, a mélység értékét rögzítő képcsatornát képvisel. Ahogy az alábbi ábrán is látható, a színes kép és a [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) kimenete úgy tűnik, mintha ugyanabból a nézőpontból, azaz a színkamera nézőpontjában vették volna.

   ![Rendszerkép átalakítása](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>Implementálás

 Ez az átalakítási függvény sokkal összetettebb, mint a [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) meghívása minden képponthoz. A háromszög alakú rácsvonalat a színkamera geometriájában a mélységi kamera geometriájában adja vissza. A háromszög háló használatával elkerülhető, hogy a lyukak az átalakított mélységű ábrán legyenek generálva. A Z-Buffer biztosítja, hogy az elzáródások megfelelően legyenek kezelve. Alapértelmezés szerint a GPU-gyorsítás engedélyezve van ehhez a függvényhez.

#### <a name="parameters"></a>Paraméterek

 A bemeneti paraméterek az átalakítási fogópontok és a mélységi képek. A mélységi képfelbontásnak meg kell egyeznie az ```depth_mode``` átalakítási leíró létrehozásához megadott mérettel. Ha például az átalakítási leírót a 1024x1024 mód használatával hozták létre ```K4A_DEPTH_MODE_WFOV_UNBINNED``` , a mélységi kép felbontásának 1024x1024 képpontnak kell lennie. A kimenet egy átalakított részletes rendszerkép, amelyet a felhasználónak kell kiosztania a [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)hívásával. Az átalakított mélységi kép felbontásának meg kell egyeznie az ```color_resolution``` átalakítási leíró létrehozásához megadott értékkel. Ha például a színfelbontás be lett állítva `K4A_COLOR_RESOLUTION_1080P` , a kimeneti képfelbontásnak 1920 × 1080 képpontnak kell lennie. A kimeneti rendszerkép lépéshossz értéke `width * sizeof(uint16_t)` , mivel a rendszerkép 16 bites mélységi értékeket tárol.

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>Áttekintés

 A [k4a_transformation_depth_image_to_color_camera_custom ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) függvény átalakítja a mélységi térképet és egy egyéni rendszerképet a részletes kamera szemszögéből a színes kamera szempontjából. [K4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)bővítményként ez a függvény úgy lett kialakítva, hogy egy megfelelő egyéni rendszerképet hozzon létre, amelyhez az egyes képpontok megfelelnek a színkamera megfelelő képpont-koordinátáinak az átalakított mélységi képhez.

#### <a name="implementation"></a>Implementálás

 Ez a transzformációs függvény ugyanúgy állítja elő az átalakított képet, ahogy [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Az egyéni rendszerkép átalakításához a függvény a lineáris interpoláció vagy a legközelebbi szomszéd interpoláció használatával biztosít lehetőségeket. A lineáris interpoláció használatával új értékeket hozhat létre az átalakított egyéni rendszerképben. A legközelebbi szomszédos interpoláció használata megakadályozza, hogy az eredeti rendszerképben lévő értékek ne jelenjenek meg a kimeneti képen, de a rendszer kevésbé zökkenőmentes képet eredményez. Az egyéni rendszerképnek egy 8 vagy 16 bites csatornának kell lennie. Alapértelmezés szerint a GPU-gyorsítás engedélyezve van ehhez a függvényhez.

#### <a name="parameters"></a>Paraméterek

 A bemeneti paraméterek az átalakítási fogópontok, a mélységi képek, az Egyéni rendszerképek és az interpoláció típusa. A mélységi képnek és az egyéni képfelbontásnak meg kell egyeznie az `depth_mode` átalakítási leíró létrehozásához megadott mérettel. Ha például az átalakítási leírót a 1024x1024 mód használatával hozták létre `K4A_DEPTH_MODE_WFOV_UNBINNED` , a mélységi kép és az egyéni kép felbontásának 1024x1024 képpontnak kell lennie. A a `interpolation_type` következők egyike lehet: `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` vagy `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` . A kimenet egy átalakított részletes rendszerkép és egy átalakított egyéni rendszerkép, amelyet a felhasználónak kell kiosztania a [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)hívásával. Az átalakított mélységi kép és az átalakított egyéni rendszerkép felbontásának meg kell egyeznie az `color_resolution` átalakítási leíró létrehozásához megadott értékkel. Ha például a színfelbontás be lett állítva `K4A_COLOR_RESOLUTION_1080P` , a kimeneti képfelbontásnak 1920 × 1080 képpontnak kell lennie. A kimeneti mélységi képet a rendszer a értékre állítja `width * sizeof(uint16_t)` , mivel a rendszerkép 16 bites mélységi értékeket tárol. A bemeneti egyéni rendszerképnek és az átalakított egyéni képnek formátumnak kell lennie `K4A_IMAGE_FORMAT_CUSTOM8` `K4A_IMAGE_FORMAT_CUSTOM16` , vagy ennek megfelelően be kell állítani a megfelelő képet. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>Áttekintés

 A [k4a_transformation_color_image_to_depth_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) függvény átalakítja a színképet a színkamera nézőpontból a részletes kamera szempontjából (lásd a fenti ábrát). Az RGB-D rendszerképek előállítására is használható.

#### <a name="implementation"></a>Implementálás

 A mélységi Térkép minden képpontjában a függvény a képpont mélységi értékével számítja ki a megfelelő alképpont-koordinátákat a színes képen. Ezután megkeresjük a színértéket ebben a koordinátaban a színes képen. A bilineáris interpolációt a színes képen kell elvégezni a színértéknek az alképpont pontosságnál való beszerzéséhez. Egy olyan képpont, amely nem rendelkezik hozzá tartozó mélységi olvasással, a kimeneti képen egy BGRA értékhez van rendelve `[0,0,0,0]` . Alapértelmezés szerint a GPU-gyorsítás engedélyezve van ehhez a függvényhez. Mivel ez a módszer lyukakat hoz létre az átalakított színben, és nem kezeli az elzáródásokat, javasoljuk, hogy használja a [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) függvényt.

#### <a name="parameters"></a>Paraméterek

A bemeneti paraméterek az átalakítási fogópontok, a mélységi képek és a színes képek. A mélységi és Színképek felbontásának meg kell egyeznie az átalakítási leíró létrehozásakor megadott depth_mode és color_resolutionval. A kimenet egy átalakított színes rendszerkép, amelyet a felhasználónak kell kiosztania a [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)hívásával. Az átalakított színes kép felbontásának meg kell egyeznie az átalakítási leíró létrehozásakor megadott depth_resolution. A kimeneti rendszerkép a BGRA jelölő 4 8 bites értékeket tárolja minden képponthoz. Ezért a rendszerkép lépése a következő: ```width * 4 * sizeof(uint8_t)``` . Az adatsorrend képpont-összekapcsolású, azaz kék érték – képpont 0, zöld érték – képpont 0, piros érték – képpont 0, alfa érték – képpont 0, kék érték – képpont 1 stb.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>Áttekintés

A [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) függvény egy kamera 2D mélységi térképét átalakítja egy 3D-s felhőbe az azonos kamera koordináta-rendszerében. A kamera így a mélységi vagy a színes kamera lehet.

#### <a name="implementation"></a>Implementálás

 A függvény a [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) minden képponthoz való futtatásának egyenértékű eredményét biztosítja, de a számítási hatékonysága hatékonyabb. [K4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10)hívásakor a rendszer előre kiszámít egy úgynevezett XY keresési táblázatot, amely az x és az y méretezési tényezőt tárolja minden képponthoz. [K4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)hívásakor egy képpont 3D x-koordinátákat kapunk, ha a képpont x méretezési tényezőjét a képpont Z-koordináta értékének szorzatával kapjuk meg. A 3D Y-koordináta Analogously az y méretezési faktorral történő szorzással számítjuk ki. Az SDK [gyors pontszerű Felhőbeli példája](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) azt mutatja be, hogyan történik az XY tábla kiszámítása. A felhasználók követhetik a példában szereplő kódot a függvény saját verziójának megvalósításához, például a GPU-folyamat felgyorsításához.

#### <a name="parameters"></a>Paraméterek

 A bemeneti paraméterek a transzformációs fogantyú, a kamera megadása és a mélységi képek. Ha a kamera megadása mélységre van állítva, a mélységi kép felbontásának meg kell egyeznie az átalakítási leíró létrehozásakor megadott depth_mode. Ellenkező esetben, ha a megadott érték a színkamera, a felbontásnak meg kell egyeznie a kiválasztott color_resolution felbontásával. A kimeneti paraméter egy XYZ-rendszerkép, amelyet a felhasználónak kell kiosztania a [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef)hívásával. Az XYZ-rendszerkép felbontásának meg kell egyeznie a bemeneti mélységi Térkép felbontásával. Minden képpont esetében három, az aláírt 16 bites koordináta-értéket tárolunk milliméterben. Ennek megfelelően az XYZ-rendszerkép Stride-ra van beállítva `width * 3 * sizeof(int16_t)` . Az adatsorrend képpont-összekapcsoló, azaz X-koordináta – pixel 0, Y koordináta – pixel 0, Z-koordináta – pixel 0, X-koordináta – pixel 1 és így tovább. Ha a képpont nem alakítható át 3D-re, a függvény az értékeket `[0,0,0]` a képponthoz rendeli.

## <a name="samples"></a>Példák

[Transzformációs példa](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>Következő lépések

Most már tudja, hogyan használhatja az Azure Kinect Sensor SDK rendszerkép-átalakítási funkcióit, továbbá megismerheti a következőt is

>[!div class="nextstepaction"]
>[Azure Kinect Sensor SDK-kalibrációs függvények](use-calibration-functions.md)

Tekintse át az alábbiakat is

[Koordináta-rendszerek](coordinate-systems.md)
