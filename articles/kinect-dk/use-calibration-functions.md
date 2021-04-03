---
title: Az Azure Kinect kalibrációs függvények használata
description: Ismerje meg, hogyan használhatja az Azure Kinect DK kalibrációs funkcióit.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, szenzor, SDK, koordináta rendszer, kalibrálás, függvények, kamera, belső, külső, projekt, nem projekt, átalakítás, RGB-d, pont felhő
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277288"
---
# <a name="use-azure-kinect-calibration-functions"></a>Az Azure Kinect kalibrációs függvények használata

A kalibrációs függvények lehetővé teszik a pontok közötti átalakítást az Azure Kinect-eszközön lévő egyes érzékelők koordináta-rendszerei között. A teljes lemezképek átalakítását igénylő alkalmazások igénybe vehetik az [átalakítási függvények](use-image-transformation.md)által elérhető gyorsított műveleteket.

## <a name="retrieve-calibration-data"></a>Kalibrációs érték beolvasása

A koordináta-rendszer átalakításának végrehajtásához le kell kérni az eszköz kalibrálását. A kalibrációs adattípust a rendszer a [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) adattípus tárolja. A rendszer az eszközről szerzi be a [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)függvény használatával. A kalibrációs érték nem csupán az egyes eszközökre vonatkozik, hanem a kamerák működési módjára is. Ezért a [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) függvénynek bemenetként kell megadnia a `depth_mode` és a `color_resolution` paramétereket.

### <a name="opencv-compatibility"></a>OpenCV kompatibilitása

A kalibrációs paraméterek kompatibilisek a [OpenCV](https://opencv.org/). Az egyes kamera-kalibrációs paraméterekkel kapcsolatos további információkért lásd a [OpenCV dokumentációját](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c)is. Lásd még: az SDK [OpenCV kompatibilitási példája](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) , amely a [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) típus és a megfelelő OpenCV adatstruktúrák közötti átalakítást mutatja be.

## <a name="coordinate-transformation-functions"></a>Az átalakítási függvények koordinálása

Az alábbi ábra az Azure Kinect különböző koordináta-rendszereit, valamint a közöttük konvertálandó funkciókat mutatja be. Kihagyjuk a giroszkóp és gyorsulásmérő 3D koordináta rendszerét, hogy az ábra egyszerűen megmaradjon.

   ![Koordináta-transzformáció](./media/how-to-guides/coordinate-transformation.png)

A lencse torzítására vonatkozó Megjegyzés: a 2D-koordináták mindig az SDK-ban található torzított képre hivatkoznak. Az SDK [torzítási példája](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) azt mutatja be, hogy a rendszerkép torzítása nem torzult. Általánosságban elmondható, hogy az objektív torzítása soha nem befolyásolja a 3D-pontokat.

### <a name="convert-between-3d-coordinate-systems"></a>Konvertálás 3D koordináta-rendszerek között

A [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) függvény átalakítja a forrás koordináta-rendszer 3D pontját a cél koordináta-rendszer 3D pontjára a kamera külső kalibrálásával. A forrás és a cél a négy 3D koordináta rendszer bármelyikére beállítható, azaz a színes kamera, a mélységi kamera, a giroszkóp vagy a gyorsulásmérő. Ha a forrás és a cél azonos, a rendszer a nem módosított bemeneti 3D pontot adja vissza kimenetként.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>Konvertálás 2D és 3D koordináta rendszerek között

A [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) függvény a forrás koordináta-rendszer 3D pontját átalakítja a cél kamera 2D képpont koordinátája alapján. Ezt a függvényt gyakran a Project függvénynek nevezzük. Míg a forrás a négy 3D koordináta-rendszer bármelyikére beállítható, a cél csak a mélység vagy a színes kamera lehet. Ha a forrás és a cél eltérő, a bemeneti 3D pont a [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)használatával konvertálódik a cél kamera 3D koordináta rendszerére. Miután a 3D pont szerepel a cél kamera koordináta-rendszeren, a megfelelő 2D képpont koordinátákat a cél kamera belső kalibrálása alapján számítjuk ki. Ha egy 3D pont kiesik a cél kamera látható területéről, az érvényes érték 0.

A [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) függvény átalakítja a forrás kamera 2D képpontjának koordinátáit a cél kamera koordináta-rendszer 3D pontjára. A forrásnak színesnek vagy mélységi kamerának kell lennie. A cél beállítható a négy 3D koordináta-rendszer bármelyikére. A 2D képpont koordináta mellett a forrás kamera rendszerképének képpont (milliméterben mért) mélységi értékének a függvény bemenetének kell lennie, az egyik módszer a színkamera geometriájának mélységi értékének származtatása, ha a [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)függvényt használja. A függvény kiszámítja a forrás kamera fókuszpontján keresztül a 3D Ray-t a megadott képpont-koordináta használatával a forrás kamera belső kalibrálásával. Ezt követően a mélységi érték megkeresi a 3D pont pontos helyét a Ray-on. Ezt a műveletet gyakran nevezik unproject függvénynek. Ha a forrás-és a cél kamerák eltérőek, a függvény átalakítja a 3D pontot a cél koordináta-rendszerére [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)segítségével. Ha egy 2D képpont-koordináta kiesik a forrás kamera látható területéről, akkor az érvényes érték 0-ra van állítva.

### <a name="converting-between-2d-coordinate-systems"></a>2D koordináta-rendszerek közötti átalakítás

A [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) függvény átalakítja a forrás kamera 2D képpontjának koordinátáit a cél kamera 2D képpontos koordinátáiba. A forrást és a célt a kamera színére vagy mélységére kell beállítani. A függvényhez a képpont mélységének (milliméterben megadott) értékének kell lennie a forrásként szolgáló kamera rendszerképében bemenetként, az egyik módszer, hogy a színkamera geometriájában a mélység értékét származtatja, a [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)függvényt használja. Meghívja a [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) metódust, hogy a forrásként szolgáló kamerarendszer 3D pontjára váltson át. Ezután meghívja a [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) metódust, hogy a cél kamera képének 2D képpont-koordinátáira váltson. Az érvényes érték 0, ha [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) vagy a [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) érvénytelen eredményt ad vissza.

## <a name="related-samples"></a>Kapcsolódó minták

- [OpenCV kompatibilitási példa](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Torzítási példa](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [Példa gyors felhőre](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a kamera-kalibrálásokat, megtudhatja, hogyan lehet
>[!div class="nextstepaction"]
>[Eszközszinkronizálás rögzítése](capture-device-synchronization.md)

Tekintse át az alábbiakat is

[Koordináta-rendszerek](coordinate-systems.md)
