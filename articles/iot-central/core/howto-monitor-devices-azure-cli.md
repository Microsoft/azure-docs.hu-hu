---
title: Az eszközök kapcsolatának figyelése az Azure IoT Central Explorer használatával
description: Az eszközök üzeneteinek figyelése és az eszköz két módosításának megfigyelése az IoT Central Explorer parancssori felületén keresztül.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 62981686c7aadc713c4abc78075be8613fe0af45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199306"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Eszközkapcsolatok monitorozása az Azure CLI-vel

*Ez a témakör az eszközök fejlesztőire és megoldás-építői vonatkozik.*

Az Azure CLI IoT bővítmény használatával megtekintheti, hogy az eszközök milyen üzeneteket küldenek IoT Centralnek, és megfigyelik a Twin eszközök változásait. Ezt az eszközt használhatja az eszközök kapcsolatának hibakeresésére és figyelésére, valamint a felhőbe nem lépő eszközök üzeneteinek diagnosztizálására, illetve a kettős módosításokra nem válaszoló eszközökre.

[További részletekért tekintse meg az Azure CLI-bővítmények referenciáját](/cli/azure/ext/azure-iot/iot/central)

## <a name="prerequisites"></a>Előfeltételek

+ Az Azure CLI telepítve van, és a verziója 2.7.0 vagy újabb. Az Azure CLI verziójának futtatásával keresse meg az alkalmazást `az --version` . Ismerje meg, hogyan telepítheti és frissítheti az [Azure CLI-docs](/cli/azure/install-azure-cli)
+ Munkahelyi vagy iskolai fiók az Azure-ban, amely felhasználóként van hozzáadva egy IoT Central alkalmazásban.

## <a name="install-the-iot-central-extension"></a>Az IoT Central-bővítmény telepítése

Futtassa a következő parancsot a parancssorból a telepítéshez:

```azurecli
az extension add --name azure-iot
```

A bővítmény verziójának ellenőrzéséhez futtassa a következőket:

```azurecli
az --version
```

Az Azure-IOT bővítménynek 0.9.9 vagy magasabbnak kell lennie. Ha nem, futtassa a következőket:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>A bővítmény használata

A következő szakaszok a futtatáskor használható általános parancsokat és beállításokat ismertetik `az iot central` . A parancsok és beállítások teljes készletének megtekintéséhez továbbítsa a `--help` `az iot central` parancsot vagy annak bármelyik alparancsát.

### <a name="login"></a>Bejelentkezés

Először jelentkezzen be az Azure CLI-be. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>A IoT Central-alkalmazás alkalmazás-AZONOSÍTÓjának beolvasása
Az **Adminisztráció/alkalmazás beállításaiban** másolja az **alkalmazás azonosítóját**. Ezt az értéket a későbbi lépésekben használhatja.

### <a name="monitor-messages"></a>Üzenetek figyelése
A IoT Central alkalmazásnak az eszközökről küldött üzeneteinek figyelése. A kimenet tartalmazza az összes fejlécet és megjegyzést.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Eszköz tulajdonságainak megtekintése
Megtekintheti egy adott eszköz aktuális olvasási és olvasási/írási/olvasási eszközének tulajdonságait.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, javasolt következő lépésként olvassa el az [eszköz kapcsolatát az Azure IoT Central-ban](./concepts-get-connected.md)című témakört.