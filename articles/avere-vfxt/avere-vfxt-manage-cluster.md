---
title: A avere vFXT-fürt kezelése – Azure
description: Avere-fürt kezelése – csomópontok hozzáadása vagy eltávolítása, újraindítás, Leállítás vagy a vFXT-fürt megsemmisítése
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4135bfe528c33a2beaeb21438181deb5b19ad12e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85505494"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Az Avere vFXT-fürt felügyelete

Előfordulhat, hogy az Azure-fürthöz tartozó avere-vFXT életciklusának egy pontján szükség lehet a fürtcsomópontok hozzáadására, illetve a fürt elindítására vagy újraindítására. A projekt befejezését követően tudnia kell, hogyan állíthatja le és véglegesen eltávolítja a fürtöt.

Ez a cikk azt ismerteti, hogyan adhat hozzá vagy távolíthat el fürtcsomópontok és egyéb alapvető fürtműveleteket. Ha módosítania kell a fürt beállításait, vagy figyelnie kell a munkáját, használja a [avere Vezérlőpultot](avere-vfxt-cluster-gui.md).

A felügyeleti feladattól függően előfordulhat, hogy a következő három eszköz egyikét kell használnia: avere Vezérlőpult, a vfxt.py parancssori fürtszolgáltatás parancsfájlja és a Azure Portal.

Ez a táblázat áttekintést nyújt az egyes feladatokhoz használható eszközökről.

| Művelet | Avere Vezérlőpult | vfxt.py  | Azure Portal |
| --- | --- | --- | --- |
| Fürtcsomópontok hozzáadása | nem | igen | nem |
| Fürtcsomópontok eltávolítása | igen | nem | nem |
| Fürtcsomópont leállítása | Igen (újraindíthatják a szolgáltatásokat vagy újraindítást is) | nem | csomópontos virtuális gép leállítása a portálról csomópont-hibaként értelmezve |
| Leállított csomópont elindítása | nem | nem | igen |
| Egyetlen fürtcsomópont megsemmisítése | nem | nem | igen |
| A fürt újraindítása |  |  |  |
| A fürt biztonságos leállítása vagy leállítása | igen | igen | nem |
| A fürt megsemmisítése  | nem | igen | igen, de az adatok integritása nem garantált |

Az egyes eszközökre vonatkozó részletes utasítások alább találhatók.

## <a name="about-stopped-instances-in-azure"></a>A leállított példányok az Azure-ban

Ha leállítja vagy leállítja az Azure-beli virtuális gépeket, leállítja a számítási költségeket, de továbbra is fizetnie kell a tárterületért. Ha leállít egy vFXT-csomópontot vagy a teljes vFXT-fürtöt, és nem kívánja újraindítani, a kapcsolódó virtuális gépek törléséhez használja a Azure Portal.

A Azure Portal egy *leállított* csomópont (amely újraindítható) megjeleníti a Azure Portalban **leállított** állapotot. A *törölt* csomópontok a leállított állapotot jelenítik meg (fel nem **osztott)** , és már nem számítanak fel számítási vagy tárolási díjat.

A virtuális gép törlése előtt győződjön meg arról, hogy az összes módosult adatok a gyorsítótárból a háttérbeli tárterületre lettek írva a avere Vezérlőpult vagy a vfxt.py beállítások használatával a fürt leállításához vagy leállításához.

## <a name="manage-the-cluster-with-avere-control-panel"></a>A fürt kezelése a avere Vezérlőpulttal

A következő feladatokhoz használhatja a avere Vezérlőpultot:

* Az egyes csomópontok leállítása vagy újraindítása
* Csomópont eltávolítása a fürtből
* A teljes fürt leállítása vagy újraindítása

A avere Vezérlőpulton rangsorolja az adatok integritását, így a valószínűleg pusztító művelet előtt megkísérli a módosított adatok írását a háttérbeli tárolóba. Ez biztonságosabbá válik, mint a Azure Portal.

A avere Vezérlőpult webböngészőből való elérése. Ha segítségre van szüksége, kövesse az [vFXT-fürt eléréséhez](avere-vfxt-cluster-gui.md) szükséges utasításokat.

### <a name="manage-nodes-with-avere-control-panel"></a>Csomópontok kezelése a avere Vezérlőpulttal

Az **FXT-csomópontok** beállításai lap az egyes csomópontok kezelésére szolgáló vezérlőket tartalmaz.

Csomópont leállításához, újraindításához vagy eltávolításához keresse meg a csomópontot a FXT- **csomópontok** lapon, és kattintson a megfelelő gombra a **műveletek** oszlopban.

