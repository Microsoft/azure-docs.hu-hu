---
title: Azure Confidential Computing-megoldások virtuális gépeken
description: Ismerje meg a virtuális gépeken elérhető Azure Confidential Computing megoldásokat.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 580c53f311bc8ee70e974df2bc4111e6361d06f6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818961"
---
# <a name="solutions-on-azure-virtual-machines"></a>Megoldások Azure-beli virtuális gépeken

Ez a cikk az Intel [Software Guard-bővítmény](https://software.intel.com/sgx) (Intel SGX) által lefedett Intel-processzorokat futtató Azure Confidential Computing virtuális gépek (VM-ek) üzembe helyezéséről nyújt tájékoztatást. 

## <a name="azure-confidential-computing-vm-sizes"></a>Az Azure Confidential Computing virtuálisgép-méretei

Az Azure Confidential Computing virtuális gépei úgy vannak kialakítva, hogy megvédjék az adatok és a kód titkosságát és integritását a felhőben való feldolgozás során 

[DCsv2 sorozat](../virtual-machines/dcv2-series.md) A virtuális gépek a legújabb és legújabb bizalmas számítási méret család. Ezek a virtuális gépek az üzembe helyezési képességek nagyobb körét támogatják, az Enklávé Lapgyorsítótár (EPC) méretének (EPC) méretének 2-ére, illetve a méretük nagyobb a DC-Series képest. A [DC-sorozat virtuális](../virtual-machines/sizes-previous-gen.md#preview-dc-series) gépei jelenleg előzetes verzióban állnak rendelkezésre, és elavultak lesznek, és nem szerepelnek az általánosan elérhető verzióban.

A rövid útmutatót DCsv2-Series a Microsoft kereskedelmi piacterén keresztül kezdheti meg egy virtuális [gép üzembe helyezését.](quick-create-marketplace.md)

### <a name="current-available-sizes-and-regions"></a>Aktuálisan elérhető méretek és régiók

Az elérhető régiókban és rendelkezésre állási zónákban elérhető összes általánosan elérhető bizalmas számítási virtuálisgép-méret listáját az alábbi paranccsal érhetők el az [Azure CLI-en:](/cli/azure/install-azure-cli-windows)

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

A fenti méretek részletesebb megtekintéséhez futtassa a következő parancsot:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Dedikált gazdagépre vonatkozó követelmények
A virtuális **Standard_DC8_v2** méretének üzembe helyezése a DCSv2-Series VM-családban a teljes gazdagépet foglalja el, és nem lesz megosztva más bérlővel vagy előfizetéssel. Ez a virtuálisgép-termékváltozat-család biztosítja azt az elkülönítést, amely szükséges lehet a megfelelőségi és biztonsági szabályozási követelmények teljesítéséhez, amelyek általában egy dedikált gazdaszolgáltatással teljesülnek. Ha a **termékváltozat Standard_DC8_v2,** a fizikai gazdakiszolgáló az összes rendelkezésre álló hardvererőforrást lefoglalja, beleértve az EPC memóriát is csak a virtuális gép számára. Vegye figyelembe, hogy ez a funkció infrastruktúra-kialakítással létezik, és a Standard_DC8_v2 **összes** szolgáltatása támogatott lesz. Ez az üzembe helyezés nem ugyanaz, [Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) más Azure-beli virtuálisgép-család által biztosított szolgáltatás.


## <a name="deployment-considerations"></a>Telepítési szempontok

Egy rövid útmutató segítségével kevesebb, mint 10 DCsv2-Series helyezhet üzembe egy virtuális gépet. 

- **Azure-előfizetés** – Egy bizalmas számítási virtuálisgép-példány üzembe helyezéséhez fontolja meg egy használatalapú fizetéses előfizetés vagy más vásárlási lehetőség üzembe helyezését. Ha ingyenes [Azure-fiókot](https://azure.microsoft.com/free/)használ, nem lesz kvótája a megfelelő mennyiségű Azure számítási maghoz.

- **Díjszabás és regionális** rendelkezésre állás – A virtuális gépek díjszabását DCsv2-Series virtuális gépek díjszabását a Virtuális gépek [díjszabása oldalon találja.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Az [Azure-régiókban való](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) rendelkezésre állásért tekintse meg a régiónként elérhető termékeket.


- **Magkvóták** – Előfordulhat, hogy az alapértelmezett értékről növelnie kell az Azure-előfizetés magkvótáját. Az előfizetés korlátozhatja az egyes virtuálisgép-méretcsaládokban üzembe helyezhető magok számát is, beleértve a DCsv2-sorozatot is. A kvóta növelésének igénylése érdekében [díjmentesen nyisson meg](../azure-portal/supportability/per-vm-quota-requests.md) egy online ügyfélszolgálati kérést. Vegye figyelembe, hogy az alapértelmezett korlátok az előfizetés kategóriájától függően változhatnak.

  > [!NOTE]
  > Ha nagy kapacitásra van szüksége, lépjen kapcsolatba a Azure ügyfélszolgálata- és a partnerrel. Az Azure-kvóták nem kapacitásgaranciák, de kreditkorlátok. A kvótától függetlenül csak a használt magokért kell fizetnie.
  
- **Átméretezés** – A speciális hardverük miatt csak az ugyanabban a méret családban található bizalmas számítási példányok átméretezhetők. Például egy DCsv2 sorozatú virtuális gépet csak átméretezhet egy DCsv2-sorozatú méretről egy másikra. A nem bizalmas számítási méretről a bizalmas számítási méretre való átméretezés nem támogatott.  

- **Kép** – Ahhoz, hogy Intel Software Guard-bővítmény (Intel SGX) támogatást nyújtson a bizalmas számítási példányokhoz, minden üzembe helyezést 2. generációs rendszerképeken kell futtatni. Az Azure Confidential Computing az Ubuntu 18.04 Gen 2, Ubuntu 20.04 Gen 2, Windows Server 2019 Gen2 és Windows Server 2016 Gen 2 rendszeren futó számítási feladatokat támogatja. További információ [a 2.](../virtual-machines/generation-2.md) generációs Azure-beli virtuális gépek támogatásról, ha többet szeretne megtudni a támogatott és nem támogatott forgatókönyvekről. 

- **Tárolás** – Az Azure Confidential Computing virtuálisgép-adatlemezei és a felhőbeli operációsrendszer-lemezek NVMe-lemezeken vannak. A példányok csak prémium SSD lemezeket standard SSD, az Ultra SSD-t és a standard HDD. A **virtuálisgép DC8_v2** mérete nem támogatja a Premium Storage-et. 

- **Lemeztitkosítás** – A bizalmas számítási példányok jelenleg nem támogatják a lemeztitkosítást. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Magas rendelkezésre állás és vészhelyreállítási szempontok

Ha azure-beli virtuális gépeket használ, az Ön feladata egy magas rendelkezésre állású és vészhelyreállítási megoldás megvalósítása az állásidő elkerülése érdekében. 

Az Azure Confidential Computing jelenleg nem támogatja a zónaredundaniát Availability Zones-n keresztül. A legmagasabb rendelkezésre állás és redundancia érdekében a bizalmas számítástechnika esetében használja a [rendelkezésre állási készleteket.](../virtual-machines/availability-set-overview.md) A hardverkorlátozások miatt a bizalmas számítási példányok rendelkezésre állási készlete legfeljebb 10 frissítési tartományt kaphat. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Üzembe helyezés Azure Resource Manager (ARM) sablonnal

Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Felügyeleti réteget biztosít, amellyel erőforrásokat hozhat létre, frissíthet és törölhet az Azure-előfizetésében. Az erőforrások biztonságossá és rendszerezésére az üzembe helyezés után olyan felügyeleti funkciókat használhat, mint a hozzáférés-vezérlés, a zárolások és a címkék.

Az ARM-sablonokkal kapcsolatos további információkért lásd [a Template deployment áttekintését.](../azure-resource-manager/templates/overview.md)

Egy virtuális DCsv2-Series ARM-sablonban való üzembe helyezéséhez a virtuális gép [erőforrását fogja használni.](../virtual-machines/windows/template-description.md) Győződjön meg arról, hogy a megfelelő tulajdonságokat adja meg a **vmSize** és az **imageReference számára.**

### <a name="vm-size"></a>Virtuális gép mérete

Adja meg az alábbi méretek egyikét az ARM-sablonban a virtuális gép erőforrásában. Ez a sztring **vmSize-ként van megszabadva** a **tulajdonságokban.**

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 operációs rendszer képe

A **tulajdonságok** alatt egy képre is hivatkozni kell a **storageProfile alatt.** Az **imageReference** csak az alábbi képek egyikét használja. 

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Következő lépések 

Ebben a cikkben a bizalmas számítási virtuális gépek létrehozásához szükséges minősítéseket és konfigurációkat sajátta el. Most már a virtuális gép Microsoft Azure Marketplace helyezhet üzembe DCsv2-Series virtuális gépet.

> [!div class="nextstepaction"]
> [Virtuális gép DCsv2-Series üzembe helyezése a Azure Marketplace](quick-create-marketplace.md)