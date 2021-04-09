---
title: Az erőforrás állapotának értelmezése
titleSuffix: Azure Digital Twins
description: 'Lásd: a Azure Resource Health használata az Azure Digital Twins-példány állapotának vizsgálatához.'
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94616549"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Az Azure Digital Twins hibaelhárítása: erőforrás állapota

[Azure Resource Health](../service-health/resource-health-overview.md) segítséget nyújt az Azure-erőforrásokat érintő szolgáltatási problémák diagnosztizálásában és támogatásában. Az erőforrások aktuális és korábbi állapotáról is beszámol.

Ez a cikk bemutatja, hogyan szerezheti be az Azure Digital Twins-példányok **erőforrás-állapotával** kapcsolatos információkat.

## <a name="use-azure-resource-health"></a>Azure Resource Health használata

Azure Resource Health segítségével figyelheti, hogy az Azure Digital Twins-példánya működik-e. Azt is megtudhatja, hogy a regionális leállás hatással van-e a példány állapotára.

A példány állapotának vizsgálatához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure Digital Twins-példányhoz. A nevét a portál keresési sávjába írja be. 

2. A példány menüjében válassza az _**erőforrás állapota**_ lehetőséget a *támogatás + hibaelhárítás* lehetőség alatt. Ekkor megnyílik az oldal az erőforrás-állapot előzményeinek megtekintéséhez. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="A &quot;Resource Health&quot; (erőforrás állapota) lapot ábrázoló képernyőkép. Az elmúlt kilenc nap napi jelentését az &quot;állapot előzményei&quot; szakasz mutatja be. Minden nap az &quot;elérhető&quot; állapotot jeleníti meg.":::

A fenti képen ez a példány *elérhetőként* jelenik meg, és az elmúlt kilenc napban van. Ha többet szeretne megtudni a *rendelkezésre álló* állapotról és az esetlegesen megjelenő egyéb állapotokról, tekintse meg az [*Azure Resource Health áttekintése*](../service-health/resource-health-overview.md)című témakört.

Emellett további információkat olvashat a különböző típusú Azure-erőforrások erőforrás-állapotáról az erőforrás- [*típusok és az állapot-ellenőrzések az Azure Resource Health*](../service-health/resource-health-checks-resource-types.md)szolgáltatásban című témakörben.

## <a name="next-steps"></a>Következő lépések

Az Azure Digital Twins-példány figyelésének egyéb módjairól az alábbi cikkekben olvashat:
* [*Hibaelhárítás: mérőszámok megtekintése Azure Monitor*](troubleshoot-metrics.md)
* [*Hibaelhárítás: a diagnosztika beállítása*](troubleshoot-diagnostics.md).
* [*Hibaelhárítás: riasztások beállítása*](troubleshoot-alerts.md)