> [!NOTE]
> Előfordulhat, hogy az IP-címek a fürtcsomópontok között mozognak, amikor az aktív csomópontok száma megváltozik.

További információért olvassa el a [fürt > FXT csomópontot](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) a avere-fürt beállítási útmutatójában.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>A fürt leállítása vagy újraindítása a avere Vezérlőpulttal

A **rendszer-karbantartási** beállítások lapon a fürtszolgáltatások újraindításához, a fürt újraindításához, vagy a fürt biztonságos bekapcsolásához szükséges parancsok vannak. Részletekért olvassa el az [adminisztráció > Rendszerkarbantartás](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (a avere-fürt beállítási útmutatója) című témakört.

Amikor egy fürt megkezdi a leállítását, az állapotjelző üzeneteket könyvel az **irányítópult** lapra. Néhány pillanat elteltével az üzenetek leállnak, és végül a avere Vezérlőpult-munkamenete leáll, ami azt jelenti, hogy a fürt leállt.

## <a name="manage-the-cluster-with-vfxtpy"></a>A fürt kezelése a vfxt.py

a vfxt.py egy parancssori eszköz a fürtök létrehozásához és felügyeletéhez.

a vfxt.py előre telepítve van a fürt vezérlő virtuális gépén. Ha másik rendszeren szeretné telepíteni, tekintse meg a dokumentációját a következő címen: <https://github.com/Azure/AvereSDK> .

A vfxt.py parancsfájl használható a következő fürtszolgáltatási feladatokhoz:

* Új csomópontok hozzáadása fürthöz
* Fürt leállítása vagy elindítása  
* Fürt elpusztítása

A avere vezérlőpulthoz hasonlóan a vfxt.py-műveletek is megpróbálják meggyőződni arról, hogy a módosult adatok véglegesen tárolódnak a háttérbeli tárolón a fürt vagy a csomópont leállítása vagy megsemmisítése előtt. Ez biztonságosabbá válik, mint a Azure Portal.

A GitHubon teljes vfxt.py használati útmutató érhető el: a [felhőalapú fürt kezelése a vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Fürtcsomópontok hozzáadása a vfxt.py

A fürt csomópontjainak hozzáadására szolgáló minta parancsfájlt a rendszer a tartományvezérlőn is tartalmazza. Keresse ``./add-nodes`` meg a vezérlőt, és nyissa meg egy szerkesztőben, és szabja testre a fürt adataival.

A parancs használatához a fürtnek futnia kell.

Adja meg a következő értékeket:

* A fürt erőforráscsoport-neve, valamint a hálózati és a tárolási erőforrások esetében is, ha nem ugyanabban az erőforráscsoporthoz találhatók, mint a fürt
* Fürt helye
* Fürt hálózata és alhálózata
* Fürtcsomópont-hozzáférési szerepkör (használja a beépített szerepkör avere- [kezelőjét](../role-based-access-control/built-in-roles.md#avere-operator))
* Fürt felügyeleti IP-címe és rendszergazdai jelszava
* Hozzáadandó csomópontok száma (1, 2 vagy 3)
* Csomópont-példány típusa és a gyorsítótár méretének értékei

Ha nem használja a prototípust, a következőhöz hasonló parancsot kell létrehoznia, beleértve az összes fent leírt információt.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

További információért olvassa el a [csomópontok hozzáadása fürthöz](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) című részt a vfxt.py használati útmutatójában.

### <a name="stop-a-cluster-with-vfxtpy"></a>Fürt leállítása a vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Leállított fürt elindítása a vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Mivel a fürt leáll, a fürtcsomópontok megadásához át kell adni a példány-azonosítókat. További információért olvassa el, hogy [melyik fürtöt szeretné módosítani](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) a vfxt.py használati útmutatójában.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Fürt elpusztítása a vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

A beállítás ``--quick-destroy`` akkor használható, ha nem szeretné menteni a módosított adatok mentését a fürt gyorsítótárából.

További információért olvassa el a [vfxt.py használati útmutatóját](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) .

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Fürtözött virtuális gépek kezelése a Azure Portal

A Azure Portal a fürt virtuális gépei egyenkénti megsemmisítésére használható, de az adatok integritása nem garantált, ha a fürt nem áll le tisztán.

A Azure Portal a következő fürtszolgáltatási feladatokhoz használhatók:

* Leállított vFXT-csomópont elindítása
* Egy adott vFXT-csomópont leállítása (a fürt csomópont-meghibásodásként értelmezi ezt)
* VFXT-fürt megsemmisítése, *Ha* nem kell meggyőződnie arról, hogy a fürt gyorsítótárában lévő módosult adattárolók a Core Filer-ba íródnak
* A vFXT-csomópontok és más fürterőforrás-erőforrások végleges eltávolítása a biztonságos leállítás után

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>VFXT-példányok újraindítása a Azure Portal

Ha újra kell indítania egy leállított csomópontot, akkor a Azure Portal kell használnia. A bal oldali menüben válassza a **virtuális gépek** lehetőséget, majd kattintson a virtuális gép nevére a listában az áttekintő oldal megnyitásához.

A virtuális gép újraaktiválásához kattintson az Áttekintés lap tetején található **Start** gombra.

![A leállított virtuális gép elindítására szolgáló lehetőséget megjelenítő Azure Portal képernyő](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Fürtcsomópontok törlése

Ha törölni szeretné az egyik csomópontot a vFXT-fürtből, de meg kell őriznie a fürt többi részét, először [el kell távolítania a csomópontot a fürtből](#manage-nodes-with-avere-control-panel) a avere Vezérlőpultján.

> [!CAUTION]
> Ha töröl egy csomópontot anélkül, hogy először el kellene távolítani a vFXT-fürtről, az adatok elveszhetnek.

A vFXT-csomópontként használt egy vagy több példány végleges megsemmisítéséhez használja a Azure Portal.
A bal oldali menüben válassza a **virtuális gépek** lehetőséget, majd kattintson a virtuális gép nevére a listában az áttekintő oldal megnyitásához.

A virtuális gép végleges megsemmisítéséhez kattintson az Áttekintés lap tetején található **Törlés** gombra.

Ezt a módszert használhatja a fürtcsomópontok végleges eltávolításához, miután azok biztonságosan leállnak.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>A fürt megsemmisítése a Azure Portal

> [!NOTE]
> Ha azt szeretné, hogy a gyorsítótárban megmaradt összes ügyfél megváltoztassa a háttérbeli tárterületet, használja a vfxt.py `--destroy` kapcsolót, vagy a avere Vezérlőpulton állítsa le a fürtöt a Azure Portal csomópont példányainak eltávolítása előtt.

A csomópont-példányok végleges megsemmisítéséhez törölje őket a Azure Portal. Ezeket egyenként törölheti a fent leírtak szerint, vagy a **Virtual Machines** lapon megkeresheti az összes fürt virtuális gépet, kijelölheti őket a jelölőnégyzetekkel, majd a **Törlés** gombra kattintva eltávolíthatja őket egy művelettel.

![A portálon lévő virtuális gépek listája, a "cluster" kifejezéssel szűrve, a négy bejelölt és a kijelölt három](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>További fürterőforrás-erőforrások törlése a Azure Portal

Ha a vFXT-fürthöz további erőforrásokat hozott létre, előfordulhat, hogy el szeretné távolítani őket a fürt lebontásának részeként. Ne semmisítse meg a szükséges adatokat tartalmazó elemeket, illetve a más projektekkel megosztott elemeket.

A fürtcsomópontok törlésén kívül érdemes eltávolítani ezeket az összetevőket:

* A fürt vezérlő virtuális gépe
* Fürtcsomópontokon társított adatlemezek
* A fürt összetevőihez társított hálózati adapterek és nyilvános IP-címek
* Virtuális hálózatok
* Storage-tárolók és Storage-fiókok (**csak** akkor, ha nem tartalmaznak fontos adatvesztést)
* Rendelkezésre állási csoport

![Azure Portal "minden erőforrás" lista, amely a tesztelési fürthöz létrehozott erőforrásokat jeleníti meg](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Fürt erőforráscsoport törlése a Azure Portal

Ha kifejezetten a fürt házához tartozó erőforráscsoportot hozott létre, akkor a fürt összes kapcsolódó erőforrását megsemmisítheti az erőforráscsoport megsemmisítésével.

> [!Caution]
> Csak akkor semmisítse meg az erőforráscsoportot, ha biztos benne, hogy a csoportban található egyik érték sincs. Győződjön meg például arról, hogy áthelyezte az összes szükséges adatforrást az erőforráscsoporthoz tartozó összes tárolóból.  

Egy erőforráscsoport törléséhez kattintson az **erőforráscsoportok** elemre a portál bal oldali menüjében, és szűrje az erőforráscsoportok listáját, hogy megkeresse a vFXT-fürthöz létrehozott csoportot. Válassza ki az erőforráscsoportot, és kattintson a panel jobb oldalán található három pontra. Válassza az **Erőforráscsoport törlése** elemet. A portál megkéri, hogy erősítse meg a törlést, amely visszafordíthatatlan.

![Az "erőforráscsoport törlése" műveletet megjelenítő erőforráscsoport](media/avere-vfxt-delete-resource-group.png)
