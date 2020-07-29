---
title: Azure App Service hálózati konfigurációjának szinkronizálása
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk azt ismerteti, hogyan szinkronizálhatja Azure App Service üzemeltetési terv hálózati konfigurációját az Azure SQL felügyelt példányával.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695453"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Azure App Service üzemeltetési csomag hálózati konfigurációjának szinkronizálása az Azure SQL felügyelt példányával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Előfordulhat, hogy bár az [alkalmazást Azure-Virtual Networkrel integrálta](../../app-service/web-sites-integrate-with-vnet.md), nem tud kapcsolatot LÉTESÍTENI az SQL felügyelt példányával. A frissítés vagy szinkronizálás során a szolgáltatási csomag hálózati konfigurációja megoldhatja a problémát. 

## <a name="sync-network-configuration"></a>Hálózati konfigurációja szinkronizálása 

Ehhez kövesse az alábbi lépéseket:  

1. Nyissa meg a Web Apps App Service csomagot.

   ![Képernyőkép a App Service tervről](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Válassza a **hálózatkezelés** lehetőséget, majd **kattintson a kezelés elemre**.

   ![A szolgáltatási csomag kezelésének képernyőképe](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Válassza ki a **VNet** , és kattintson a **hálózat szinkronizálása**elemre.

   ![A szinkronizálási hálózat képernyőképe](./media/azure-app-sync-network-configuration/sync.png)

4. Várjon, amíg a szinkronizálás elkészült.
  
   ![A szinkronizálás kész képernyőkép](./media/azure-app-sync-network-configuration/sync-done.png)

Most már készen áll arra, hogy megpróbálja újból létrehozni a kapcsolatot az SQL felügyelt példányával.

## <a name="next-steps"></a>További lépések

- További információ a VNet SQL felügyelt példányhoz való konfigurálásáról: az [SQL felügyelt példányok VNet architektúrája](connectivity-architecture-overview.md) és [a meglévő VNet konfigurálása](vnet-existing-add-subnet.md).
