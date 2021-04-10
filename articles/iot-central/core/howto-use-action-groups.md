---
title: Több művelet futtatása Azure IoT Central-szabályból | Microsoft Docs
description: Futtasson több műveletet egyetlen IoT Central szabályból, és hozzon létre újrafelhasználható műveleteket, amelyek több szabályból is futtathatók.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 84a723023bf8614b837ba9783bae987403921c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585487"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Több művelet csoportosítása egy vagy több szabályból való futtatáshoz

*Ez a cikk az építők és a rendszergazdákra vonatkozik.*

Az Azure IoT Centralban szabályokat hozhat létre a műveletek futtatásához, ha egy feltétel teljesül. A szabályok az eszköz telemetria vagy eseményein alapulnak. Például értesítheti az operátort, ha az eszköz hőmérséklete meghaladja a küszöbértéket. Ez a cikk azt ismerteti, hogyan használhatók [Azure monitor](../../azure-monitor/overview.md) *műveleti csoportok* több művelet egy IoT Central-szabályhoz való csatolásához. A műveleti csoportokat több szabályhoz is csatolhatja. A [műveleti csoport](../../azure-monitor/alerts/action-groups.md) az Azure-előfizetés tulajdonosa által meghatározott értesítési beállítások gyűjteménye.

## <a name="prerequisites"></a>Előfeltételek

- Standard díjszabási csomag használatával létrehozott alkalmazás
- Azure-fiók és-előfizetés Azure Monitor műveleti csoportok létrehozásához és kezeléséhez

## <a name="create-action-groups"></a>Műveletcsoportok létrehozása

[Létrehozhat és kezelhet műveleti csoportokat a Azure Portal](../../azure-monitor/alerts/action-groups.md) vagy egy [Azure Resource Manager sablonnal](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md).

Egy műveleti csoport a következőket teheti:

- Értesítéseket küldhet, például e-mailben, SMS-ben vagy hanghívást készíthet.
- Futtasson egy olyan műveletet, mint például egy webhook hívása.

Az alábbi képernyőfelvételen egy olyan műveleti csoport látható, amely e-mailben és SMS-értesítéseket küld, és egy webhookot hív meg:

![Műveletcsoport](media/howto-use-action-groups/actiongroup.png)

Ha IoT Central szabályban szeretne műveleti csoportot használni, a műveleti csoportnak ugyanabban az Azure-előfizetésben kell lennie, mint a IoT Central alkalmazásnak.

## <a name="use-an-action-group"></a>Műveleti csoport használata

Ha egy műveleti csoportot szeretne használni a IoT Central alkalmazásban, először hozzon létre egy szabályt. Ha műveletet ad hozzá a szabályhoz, válassza ki **Azure monitor műveleti csoportokat**:

![Művelet kiválasztása](media/howto-use-action-groups/chooseaction.png)

Válasszon egy műveleti csoportot az Azure-előfizetésből:

![Műveleti csoport kiválasztása](media/howto-use-action-groups/chooseactiongroup.png)

Kattintson a **Mentés** gombra. A műveleti csoport most megjelenik a szabály indításakor futtatandó műveletek listájában:

![Mentett műveleti csoport](media/howto-use-action-groups/savedactiongroup.png)

A következő táblázat összefoglalja a támogatott tevékenységtípusok számára továbbított adatokat:

| Művelettípus | Kimeneti formátum |
| ----------- | -------------- |
| E-mail       | Standard IoT Central e-mail sablon |
| SMS         | Azure IoT Central riasztás: $ {applicationName}-"$ {ruleName}" aktiválva: "$ {deviceName}" a $ {triggerDate} $ {triggerTime} |
| Hang       | Azure I. O. T központi riasztás: a (z) "$ {ruleName}" szabály aktiválva lett a (z) "$ {deviceName}" eszközön a $ {triggerDate} $ {triggerTime} alkalmazásban a $ {applicationName} alkalmazásban. |
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "adatok": {[normál webhook hasznos](howto-create-webhooks.md#payload)adat}} |

A következő szöveg egy példa SMS-üzenetet küld egy műveleti csoportból:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatók a műveleti csoportok szabályokkal, a javasolt következő lépés az [eszközök kezelésének](howto-manage-devices.md)megismerése.
