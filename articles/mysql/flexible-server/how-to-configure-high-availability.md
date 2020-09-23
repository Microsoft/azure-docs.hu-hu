---
title: A zóna redundáns magas rendelkezésre állásának kezelése – Azure Portal – Azure Database for MySQL rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan engedélyezhető vagy tiltható le a zóna redundáns magas rendelkezésre állása Azure Database for MySQL rugalmas kiszolgálón a Azure Portal használatával.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 09cd7428519cbf84c785efa16b61b9507a3c0b94
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940509"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>A zóna redundáns magas rendelkezésre állásának kezelése Azure Database for MySQL rugalmas kiszolgálóban (előzetes verzió)

Ez a cikk azt ismerteti, hogyan engedélyezheti vagy tilthatja le a zóna redundáns magas rendelkezésre állási konfigurációját a rugalmas kiszolgálókon.

A magas rendelkezésre állási funkció a különböző zónákban lévő elsődleges és készenléti replikák fizikai elkülönítését. További részletekért tekintse meg a [magas rendelkezésre állással kapcsolatos fogalmak dokumentációját](./concepts/../concepts-high-availability.md). 

> [!IMPORTANT]
> A zóna rugalmas, magas rendelkezésre állása csak a kiszolgáló rugalmas létrehozásakor engedélyezhető.

Ez az oldal útmutatást nyújt a magas rendelkezésre állás engedélyezéséhez vagy letiltásához. Ez a művelet nem változtatja meg a többi beállítást, például a VNET konfigurációját, a tűzfal beállításait és a biztonsági másolatok megőrzését. Hasonlóképpen, a magas rendelkezésre állás letiltása egy online művelet, amely nem befolyásolja az alkalmazás kapcsolatát és műveleteit.

> [!IMPORTANT]
> A zóna redundáns magas rendelkezésre állása korlátozott számú régióban érhető el: Délkelet-Ázsia, 2. WestUS, Nyugat-Európa és az USA keleti régiója.  

## <a name="enable-high-availability-during-server-creation"></a>Magas rendelkezésre állás engedélyezése a kiszolgáló létrehozása során

Ez a szakasz részletesen ismerteti a HA kapcsolódó mezőket. Az alábbi lépések végrehajtásával magas rendelkezésre állást helyezhet üzembe a rugalmas kiszolgáló létrehozása során.

1.  A [Azure Portal](https://portal.azure.com/)válassza a rugalmas kiszolgáló elemet, majd kattintson a **Létrehozás**gombra.  A részleteket, például az **előfizetést**, az **erőforráscsoportot**, a **kiszolgálónevet**, a **régiót**és az egyéb mezőket a kiszolgáló létrehozásának dokumentációjában találja.

2.  A rendelkezésre állási lehetőségnél kattintson a **zóna redundáns magas rendelkezésre állása** jelölőnégyzetére.

3.  Ha módosítani szeretné az alapértelmezett számítási és tárolási tárterületet, kattintson a  **kiszolgáló konfigurálása**elemre.

4.  Ha a magas rendelkezésre állás beállítás be van jelölve, a feltört szint nem lesz elérhető a választáshoz. Kiválaszthatja az **általános célú** vagy a **memóriára optimalizált** számítási szinteket is.

    > [!IMPORTANT]
    > Az ***általános célú*** és a ***memóriát optimalizáló*** díjszabási szinten csak a zóna redundáns magas rendelkezésre állását támogatjuk.

5.  Válassza ki a legördülő menüből választott **számítási méretet** .

6.  A kicsúsztatható sáv használatával válassza ki a **Storage-méretet** , és válassza ki a **biztonsági mentés megőrzési időtartamát** 7 nap és 35 nap között.   

## <a name="disable-high-availability"></a>Magas rendelkezésre állás letiltása

A következő lépésekkel letilthatja a rugalmas kiszolgáló magas rendelkezésre állását, amely már konfigurálva van a zóna-redundanciával.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL rugalmas kiszolgálót.

2.  A rugalmas kiszolgáló lapon kattintson a **magas rendelkezésre állás** lehetőségre az előlapon a magas rendelkezésre állású lap megnyitásához.

3.  Kattintson a **zóna redundáns magas rendelkezésre állása** jelölőnégyzetre a beállítás letiltásához, majd kattintson a **Save (Mentés**) gombra a   módosítás mentéséhez.

4.  Ekkor megjelenik egy megerősítő párbeszédpanel, ahol ellenőrizheti, hogy le kell-e tiltani A letiltást.

5.  Ha le szeretné tiltani a magas rendelkezésre állást, kattintson a **Letiltás** gombra.

6.  Egy értesítés jelenik meg a magas rendelkezésre állású központi telepítés leszerelése közben.

## <a name="next-steps"></a>Következő lépések

-   Az [üzletmenet folytonosságának](./concepts-business-continuity.md) megismerése
-   További információ a [zónák redundáns magas rendelkezésre állásáról](./concepts-high-availability.md)
