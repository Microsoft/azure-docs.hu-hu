---
title: Azure Hybrid Benefit és Linux rendszerű virtuális gépek
description: Ismerje meg, hogyan segítheti a Azure Hybrid Benefit az Azure-on futó linuxos virtuális gépek pénzének megtakarítását.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 73747222b9131fa85ae6ac01c9dedd5b0bbe1d63
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543405"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>A Linux rendszerű virtuális gépekre vonatkozó Azure Hybrid Benefit alkalmazása

A Azure Hybrid Benefit egy licencelési kedvezmény, amely segítségével jelentősen csökkentheti a Red Hat Enterprise Linux (RHEL) és a SUSE Linux Enterprise Server (SLES) virtuális gépek felhőben történő futtatásának költségeit. Ezzel a kedvezménysel csak a virtuális gép infrastrukturális költségeit számítjuk fel, mivel a RHEL vagy a SLES előfizetése tartalmazza a szoftver díját. Az előnyök az összes RHEL és SLES piactéren elvégezhető (TB) lemezkép esetében elérhetők.

A Linux rendszerű virtuális gépek Azure Hybrid Benefit már nyilvánosan elérhető.

## <a name="benefit-description"></a>Juttatás leírása

A Azure Hybrid Benefit segítségével áttelepítheti a helyszíni RHEL és a SLES-kiszolgálókat az Azure-ba, ha átalakítja a meglévő RHEL és SLES TB virtuális gépeket az Azure-ban a saját előfizetés (BYOS) számlázására. Az Azure-beli TB-rendszerképekből üzembe helyezett virtuális gépek jellemzően az infrastruktúra díját és a szoftver díját is megterhelik. A Azure Hybrid Benefit használatával a TB virtuális gépek újratelepítése nélkül alakíthatók át BYOS számlázási modellre, így elkerülhető az állásidő kockázata.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit a Cost vizualizációt Linux rendszerű virtuális gépeken.":::

Miután engedélyezte a juttatást a RHEL vagy a SLES VM-re, már nem számítunk fel díjat a további, általában a TB-alapú virtuális gépeken felmerülő díjakért. Ehelyett a virtuális gép egy BYOS díjat számít fel, amely csak a számítási hardver díját és a szoftveres díjat tartalmazza.

Azt is megteheti, hogy olyan virtuális gépet alakít át, amelynek az előnye engedélyezve volt a TB számlázási modellre.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>A Linux rendszerű virtuális gépekre vonatkozó Azure Hybrid Benefit jogosultságok köre

Azure Hybrid Benefit elérhető az Azure Marketplace-ről származó összes RHEL és SLES TB-lemezképhez. Az előny még nem érhető el RHEL vagy SLES BYOS-lemezképekhez vagy egyéni rendszerképekhez az Azure Marketplace-en.

Az Azure dedikált gazdagép példányai és az SQL Hybrid előnyök nem jogosultak Azure Hybrid Benefitre, ha már használja a Linux rendszerű virtuális gépek előnyeit.

## <a name="get-started"></a>Bevezetés

### <a name="red-hat-customers"></a>Red Hat-ügyfelek

A RHEL Azure Hybrid Benefit a következő feltételeknek megfelelő Red Hat-ügyfelek számára érhető el:

- Az Azure-ban való használatra jogosult aktív vagy nem használt RHEL-előfizetések
- Egy vagy több előfizetést engedélyeztek az Azure-ban a [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) program használatával

> [!IMPORTANT]
> Győződjön meg arról, hogy a megfelelő előfizetés engedélyezve van a [felhőalapú hozzáférési](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) programban.

A Red Hat előnyeinek használatának megkezdéséhez:

