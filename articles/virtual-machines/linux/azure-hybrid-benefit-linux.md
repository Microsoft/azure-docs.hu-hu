---
title: Azure Hybrid Benefit és Linux rendszerű virtuális gépek
description: Megtudhatja, Azure Hybrid Benefit hogyan takaríthat meg pénzt az Azure-ban futó Linux rendszerű virtuális gépeken.
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
ms.openlocfilehash: 774f4be6a5aa0e0e772086c52938881c6637b261
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588190"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>A Azure Hybrid Benefit linuxos virtuális gépekre

Azure Hybrid Benefit licencelési előny, amely segít jelentősen csökkenteni az Red Hat Enterprise Linux (RHEL) és az SUSE Linux Enterprise Server (SLES) virtuális gépek felhőben való futtatásának költségeit. Ezzel a kedvezménysel csak a virtuális gép infrastrukturális költségeiért fizet, mivel az RHEL- vagy SLES-előfizetés fedezi a szoftver díját. Ez a kedvezmény minden RHEL- és SLES Marketplace-ről elérhető, pay-as-you-go (PAYG) rendszerképhez.

Azure Hybrid Benefit linuxos virtuális gépekhez elérhető.

## <a name="benefit-description"></a>Juttatás leírása

Az Azure Hybrid Benefit használatával a helyszíni RHEL- és SLES-kiszolgálókat az Azure-ba miminálhatja úgy, hogy az Azure-ban meglévő RHEL és SLES PAYG virtuális gépeket saját előfizetéses (BYOS) számlázásra konvertálja. Az Azure PAYG-rendszerképeiből üzembe helyezett virtuális gépek általában infrastruktúra- és szoftverdíjat is díjat számítnak fel. A Azure Hybrid Benefit a PAYG virtuális gépek újratelepítés nélkül konvertálhatók BYOS számlázási modellre, így elkerülhető az állásidővel kapcsolatos kockázat.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit költségek megjelenítése Linux rendszerű virtuális gépeken.":::

Miután engedélyezi az kedvezményt az RHEL vagy SLES virtuális gépen, a továbbiakban nem számítunk fel díjat a PAYG virtuális gépeken jellemzően felmerülő további szoftverdíjért. Ehelyett a virtuális gép elkezdi a BYOS-díjat fizetni, amely csak a számítási hardver díját tartalmazza, szoftverdíj nélkül.

Dönthet úgy is, hogy egy olyan virtuális gépet alakít át payG számlázási modellre, amely esetében engedélyezve volt a kedvezmény.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Az Azure Hybrid Benefit jogosultsága Linux rendszerű virtuális gépekre

Azure Hybrid Benefit RHEL- és SLES PAYG-rendszerképekhez a Azure Marketplace. Ez az előny még nem érhető el RHEL- vagy SLES BYOS-rendszerképekhez vagy egyéni rendszerképekhez a Azure Marketplace.

Azure Dedicated Host példányok és az SQL Hybrid Benefits nem jogosultak a Azure Hybrid Benefit, ha már használja a Linux rendszerű virtuális gépek előnyeit.

## <a name="get-started"></a>Bevezetés

### <a name="red-hat-customers"></a>Red Hat-ügyfelek

Azure Hybrid Benefit RHEL-hez való Azure Hybrid Benefit az alábbi két feltételnek megfelelő Red Hat-ügyfelek számára érhető el:

