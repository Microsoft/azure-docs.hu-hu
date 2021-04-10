---
title: Az Azure Kinect Sensor SDK rendszerkövetelményei
description: Ismerje meg az Azure Kinect Sensor SDK rendszerkövetelményeit Windows és Linux rendszeren.
author: qm13
ms.author: quentinm
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: Azure, Kinect, rendszerkövetelmények, CPU, GPU, USB, beállítás, telepítés, minimum, követelmények
ms.openlocfilehash: 558c1b9ca264874fa808aeba5f1e8a809a8f722d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656974"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Az Azure Kinect Sensor SDK rendszerkövetelményei

Ez a dokumentum részletesen ismerteti az Sensor SDK telepítéséhez és az Azure Kinect DK sikeres üzembe helyezéséhez szükséges rendszerkövetelményeket.

## <a name="supported-operating-systems-and-architectures"></a>Támogatott operációs rendszerek és architektúrák

- Windows 10 április 2018 (1803-es verzió, OS Build 17134) kiadás (x64) vagy újabb verzió
- Linux Ubuntu 18,04 (x64), OpenGLv 4.4-et vagy újabb verziót használó GPU-illesztővel

Az Sensor SDK elérhető a Windows API (Win32) számára a natív C/C++ Windows-alkalmazásokhoz. Az SDK jelenleg nem érhető el UWP-alkalmazások számára. Az Azure Kinect DK nem támogatott a Windows 10 S módban.

## <a name="development-environment-requirements"></a>A fejlesztési környezet követelményei

Az érzékelő SDK-fejlesztéshez való hozzájáruláshoz látogasson el a [githubra](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK).

## <a name="minimum-host-pc-hardware-requirements"></a>Gazdagép minimális hardverkövetelmények

A számítógép gazdagépének hardverkövetelmények a gazdagépen futtatott alkalmazás/algoritmus/érzékelő Képkockasebesség/-megoldástól függenek. A Windowshoz készült ajánlott minimális Sensor SDK-konfiguráció:

- Hetedik generációs Intel &reg; Core i3 processzor (Dual Core 2,4 GHz, HD620 GPU vagy gyorsabb)
- 4 GB memória
- Dedikált USB3-port
- Grafikus illesztőprogram-támogatás az OpenGL 4,4 vagy a DirectX 11,0

Az alacsonyabb vagy régebbi processzorok a használati esettől függően is működhetnek.

A teljesítmény a Windows/Linux operációs rendszerek és a használatban lévő grafikus illesztőprogramok között is eltér.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Body Tracking Host PC-hardverre vonatkozó követelmények

A Body Tracking PC-gazdagépre vonatkozó követelmény szigorúbb, mint az általános PC-gazdagépre vonatkozó követelmény. A Windows minimálisan ajánlott követési SDK-konfigurációja:

- Hetedik generációs Intel &reg; Core i5 processzor (Quad Core 2,4 GHz vagy gyorsabb)
- 4 GB memória
- NVIDIA GEFORCE GTX 1050 vagy azzal egyenértékű
- Dedikált USB3-port

Az ajánlott minimális konfiguráció az 5 személy 30fps követése K4A_DEPTH_MODE_NFOV_UNBINNED mélységi módot feltételezi. Az alacsonyabb vagy régebbi processzorok és az NVIDIA GPU-k is működhetnek a használati esettől függően.

## <a name="usb3"></a>USB3

Az USB-állomások ismert kompatibilitási problémákkal rendelkeznek. További információt a [hibaelhárítási oldalon](troubleshooting.md#usb3-host-controller-compatibility) talál

## <a name="next-steps"></a>Következő lépések

- [Az Azure Kinect DK áttekintése](about-azure-kinect-dk.md)

- [Az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)

- [Az Azure Kinect Body követésének beállítása](body-sdk-setup.md)