1. A [Red Hat Cloud Access felhasználói felületének](https://access.redhat.com/management/cloud)használatával engedélyezzen egy vagy több jogosult RHEL-előfizetést az Azure-ban való használathoz.

   A Red Hat Cloud Access engedélyezési folyamat során megadott Azure-előfizetések ezután használhatják a Azure Hybrid Benefit funkciót.
1. Alkalmazza Azure Hybrid Benefit a meglévő RHEL TB virtuális gépekre, valamint az Azure Marketplace TB-lemezképekről üzembe helyezett új RHEL virtuális gépekre. Az előny engedélyezéséhez használhatja a Azure Portal vagy az Azure CLI-t is.
1. Kövesse az ajánlott [következő lépéseket](https://access.redhat.com/articles/5419341) a RHEL virtuális gépek frissítési forrásainak konfigurálásához és a RHEL-előfizetés megfelelőségi iránymutatásaihoz.


### <a name="suse-customers"></a>SUSE-ügyfelek

A SUSE előnyeinek használatának megkezdéséhez:

1. Regisztráljon a SUSE nyilvános felhő programjával.
1. Alkalmazza az előnyt az újonnan létrehozott vagy meglévő virtuális gépekre a Azure Portal vagy az Azure CLI használatával.
1. Regisztrálja az előnyben részesített virtuális gépeket egy külön frissítési forrással.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>A Azure Portal kedvezményének engedélyezése és letiltása

Engedélyezheti a meglévő virtuális gépek előnyét, ha a bal oldalon meglátogatja a **konfigurációs** lehetőséget, és követi a szükséges lépéseket. Az új virtuális gépek előnyeit a virtuálisgép-létrehozási élményben engedélyezheti.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Azure Portal például egy meglévő virtuális gép előnyének engedélyezéséhez:
1. Látogasson el [Microsoft Azure Portal](https://portal.azure.com/)
1. A portálon nyissa meg a virtuális gép létrehozása lapot.
 ![AHB virtuális gép létrehozásakor](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Kattintson a jelölőnégyzetre a AHB konvertálásának engedélyezéséhez és a felhőalapú hozzáférési licencek használatához.
 ![AHB virtuális gép létrehozásakor jelölőnégyzet](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. A következő utasításokat követve hozzon létre egy virtuális gépet
1. Ellenőrizze a **konfiguráció** panelt, és megjelenik az engedélyezve lehetőség. 
![AHB-konfiguráció panel létrehozása a létrehozás után](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Azure Portal példa az előny engedélyezésére a virtuális gép létrehozása során:
1. Látogasson el [Microsoft Azure Portal](https://portal.azure.com/)
1. Nyissa meg a virtuális gép lapot, amelyen alkalmazni kívánja az átalakítást.
1. Válassza a bal oldali **konfigurációs** lehetőséget. Ekkor megjelenik a licencelés szakasz. A AHB átalakításának engedélyezéséhez jelölje be az "igen" választógombot, és jelölje be a megerősítő jelölőnégyzetet.
![AHB-konfiguráció panel létrehozása a létrehozás után](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> Ha létrehozott egy **Egyéni pillanatképet** vagy egy RHEL vagy SLES TB-piactér rendszerképét, akkor **csak az Azure** CLI-t használhatja Azure Hybrid Benefit engedélyezéséhez. Ez ismert korlátozás, és jelenleg nincs olyan ütemterv, amely az Azure Portalon is elérhetővé teszi ezt a képességet.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Az előny engedélyezése és letiltása az Azure CLI-ben

A `az vm update` parancs használatával frissítheti a meglévő virtuális gépeket. RHEL virtuális gépek esetén futtassa a parancsot a `--license-type` paraméterrel `RHEL_BYOS` . SLES virtuális gépek esetén futtassa a parancsot a `--license-type` paraméterrel `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>PARANCSSORI felület – példa az előny engedélyezésére
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>PARANCSSORI felület – példa az előny letiltására
Az előny letiltásához használja a következő `--license-type` értéket `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-példa a nagy mennyiségű virtuális gépen elérhető előnyök engedélyezésére
Ha nagyszámú virtuális gépen szeretné engedélyezni az előnyt, használhatja a `--ids` paramétert az Azure CLI-ben:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Az alábbi példák két módszert mutatnak be az erőforrás-azonosítók listájának lekéréséhez: egyet az erőforráscsoport szintjén, és egyet az előfizetés szintjén.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>A Azure Hybrid Benefit alkalmazása a virtuális gép létrehozási idején
A Azure Hybrid Benefit meglévő utólagos elszámolású virtuális gépekre való alkalmazása mellett a virtuális gép létrehozásakor is meghívhatja azt. Az előnyök a következők:
- A TB és a BYOS virtuális gépeket ugyanazzal a képpel és folyamattal is kiépítheti.
- Lehetővé teszi a jövőbeli licencelési mód módosításait, ami nem érhető el egy BYOS képpel, vagy ha saját virtuális gépet hoz.
- A virtuális gép alapértelmezés szerint a Red Hat frissítési infrastruktúrához (RHUI) lesz csatlakoztatva, így biztosítva, hogy naprakész és biztonságos maradjon. Az üzembe helyezést követően bármikor módosíthatja a frissített mechanizmust.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Virtuális gép Azure Hybrid Benefit állapotának keresése
Egy virtuális gép Azure Hybrid Benefit állapotát az Azure CLI-vel vagy az Azure Instance Metadata Service használatával tekintheti meg.

### <a name="azure-cli"></a>Azure CLI

Erre a célra a `az vm get-instance-view` parancsot használhatja. Keressen egy `licenseType` mezőt a válaszban. Ha a `licenseType` mező létezik, és az értéke `RHEL_BYOS` vagy `SLES_BYOS` , a virtuális gépnek engedélyezve van az előnye.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata szolgáltatás

A virtuális GÉPEN belül lekérdezheti az igazolt metaadatokat az Azure Instance Metadata Serviceban a virtuális gép `licenseType` értékének meghatározásához. A `licenseType` vagy a `RHEL_BYOS` érték `SLES_BYOS` azt jelzi, hogy a virtuális gépnek engedélyezve van az előnye. [További információ az igazolt metaadatokról](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Megfelelőség

### <a name="red-hat"></a>Red Hat

A RHEL Azure Hybrid Benefitt használó ügyfelek elfogadják az Azure Marketplace RHEL-ajánlatokkal kapcsolatos általános [jogi feltételeket](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) és [adatvédelmi nyilatkozatot](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) .

Azok a felhasználók, akik a RHEL Azure Hybrid Benefit használják, három lehetősége van a szoftverfrissítések és a javítások biztosítására a virtuális gépeken:

- [Red Hat frissítési infrastruktúra](../workloads/redhat/redhat-rhui.md) (alapértelmezett beállítás)
- Red Hat Satellite Server
- Red Hat előfizetés-kezelő

Azok az ügyfelek, akik a RHUI lehetőséget választják, továbbra is használhatják a RHUI fő frissítési forrásaként a Azure Hybrid Benefit RHEL virtuális gépekhez anélkül, hogy RHEL előfizetéseket csatlakoztatni a virtuális gépekhez. Azok az ügyfelek, akik a RHUI lehetőséget választják, a RHEL-előfizetés megfelelőségének biztosításáért felelősek.

A Red Hat Satellite Servert vagy a Red Hat előfizetés-kezelőt használó ügyfeleknek el kell távolítaniuk a RHUI-konfigurációt, majd csatlakoztatnia kell a felhőalapú hozzáférés-kompatibilis RHEL-előfizetést a Azure Hybrid Benefit RHEL virtuális gépekhez  

A Red Hat-előfizetések megfelelőségével, a szoftverfrissítések és a Azure Hybrid Benefit RHEL virtuális gépek forrásaival kapcsolatos további információkért tekintse meg a [Red Hat című cikket, amely az RHEL-előfizetések használatáról Azure Hybrid Benefit](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Ha a SLES-alapú virtuális gépekhez Azure Hybrid Benefit szeretne használni, és a SLES-TB a BYOS-re vagy a SLES BYOS-re való áttéréssel kapcsolatos információkért lásd: [SUSE Linux Enterprise és Azure Hybrid Benefit](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/). 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>A fenntartott példányok Azure Hybrid Benefit előzetes verzióban érhető el

Azure Reservations (Azure Reserved Virtual Machine Instances) segítségével pénzt takaríthat meg, ha egy vagy több termékre vonatkozó egyéves vagy hároméves terveket végez. A [fenntartott példányokról itt](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)talál további információt. A Azure Hybrid Benefit a [fenntartott virtuálisgép-példány (RIs)](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation)előzetes verziójában érhető el. Ez azt jelenti, hogy ha kedvezményes díjszabással vásárolt számítási költségeket a RI használatával, a RHEL és a SUSE licencelési költségeihez AHB kedvezményt is alkalmazhat. Az AHB juttatás egy RI-példányra való alkalmazásának lépései pontosan ugyanazok maradnak, mint a normál virtuális gépek esetében.
![AHB a RIs számára](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Ha már megvásárolta a RHEL vagy a SUSE TB szoftver foglalását az Azure Marketplace-en, várjon, amíg a foglalás befejeződik a Azure Hybrid Benefit használata előtt.


## <a name="frequently-asked-questions"></a>Gyakori kérdések
*K: használhatok `RHEL_BYOS` SLES-lemezképpel rendelkező licencet, vagy fordítva?*

A: nem. Olyan licencelési típust próbál meg megadni, amely nem felel meg a virtuális gépen futó terjesztésnek, nem frissíti a számlázási metaadatokat. Ha azonban véletlenül helytelenül írja be a licenc típusát, a virtuális gép újbóli frissítése a megfelelő licenc-típusra továbbra is engedélyezi a kedvezményt.

*K: a Red Hat Cloud Access szolgáltatásban regisztráltam, de továbbra sem engedélyezhető a RHEL virtuális gépeken való előny. Mit tegyek?*

Válasz: eltarthat egy ideig, amíg a Red Hat Cloud Access-előfizetés regisztrálása a Red Hat-ról az Azure-ra terjed. Ha egy munkanap után is megjelenik a hiba, forduljon a Microsoft támogatási szolgálatához.

*K: üzembe helyezett egy virtuális gépet a RHEL BYOS "Golden lemezkép" használatával. Átválthatom a BYOS-ből a TB-re való számlázást?*

A: nem. A Azure Hybrid Benefit csak utólagos elszámolású lemezképeken támogatja az átalakítást.

*K: Feltöltöttem a saját RHEL-rendszerképét a helyszínen (Azure Migrate, Azure Site Recovery vagy más módon) az Azure-ba. Átválthatom a BYOS-ből a TB-re való számlázást?*

A: nem. A Azure Hybrid Benefit képesség jelenleg csak az Azure Marketplace-en RHEL és SLES lemezképek esetében érhető el. 

*K: Feltöltöttem a saját RHEL-rendszerképét a helyszínen (Azure Migrate, Azure Site Recovery vagy más módon) az Azure-ba. Kell-e bármit kihasználnia a Azure Hybrid Benefit?*

A: nem. A feltöltött RHEL-lemezképek már BYOS minősülnek, és csak az Azure infrastrukturális költségeiért kell fizetni. Ön felelős az előfizetési költségek RHEL, ugyanúgy, mint a helyszíni környezetekhez. 

*K: használhatok Azure Hybrid Benefit az Azure Marketplace RHEL és a SLES SAP-lemezképeken üzembe helyezett virtuális gépeken?*

Válasz: igen. A `RHEL_BYOS` RHEL virtuális gépekhez és az `SLES_BYOS` Azure Marketplace RHEL és SLES SAP-lemezképekben üzembe helyezett virtuális gépek átalakításához használhatja a licenc típusát.

*K: használhatom a Azure Hybrid Benefitt a virtuálisgép-méretezési csoportokban a RHEL és a SLES?*

A: igen, Azure Hybrid Benefit a RHEL és SLES virtuálisgép-méretezési csoportjaiban előzetes verzióban érhető el. Erről [a kedvezményről és annak használatáról itt](https://docs.microsoft.com/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux-vmss)tájékozódhat. 

*K: használhatom Azure Hybrid Benefit a RHEL és a SLES fenntartott példányain?*

A: igen, Azure Hybrid Benefit a RHEL és a SLES fenntartott példányán előzetes verzióban érhető el. Erről [a kedvezményről és annak használatáról itt](#azure-hybrid-benefit-on-reserved-instances-is-in-preview)tájékozódhat.

*K: használhatom a Azure Hybrid Benefitt a RHEL-lemezképeken SQL Server üzembe helyezett virtuális gépen?*

A: nem. A virtuális gépek támogatásához nincs terv.

*K: használhatom Azure Hybrid Benefit a RHEL Virtual adatközpont-előfizetéshez?*

A: nem. A VDC nem támogatott az Azure-ban, beleértve a AHB is.  
 

## <a name="common-problems"></a>Gyakori problémák
Ez a szakasz az esetlegesen felmerülő gyakori problémákat és a mérséklési lépéseket sorolja fel.

| Hiba | Kockázatcsökkentés |
| ----- | ---------- |
| "A műveletet nem lehetett befejezni, mert a rekordok azt mutatják, hogy az Azure-előfizetésében nem sikerült engedélyezni a Red Hat Cloud Access használatát..." | A RHEL virtuális gépekkel való ellátás használatához először [regisztrálnia kell az Azure-előfizetéseket a Red Hat Cloud Access](https://access.redhat.com/management/cloud)használatával.

## <a name="next-steps"></a>Következő lépések
* [Megtudhatja, hogyan hozhat létre és frissíthet virtuális gépeket, és hogyan adhat hozzá licenceket (RHEL_BYOS, SLES_BYOS) az Azure CLI-vel való Azure Hybrid Benefithoz](/cli/azure/vm)
