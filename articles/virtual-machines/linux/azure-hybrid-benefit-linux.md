---
title: Azure Hybrid Benefit és Linux rendszerű virtuális gépek
description: A Azure Hybrid Benefit lehetővé teszi, hogy pénzt takarítson meg az Azure-on futó linuxos virtuális gépeken.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: alsin
ms.openlocfilehash: c1200121d1c768a3fdddd7749184d7f8b5c98a96
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413105"
---
# <a name="preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Előzetes verzió: Azure Hybrid Benefit – hogyan vonatkozik Linux Virtual Machines

## <a name="overview"></a>Áttekintés

A Azure Hybrid Benefit lehetővé teszi a helyszíni Red Hat Enterprise Linux (RHEL) és a SUSE Linux Enterprise Server (SLES) virtuális gépek (VM-EK) Azure-ba történő áttelepítését a saját, korábban már meglévő Red hat vagy SUSE szoftveres előfizetéssel. Ezzel a kedvezménysel csak a virtuális gép infrastrukturális költségeiért kell fizetnie, mert a RHEL vagy a SLES előfizetése fedezi a szoftver díját. Az előnyök az összes RHEL és SLES piactéren elvégzett (TB) lemezképre érvényesek.

> [!IMPORTANT]
> A Linux rendszerű virtuális gépek Azure Hybrid Benefit jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefit-description"></a>Juttatás leírása

A Azure Hybrid Benefit segítségével könnyebben áttelepítheti a helyszíni RHEL és a SLES-kiszolgálókat az Azure-ba, ha átalakítja a meglévő RHEL-és SLES-TB-alapú virtuális gépeket az Azure-ban, hogy saját előfizetést (BYOS) számlázjon. Az Azure-beli TB-rendszerképekből üzembe helyezett virtuális gépek általában az infrastruktúra díját és a szoftver díját is felszámítják. A Azure Hybrid Benefit a TB virtuális gépek újratelepítése nélkül alakíthatók át BYOS számlázási modellre, így elkerülhető az állásidő kockázata.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit a Cost vizualizációt Linux rendszerű virtuális gépeken.":::

Ha engedélyezi a kedvezményt egy RHEL vagy SLES virtuális gépen, akkor a továbbiakban nem számítunk fel díjat a TB virtuális gépen általában felmerülő további szoftverekért. Ehelyett a virtuális gép megkezdi a BYOS-díj kivezetését, amely csak a számítási hardver díját és a szoftver díját tartalmazza.

Ha szeretne, olyan virtuális gépet is átalakíthat, amelynek az előnye engedélyezve lett a TB számlázási modellben.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>A Linux rendszerű virtuális gépekre vonatkozó Azure Hybrid Benefit jogosultságok köre

Azure Hybrid Benefit az összes RHEL és SLES Marketplace TB-lemezképhez elérhető. Az előny még nem érhető el a RHEL vagy a SLES Marketplace BYOS-lemezképekhez vagy egyéni lemezképekhez.

A fenntartott példányok, a dedikált gazdagépek és az SQL Hybrid előnyök nem jogosultak a Azure Hybrid Benefit használatára, ha már használja a Linux rendszerű virtuális gépek előnyeit.

## <a name="how-to-get-started"></a>Első lépések

Azure Hybrid Benefit jelenleg a Linux rendszerű virtuális gépek előzetes verziójának fázisában van. Miután hozzáfér az előzetes verzióhoz, a Azure Portal vagy az Azure CLI használatával engedélyezheti a kedvezményt.

### <a name="preview"></a>Előnézet

