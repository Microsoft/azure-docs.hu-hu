---
title: Kiszolgálószintű tűzfalszabályok
description: Kiszolgálószintű tűzfalszabályok
keywords: SQL-kapcsolatok, kapcsolatok karakterlánca
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179198"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali listában válassza a **minden szolgáltatás** lehetőséget.

3. Görgessen le, és válassza az **SQL-kiszolgálók** lehetőséget.

    ![A Azure SQL Database-kiszolgáló megkeresése a portálon][b21-FindServerInPortal]
5. A szűrő szövegmezőbe kezdje el beírni a kiszolgáló nevét. Ekkor megjelenik a sor.

6. Válassza ki a kiszolgáló sorát. Megjelenik a kiszolgáló panelje.

7. A kiszolgáló paneljén válassza a **Beállítások** lehetőséget.

8. Válassza a **tűzfal** lehetőséget.

    ![Beállítások kiválasztása > tűzfal][b31-SettingsFirewallNavig]
9. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget. Írja be az új szabály nevét az első szövegmezőbe.

10. Adja meg az engedélyezni kívánt tartomány alacsony és magas IP-címének értékeit.

    * Hasznos lehet, ha az alacsony érték a **. 0** és a magas értékkel végződik **. 255**.

11. Válassza a **Mentés** lehetőséget.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
