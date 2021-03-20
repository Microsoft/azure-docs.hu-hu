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
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017495"
---
Amikor az oktatóanyag későbbi részében futtatja a minta-eszköz alkalmazást, a következő konfigurációs értékekre lesz szüksége:

* AZONOSÍTÓ hatóköre: a IoT Central alkalmazásban navigáljon a **felügyelet > eszköz kapcsolatai** elemre. Jegyezze fel az **azonosító hatókörének** értékét.
* Csoport elsődleges kulcsa: a IoT Central alkalmazásban navigáljon a **felügyelet > eszköz-kapcsolódás > sas-IoT-Devices** elemre. Jegyezze fel a közös hozzáférési aláírás **elsődleges kulcsának** értékét.

Az imént beolvasott SAS-kulcsból a Cloud Shell használatával hozhatja elő az eszköz kulcsát:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Jegyezze fel a generált eszköz kulcsát, amelyet később az oktatóanyagban fog használni.
