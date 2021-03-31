---
title: Azure-beli megerősített erőforrás-naplók engedélyezése és használata
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti és dolgozhat az Azure Bastion diagnosztikai naplóival.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 1e76fffd17ee565d4103ca8a7bf1523bbd16209d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91445387"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>A Bastion-erőforrásnaplók engedélyezése és használata

Ahogy a felhasználók az Azure Bastion használatával csatlakoznak a munkaterhelésekhez, a Bastion naplózhatja a távoli munkamenetek diagnosztikát. Ezután a diagnosztika használatával megtekintheti, hogy mely felhasználók kapcsolódtak a munkaterhelésekhez, hogy milyen időpontban, hol és más hasonló naplózási információkkal. A diagnosztika használatához engedélyeznie kell a diagnosztikai naplókat az Azure Bastion-ben. Ennek a cikknek a segítségével engedélyezheti a diagnosztikai naplókat, majd megtekintheti a naplókat.

## <a name="enable-the-resource-log"></a><a name="enable"></a>Az erőforrás-napló engedélyezése

1. A [Azure Portal](https://portal.azure.com)navigáljon az Azure Bastion-erőforráshoz, és válassza a **diagnosztikai beállítások** lehetőséget az Azure Bastion oldalon.

   ![A "diagnosztikai beállítások" oldalt megjelenítő képernyőkép.](./media/diagnostic-logs/1diagnostics-settings.png)
2. Válassza a **diagnosztikai beállítások**, majd a **+ diagnosztikai beállítás hozzáadása** elemet a naplók célhelyének hozzáadásához.

   ![Képernyőkép, amely a "diagnosztikai beállítások hozzáadása" gomb kiválasztásával jelenik meg.](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. A **diagnosztikai beállítások** lapon válassza ki a diagnosztikai naplók tárolására szolgáló Storage-fiók típusát.

   ![Képernyőfelvétel: a "diagnosztikai beállítások" oldal, amely a kijelölt tárolóhely kiválasztására szolgáló szakaszt tartalmazza.](./media/diagnostic-logs/3add-storage-account.png)
4. A beállítások elvégzése után az alábbi példához hasonlóan fog kinézni:

   ![Példa beállításai](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Diagnosztikai napló megtekintése

A diagnosztikai naplók eléréséhez közvetlenül használhatja azt a Storage-fiókot, amelyet a diagnosztika beállításainak engedélyezésekor adott meg.

1. Keresse meg a Storage-fiók erőforrását, majd a **tárolók** lehetőséget. Megjelenik a Storage-fiók blob-tárolójában létrehozott elemzések **– naplók – bastionauditlogs** blob.

   ![diagnosztikai beállítások](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Ahogy a tárolón belül navigál, a blogban különféle mappák jelennek meg. Ezek a mappák az Azure-beli megerősített erőforráshoz tartozó erőforrás-hierarchiát jelölik.

   ![diagnosztikai beállítás hozzáadása](./media/diagnostic-logs/2-resource-h.png)
3. Navigáljon az Azure-beli megerősített erőforrás teljes hierarchiához, amelynek diagnosztikai naplóit szeretné elérni/megtekinteni. Az "y =", 'm = ", 'd =", "h =" és "m =" érték azt jelzi, hogy az év, hónap, nap, óra és perc szerepel az erőforrás-naplóknál.

   ![tárolási hely kiválasztása](./media/diagnostic-logs/3-resource-location.png)
4. Keresse meg az Azure Bastion által létrehozott JSON-fájlt, amely a diagnosztikai napló adatait tartalmazza az adott időszakra vonatkozóan.

5. Töltse le a JSON-fájlt a Storage blob-tárolóból. A JSON-fájlból például a következő hivatkozás látható:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.
