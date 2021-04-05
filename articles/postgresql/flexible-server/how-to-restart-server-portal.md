---
title: Restart-Azure Portal-Azure Database for PostgreSQL-rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan végezhető el az újraindítási műveletek végrehajtása a Azure Database for PostgreSQL az Azure Portalon keresztül.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90934873"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL újraindítása – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ez a cikk részletes eljárást tartalmaz a rugalmas kiszolgáló újraindításának végrehajtásához. Ez a művelet hasznos lehet az adatbázis-kiszolgáló újraindítását igénylő statikus paraméterek módosításainak alkalmazásához. Az eljárás azonos a zóna redundáns magas rendelkezésre állásával konfigurált kiszolgálók esetében. 

> [!IMPORTANT]
> Ha magas rendelkezésre állással van konfigurálva, akkor mind az elsődleges, mind a készenléti kiszolgáló újraindul.

## <a name="pre-requisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

-   Rugalmas kiszolgálóval kell rendelkeznie.

## <a name="restart-your-flexible-server"></a>A rugalmas kiszolgáló újraindítása

A rugalmas kiszolgáló újraindításához kövesse az alábbi lépéseket.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki az újraindítani kívánt rugalmas kiszolgálót.

2.  Kattintson az **Áttekintés** elemre a bal oldali panelen, majd kattintson az **Újraindítás** elemre.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Kijelölés újraindítása":::

3.  Ekkor megjelenik egy előugró megerősítési üzenet.

4.  Ha folytatni szeretné a folytatást, kattintson az **Igen** gombra.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Újraindítás megerősítése":::
 
6.  Ekkor megjelenik egy értesítés, amely szerint az újraindítási művelet megkezdődött.

## <a name="next-steps"></a>Következő lépések

-   Az [üzletmenet folytonosságának](./concepts-business-continuity.md) megismerése
-   További információ a [zónák redundáns magas rendelkezésre állásáról](./concepts-high-availability.md)
