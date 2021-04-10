---
title: Az Azure IoT Central alkalmazás beállításainak módosítása | Microsoft Docs
description: Rendszergazdaként az alkalmazás nevének, URL-címének, feltöltésének és az alkalmazás törlésének megváltoztatásával hogyan kezelheti Azure IoT Central alkalmazását.
author: viv-liu
ms.author: viviali
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 81cd2ca1cb47e6fdfb4858df930b73c1bd10118a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091821"
---
# <a name="change-iot-central-application-settings"></a>IoT Central alkalmazás beállításainak módosítása



Ez a cikk azt ismerteti, hogyan kezelheti az alkalmazást rendszergazdaként az alkalmazás nevének és URL-címének módosításával, a rendszerkép feltöltésével és az alkalmazás törlésével az Azure IoT Central alkalmazásban.

Az **Adminisztráció** szakasz eléréséhez és használatához **rendszergazdai** szerepkörrel kell rendelkeznie egy Azure IoT Central-alkalmazáshoz. Ha létrehoz egy Azure IoT Central alkalmazást, a rendszer automatikusan hozzárendeli az adott alkalmazáshoz tartozó **rendszergazdai** szerepkörhöz.

## <a name="change-application-name-and-url"></a>Alkalmazás nevének és URL-címének módosítása

Az **Alkalmazásbeállítások** lapon módosíthatja az alkalmazás nevét és URL-címét, majd válassza a **Mentés** lehetőséget.

![Alkalmazásbeállítások lap](media/howto-administer/image0-a.png)

Ha a rendszergazda egyéni témát hoz létre az alkalmazáshoz, ez a lap tartalmaz egy lehetőséget az **alkalmazás nevének** elrejtésére a felhasználói felületen. Ez a beállítás akkor hasznos, ha az egyéni téma alkalmazásának emblémája tartalmazza az alkalmazás nevét. További információ: [Az Azure IoT Central felhasználói felületének testreszabása](./howto-customize-ui.md).

> [!Note]
> Ha megváltoztatja az URL-címet, a régi URL-címet egy másik Azure IoT Central ügyfél is elvégezheti. Ha ez történik, már nem használható. Ha megváltoztatja az URL-címet, a régi URL-cím már nem működik, és értesítenie kell a felhasználókat a használni kívánt új URL-címről.

## <a name="delete-an-application"></a>Alkalmazás törlése

Az IoT Central alkalmazás végleges törléséhez használja a **delete (Törlés** ) gombot. Ez a művelet véglegesen törli az alkalmazáshoz társított összes adatmennyiséget.

> [!Note]
> Egy alkalmazás törléséhez az alkalmazás létrehozásakor kiválasztott Azure-előfizetésben is rendelkeznie kell az erőforrások törléséhez szükséges engedélyekkel. További információk: [Azure-szerepkörök társítása az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez](../../role-based-access-control/role-assignments-portal.md).

## <a name="manage-programmatically"></a>Programozott kezelés

IoT Central Azure Resource Manager SDK-csomagok a Node, a Python, a C#, a Ruby, a Java és a go esetében érhetők el. Ezeket a csomagokat IoT Central-alkalmazások létrehozásához, listázásához, frissítéséhez vagy törléséhez használhatja. A csomagok közé tartoznak a hitelesítés és a hibakezelés kezeléséhez szükséges segítők.

Példákat talál arra, hogyan használhatja a Azure Resource Manager SDK-kat a következő helyen: [https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples) .

További információért lásd a következő GitHub-adattárakat és-csomagokat:

| Nyelv | Adattár | Csomag |
| ---------| ---------- | ------- |
| Csomópont | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az Azure IoT Central-alkalmazás felügyeletének módját, a javasolt következő lépés a [felhasználók és szerepkörök kezelése](howto-manage-users-roles.md) az Azure IoT Central-ban című témakörben.
