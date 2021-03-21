---
title: 'Oktatóanyag: csomópontok hozzáadása egy Azure FXT Edge Filer-fürthöz'
description: Ismerje meg, hogyan adhat hozzá fürtcsomópontok az Azure FXT Edge Filer Storage cache-hez, és engedélyezheti a magas rendelkezésre állású (HA) szolgáltatást.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 85ad78eeb095b427b1a6334f57c351e926022dff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021877"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Oktatóanyag: fürtcsomópontok hozzáadása egy Azure FXT Edge Filer-fürthöz

Egy új Azure FXT Edge Filer-fürt csak egy csomóponttal hozható létre. További konfiguráció előtt legalább két csomópontot kell hozzáadnia, és magas rendelkezésre állást kell engedélyeznie.

Ez az oktatóanyag bemutatja, hogyan adhat hozzá fürtcsomópontok és engedélyezheti a magas rendelkezésre állású (HA) szolgáltatást.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
>
> * Csomópontok hozzáadása a FXT-fürthöz
> * A HA engedélyezése

Az oktatóanyag lépéseinek elvégzése körülbelül 45 percet vesz igénybe.

Az oktatóanyag elindítása előtt kapcsolja be a hozzáadni kívánt csomópontokat, és [állítsa be a kezdeti jelszavukat](fxt-node-password.md).

## <a name="1-load-the-cluster-nodes-page"></a>1. a fürtcsomópontok oldal betöltése

