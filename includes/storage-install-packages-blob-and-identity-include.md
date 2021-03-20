---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74806582"
---
## <a name="install-client-library-packages"></a>Ügyféloldali függvénytár-csomagok telepítése

> [!NOTE]
> Az itt bemutatott példák az Azure Storage ügyféloldali kódtár 12-es verzióját használják. A 12-es verziójú ügyféloldali kódtár az Azure SDK része. Az Azure SDK-val kapcsolatos további információkért tekintse meg az Azure SDK-tárházat a [githubon](https://github.com/Azure/azure-sdk).

A blob Storage-csomag telepítéséhez futtassa a következő parancsot a NuGet Package Manager konzolról:

```powershell
Install-Package Azure.Storage.Blobs
```

Az itt bemutatott példák a [.net-hez készült Azure Identity Client Library](https://www.nuget.org/packages/Azure.Identity/) legújabb verzióját is használják az Azure ad-beli hitelesítő adatokkal való hitelesítéshez. A csomag telepítéséhez futtassa a következő parancsot a NuGet Package Manager konzolról:

```powershell
Install-Package Azure.Identity
```
