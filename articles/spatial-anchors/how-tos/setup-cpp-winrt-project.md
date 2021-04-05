---
title: Azure térbeli horgonyok telepítése C++/WinRT HoloLens-alkalmazáshoz
description: C++/WinRT HoloLens-projekt konfigurálása az Azure térbeli horgonyok használatára
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 69134ef87d90fe69de2d9e4e9222e90f65edc785
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95507001"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Azure térbeli horgonyok konfigurálása C++/WinRT HoloLens projektben

## <a name="requirements"></a>Követelmények

* A [Visual Studio 2019](https://www.visualstudio.com/downloads/) telepítve van a **univerzális Windows-platform fejlesztési** számítási feladattal és a **Windows 10 SDK-val (10.0.18362.0 vagy újabb)** .

## <a name="configuring-a-project"></a>Projekt konfigurálása

A HoloLens és C++/WinRT Azure térbeli horgonyai a [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet csomag használatával terjeszthetők.

Kövesse az [itt](/nuget/consume-packages/install-use-packages-visual-studio) található utasításokat a Visual Studio NuGet Package Manager használatával a [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet csomag telepítéséhez a projektben.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató: horgonyok létrehozása és megkeresése a C++/WinRT](./create-locate-anchors-cpp-winrt.md)