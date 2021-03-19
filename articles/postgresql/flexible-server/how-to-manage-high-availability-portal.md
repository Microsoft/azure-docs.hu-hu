---
title: A zóna redundáns magas rendelkezésre állásának kezelése – Azure Portal-Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan engedélyezhető vagy tiltható le a zóna redundáns magas rendelkezésre állása Azure Database for PostgreSQL rugalmas kiszolgálón a Azure Portal használatával.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90934909"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>A zóna redundáns magas rendelkezésre állásának kezelése rugalmas kiszolgálón

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ez a cikk azt ismerteti, hogyan engedélyezheti vagy tilthatja le a zóna redundáns magas rendelkezésre állási konfigurációját a rugalmas kiszolgálókon.

A magas rendelkezésre állási funkció a különböző zónákban lévő elsődleges és készenléti replikák fizikai elkülönítését. További részletekért tekintse meg a [magas rendelkezésre állással kapcsolatos fogalmak dokumentációját](./concepts-high-availability.md). Dönthet úgy, hogy a rugalmas kiszolgáló létrehozásakor vagy a létrehozás után lehetővé teszi a magas rendelkezésre állást. Ez az oldal útmutatást nyújt a magas rendelkezésre állás engedélyezéséhez vagy letiltásához. Ez a művelet nem változtatja meg a többi beállítást, például a VNET konfigurációját, a tűzfal beállításait és a biztonsági másolatok megőrzését. Hasonlóképpen, a magas rendelkezésre állás engedélyezése és letiltása egy online művelet, amely nem befolyásolja az alkalmazás kapcsolatát és műveleteit.

## <a name="pre-requisites"></a>Előfeltételek

A zóna redundáns magas rendelkezésre állása csak olyan régiókban érhető el, ahol több zóna is támogatott. 

## <a name="enable-high-availability-during-server-creation"></a>Magas rendelkezésre állás engedélyezése a kiszolgáló létrehozása során

Ez a szakasz részletesen ismerteti a HA kapcsolódó mezőket. Az alábbi lépések végrehajtásával magas rendelkezésre állást helyezhet üzembe a rugalmas kiszolgáló létrehozása során.

1.  A [Azure Portal](https://portal.azure.com/)válassza a rugalmas kiszolgáló elemet, majd kattintson a Létrehozás gombra.  A részleteket, például az **előfizetést**, az **erőforráscsoportot**, a **kiszolgálónevet**, a **régiót** és az egyéb mezőket a kiszolgáló létrehozásának dokumentációjában találja.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="Előfizetés és régió megtekintése":::

2.  Válassza ki a **rendelkezésre állási zónát**. Ez akkor lehet hasznos, ha az alkalmazást ugyanabban a rendelkezésre állási zónában szeretné rézvezetékes végezhet, mint az adatbázist a késés csökkentése érdekében. Ha azt szeretné, hogy a rugalmas kiszolgáló minden rendelkezésre állási zónán üzembe helyezhető, válassza a **Nincs beállítás** lehetőséget.
    ![AZ AZ Selection ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="rendelkezésre állási zóna kiválasztása":::  

3.  A rendelkezésre állási lehetőségnél kattintson a **zóna redundáns magas rendelkezésre állása** jelölőnégyzetére.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="Magas rendelkezésre állás jelölőnégyzet":::

4.  Ha módosítani szeretné az alapértelmezett számítási és tárolási tárterületet, kattintson a  **kiszolgáló konfigurálása** elemre.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="kiszolgáló konfigurálása – számítás és tárolás":::  

5.  Ha a magas rendelkezésre állás beállítás be van jelölve, a feltört szint nem lesz elérhető a választáshoz. Kiválaszthatja az **általános célú** vagy a **memóriára optimalizált** számítási szinteket is. Ezután kiválaszthatja a kívánt **számítási méretet** a legördülő listából.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="Számítási rétegek kiválasztása":::  


6.  A kicsúsztatható sáv használatával válassza ki a **Storage-méretet** , és válassza ki a **biztonsági mentés megőrzési időtartamát** 7 nap és 35 nap között.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="Tároló biztonsági mentése"::: 

7. Kattintson a **Mentés** gombra. 

## <a name="enable-high-availability-post-server-creation"></a>Magas rendelkezésre állást követő kiszolgáló létrehozásának engedélyezése

A meglévő rugalmas kiszolgáló magas rendelkezésre állásának engedélyezéséhez kövesse az alábbi lépéseket.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő PostgreSQL rugalmas kiszolgálót.

2.  A rugalmas kiszolgáló lapon kattintson a **magas rendelkezésre állás** lehetőségre a bal oldali panelen a magas rendelkezésre állási lap megnyitásához.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Bal oldali panel kijelölése"::: 

3.  Kattintson a **zóna redundáns magas rendelkezésre állása** jelölőnégyzetre a beállítás **engedélyezéséhez** , majd kattintson a **Mentés** gombra a módosítás mentéséhez.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="Magas rendelkezésre állás engedélyezése"::: 

4.  A megerősítő párbeszédpanel azt jelzi, hogy a magas rendelkezésre állás engedélyezésével a további kiszolgáló-és tárterület-telepítés miatt növekedni fog a díj.

5.  A magas rendelkezésre állás engedélyezéséhez kattintson a gomb **engedélyezése** gombra.

6.  Megjelenik egy értesítés, amely jelzi, hogy a magas rendelkezésre állású központi telepítés folyamatban van.

## <a name="disable-high-availability"></a>Magas rendelkezésre állás letiltása

A következő lépésekkel letilthatja a rugalmas kiszolgáló magas rendelkezésre állását, amely már konfigurálva van a zóna-redundanciával.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for PostgreSQL rugalmas kiszolgálót.

2.  A rugalmas kiszolgáló lapon kattintson a **magas rendelkezésre állás** lehetőségre az előlapon a magas rendelkezésre állású lap megnyitásához.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Bal oldali panel kijelölése"::: 

3.  A beállítás **letiltásához** kattintson a **zóna redundáns magas rendelkezésre állása** jelölőnégyzetre. Ezután kattintson a **Save (Mentés** ) gombra a módosítás mentéséhez.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="Magas rendelkezésre állás letiltása"::: 

4.  Ekkor megjelenik egy megerősítő párbeszédpanel, ahol ellenőrizheti A magas rendelkezésre állást.

5.  Ha le szeretné tiltani a magas rendelkezésre állást, kattintson a **Letiltás** gombra.

6.  Egy értesítés jelenik meg a magas rendelkezésre állású központi telepítés leszerelése közben.

## <a name="next-steps"></a>Következő lépések

-   Az [üzletmenet folytonosságának](./concepts-business-continuity.md) megismerése
-   További információ a [zónák redundáns magas rendelkezésre állásáról](./concepts-high-availability.md)
