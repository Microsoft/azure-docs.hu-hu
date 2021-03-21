---
title: További tudnivalók az Azure rendszerkép-Készítőről (előzetes verzió)
description: Tudjon meg többet az Azure-beli Virtual Machines Azure rendszerkép-Szerkesztőről.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 20bb6925f859d497046eb42bbafb5264826b77b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604066"
---
# <a name="preview-azure-image-builder-overview"></a>Előzetes verzió: az Azure rendszerkép-szerkesztő áttekintése

A szabványosított virtuálisgép-lemezképek lehetővé teszik a szervezetek számára a felhőbe való Migrálás és az üzemelő példányok egységességének biztosítását. A képek jellemzően előre definiált biztonsági és konfigurációs beállításokat és szükséges szoftvereket tartalmaznak. A saját képfeldolgozási folyamatának beállítása idő, infrastruktúra és beállítás szükséges, de az Azure virtuálisgép-rendszerkép-készítővel egyszerűen meg kell adnia egy konfigurációt, amely leírja a lemezképet, elküldi a szolgáltatást, és a lemezképet felépíti és terjeszti.
 
Az Azure VM Image Builder (Azure Image Builder) segítségével elkezdheti a Windows-vagy Linux-alapú Azure Marketplace-lemezképek, a meglévő Egyéni rendszerképek és a saját testreszabások hozzáadásának megkezdését. Mivel a rendszerkép-szerkesztő a [HashiCorp Packer](https://packer.io/) -re épül, bizonyos hasonlóságok fognak megjelenni, de a felügyelt szolgáltatás előnyeit élvezheti. Megadhatja azt is, hogy hol szeretné tárolni a rendszerképeket az [Azure megosztott rendszerkép](shared-image-galleries.md)-katalógusban felügyelt képként vagy virtuális merevlemezként.

> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Előzetes verziójú funkciók

Az előzetes verzióban ezek a funkciók támogatottak:

- Alapszintű lemezképek létrehozása, amely tartalmazza a minimális biztonsági és vállalati konfigurációkat, és lehetővé teszi a részlegek számára a további testreszabást.
- Az alapalkalmazások integrálása, így a virtuális gép a létrehozás után igénybe veheti a számítási feladatokat, vagy hozzáadhat konfigurációkat a Windows rendszerű virtuális asztali rendszerképek támogatásához.
- A meglévő lemezképek javításához a rendszerkép-készítő lehetővé teszi a meglévő Egyéni rendszerképek folyamatos javítását.
- A rendszerkép-készítő összekapcsolhatók a meglévő virtuális hálózatokkal, így csatlakozhat a meglévő konfigurációs kiszolgálókhoz (DSC, Chef, Puppet stb.), a fájlmegosztás vagy bármely más, az útválasztásra alkalmas kiszolgáló/szolgáltatáshoz.
- Az Azure Shared Image Gallery integrációja lehetővé teszi a lemezképek globális terjesztését, verzióját és méretezését, valamint egy rendszerkép-kezelő rendszer létrehozását.
- Integráció meglévő rendszerkép-létrehozási folyamatokkal, csak a rendszerkép-készítő hívása a folyamatból, vagy az egyszerű előzetes rendszerkép-készítő Azure DevOps feladat használata.
- Meglévő rendszerkép-testreszabási folyamat migrálása az Azure-ba. A lemezképek testreszabásához használja meglévő parancsfájlait, parancsait és folyamatait.
- Lemezképek létrehozása VHD formátumban a Azure Stack támogatásához.
 

## <a name="regions"></a>Régiók
Az Azure rendszerkép-szerkesztő szolgáltatás elérhető lesz az előzetes verzióban ezekben a régiókban. A képeket ezen régiókon kívül is el lehet osztani.
- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- Észak-Európa
- Nyugat-Európa

## <a name="os-support"></a>Operációs rendszer támogatása
A AIB az Azure Marketplace alap operációsrendszer-lemezképeit fogja támogatni:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multi-session/Professional
- Windows 2016
- Windows 2019

## <a name="how-it-works"></a>Működés

Az Azure VM rendszerkép-készítő egy teljes körűen felügyelt Azure-szolgáltatás, amelyet egy Azure-erőforrás-szolgáltató is elérhet. Adjon meg egy olyan konfigurációt a szolgáltatás számára, amely megadja a forrás rendszerképet, a végrehajtandó testreszabást, valamint az új rendszerkép terjesztésének helyét, az alábbi ábrán egy magas szintű munkafolyamat látható:

![Az Azure rendszerkép-készítő folyamatának elméleti rajza, amely a forrásokat (Windows/Linux), a testreszabásokat (rendszerhéj, PowerShell, a Windows újraindítását & frissítését, a fájlok hozzáadását) és a globális terjesztést mutatja be az Azure megosztott rendszerkép-katalógusával](./media/image-builder-overview/image-builder-flow.png)

A sablon konfigurációit a PowerShell, az parancssori felület, az ARM-sablonok és az Azure VM rendszerkép-készítő DevOps feladatának használatával lehet átadni, amikor beküldi azt a szolgáltatásba, létre fog hozni egy Képsablon-erőforrást. A rendszerkép-sablon erőforrásának létrehozásakor az előfizetésében létrehozott átmeneti erőforráscsoport jelenik meg a következő formátumban: IT_ \<DestinationResourceGroup> _\<TemplateName>_ \( GUID). Az előkészítési erőforráscsoport a fájlban, a rendszerhéjban és a PowerShell-testreszabásban hivatkozott fájlokat és parancsfájlokat tartalmazza a ScriptURI tulajdonságban.

A Build a `Run` lemezkép-sablon erőforráson való futtatásához a szolgáltatás ezután további erőforrásokat helyez üzembe a Build számára, mint például a virtuális gép, a hálózat, a lemez, a hálózati adapter stb. Ha meglévő VNET-rendszerkép használata nélkül hoz létre lemezképet, akkor a szolgáltatás egy nyilvános IP-címet és egy NSG is üzembe helyez, a szolgáltatás SSH vagy WinRM használatával csatlakozik a Build virtuális géphez. Ha kiválaszt egy meglévő VNET, akkor a szolgáltatás az Azure Private link használatával lesz üzembe helyezve, és a nyilvános IP-cím megadása nem kötelező, a rendszerkép-készítő hálózatkezeléssel kapcsolatos további részletekért tekintse meg a [részleteket](./linux/image-builder-networking.md).

Ha a Build befejezi az összes erőforrást, az előkészítési erőforráscsoport és a Storage-fiók kivételével törölje a Képsablon-erőforrást, vagy hagyja ott a Build újbóli futtatását.

Ebben a dokumentációban több példát és lépésenkénti útmutatót is talál, amelyek az [Azure rendszerkép-szerkesztő GitHub-tárházában](https://github.com/azure/azvmimagebuilder)található konfigurációs sablonokra és megoldásokra hivatkoznak.

### <a name="move-support"></a>Támogatás áthelyezése
A rendszerkép-sablon erőforrása nem módosítható, és az erőforrásokra és az átmeneti erőforrás-csoportra mutató hivatkozásokat tartalmaz, ezért az erőforrástípus nem támogatja az áthelyezést. Ha át szeretné helyezni a Képsablon erőforrását, győződjön meg róla, hogy rendelkezik a konfigurációs sablon egy másolatával (ha nem rendelkezik a meglévő konfiguráció kibontásával), hozzon létre egy új rendszerkép-sablon-erőforrást az új erőforráscsoport új névvel, és törölje az előző rendszerkép-sablon erőforrást. 

## <a name="permissions"></a>Engedélyek
A (z) (AIB) regisztrálásakor a AIB szolgáltatás engedélyt ad az átmeneti erőforráscsoport létrehozásához, kezeléséhez és törléséhez (IT_ *), és jogosultsággal rendelkezik ahhoz, hogy erőforrásokat adjon hozzá, amelyek szükségesek a rendszerkép létrehozásához. Ezt egy AIB egyszerű szolgáltatásnév (SPN) hajtja végre, amely a sikeres regisztráció során elérhetővé válik az előfizetésben.

Annak engedélyezéséhez, hogy az Azure VM Image Builder lemezképeket terjesszen a felügyelt lemezképekre vagy egy megosztott képkatalógusba, létre kell hoznia egy Azure-beli felhasználó által hozzárendelt identitást, amely jogosult a képek olvasására és írására. Ha az Azure Storage-hoz fér hozzá, akkor a saját és a nyilvános tárolók olvasásához engedélyre van szüksége.

Az engedélyeket részletesebben ismerteti a [PowerShell](./linux/image-builder-permissions-powershell.md)és [az az parancssori](./linux/image-builder-permissions-cli.md)felület.

## <a name="costs"></a>Költségek
A lemezképek Azure Image Builder szolgáltatással való létrehozásakor, kiépítésekor és tárolásakor a számítási, hálózati és tárolási költségek is felmerülhetnek. Ezek a költségek hasonlóak az egyéni lemezképek manuális létrehozása során felmerülő költségekhez. Az erőforrásokra az Azure díjszabása alapján számítunk fel díjat. 

A rendszerkép-létrehozási folyamat során a rendszer letölti és tárolja a fájlokat az `IT_<DestinationResourceGroup>_<TemplateName>` erőforráscsoporthoz, ami kisebb tárolási költségekkel jár. Ha nem szeretné megtartani ezeket a fájlokat, törölje a **képsablont** a rendszerkép létrehozása után.
 
A rendszerkép-készítő létrehoz egy virtuális gépet egy D1v2 VM-mérettel, valamint a virtuális gép számára szükséges tárterületet és hálózatkezelést. Ezek az erőforrások a létrehozási folyamat időtartama alatt maradnak, és törölve lesznek, ha a rendszerkép-készítő befejezte a rendszerkép létrehozását. 
 
Az Azure-rendszerkép-szerkesztő továbbítja a rendszerképet a kiválasztott régióknak, ami hálózati kimenő forgalomra is felmerülhet.

## <a name="hyper-v-generation"></a>Hyper-V generáció
A rendszerkép-készítő jelenleg csak natív módon támogatja a Hyper-V generációs (Gen1) 1 lemezképek létrehozását az Azure Shared Image Gallery (SIG) vagy a Managed Image használatával. 
 
## <a name="next-steps"></a>Következő lépések 
 
Az Azure Image Builder kipróbálásához tekintse meg a [Linux](./linux/image-builder.md) -és [Windows](./windows/image-builder.md) -rendszerképek készítésével foglalkozó cikkeket.