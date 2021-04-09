---
title: Azure HANA nagyméretű példányok vezérlése Azure Portal | Microsoft Docs
description: Leírja, hogyan azonosítható és hogyan kezelhető az Azure HANA nagyméretű példányai a portálon keresztül
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/31/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 07094e399f153f56746a931a4a100b3c0d38f92f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772076"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Az Azure HANA nagyméretű példányok vezérlése az Azure Portalon keresztül

>[!NOTE]
>A Rev 4,2 esetében kövesse az [BareMetal-példányok kezelése a Azure Portal](../../../baremetal-infrastructure/connect-baremetal-infrastructure.md) témakör utasításait.

Ez a dokumentum azt ismerteti, hogyan jelennek meg a [Hana nagyméretű példányai](./hana-overview-architecture.md) [Azure Portalban](https://portal.azure.com) , és milyen tevékenységek hajthatók végre az Ön számára üzembe helyezett Hana nagyméretű példány-egységekkel Azure Portal használatával. A HANA nagyméretű példányainak láthatósága Azure Portal egy Azure-erőforrás-szolgáltatón keresztül érhető el a HANA nagyméretű példányain, amelyek jelenleg nyilvános előzetes verzióban érhetők el

## <a name="register-hana-large-instance-resource-provider"></a>HANA nagyméretű példány erőforrás-szolgáltató regisztrálása
Általában a Hana nagyméretű példányok üzembe helyezéséhez használt Azure-előfizetése regisztrálva van a HANA nagyméretű példány erőforrás-szolgáltatónál. Ha azonban nem jelenik meg a nagyméretű HANA-példányok üzembe helyezése, akkor regisztrálnia kell az erőforrás-szolgáltatót az Azure-előfizetésében. A HANA nagyméretű példány erőforrás-szolgáltatójának regisztrálása kétféleképpen lehetséges

### <a name="register-through-cli-interface"></a>Regisztrálás CLI felületen
Be kell jelentkeznie az Azure-előfizetésbe, amelyet a HANA nagyméretű példány üzembe helyezéséhez használ az Azure CLI felületén keresztül. A HANA nagyméretű példány-szolgáltatót (újra) a következő paranccsal regisztrálhatja:
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

További információ: [Azure-erőforrás-szolgáltatók és-típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) .


### <a name="register-through-azure-portal"></a>Regisztrálás Azure Portal
A HANA nagyméretű példány erőforrás-szolgáltatóját (újra) Azure Portalon keresztül regisztrálhatja. Az előfizetést fel kell sorolnia Azure Portalban, majd duplán az előfizetésre kell kattintania, amely a HANA nagyméretű példány-egység (ek) üzembe helyezésére szolgál. Ha Ön az előfizetés Áttekintés oldalán található, válassza az "erőforrás-szolgáltatók" lehetőséget az alább látható módon, és írja be a "HANA" kifejezést a keresési ablakba. 

![HLI RP regisztrálása Azure Portal](./media/hana-li-portal/portal-register-hli-rp.png)

A képernyőképen látható, hogy az erőforrás-szolgáltató már regisztrálva van. Ha az erőforrás-szolgáltató még nincs regisztrálva, nyomja meg az "ismételt regisztráció" vagy a "regisztráció" gombot.

További információ: [Azure-erőforrás-szolgáltatók és-típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) .


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>HANA nagyméretű példány-egységek megjelenítése a Azure Portal
A HANA nagyméretű példányok üzembe helyezési kérelmének elküldésekor meg kell adnia azt az Azure-előfizetést, amelyet a HANA nagyméretű példányaihoz is csatlakoztat. Azt javasoljuk, hogy használja ugyanazt az előfizetést, amelyet a HANA nagyméretű példány-egységeken működő SAP-alkalmazás rétegének telepítéséhez használ.
Az első HANA nagyméretű példányok üzembe helyezése után a rendszer létrehoz egy új [Azure-erőforráscsoportot](../../../azure-resource-manager/management/manage-resources-portal.md) az Azure-előfizetésben, amelyet a Hana nagyméretű példány (ok) üzembe helyezési kérelmében küldött be.  Az új erőforráscsoport felsorolja az adott előfizetésben üzembe helyezett HANA nagyméretű példányok egységeit.

Az új Azure-erőforráscsoport megkereséséhez az előfizetésben lévő erőforráscsoportot a Azure Portal bal oldali navigációs paneljén navigálva listázhatja.

![Képernyőfelvétel: Az erőforráscsoportok lehetőség kiemelése.](./media/hana-li-portal/portal-resource-group.png)

A listában szereplő erőforráscsoportok listájában előfordulhat, hogy szűrnie kell azt az előfizetést, amelyet a HANA Large-példányok üzembe helyezéséhez használt.

![Erőforráscsoportok szűrése Azure Portal](./media/hana-li-portal/portal-filtering-subscription.png)

A megfelelő előfizetésre való szűrés után továbbra is lehetnek az erőforráscsoportok hosszú listája. Keressen egy olyan **TXXX** , amelyben az "xxx" három számjegyből áll, például: **T050**. 

Amikor megtalálta az erőforráscsoportot, sorolja fel a részleteit. A kapott lista így néz ki:

![HLI listája Azure Portal](./media/hana-li-portal/portal-hli-units-list.png)

Az összes felsorolt egység egyetlen HANA nagyméretű példány-egységet jelöl, amelyet az előfizetésében telepítettek. Ebben az esetben az előfizetésében üzembe helyezett nyolc különböző HANA nagyméretű példány-egységet tekintjük át.

Ha több HANA nagyméretű példány bérlőt telepített ugyanahhoz az Azure-előfizetéshez, több Azure-erőforráscsoportot is talál 


## <a name="look-at-attributes-of-single-hli-unit"></a>Tekintse meg az egyes HLI egységek attribútumait
A HANA nagyméretű példány-egységek listájában kattintson egyetlen egységre, és Ismerje meg az egyetlen HANA nagyméretű példány-egység részleteit. 

Az áttekintő képernyőn a "továbbiak" gombra kattintva bemutatjuk az egység megjelenítését, amely a következőképpen néz ki:

![HLI-egység áttekintésének megjelenítése](./media/hana-li-portal/portal-show-overview.png)

A különböző attribútumok láthatók, ezek az attribútumok alig különböznek az Azure-beli virtuális gépek attribútumaitól. A bal oldali fejlécben az erőforráscsoport, az Azure-régió, az előfizetés neve és az azonosító, valamint néhány hozzáadott címke látható. Alapértelmezés szerint a HANA nagyméretű példány-egységekhez nincs hozzárendelve címke. A fejléc jobb oldalán megjelenik az egység neve, amelyet a rendszer az üzembe helyezéskor rendel hozzá. Az operációs rendszer és az IP-cím is megjelenik. A virtuális gépekhez hasonlóan a HANA nagyméretű példányának típusa a CPU-szálak és a memória számára is megjelenik. A különböző HANA nagyméretű példány-egységekre vonatkozó további részletek itt láthatók:

- [HLI-hez elérhető termékváltozatok](./hana-available-skus.md)
- [SAP HANA (nagyméretű példányok) tárolási architektúrája](./hana-storage-architecture.md) 

A jobb alsó oldalon található további információk a HANA nagyméretű példány-bélyegző változatát jelentik. Lehetséges értékek:

- 3. változat
- 4. változat

A 4. változat a nagy méretű HANA-példányok legújabb architektúrája, amely az Azure-beli virtuális gépek és a 4. változatban vagy sorokban üzembe helyezett HANA nagyméretű példányok közötti hálózati késés jelentős javításával rendelkezik.
Egy másik nagyon fontos információ található az Áttekintés jobb alsó sarkában, az Azure közelségi elhelyezési csoportjának nevével, amelyet a rendszer automatikusan hoz létre az egyes üzembe helyezett HANA nagyméretű példányok egységéhez. Az SAP-alkalmazás rétegét futtató Azure-beli virtuális gépek üzembe helyezése során a földrajzi elhelyezési csoportra kell hivatkozni. A HANA nagyméretű példány-egységhez társított [Azure Proximity elhelyezési csoport](../../co-location.md) használatával győződjön meg arról, hogy az Azure-beli virtuális gépek üzembe helyezése a Hana nagyméretű példány-egység közelében történik. A közelségi elhelyezési csoportok segítségével megkeresheti az SAP-alkalmazás rétegét ugyanabban az Azure-adatközpontban, mint a 4. változatban üzemeltetett HANA nagyméretű példány-egységeket az [Azure Proximity-elhelyezési csoportok az SAP-alkalmazásokkal való optimális hálózati késés](sap-proximity-placement-scenarios.md)érdekében.

A fejléc jobb oldali oszlopában egy további mező tájékoztatja a HANA nagyméretű példány egységének energiagazdálkodási állapotáról.

> [!NOTE]
> A tápellátási állapot azt ismerteti, hogy a hardvereszköz be van-e kapcsolva vagy ki van-e kapcsolva. Nem ad információt az operációs rendszer működéséről. Egy HANA nagyméretű példány-egység újraindításakor kis idő alatt fog megjelenni, amikor az egység állapota úgy változik, hogy a **kezdési** állapotba **lépjen.** Az **Indítás** állapota azt jelenti, hogy az operációs rendszer elindul, vagy az operációs rendszer teljesen elindult. Ennek eredményeképpen az egység újraindítása után azonnal be kell jelentkeznie az egységbe, amint az állapot **megkezdésre** vált.
> 

Ha a "továbbiak" gombra kattint, további információk jelennek meg. Az egyik további információ a HANA nagyméretű példányok Stamp változatának felülvizsgálatát jeleníti meg, amely az egységet telepítette. Tekintse meg a HANA nagy példányszámú bélyegzők különböző változatait ismertető cikket az [Azure-ban (nagyméretű példányok) SAP HANA](./hana-overview-architecture.md)

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Egyetlen HANA nagyméretű példány-egység tevékenységének keresése 
A HANA nagyméretű példány-egységek áttekintésének megadását követően megtekintheti az adott egység tevékenységeit. A tevékenység naplója a következőképpen nézhet ki:

![Navigációs ablaktábla Azure Portal](./media/hana-li-portal/portal-activity-list.png)

A rögzített fő tevékenységek egyike egy egység újraindítása. A felsorolt adatmennyiség tartalmazza a tevékenység állapotát, a tevékenység elindításának időbélyegét, az előfizetés AZONOSÍTÓját, amelyből a tevékenység aktiválva lett, valamint a tevékenységet indító Azure-felhasználó. 

Egy másik tevékenység, amely bekerül, a rendszer az Azure meta-adataiban lévő egységre módosítja. Az újraindítás után megtekintheti az **írási HANAInstances** tevékenységeit. Ez a típusú tevékenység nem végez módosításokat a HANA nagyméretű Példányi egységen, de az Azure-ban lévő egység meta-adatokra vonatkozó módosításokat dokumentálja. A felsorolt esetekben hozzáadunk egy címkét, és töröltünk egy címkét (lásd a következő szakaszt).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Azure-címke hozzáadása és törlése egy HANA nagyméretű példány-egységhez
Egy másik lehetőség, hogy hozzáad egy [címkét](../../../azure-resource-manager/management/tag-resources.md) egy HANA nagyméretű példány-egységhez. A címkék hozzárendelésének módja nem különbözik a címkék virtuális gépekhez való hozzárendelésének módjától. Csakúgy, mint a virtuális gépek esetében, a címkék az Azure-metaadatokban találhatók, és a HANA nagyméretű példányainál ugyanazok a korlátozások érvényesek, mint a virtuális gépek címkéi.

A címkék törlése ugyanúgy működik, mint a virtuális gépeken. Mindkét tevékenység, a címke alkalmazása és törlése az adott HANA nagyméretű példány-egység tevékenységi naplójában jelenik meg.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>HANA nagyméretű példány-egység tulajdonságainak megtekintése
A szakasz **tulajdonságai** olyan fontos információkat tartalmaznak, amelyeket a példányok átadásakor kap. Ez egy szakasz, amelyben minden olyan információt megkap, amelyet a támogatási esetekben igényelhet, vagy amelyre szüksége lehet a tárolási pillanatkép-konfiguráció beállításakor. Ez a szakasz a példányok körébe tartozó adatgyűjtemény, a példány és az Azure közötti kapcsolat, valamint a tárolási háttérrendszer. A szakasz teteje a következőképpen néz ki:


![a HLI tulajdonságainak legfelső része Azure Portal](./media/hana-li-portal/portal-properties-top.png)

Az első néhány adatelem már az Áttekintés képernyőn látható. Az adat fontos része azonban a ExpressRoute Circuit azonosítója, amelyet az első üzembe helyezett egységek átadásával kapott. Bizonyos támogatási esetekben előfordulhat, hogy a rendszer megkérdezi ezeket az adatkéréseket. Fontos adatbejegyzés jelenik meg a képernyőkép alján. A megjelenő adatmennyiség az NFS-tároló feje azon IP-címe, amely elkülöníti a tárolót a **bérlőhöz** a HANA nagyméretű példány-veremben. Erre az IP-címére akkor is szükség van, ha szerkeszti a [konfigurációs fájlt a tárolási Pillanatképek biztonsági másolatai számára](./hana-backup-restore.md#set-up-storage-snapshots). 

Ahogy lefelé görget a Tulajdonságok ablaktáblán, további, például a HANA nagyméretű példány-egységhez tartozó egyedi erőforrás-AZONOSÍTÓhoz, vagy az üzembe helyezéshez rendelt előfizetés-AZONOSÍTÓhoz juthat.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>HANA nagyméretű példány-egység újraindítása Azure Portal
A Linux operációs rendszer újraindításának kezdeményezése különböző helyzetekben történt, amikor az operációs rendszer nem tudta befejezni az újraindítást. Az újraindítás kényszerítéséhez meg kell nyitnia egy szolgáltatási kérelmet, hogy a Microsoft-műveletek a HANA nagyméretű példányok egységének újraindítását hajtsák végre. A HANA nagyméretű példány-egységek újraindításának funkciói már integrálva vannak a Azure Portalba. Ahogy a HANA nagyméretű példány egységének áttekintés részében szerepel, a gomb az újraindításhoz az adatszakasz tetején jelenik meg.

![Újraindítási lépés #1 a Azure Portal](./media/hana-li-portal/portal-restart-first-step.png)

Az Újraindítás gomb megnyomásakor a rendszer megkérdezi, hogy valóban szeretné-e újraindítani az egységet. Az "igen" gomb megnyomásával erősítse meg, hogy az egység újra fog indulni.

> [!NOTE]
> Az újraindítási folyamat során egy kis idő fog megjelenni, ahol az egység állapota úgy változik, hogy **megkezdi** az indítást az **elindított** állapotba. Az **Indítás** állapota azt jelenti, hogy az operációs rendszer elindul, vagy az operációs rendszer teljesen elindult. Ennek eredményeképpen az egység újraindítása után azonnal be kell jelentkeznie az egységbe, amint az állapot **megkezdésre** vált.

> [!IMPORTANT]
> A HANA nagyméretű példány egységében lévő memóriától függ, a hardver újraindítása és újraindítása, valamint az operációs rendszer akár egy órát is igénybe vehet.


## <a name="open-a-support-request-for-hana-large-instances"></a>Támogatási kérelem megnyitása a HANA nagyméretű példányaihoz
A HANA Large instances-egységek Azure Portal megjelenítésének kihagyása esetén a támogatási kérelmeket kifejezetten HANA nagyméretű példány-egységhez is létrehozhatja. Az **új támogatási kérelem** hivatkozásának követése 

![a szolgáltatási kérelem lépésének kezdeményezése #1 Azure Portal](./media/hana-li-portal/portal-initiate-support-request.png)

A következő képernyőn megjelenő SAP HANA Large Instances szolgáltatás beszerzéséhez előfordulhat, hogy a "minden szolgáltatás" lehetőséget kell választania az alább látható módon.

![Válassza ki a Azure Portal összes szolgáltatását](./media/hana-li-portal/portal-create-service-request.png)

A szolgáltatások listájában megtalálhatja a szolgáltatás **SAP HANA nagyméretű példányát**. A szolgáltatás kiválasztásakor kiválaszthatja az egyes problémák típusait az alábbiak szerint:


![A probléma osztály kiválasztása Azure Portal](./media/hana-li-portal/portal-select-problem-class.png)

A különböző problémák típusai közül választhatja ki a probléma altípusait, hogy kiválassza a probléma további jellemzését. Miután kiválasztotta az altípust, megadhatja a tárgy nevet. Ha elkészült a kiválasztási folyamattal, átléphet a létrehozás következő lépésére. A **megoldások** szakaszban a HANA nagyméretű példányain található dokumentációra mutat, amely a probléma megoldására mutató mutatót jelez. Ha nem talál megoldást a problémára a javasolt dokumentációban, folytassa a következő lépéssel. A következő lépésben a rendszer megkérdezi, hogy a probléma a virtuális gépekkel vagy a HANA Large instances-egységekkel működik-e. Ez az információ segít a támogatási kérelemnek a megfelelő szakértőknek történő irányításában. 

![A Azure Portal támogatási esetének részletei](./media/hana-li-portal/portal-support-request-details.png)

A kérdések megválaszolásával és további részletek megadásával a következő lépéssel ellenőrizheti a támogatási kérést és a küldést.

## <a name="next-steps"></a>Következő lépések

- [SAP HANA (nagyméretű példányok) figyelése az Azure-ban](./troubleshooting-monitoring.md)
- [HANA-oldali monitorozás és hibaelhárítás](./hana-monitor-troubleshoot.md)