- Olyan aktív vagy nem használt RHEL-előfizetésekkel rendelkezik, amelyek jogosultak az Azure-ban való használatra
- Engedélyezte egy vagy több előfizetés használatát az Azure-ban a Red Hat Cloud Access [programmal](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

> [!IMPORTANT]
> Győződjön meg arról, hogy a megfelelő előfizetés van engedélyezve a [felhőelérési](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) programban.

A Red Hat juttatás használatának elkezdenie:

1. Engedélyezzen egy vagy több jogosult RHEL-előfizetést az Azure-ban való használatra a Red Hat Cloud Access [felületén.](https://access.redhat.com/management/cloud)

   Az engedélyezési folyamat során Red Hat Cloud Access Azure-előfizetések ezután használhatják a Azure Hybrid Benefit funkciót.
1. Alkalmazza Azure Hybrid Benefit RHEL PAYG virtuális gépekre és a PAYG-rendszerképekből üzembe helyezett Azure Marketplace RHEL virtuális gépekre. A kedvezmény engedélyezéséhez Azure Portal Azure CLI-t is használhatja.
1. Kövesse az [ajánlott következő lépéseket](https://access.redhat.com/articles/5419341) a frissítési források RHEL virtuális gépekhez való konfigurálásához és az RHEL-előfizetés megfelelőségi irányelveinek beállításához.


### <a name="suse-customers"></a>SUSE-ügyfelek

A SUSE-kedvezmény használatának elkezdenie:

1. Regisztráljon a nyilvános SUSE felhőprogramra.
1. Alkalmazza a kedvezményt az újonnan létrehozott vagy meglévő virtuális gépekre a Azure Portal Azure CLI használatával.
1. Regisztrálja a kedvezményben részesülő virtuális gépeket egy külön frissítésforrással.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Az előnyök engedélyezése és letiltása a Azure Portal

A meglévő virtuális gépekre vonatkozó előnyöket  úgy engedélyezheti, ha a bal oldalon található Konfiguráció lehetőséget választja, és követi az ott található lépéseket. Az új virtuális gépeken a virtuális gép létrehozása során engedélyezheti az előnyöket.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Azure Portal példa egy meglévő virtuális gép előnyének engedélyezéséhez:
1. Látogasson [el Microsoft Azure Portal](https://portal.azure.com/)
1. A portálon kattintson a Virtuális gép létrehozása lapra.
 ![AHB a virtuális gép létrehozása során](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. A AHB-konverzió engedélyezéséhez és a felhőalapú hozzáférési licencek használatának engedélyezéséhez kattintson a jelölőnégyzetre.
 ![AHB a virtuális gép létrehozásakor jelölőnégyzet](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Virtuális gép létrehozása a következő utasítások alapján
1. Ellenőrizze a **Konfiguráció panelt,** és a beállítás engedélyezve lesz. 
![AHB-konfiguráció panel a létrehozás után](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Azure Portal, hogy a virtuális gép létrehozása során engedélyezze az előnyöket:
1. Látogasson [el Microsoft Azure Portal](https://portal.azure.com/)
1. Nyissa meg a Virtuális gép lapot, amelyen alkalmazni szeretné az átalakítást.
1. A bal **oldalon válassza** a Konfiguráció lehetőséget. A Licencelés szakaszt fogja látni. A AHB átalakítás engedélyezéséhez jelölje be az "Igen" választógombot, és jelölje be a Megerősítés jelölőnégyzetet.
![AHB-konfiguráció panel a létrehozás után](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> Ha egyéni pillanatképet vagy megosztott rendszerképet **(SIG)** hozott létre egy RHEL- vagy SLES PAYG Marketplace-rendszerképről, csak az Azure CLI-t használhatja a Azure Hybrid Benefit.  Ez ismert korlátozás, és jelenleg nincs időkorlát, hogy ezt a képességet az Azure Portalon is biztosítani tudjuk.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Az előnyök engedélyezése és letiltása az Azure CLI-ban

A paranccsal `az vm update` frissítheti a meglévő virtuális gépeket. RHEL virtuális gépeken futtassa a parancsot a `--license-type` `RHEL_BYOS` paraméterrel. SLES virtuális gépeken futtassa a parancsot a `--license-type` `SLES_BYOS` paraméterrel.

### <a name="cli-example-to-enable-the-benefit"></a>Cli-példa az előny engedélyezéséhez
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Cli-példa az előny letiltásához
Az előny letiltásához használja a `--license-type` `None` értéket:

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-példa, amely nagy számú virtuális gép számára teszi lehetővé az előnyöket
Ha nagy számú virtuális gép számára szeretné engedélyezni az előnyöket, használja a paramétert az `--ids` Azure CLI-ban:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Az alábbi példák két módszert mutatnak be az erőforrás-adatokat listának lekért listára: egyet az erőforráscsoport szintjén, egyet pedig az előfizetés szintjén.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>A virtuális Azure Hybrid Benefit a virtuális gép létrehozásakor
A virtuális gép Azure Hybrid Benefit virtuális gépekre való alkalmazása mellett meghívhatja is azt a virtuális gép létrehozásakor. Ennek három előnye van:
- A PAYG és a BYOS virtuális gépeket is kiépítheti ugyanazokkal a rendszerképekkel és folyamatokkal.
- Lehetővé teszi a licencelési mód későbbi módosításait, ami nem érhető el csak BYOS-rendszerképekkel, vagy ha saját virtuális gépet hoz létre.
- A virtuális gép alapértelmezés szerint csatlakozik a Red Hat frissítési infrastruktúrához (ANNAK ÉRDEKÉBEN, hogy naprakész és biztonságos maradjon). Az üzembe helyezés után bármikor módosíthatja a frissített mechanizmust.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Virtuális Azure Hybrid Benefit állapotának ellenőrzése
A virtuális gépek Azure Hybrid Benefit állapotának megtekintéséhez használhatja az Azure CLI-t vagy az Azure Instance Metadata Service.

### <a name="azure-cli"></a>Azure CLI

Erre a célra `az vm get-instance-view` használhatja a parancsot. Keresse meg a `licenseType` válasz egyik mezőjét. Ha a mező létezik, és az értéke vagy , akkor a `licenseType` virtuális gépen engedélyezve van az `RHEL_BYOS` `SLES_BYOS` előny.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata szolgáltatás

Magában a virtuális gépen belül lekérdezheti az Azure Instance Metadata Service a virtuális gép értékének `licenseType` meghatározásához. A `licenseType` vagy érték azt `RHEL_BYOS` `SLES_BYOS` jelzi, hogy a virtuális gépen engedélyezve van az előny. [További információ az igazolt metaadatokról.](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Megfelelőség

### <a name="red-hat"></a>Red Hat

Az RHEL Azure Hybrid Benefit ügyfelek beleegyeznek a [](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) Azure Marketplace RHEL-ajánlatokhoz társított általános jogi feltételekbe és adatvédelmi nyilatkozatba. [](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf)

Az RHEL Azure Hybrid Benefit ügyfelek három szoftverfrissítést és javítást kínálnak a virtuális gépekhez:

- [Red Hat frissítési infrastruktúra](../workloads/redhat/redhat-rhui.md) (alapértelmezett beállítás)
- Red Hat Satellite Server
- Red Hat Subscription Manager

Azok az ügyfelek, akik a SAJÁTUI lehetőséget választják, továbbra is a SAJÁT RHEL virtuális gépük fő frissítési forrásaként használhatják a AZURE HYBRID BENEFIT RHEL-előfizetéseket anélkül, hogy RHEL-előfizetéseket csatolnak ezekhez a virtuális gépekhez. Az RHEL-előfizetés megfelelőségét azok az ügyfelek biztosítják, akik a SAJÁTUI lehetőséget választják.

A Red Hat műholdas kiszolgálót vagy a Red Hat Subscription Managert választó ügyfeleknek el kell távolítaniuk a SATELLITEUI konfigurációt, majd egy Cloud Access-kompatibilis RHEL-előfizetést kell csatolniuk a Azure Hybrid Benefit RHEL virtuális gépeikhez.  

A Red Hat-előfizetés megfelelőségével, a szoftverfrissítésekkel és a Azure Hybrid Benefit RHEL virtuális gépek forrásaival kapcsolatos további információkért lásd a [Red Hat-előfizetések](https://access.redhat.com/articles/5419341)és a Azure Hybrid Benefit.

### <a name="suse"></a>SUSE

A Azure Hybrid Benefit használata SLES virtuális gépekhez, valamint az SLES PAYG-ről BYOS-re vagy SLES BYOS-ről PAYG-re való áthelyezésről a [SUSE Linux Enterprise](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/)és a Azure Hybrid Benefit. 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>Azure Hybrid Benefit példányok előzetes verziója érhető el

Az Azure Reservations (Azure Reserved Virtual Machine Instances) több termékre vonatkozó egy- vagy három éves csomagokra való kötelezettséget vállalva segít pénzt takaríthat meg. A fenntartott példányokkal kapcsolatban [itt olvashat bővebben.](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations) A Azure Hybrid Benefit előzetes verzióban érhető el a [Fenntartott virtuálisgép-példányok (RI-k) számára.](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation) Ez azt jelenti, hogy ha kedvezményes áron vásárolt számítási költségeket a fenntartott példány használatával, a AHB kedvezményt az RHEL és a SUSE licencelési költségeire is alkalmazhatja. A AHB kedvezmény RI-példányra való alkalmazásának lépései pontosan ugyanazok maradnak, mint egy normál virtuális gépnél.
![AHB a RI-khez](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Ha már vásárolt foglalásokat az RHEL vagy a SUSE PAYG Azure Marketplace-on, a foglalás használatának befejezése előtt várja meg a foglalási Azure Hybrid Benefit.


## <a name="frequently-asked-questions"></a>Gyakori kérdések
*K: Használhatok licenctípust `RHEL_BYOS` SLES-rendszerképekkel, vagy fordítva?*

A: Nem, nem. Ha olyan licenctípust próbál meg megadni, amely helytelenül egyezik meg a virtuális gépen futó disztribúcióval, nem frissíti a számlázási metaadatokat. Ha azonban véletlenül rossz licenctípust ad meg, a virtuális gép a megfelelő licenctípusra való újrafrissítése továbbra is lehetővé teszi a kedvezményt.

*K: Regisztráltam a Red Hat Cloud Access, de továbbra sem tudom engedélyezni az előnyt az RHEL virtuális gépeimen. Mit tegyek?*

A: Az előfizetés regisztrációja a Red Hatról az Azure Red Hat Cloud Access ra propagálása némi időt is igénybe vehet. Ha egy nap után is jelenik meg a hiba, forduljon a Microsoft ügyfélszolgálatához.

*K: Üzembe helyezett egy virtuális gépet az RHEL BYOS "golden image" (arany rendszerkép) használatával. Konvertálható a rendszerképek számlázása BYOS-ről PAYG-re?*

A: Nem, nem. Azure Hybrid Benefit a konverziót csak a fizetéses képeken támogatja.

*K: Feltöltöttem a saját RHEL-rendszerképemet a helyszínről (Azure Migrate, Azure Site Recovery vagy más módon) az Azure-ba. Konvertálható a rendszerképek számlázása BYOS-ről PAYG-re?*

A: Nem, nem. Az Azure Hybrid Benefit képesség jelenleg csak az RHEL- és SLES-képekhez érhető el a Azure Marketplace. 

*K: Feltöltöttem a saját RHEL-rendszerképemet a helyszínről (Azure Migrate, Azure Site Recovery vagy más módon) az Azure-ba. Kell tennem valamit, hogy kihasználjam a Azure Hybrid Benefit?*

A: Nem, nincs. A feltöltött RHEL-rendszerképek már BYOS-nak minősülnek, és csak az Azure-infrastruktúra költségeiért kell fizetnie. Az RHEL-előfizetések költségeiért Ön felel, csakúgy, mint a helyszíni környezetekért. 

*K: Használhatok Azure Hybrid Benefit RHEL- és SLES SAP-rendszerképekből üzembe helyezett virtuális gépeken Azure Marketplace virtuális gépeket?*

A: Igen, igen. A licenctípust RHEL virtuális gépekhez és az RHEL és SLES SAP-rendszerképekből üzembe helyezett virtuális gépek `RHEL_BYOS` `SLES_BYOS` átalakításához Azure Marketplace használhatja.

*K: Használhatok virtuálisgép Azure Hybrid Benefit méretezési készleteken RHEL-hez és SLES-hez való virtuálisgép-méretezési készleteket?*

V: Igen, Azure Hybrid Benefit RHEL-hez és SLES-hez elérhető virtuálisgép-méretezési készletek előzetes verziója. Erről az előnyökről és használatukról itt [olvashat bővebben.](/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux) 

*K: Használhatok fenntartott Azure Hybrid Benefit RHEL-hez és SLES-hez fenntartott példányokat?*

V: Igen, Azure Hybrid Benefit RHEL-hez és SLES-hez fenntartott példányon való használat előzetes verzióban érhető el. Erről az előnyökről és használatukról itt [olvashat bővebben.](#azure-hybrid-benefit-on-reserved-instances-is-in-preview)

*K: Használhatok Azure Hybrid Benefit RHEL-rendszerképeken való üzembe helyezésre SQL Server virtuális gépen?*

A: Nem, nem. Ezeknek a virtuális gépeknek a támogatása nem tervezve.

*K: Használhatom az Azure Hybrid Benefit RHEL Virtual Data Center-előfizetésemhez?*

A: Nem, nem. A VDC egyáltalán nem támogatott az Azure-ban, beleértve a AHB-t is.  
 

## <a name="common-problems"></a>Gyakori problémák
Ez a szakasz az esetleg felmerülő gyakori problémákat és a megoldás lépéseit sorolja fel.

| Hiba | Kockázatcsökkentés |
| ----- | ---------- |
| "A műveletet nem sikerült befejezni, mert a rekordok azt mutatják, hogy nem engedélyezte sikeresen Red Hat Cloud Access Azure-előfizetésében...." | A kedvezmény RHEL virtuális gépeken való alkalmazásához először regisztrálnia kell az Azure-előfizetéseket [a Red Hat Cloud Access.](https://access.redhat.com/management/cloud)

## <a name="next-steps"></a>Következő lépések
* [Megtudhatja, hogyan hozhat létre és frissítheti a virtuális gépeket, és hogyan adhat hozzá licenctípusokat (RHEL_BYOS, SLES_BYOS) Azure Hybrid Benefit azure cli használatával](/cli/azure/vm)