Ebben a fázisban az [alábbi](https://aka.ms/ahb-linux-form)űrlap kitöltésével veheti igénybe a kedvezményt. Miután kitöltötte az űrlapot, az Azure-előfizetés (ok) engedélyezve lesz a kedvezményhez, és három munkanapon belül megerősítő lesz a Microsofttól.

### <a name="red-hat-customers"></a>Red Hat-ügyfelek

1.    Töltse ki a fenti előnézeti kérelem űrlapját
1.    Regisztráció a [Red Hat Cloud Access programba](https://aka.ms/rhel-cloud-access)
1.    Engedélyezze az Azure-előfizetését a Felhőbeli hozzáféréshez, és engedélyezze azokat a virtuális gépeket tartalmazó előfizetéseket, amelyekhez a kedvezményt használni szeretné
1.    A meglévő virtuális gépek előnyének alkalmazása a Azure Portal vagy az Azure CLI használatával
1.    Nem kötelező, regisztrálja az előnyben részesített virtuális gépeket egy külön frissítési forrással (a kapcsolt virtuális gépek [RHUI](../workloads/redhat/redhat-rhui.md) vagy RHSM keresztül regisztrálhatók)

### <a name="suse-customers"></a>SUSE-ügyfelek

1.    Töltse ki a fenti előnézeti kérelem űrlapját
1.    Regisztrálás a SUSE nyilvános felhő programban
1.    A meglévő virtuális gépek előnyének alkalmazása a Azure Portal vagy az Azure CLI használatával
1.    Az előnyt élvező virtuális gépek regisztrálása külön frissítési forrással

### <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>A Azure Portal kedvezményének engedélyezése és letiltása

A meglévő virtuális gépek előnyének engedélyezéséhez látogasson el a **konfiguráció** panelre, és kövesse a következő lépéseket. Az új virtuális gépek előnyeit a virtuálisgép-létrehozási élményben engedélyezheti.

### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Az előny engedélyezése és letiltása az Azure CLI-ben

A meglévő virtuális gépek frissítéséhez használhatja az "az VM Update" parancsot. RHEL virtuális gépek esetén futtassa a parancsot a (z) "RHEL_BYOS" licenc-Type paraméterrel. SLES virtuális gépek esetén futtassa a parancsot a (z) "SLES_BYOS" licenc-Type paraméterrel.

#### <a name="cli-example-to-enable-the-benefit"></a>CLI-példa az előny engedélyezéséhez:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>PARANCSSORI felület – példa az előny letiltására:
Az előny letiltásához használja a "None" licenc típusú értéket
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-példa a nagy mennyiségű virtuális gépen elérhető előnyök engedélyezésére
Ha nagyszámú virtuális gépen szeretné engedélyezni az előnyt, használhatja a `--ids` paramétert az Azure CLI-ben.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Az alábbi példák két módszert mutatnak be az erőforrás-azonosítók listájának lekéréséhez – egyet az erőforráscsoport szintjén, egyet az előfizetés szintjén.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Virtuális gép AHB-állapotának megkeresése
A virtuális gépek AHB állapotát háromféleképpen tekintheti meg: ellenőrzés a portálon, az Azure CLI használatával vagy az Azure Instance Metadata Service (Azure IMDS) használatával.


### <a name="portal"></a>Portál

Tekintse meg a konfiguráció panelt, és ellenőrizze a licencelés állapotát, hogy a AHB engedélyezve van-e a virtuális gépen.

### <a name="azure-cli"></a>Azure CLI

`az vm get-instance-view`Ezt a parancsot használhatja erre a célra. Keresse meg a válaszban a licenseType mezőt. Ha a licenseType mező létezik, és az értéke "RHEL_BYOS" vagy "SLES_BYOS", akkor a virtuális gépnek engedélyezve van az előnye.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata szolgáltatás

A virtuális GÉPEN belül lekérdezheti a IMDS által tanúsított metaadatokat a virtuális gép licenseType meghatározásához. A "RHEL_BYOS" vagy a "SLES_BYOS" licenseType érték azt jelzi, hogy a virtuális gépnek engedélyezve van az előnye. További információ az [igazolt metaadatokról](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Megfelelőség

### <a name="red-hat"></a>Red Hat

A RHEL virtuális gépekhez való Azure Hybrid Benefit használatához először regisztrálnia kell a Red Hat Cloud Access programba. Ezt a Red Hat Cloud Access webhely használatával teheti meg. Miután engedélyezte a kedvezményt a virtuális gépen, regisztrálnia kell a virtuális gépet saját frissítési forrásával, vagy a Red Hat előfizetés-kezelővel vagy a Red Hat Satellitével. A frissítések regisztrálása biztosítja, hogy továbbra is támogatott állapotban maradjon.

### <a name="suse"></a>SUSE

A SLES virtuális gépekhez való Azure Hybrid Benefit használatához először regisztrálnia kell a SUSE nyilvános felhő programban. További információk a programról. Miután megvásárolta a SUSE-előfizetéseket, regisztrálnia kell a virtuális gépeket az előfizetések saját frissítési forrásaira a SUSE Customer Center, az előfizetés-kezelő eszköz kiszolgálója vagy a SUSE Manager használatával.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
*K: használhatok SLES-lemezképpel rendelkező "RHEL_BYOS" licencet, vagy fordítva?*

A: nem. A virtuális gépen futó disztribúcióval nem egyező típusú licenc beírására tett kísérlet nem frissíti a számlázási metaadatokat. Ha azonban véletlenül helytelenül írja be a licenc típusát, a virtuális gép újbóli frissítése a megfelelő licenc-típusra továbbra is engedélyezi a kedvezményt.

*K: a Red Hat Cloud Access szolgáltatásban regisztráltam, de továbbra sem tudom engedélyezni a RHEL virtuális gépeken nyújtott előnyöket. Mi a teendő?*

Válasz: eltarthat egy ideig, amíg a Red Hat Cloud Access-előfizetés regisztrálása a Red Hat-ról az Azure-ra terjed. Ha továbbra is látni szeretné a hibát egy munkanap után, forduljon a Microsoft támogatási szolgálatához.

## <a name="common-issues"></a>Gyakori problémák
Ez a szakasz az észlelt gyakori problémák listáját és a mérséklési lépéseket tartalmazza.

| Hiba | Kockázatcsökkentés |
| ----- | ---------- |
| "Az előfizetés nincs regisztrálva a Azure Hybrid Benefit Linux-előnézetéhez. Részletes útmutatásért tekintse meg a https://aka.ms/ahb-linux következőt: " | Töltse ki az űrlapot https://aka.ms/ahb-linux-form , és regisztráljon a Azure Hybrid Benefit Linux előzetes verziójára.
| "A műveletet nem lehetett befejezni, mert a rekordok azt mutatják, hogy az Azure-előfizetésében nem sikerült engedélyezni a Red Hat Cloud Access használatát..." | Ahhoz, hogy a juttatást a RHEL virtuális gépekkel használhassa, először regisztrálnia kell az Azure-előfizetését a Red Hat Cloud Access használatával. Tekintse meg ezt a hivatkozást, ha többet szeretne megtudni arról, hogyan regisztrálhat Azure-előfizetéseket a Red Hat Cloud Accesshez
|"A Azure Hybrid Benefit lehetősége nem jelenik meg a portálon" | Ez egy ismert probléma a megosztott képkatalógusból, pillanatképekről vagy rögzített TB-lemezképekről létrehozott RHEL és SLES virtuális gépek esetében. Ebben az esetben használja az "[Engedélyezés és letiltás az Azure CLI-ben](#enable-and-disable-the-benefit-in-the-azure-cli)" című szakaszban ISMERTETett CLI-lépéseket. A AHB állapotának megtekintéséhez használja a parancsot ` az vm get-instance-view -g MyResourceGroup -n MyVm` .|

## <a name="next-steps"></a>További lépések
* Első lépésként tekintse meg az előzetes verziót [az űrlap](https://aka.ms/ahb-linux-form)kitöltésével.