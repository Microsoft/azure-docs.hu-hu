---
title: További tudnivalók az Azure rendszerkép-Készítőről (előzetes verzió)
description: Tudjon meg többet az Azure-beli Virtual Machines Azure rendszerkép-Szerkesztőről.
author: danielsollondon
ms.author: danis
ms.date: 05/02/2019
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.reviewer: cynthn
ms.openlocfilehash: 1c70edfc3bad2be70d26c71736ca06fcc4a8dcdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672510"
---
# <a name="preview-azure-image-builder-overview"></a>Előzetes verzió: az Azure rendszerkép-szerkesztő áttekintése

A szabványosított virtuálisgép-lemezképek lehetővé teszik a szervezetek számára a felhőbe való Migrálás és az üzemelő példányok egységességének biztosítását. A képek jellemzően előre definiált biztonsági és konfigurációs beállításokat és szükséges szoftvereket tartalmaznak. A saját képfeldolgozási folyamatának beállítása idő, infrastruktúra és beállítás szükséges, de az Azure VM rendszerkép-készítővel egyszerűen egy egyszerű konfigurációt kell megadnia, amely leírja a lemezképet, beküldi a szolgáltatást, és a lemezképet összeállította és terjeszti.
 
Az Azure VM Image Builder (Azure Image Builder) segítségével elkezdheti a Windows vagy Linux-alapú Azure Marketplace-rendszerkép, a meglévő Egyéni rendszerképek vagy a Red Hat Enterprise Linux (RHEL) ISO-fájljának megadását, és megkezdheti saját testreszabásainak hozzáadását. Mivel a rendszerkép-szerkesztő a [HashiCorp Packer](https://packer.io/)-re épül, importálhatja a meglévő csomagoló rendszerhéj-kiépítési parancsfájlokat is. Megadhatja azt is, hogy hol szeretné tárolni a rendszerképeket az [Azure megosztott rendszerkép](shared-image-galleries.md)-katalógusban felügyelt képként vagy virtuális merevlemezként.

> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Előzetes verziójú funkciók

Az előzetes verzióban ezek a funkciók támogatottak:

- Az alapszintű rendszerképek létrehozása, amely magában foglalja a minimális biztonsági és vállalati konfigurációkat, és lehetővé teszi a részlegek számára, hogy igényeiknek megfelelően testre szabják azokat.
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

A RHEL ISOs-támogatás már nem támogatott.

## <a name="how-it-works"></a>Működés

Az Azure rendszerkép-szerkesztő egy teljes körűen felügyelt Azure-szolgáltatás, amelyet egy Azure-erőforrás-szolgáltató is elérhet. Az Azure rendszerkép-készítő folyamat három fő részből áll: forrás, testreszabás és terjesztés, ezek egy sablonban jelennek meg. Az alábbi ábrán az összetevők láthatók a tulajdonságaik némelyikével. 
 

**Rendszerkép-készítő folyamat** 

![Az Azure rendszerkép-készítő folyamatának elméleti rajza](./media/image-builder-overview/image-builder-process.png)

1. Hozza létre a rendszerkép sablonját. JSON-fájlként. Ez a. JSON fájl a rendszerkép forrásával, testreszabásával és terjesztésével kapcsolatos információkat tartalmaz. Az [Azure rendszerkép-készítő GitHub-tárházban](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)több példa is van.
1. Küldje el a szolgáltatást, amely létrehoz egy Képsablon-összetevőt az Ön által megadott erőforráscsoporthoz. A háttérben a rendszerkép-szerkesztő szükség szerint letölti a forrás-vagy ISO-és parancsfájl-fájlokat. Ezeket egy külön erőforráscsoport tárolja, amely automatikusan létrejön az előfizetésben, a következő formátumban: IT_ \<DestinationResourceGroup> _ \<TemplateName> . 
1. A rendszerkép-sablon létrehozása után létrehozhatja a rendszerképet. A háttérkép-szerkesztőben a sablon és a forrásfájlok használatával hozzon létre egy virtuális gépet (alapértelmezett méret: Standard_D1_v2), a hálózat, a nyilvános IP-cím, a NSG és a tárterületet a IT_ \<DestinationResourceGroup> _ \<TemplateName> erőforráscsoporthoz.
1. A rendszerkép létrehozásának részeként a rendszerkép-szerkesztő a sablon alapján osztja el a képet, majd törli a \<DestinationResourceGroup> folyamathoz létrehozott IT_ _ erőforráscsoport további erőforrásait \<TemplateName> .


## <a name="permissions"></a>Engedélyek
A (z) (AIB) regisztrálásakor a AIB szolgáltatás engedélyt ad az átmeneti erőforráscsoport létrehozásához, kezeléséhez és törléséhez (IT_ *), és jogosultsággal rendelkezik ahhoz, hogy erőforrásokat adjon hozzá, amelyek szükségesek a rendszerkép létrehozásához. Ezt egy AIB egyszerű szolgáltatásnév (SPN) hajtja végre, amely a sikeres regisztráció során elérhetővé válik az előfizetésben.

Annak engedélyezéséhez, hogy az Azure VM Image Builder lemezképeket terjesszen a felügyelt lemezképekre vagy egy megosztott képkatalógusba, létre kell hoznia egy Azure-beli felhasználó által hozzárendelt identitást, amely jogosult a képek olvasására és írására. Ha az Azure Storage-hoz fér hozzá, akkor a privát tárolók olvasásához engedélyre van szüksége.

Először meg kell felelnie az [Azure-felhasználóhoz rendelt felügyelt identitás létrehozásához](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) szükséges dokumentációnak az identitás létrehozásával kapcsolatban.

Ha rendelkezik az Ön számára szükséges engedélyekkel, ezt megteheti, használhat egy egyéni Azure-szerepkör-definíciót, majd hozzárendelheti a felhasználó által hozzárendelt felügyelt identitást az egyéni szerepkör-definíció használatához.

Az engedélyeket [itt](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)részletesebben ismertetjük, a példák pedig azt mutatják be, hogy ez hogyan valósul meg.

> [!Note]
> A AIB korábban a AIB SPN-t használja, és megadja az SPN-engedélyeket a rendszerkép-erőforráscsoportok számára. A jövőbeli képességek lehetővé teszik a modellből való átlépést. 2020. május 26-án a képszerkesztő nem fogad el olyan sablonokat, amelyek nem rendelkeznek felhasználó által hozzárendelt identitással, a meglévő sablonokat újra el kell küldeni a szolgáltatásnak [felhasználói identitással](./linux/image-builder-json.md). A példák már azt mutatják be, hogyan hozhat létre felhasználó által hozzárendelt identitást, és hogyan adhatja hozzá őket egy sablonhoz. További információkért tekintse át ezt a [dokumentációt](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) a változásról és a kiadásokról.

## <a name="costs"></a>Költségek
A lemezképek Azure Image Builder szolgáltatással való létrehozásakor, kiépítésekor és tárolásakor a számítási, hálózati és tárolási költségek is felmerülhetnek. Ezek a költségek hasonlóak az egyéni lemezképek manuális létrehozása során felmerülő költségekhez. Az erőforrásokra az Azure díjszabása alapján számítunk fel díjat. 

A rendszerkép-létrehozási folyamat során a rendszer letölti és tárolja a fájlokat az `IT_<DestinationResourceGroup>_<TemplateName>` erőforráscsoporthoz, ami kisebb tárolási költségekkel jár. Ha nem szeretné megtartani ezeket a fájlokat, törölje a **képsablont** a rendszerkép létrehozása után.
 
A rendszerkép-készítő létrehoz egy virtuális gépet egy D1v2 VM-mérettel, valamint a virtuális gép számára szükséges tárterületet és hálózatkezelést. Ezek az erőforrások a létrehozási folyamat időtartama alatt maradnak, és törölve lesznek, ha a rendszerkép-készítő befejezte a rendszerkép létrehozását. 
 
Az Azure-rendszerkép-szerkesztő továbbítja a rendszerképet a kiválasztott régióknak, ami hálózati kimenő forgalomra is felmerülhet.

## <a name="hyper-v-generation"></a>Hyper-V generáció
A rendszerkép-készítő jelenleg csak natív módon támogatja a Hyper-V generációs (Gen1) 1 lemezképek létrehozását az Azure Shared Image Gallery (SIG) vagy a Managed Image használatával. Ha Gen2-lemezképeket szeretne létrehozni, akkor forrás-Gen2 lemezképet kell használnia, és a virtuális merevlemezre kell terjesztenie. Ezután létre kell hoznia egy felügyelt rendszerképet a VHD-ből, és be kell szúrnia a SIG-ba Gen2-képként.
 
## <a name="next-steps"></a>Következő lépések 
 
Az Azure Image Builder kipróbálásához tekintse meg a [Linux](./linux/image-builder.md) -és [Windows](./windows/image-builder.md) -rendszerképek készítésével foglalkozó cikkeket.

