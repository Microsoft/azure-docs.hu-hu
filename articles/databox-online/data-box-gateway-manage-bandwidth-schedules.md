---
title: Sávszélesség-ütemtervek kezelése Azure Data Box Gatewayon | Microsoft Docs
description: Ez a cikk bemutatja, hogyan történik az Azure Data Box Gateway sávszélesség-ütemezési beállításainak kezelése az Azure Portalon.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 25f74fb78979313acc068320d842d09a982d8d16
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84339144"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>Az Azure Data Box Gateway sávszélesség-ütemezési beállításait az Azure Portalon keresztül kezelheti  

Ez a cikk bemutatja, hogyan történik a felhasználók kezelése az Azure Data Box Gatewayben. A sávszélesség-ütemezéssel napi szinten szabályozható a hálózati sávszélesség felhasználása. Ezek az ütemezések az eszköz és a felhő közötti összes fel- és letöltési műveletre alkalmazhatók.

A Data Box Gatewayre vonatkozó sávszélesség-ütemezéseket az Azure Portalon lehet hozzáadni, módosítani és törölni.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Ütemezés hozzáadása
> * Ütemezés módosítása
> * Ütemezés törlése

## <a name="add-a-schedule"></a>Ütemezés hozzáadása

Felhasználó hozzáadásához hajtsa végre az alábbi lépéseket az Azure Portalon.

1. A Data Box Gateway-erőforráshoz tartozó Azure Portalon lépjen a **Sávszélesség** területre.
2. A jobb oldali panelen kattintson az **+ Ütemezés megadása** elemre.

    ![Kattintson a Felhasználó hozzáadása gombra](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. Az **Ütemezés hozzáadása** területen: 

   1. Adja meg az ütemezés **Kezdő nap**, **Záró nap**, **Kezdés** és **Befejezés** paramétereinek értékeit. 
   2. Ha az adott ütemezés az egész napra vonatkozik, jelölje be az **Egész nap** jelölőnégyzetet. 
   3. A **Sávszélesség** az eszköz által felhasznált sávszélesség Mb/s-ban kifejezett értéke a felhőműveletek (fel- és letöltés egyaránt) esetében. Ebben a mezőben 1–1000 közötti értéket adjon meg. 
   4. Ha nem kívánja korlátozni a fel- és letöltéseket, válassza a **Korlátlan** sávszélesség-beállítást. 
   5. Kattintson a **Hozzáadás** parancsra.

      ![Kattintson a Felhasználó hozzáadása gombra](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. Ekkor létrejön egy ütemezés a megadott paraméterekkel. Az ütemezés ezután megjelenik a portálon is, a sávszélesség-ütemezések listájában.


## <a name="edit-schedule"></a>Ütemezés szerkesztése

A sávszélesség-ütemezéseket az alábbi lépesek végrehajtásával szerkesztheti. 

1. Az Azure Portalon keresse meg a Data Box Gateway-erőforrást, majd lépjen a Sávszélesség területre. 
2. A sávszélesség-ütemezések listájában jelölje ki a módosítani kívánt ütemezést, majd kattintson rá.
    ![Felhasználó módosítása](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. Hajtsa végre és mentse a kívánt módosításokat.

    ![Felhasználó módosítása](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Az ütemezés módosítása után annak megfelelően frissül az ütemezések listája.

    ![Felhasználó módosítása](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Ütemezés törlése

A Data Box Gateway-eszközhöz társított sávszélesség-ütemezéseket az alábbi lépesek végrehajtásával törölheti.

1. Az Azure Portalon keresse meg a Data Box Gateway-erőforrást, majd lépjen a **Sávszélesség** területre.  

2. A sávszélesség-ütemezések listájában válassza ki a törölni kívánt ütemezést. Jobb gombbal nyissa meg a helyi menüt, majd kattintson a **Törlés** elemre. 

   ![Felhasználó törlése](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Az ütemezés törlése után frissül az ütemezések listája.



## <a name="next-steps"></a>További lépések

- További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-gateway-manage-bandwidth-schedules.md) talál. 
