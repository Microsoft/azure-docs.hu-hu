---
title: Azure Kinect DK – Windows összehasonlítása
description: Hardveres és szoftveres különbségek az Azure Kinect DK és a Windows v2 rendszerhez készült Kinect között
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, összehasonlítás, SDK, különbségek, hardver, szoftver
ms.openlocfilehash: 1791767e842043bbb0eda058cc3ea92be90a00fb
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078298"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Az Azure Kinect és a Kinect Windows v2 összehasonlítása

Az Azure Kinect DK hardver-és szoftverfejlesztői készletei eltérnek a Windows v2 Kinect-verziójától. A Windows v2 rendszerhez készült meglévő Kinect-alkalmazások nem fognak közvetlenül működni az Azure Kinect DK-mel, és az új SDK-ra való csatlakozásra lesz szükség.  

## <a name="hardware"></a>Hardver

Az Azure Kinect Development Kit és a Windows v2 rendszerhez készült Kinect közötti magas szintű különbségek az alábbi táblázatban láthatók.

| Szolgáltatás | Típus | Azure Kinect DK | Windows v2 Kinect |
| ------- | ---- | --------------- | --------------------- |
| **Audió** | Részletek  | 7 – MIC kör alakú tömb | 4 – MIC lineáris fázisú tömb |
| **Mozgásérzékelő** | Részletek | 3 tengelyes gyorsulásmérő 3 tengelyes giroszkóp | 3 tengelyes gyorsulásmérő |
| **RGB-kamera**    | Részletek | 3840 x 2160 px @30 fps | 1920 x 1080 px @30 fps |
| **Részletes kamera**  | Metódus   | Repülési idő | Repülési idő |
|                   | Feloldás | 640 x 576 px @30 fps | 512 x 424 px @ 30 fps |
|                   |            | 512 x 512 px @30 fps |                       |
|                   |            | 1024x1024 px @15 fps |                       |
| **Kapcsolódás** | Adatok | USB 3.1 Gen 1, USB-C típussal  | USB 3,1 Gen 1|
|  | Energiaellátás | Külső PSU vagy USB-C | Külső PSU |
|  | Szinkronizálás | RGB & belső, külső eszköz – eszköz| RGB & csak belső mélységben |
| **Elejét vehesse** | Dimenziók | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | Háttértár | 440 g | 970 g |
| | Szerelési | Egy 1/4-20 UNC. Négy belső csavaros pont | Egy 1/4-20 UNC |

További részleteket az [Azure Kinect DK hardveres](hardware-specification.md) dokumentumában talál.

## <a name="sensor-access"></a>Érzékelő hozzáférése

Az alábbi táblázat az alacsony szintű eszköz-érzékelő hozzáférési képességének összehasonlítását tartalmazza.

| **Funkció**| **Azure Kinect** | **Windows rendszerhez készült Kinect** | **Megjegyzések** |
|---------|---------|------------|---------|
| **Mélység** | ✔️ | ✔️ |    | 
| **IR** | ✔️ | ✔️ |  |
| **Szín** | ✔️ | ✔️ | A Színformátum támogatja a különbségeket, az Azure Kinect DK a következő kamera-vezérlőket támogatja: expozíció, fehéregyensúly, fényerő, kontraszt, telítettség, élesség és hozzáférés |
| **Audió** | ✔️ | ✔️ | Az Azure Kinect DK mikrofonok a Speech SDK vagy a Windows natív API használatával érhetők el |
| **IMU** | ✔️ |  | Az Azure Kinect DK teljes 6 tengelyes IMU rendelkezik, a Windows Kinect pedig csak 1 tengelyt biztosít. |
| **Kalibrálási adatértékek** | ✔️ | ✔️ | OpenCV-kompatibilis kamera-modell kalibrálása |
| **Mélység – RGB belső szinkronizálás** | ✔️ | ✔️ |  |
| **Külső szinkronizálás**| ✔️|  | Az Azure Kinect DK lehetővé teszi a külső szinkronizáláshoz való programozható késleltetést |
| **Hozzáférés megosztása több ügyféllel** | | ✔️ | Az Azure Kinect Sensor SDK az WinUSB/libUSB az eszköz elérésére támaszkodik, és nem rendelkezik olyan szolgáltatással, amely lehetővé teszi az eszközök hozzáférésének több folyamattal való megosztását. |
| **Stream Record/lejátszás eszköz** | ✔️ | ✔️ | Az Azure Kinect DK nyílt forráskódú Matroska Container-alapú implementációt használ |

## <a name="features"></a>Funkciók

Az Azure Kinect SDK szolgáltatáskészlet különbözik a Kinect for Windows v2 szolgáltatástól, az alábbiakban részletezett módon:

| **Kinect v2 funkció** | **Kinect v2 – adattípus** | **Azure Kinect SDK/szolgáltatás** |
|--------|--------|------|
| Érzékelő adathozzáférése |DepthFrame| [Sensor SDK – lemezképek lekérése](retrieve-images.md) 
| |InfraredFrame | [Sensor SDK – lemezképek lekérése](retrieve-images.md) 
| | ColorFrame | [Sensor SDK – lemezképek lekérése](retrieve-images.md) | 
| | AudioBeamFrame |Egyelőre nem támogatott 
| Törzs nyomon követése | BodyFrame | Body Tracking SDK |
| | BodyIndexFrame | Body Tracking SDK  |
| Koordináta-leképezés|CoordinateMapper| [Sensor SDK – rendszerkép-átalakítások](use-image-transformation.md) |
|Arcfelismerés | FaceFrame | [Cognitive Services: Face](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Beszédfelismerés    |    N/A                      |    [Cognitive Services: beszéd](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>Következő lépések

[Kinect a Windows fejlesztői oldalaihoz](https://developer.microsoft.com/windows/kinect)
