---
title: 'Oktatóanyag: Azure Spring Cloud-erőforrások figyelése riasztások és műveleti csoportok használatával | Microsoft Docs'
description: Megtudhatja, hogyan használhatja a Spring Cloud-riasztásokat.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: 59791b72272ccd721a61da88d13b973119cef5d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596142"
---
# <a name="tutorial-how-to-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Oktatóanyag: a Spring Cloud-erőforrások figyelése riasztások és műveleti csoportok használatával

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az Azure Spring Cloud-riasztások támogatják a figyelési erőforrásokat olyan feltételek alapján, mint a rendelkezésre álló tár, a kérelmek sebessége vagy az adatfelhasználás. A riasztások értesítést küldenek, ha a díjszabás vagy a feltételek megfelelnek a megadott specifikációknak.

A riasztási folyamat beállítása két lépésből áll: 
1. Hozzon létre egy műveleti csoportot a riasztás indításakor végrehajtandó műveletekkel, például e-mailben, SMS-ben, Runbook vagy webhookon. A műveleti csoportok újra felhasználhatók a különböző riasztások között.
2. Riasztási szabályok beállítása. A szabályok a műveleti csoportokra vonatkozó metrikai mintákat kötik a cél erőforrás, a metrika, a feltétel, az idő összesítése stb. alapján.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Spring követelményei mellett az oktatóanyagban szereplő eljárások egy üzembe helyezett Azure Spring Cloud-példánnyal is működnek.  [A kezdéshez kövesse az első](spring-cloud-quickstart.md) lépéseket.

A következő eljárások inicializálják mindkét **műveleti csoportot** és a **riasztást** a Spring Cloud-példány bal oldali navigációs paneljének **riasztások** lehetőségével. (Az eljárás a Azure Portal **figyelő áttekintés** lapján is elindítható.) 

Navigáljon egy erőforráscsoporthoz a Spring Cloud-példányra. Válassza a **riasztások** lehetőséget a bal oldali ablaktáblán, majd válassza a **műveletek kezelése** lehetőséget:

![Képernyőfelvétel-portál erőforráscsoport lapja](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Műveleti csoport beállítása

Az új **műveleti csoport** inicializálási eljárásának megkezdéséhez válassza a **+ műveleti csoport hozzáadása** lehetőséget.

![Képernyőfelvétel-portál – műveleti csoport hozzáadása](media/alerts-action-groups/action-1.png)

A **műveleti csoport hozzáadása** oldalon:

 1. Adja meg a **műveleti csoport nevét** és **rövid nevét**.

 1. Az **előfizetés** és az **erőforráscsoport** meghatározása.

 1. Adja meg a **művelet nevét**.

 1. Válassza a **művelet típusa** lehetőséget.  Ekkor megnyílik egy másik ablaktábla a jobb oldalon az aktiváláskor végrehajtandó művelet definiálásához.

 1. Adja meg a műveletet a jobb oldali ablaktáblán található beállítások használatával.  Ez az eset e-mailes értesítést használ.

 1. A jobb oldali művelet ablaktáblán kattintson **az OK** gombra.

 1. A **műveleti csoport hozzáadása** párbeszédpanelen kattintson **az OK** gombra. 

  ![A képernyőfelvétel-portál definiálása művelet](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Riasztás beállítása 

Az előző lépések olyan **műveleti csoportot** hoztak létre, amely e-mailt használ. Telefonos értesítéseket, webhookokat, Azure-függvényeket és így tovább is használhat. A következő lépésekkel konfigurálhatja a **riasztásokat**.

1. váltson vissza a **riasztások** lapra, majd kattintson a **riasztási szabályok kezelése** elemre.

   ![A képernyőfelvétel-portál meghatározza a riasztást](media/alerts-action-groups/alerts-2.png)

1. Válassza ki a riasztás **erőforrását** .

1. Kattintson az **+ új riasztási szabály** elemre.

   ![Képernyőkép-portál új riasztási szabálya](media/alerts-action-groups/alerts-3.png)

1. A **szabály létrehozása** lapon határozza meg az **erőforrást**.

1. A **feltétel** beállítása számos lehetőséget kínál a **Spring Cloud** -erőforrások figyelésére.  Kattintson a **Hozzáadás** gombra a **jel logikájának konfigurálása** panel megnyitásához.

1. Válasszon ki egy feltételt. Ez a példa a **rendszer CPU-kihasználtságának százalékos arányát** használja.

   ![Képernyőkép-portál – új riasztási szabály 2](media/alerts-action-groups/alerts-3-1.png)

1. Görgessen le a **jelzőlámpa konfigurálása** panelre, és állítsa be a figyelni kívánt **küszöbértéket** .

   ![Képernyőkép-portál új riasztási szabály 3](media/alerts-action-groups/alerts-3-2.png)

1. Kattintson a **Kész** gombra.

   A figyeléshez rendelkezésre álló feltételek részleteiért lásd: [felhasználói portál metrikái – beállítások](spring-cloud-concept-metrics.md#user-metrics-options).

1. A **műveletek** területen kattintson a **műveleti csoport kiválasztása** elemre. A **műveletek** ablaktáblán válassza ki a korábban definiált **műveleti csoportot**.

   ![Képernyőkép-portál új riasztási szabály 4](media/alerts-action-groups/alerts-3-3.png) 

1. Görgessen le, és a **riasztás részletei** területen nevezze el a riasztási szabályt.

1. Állítsa be a **súlyosságot**.

1. Kattintson a **Riasztási szabály létrehozása** lehetőségre.

   ![Képernyőkép-portál új riasztási szabály 5](media/alerts-action-groups/alerts-3-4.png)

1. Ellenőrizze, hogy az új riasztási szabály engedélyezve van-e.

   ![Képernyőkép-portál új riasztási szabálya 6](media/alerts-action-groups/alerts-4.png)

A **metrikák** lapról is létrehozhat egy szabályt:

![Képernyőkép-portál új riasztási szabálya 7](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan állíthat be riasztásokat és műveleti csoportokat egy Azure Spring Cloud-alkalmazáshoz. A műveleti csoportokról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Műveletcsoportok létrehozása és felügyelete az Azure Portalon](../azure-monitor/alerts/action-groups.md)

> [!div class="nextstepaction"]
> [SMS-riasztás viselkedése a műveleti csoportokban](../azure-monitor/alerts/alerts-sms-behavior.md)