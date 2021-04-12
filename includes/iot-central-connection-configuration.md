---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491163"
---
Amikor az oktatóanyag későbbi részében futtatja a minta-eszköz alkalmazást, a következő konfigurációs értékekre lesz szüksége:

* AZONOSÍTÓ hatóköre: a IoT Central alkalmazásban navigáljon a **felügyelet > eszköz kapcsolatai** elemre. Jegyezze fel az **azonosító hatókörének** értékét.
* Csoport elsődleges kulcsa: a IoT Central alkalmazásban navigáljon a **felügyelet > eszköz-kapcsolódás > sas-IoT-Devices** elemre. Jegyezze fel a közös hozzáférési aláírás **elsődleges kulcsának** értékét.

A Cloud Shell használatával állítson elő egy eszközt a csoport elsődleges kulcsáról:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

Jegyezze fel a generált eszköz kulcsát, amelyet később az oktatóanyagban fog használni.
