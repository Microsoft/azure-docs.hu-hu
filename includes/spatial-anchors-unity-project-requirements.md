---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: 1c2eb0b4350f24ed1b7c4d0f662c7afec3feffeb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669600"
---
Az 2,9-es vagy újabb verzióhoz készült Azure térbeli horgonyok SDK-t a következő csomagok használatával kell konfigurálni az Unity [2020,3 (LTS) egységben](https://unity3d.com/unity/whats-new/2020.3.0) , a [Unity XR beépülő](https://docs.unity3d.com/Manual/XRPluginArchitecture.html) modullal:

- AR Foundation: [4.0.12](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@4.0/manual/index.html)
- Windows XR beépülő modul (HoloLens-eszközök támogatásához): [4.4.1](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr@4.4/manual/index.html)
- ARCore XR beépülő modul (Android-eszközök támogatásához): [4.0.12](https://docs.unity3d.com/Packages/com.unity.xr.arcore@4.0/manual/index.html)
- ARKit XR beépülő modul (iOS-eszközök támogatásához): [4.0.12](https://docs.unity3d.com/Packages/com.unity.xr.arkit@4.0/manual/index.html)

Az Azure térbeli horgonyok SDK 2,8-es vagy korábbi verziói nem támogatják a [Unity XR beépülő moduljának keretrendszerét](https://docs.unity3d.com/Manual/XRPluginArchitecture.html) , és a [(z) 2019,4 (LTS) egységben](https://unity.com/releases/2019-lts) kell konfigurálni a következő csomagok használatával:

- AR Foundation: [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@3.1/manual/index.html)
- Windows vegyes valóság (HoloLens-eszközök támogatásához): [4.2.1](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr.metro@4.2/manual/index.html)
- ARCore XR beépülő modul (Android-eszközök támogatásához): [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arcore@3.1/manual/index.html)
- ARKit XR beépülő modul (iOS-eszközök támogatásához): [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arkit@3.1/manual/index.html)
