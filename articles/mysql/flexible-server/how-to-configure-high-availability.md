---
title: Zónaredundáns magas rendelkezésre állás kezelése – Azure Portal – Azure Database for MySQL rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan engedélyezheti vagy tilthatja le a zónaredundáns magas rendelkezésre állást Azure Database for MySQL rugalmas kiszolgálón a Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: e217dcaeafd553803f5c9699ab6d7779ed755b67
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818284"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Zónaredundáns magas rendelkezésre állás kezelése rugalmas Azure Database for MySQL (előzetes verzió)

Ez a cikk azt ismerteti, hogyan engedélyezheti vagy tilthatja le a zónaredundáns magas rendelkezésre állású konfigurációt a rugalmas kiszolgálón.

A magas rendelkezésre állású funkció fizikailag elkülönített elsődleges és készenléti replikát biztosít különböző zónákban. További részletekért tekintse meg [a magas rendelkezésre állás fogalmait tartalmazó dokumentációt.](./concepts/../concepts-high-availability.md) 

> [!IMPORTANT]
> A zónaredundáns magas rendelkezésre állást csak a rugalmas kiszolgáló létrehozása során engedélyezheti.

Ez az oldal a magas rendelkezésre állás engedélyezésére vagy letiltására vonatkozó irányelveket tartalmaz. Ez a művelet nem módosítja a többi beállítást, például a virtuális hálózat konfigurációját, a tűzfalbeállításokat és a biztonsági másolatok megőrzését. Hasonlóképpen, a magas rendelkezésre állás letiltása online művelet, és nem befolyásolja az alkalmazás kapcsolatát és műveleteit.

> [!IMPORTANT]
> A zónaredundáns magas rendelkezésre állás korlátozott régiókban érhető el: Délkelet-Ázsia, USA 2. nyugati régiója, Nyugat-Európa és az USA keleti régiója.  

## <a name="enable-high-availability-during-server-creation"></a>Magas rendelkezésre állás engedélyezése a kiszolgáló létrehozása során

Ez a szakasz kifejezetten a HA-hez kapcsolódó mezők adatait tartalmazza. Az alábbi lépéseket követve magas rendelkezésre állást helyezhet üzembe a rugalmas kiszolgáló létrehozása során.

1.  A [Azure Portal](https://portal.azure.com/)válassza a rugalmas kiszolgálót, majd kattintson a **Létrehozás gombra.**  Az előfizetéssel, erőforráscsoporttal, kiszolgálónévvel, régióval és egyéb mezőkkel kapcsolatos részletekért tekintse meg a kiszolgáló létrehozásának dokumentációját. 

2.  A Rendelkezésre állás  lehetőségben jelölje be a Zónaredundáns magas rendelkezésre állás jelölőnégyzetet.

3.  Ha módosítani szeretné az alapértelmezett számítást és tárolást, kattintson a **Kiszolgáló konfigurálása elemre.**

4.  Ha be van jelölve a magas rendelkezésre állási lehetőség, akkor az adatlok-ható szint nem lesz kiválasztható. Választhat az Általános célú **vagy** a **Memóriaoptimalált** számítási szintek közül.

    > [!IMPORTANT]
    > A zónaredundáns magas rendelkezésre állást csak az ***Általános** célú _ és *___* Memóriaoptimalált * tarifacsomag esetében támogatjuk.

5.  Válassza ki **a kívánt** Számítási méretet a legördülő menüből.

6.  A **csúszkával** válassza a Tárolási méret  GiB-ban lehetőséget, majd válassza ki a Biztonsági másolat megőrzési időszakát 7 nap és 35 nap között.   

## <a name="disable-high-availability"></a>A magas rendelkezésre állás letiltása

Az alábbi lépésekkel letilthatja a magas rendelkezésre állást a zónaredundaniával már konfigurált rugalmas kiszolgálón.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő rugalmas Azure Database for MySQL kiszolgálót.

2.  A rugalmas kiszolgáló oldalán kattintson **a** Magas rendelkezésre állás elemre az előlapon a magas rendelkezésre állás lap megnyitásához.

3.  Kattintson a zónaredundáns **magas** rendelkezésre állás  jelölőnégyzetére a beállítás letiltásához, majd kattintson a Mentés gombra a módosítás mentéshez.

4.  Megjelenik egy megerősítő párbeszédpanel, ahol megerősítheti a hatékony letiltásának megerősítését.

5.  A **magas rendelkezésre állás letiltásához** kattintson a Magas rendelkezésre állás letiltása gombra.

6.  Egy értesítés jelenik meg, amely szerint folyamatban van a magas rendelkezésre állású üzemelő példány leszerelése.


## <a name="forced-failover"></a>Kényszerített feladatátvétel

Kövesse az alábbi lépéseket az elsődleges kiszolgálóról a készenléti rugalmas kiszolgálóra való feladatátvétel kényszerítéhez

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő rugalmas Azure Database for MySQL, amelyen engedélyezve van a magas rendelkezésre állási funkció.

2.  A rugalmas kiszolgáló oldalán kattintson **a** Magas rendelkezésre állás elemre az előlapon a magas rendelkezésre állás lap megnyitásához.

3.  Ellenőrizze az **elsődleges rendelkezésre állási zónát** és **a készenléti rendelkezésre állási zónát**

4.  Kattintson a **Kényszerített feladatátvétel elemre** a manuális feladatátvételi eljárás kezdeményezéshez. Egy előugró ablak tájékoztatja a feladatátvétel várható idejét az elsődleges feladat aktuális számítási feladatától és az utolsó ellenőrzőpont helyességétől függően, olvassa el az üzenetet, és kattintson az OK gombra.
 
5. Megjelenik egy értesítés, amely megemlíti, hogy a feladatátvétel folyamatban van.

6. Ha a feladatátvétel sikeresen befejeződött a készenléti kiszolgálóra, megjelenik egy értesítés.

7. Ellenőrizze az új **elsődleges rendelkezésre állási zónát** és **a készenléti rendelkezésre állási zónát.**

![Kényszerített feladatátvétel](media/how-to-configure-high-availability/how-to-forced-failover.png) 

## <a name="next-steps"></a>Következő lépések

-   További információ az [üzletmenet-folytonosságról](./concepts-business-continuity.md)
-   Tudnivalók a [zónaredundáns magas rendelkezésre állásról](./concepts-high-availability.md)