Nyissa meg a fürt Vezérlőpultját egy böngészőben, és jelentkezzen be rendszergazdaként. (Részletes utasítások az áttekintő cikkben, [a beállítások lapok megnyitása](fxt-cluster-create.md#open-the-settings-pages)alatt találhatók.)

A Vezérlőpult megjeleníti az **irányítópult** fület, amikor megnyílik. 

![Vezérlőpult irányítópultja (első lap)](media/fxt-cluster-config/dashboard-1-node.png)

Ez a rendszerkép egy újonnan létrehozott fürt irányítópultját mutatja egyetlen csomóponttal.

## <a name="2-locate-the-node-to-add"></a>2. Keresse meg a hozzáadni kívánt csomópontot.

Csomópontok hozzáadásához kattintson a **Beállítások** fülre, és válassza a **FXT-csomópontok** lapot a **fürt** szakaszban.

![Vezérlőpult-beállítások lap (második lap) fürttel > FXT-csomópontok betöltve](media/fxt-cluster-config/settings-fxt-nodes.png)

Az **FXT-csomópontok – nem összeillesztett** lista megjeleníti az összes ki nem osztott FXT-csomópontot (a legtöbb adatközpont csak néhányat tartalmaz. Keresse meg a fürthöz hozzáadni kívánt FXT-csomópontokat.

> [!Tip]
> Ha nem találja a kívánt csomópontot a nem **összeillesztett** listán, ellenőrizze, hogy az megfelel-e a követelményeknek:
>
> * A szolgáltatás be van kapcsolva, és [rendszergazdai jelszóval](fxt-node-password.md)rendelkezik.
> * Csatlakoztatva van egy hálózathoz, amelyhez hozzáfér. Ha VLAN-okat használ, annak a fürttel azonos VLAN-on kell lennie.
> * A Bonjour protokoll használatával észlelhető.
>
>   Egyes tűzfalbeállítások letiltják a Bonjour által használt TCP/UDP-portokat, ami megakadályozza, hogy a FXT operációs rendszer automatikusan észlelje a csomópontokat.
>
> Ha a hozzáadni kívánt csomópont nem szerepel a listán, próbálja meg ezeket a megoldásokat:
>
> * Az IP-cím megkereséséhez kattintson a **manuális felderítés** gombra.
>
> * Rendeljen hozzá manuálisan ideiglenes IP-címeket. Ez ritka, de szükség lehet a címkézett VLAN-ok használatára, és a csomópontok nem a megfelelő hálózaton vannak, vagy a hálózat nem engedélyezi az önkiszolgáló IP-címeket. A [statikus IP-cím manuális beállításához](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html)kövesse a jelen dokumentum örökölt verziójának utasításait.

A listában megjelenik a csomópont neve, az IP-cím, a szoftver verziója és a jogosultsági állapot. Az **állapot** oszlopban általában "szeretne csatlakozni", vagy olyan rendszer-vagy hardverhiba-problémát jelez, amely miatt a csomópont nem jogosult a fürthöz való csatlakozásra.

A **műveletek** oszlop olyan gombokkal rendelkezik, amelyek lehetővé teszik a csomópont hozzáadását a fürthöz vagy a szoftver frissítését. A frissítés gomb automatikusan telepíti a fürtben már meglévő csomópontoknak megfelelő szoftververzió-verziót.

A fürt összes csomópontjának az operációs rendszer azonos verzióját kell használnia, de a csomópont hozzáadása előtt nem szükséges frissítenie a szoftvereket. Miután rákattintott az **Engedélyezés a csatlakozásra** gombra, a fürt csatlakoztatási folyamata automatikusan ellenőrzi és telepíti a fürt verziójának megfelelő operációsrendszer-szoftvert.

Ha többet szeretne megtudni az ezen a lapon található beállításokról, olvassa el a fürt [   >  **FXT csomópontjait**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) a fürt konfigurációs útmutatójában.

## <a name="3-click-the-allow-to-join-button"></a>3. kattintson a "csatlakozás engedélyezése" gombra

A hozzáadni kívánt csomópont _ *Actions** oszlopában kattintson a **Csatlakozás engedélyezése** _ gombra.

Miután rákattintott a gombra, a csomópont állapota változhat, mivel a szoftver frissítése folyamatban van a fürthöz való hozzáadás előkészítésében.

Az alábbi képen egy olyan csomópont látható, amely a fürt csatlakoztatásának folyamatában van (a legvalószínűbb, hogy a Hozzáadás előtt egy operációsrendszer-frissítést kap). Nem jelenik meg gomb a **műveletek** oszlopban azon csomópontok esetében, amelyek a fürthöz való hozzáadás folyamatában vannak.

![a csomópont-tábla egy sora, amely a csomópont nevét, az IP-címet, a szoftvert, a "csatlakozásra engedélyezett" üzenetet és egy üres utolsó oszlopot jelenít meg.](media/fxt-cluster-config/node-join-in-process.png)

Néhány pillanat elteltével az új csomópontnak szerepelnie kell a **FXT-csomópontok** beállításai lap tetején található fürtcsomópontok listájában.

Ismételje meg ezt a folyamatot, hogy hozzáadja a többi csomópontot a fürthöz. Nem kell megvárnia, amíg egy csomópont befejezi a fürthöz való csatlakozást a másik elindítása előtt.

## <a name="enable-high-availability"></a>Magas rendelkezésre állás engedélyezése

Miután hozzáadott egy második csomópontot a fürthöz, egy figyelmeztető üzenet jelenik meg a Vezérlőpult irányítópultján, hogy a magas rendelkezésre állási szolgáltatás nincs konfigurálva.

A magas rendelkezésre állás (HA) lehetővé teszi, hogy a fürtcsomópontok kompenzálják egymást, ha az egyik leáll. A HA alapértelmezés szerint nincs engedélyezve.

![Az irányítópult lap "a fürt több csomóponttal rendelkezik, de a HA nincs engedélyezve..." üzenet jelenik meg. a feltételek táblában](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note]
> Ne engedélyezze, HA legalább három csomópontja van a fürtben.

HA bekapcsolja ezt az eljárást, kapcsolja be a következőt:

1. Töltse be a **magas rendelkezésre állás** lapot a **Beállítások** lap **fürt** szakaszában.

   ![HA konfiguráció lap (a fürt > magas rendelkezésre állása). Az "engedélyezés HA" jelölőnégyzet felül van, és a Küldés gomb alul található.](media/fxt-cluster-config/enable-ha.png)

2. Kattintson az **Engedélyezés** lehetőségre, majd kattintson a **Küldés** gombra.

Az **irányítópulton** megjelenik egy riasztás, amely megerősíti, hogy a ha engedélyezve van.

![A "HA most már teljesen konfigurált" üzenetet megjelenítő irányítópult-táblázat](media/fxt-cluster-config/ha-configured-alert.png)

## <a name="next-steps"></a>Következő lépések

Miután hozzáadta az összes csomópontot a fürthöz, a fürt hosszú távú tárolásának konfigurálásával folytassa a telepítést.

> [!div class="nextstepaction"]
> [Háttérbeli tároló hozzáadása és a virtuális névtér beállítása](fxt-add-storage.md)