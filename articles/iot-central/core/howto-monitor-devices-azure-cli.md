---
title: Eszközkapcsolatok figyelése a Azure IoT Central Explorerrel
description: Figyelje az eszközüzeneteket, és figyelje meg az ikereszköz változásait az IoT Central Explorer CLI-s használatával.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 30a823b7e78145224929b427e9e37522a7e29f09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871290"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Eszközkapcsolatok monitorozása az Azure CLI-vel

*Ez a témakör az eszközfejlesztőkre és a megoldáskészítőkre vonatkozik.*

Az Azure CLI IoT-bővítmény használatával láthatja az eszközök által a IoT Central küldött üzeneteket, és megfigyelheti az ikereszköz változásait. Ezzel az eszközzel hibakeresést és az eszközkapcsolat megfigyelését, valamint az olyan eszközüzenetek problémáit diagnosztizálhatja, amelyek nem érik el a felhőt vagy az ikereszközök nem válaszolnak az ikereszközök változásaira.

[További részletekért keresse fel az Azure CLI-bővítmények referenciáját](/cli/azure/iot/central)

## <a name="prerequisites"></a>Előfeltételek

+ Az Azure CLI telepítve van, és 2.7.0-s vagy újabb verziójú. Ellenőrizze az Azure CLI verzióját az `az --version` futtatásával. Megtudhatja, hogyan telepíthet és frissíthet az [Azure CLI-dokumentumokból](/cli/azure/install-azure-cli)
+ Munkahelyi vagy iskolai fiók az Azure-ban, felhasználóként hozzáadva egy IoT Central alkalmazásban.

## <a name="install-the-iot-central-extension"></a>Az IoT Central bővítmény telepítése

A telepítéshez futtassa a következő parancsot a parancssorból:

```azurecli
az extension add --name azure-iot
```

Ellenőrizze a bővítmény verzióját a következő futtatásával:

```azurecli
az --version
```

Az azure-iot bővítménynek 0.9.9-es vagy újabb verziónak kell lennie. Ha nem, futtassa a következőt:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>A bővítmény használata

A következő szakaszok a futtatásakor használható általános parancsokat és beállításokat `az iot central` ismertetik. A parancsok és beállítások teljes készletének megtekintéséhez adja meg a parancsot vagy annak bármely `--help` `az iot central` alparancsát.

### <a name="login"></a>Bejelentkezés

Először is bejelentkezik az Azure CLI-be. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>A saját alkalmazás alkalmazásazonosítójának IoT Central le
Az **Adminisztráció/Alkalmazásbeállítások lapon** másolja ki az **alkalmazásazonosítót.** Ezt az értéket a későbbi lépésekben használhatja.

### <a name="monitor-messages"></a>Üzenetek figyelése
Figyelje az eszközökről a IoT Central alkalmazásnak küldött üzeneteket. A kimenet minden fejlécet és jegyzetet tartalmaz.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Eszköztulajdonságok megtekintése
Megtekintheti az adott eszköz aktuális olvasási és olvasási/írási eszköztulajdonságokat.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Következő lépések

Ha Ön eszközfejlesztő, a következő javasolt lépés az eszközkapcsolatról való olvasás a [Azure IoT Central.](./concepts-get-connected.md)