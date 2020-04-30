---
title: Windows rendszerű virtuális gépek áttekintése az Azure-ban
description: Az Azure-beli Windows rendszerű virtuális gépek áttekintése.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e304841d09913aac59f5e6ba082d3e76ec791e81
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81869343"
---
# <a name="windows-virtual-machines-in-azure"></a>Windows rendszerű virtuális gépek az Azure-ban

Az Azure Virtual Machines (VM) az Azure által kínált különböző típusú [, igény szerinti, méretezhető számítási erőforrások](/azure/architecture/guide/technology-choices/compute-decision-tree) egyike. Virtuális gépet általában akkor érdemes választani, ha a számítási környezet átfogóbb vezérlésére van szüksége annál, amelyet az egyéb lehetőségek kínálnak. Ez a cikk bemutatja, hogy mit kell szem előtt tartania egy virtuális gép létrehozása előtt, valamint hogy hogyan hozhatja létre és kezelheti azt.

Az Azure VM a virtualizálás rugalmasságát biztosítja anélkül, hogy a virtuális gépet futtató fizikai hardvereket kellene vásárolnia és karbantartania. A virtuális gép karbantartásához azonban így is szükséges elvégeznie néhány feladatot, például a virtuális gépen futó szoftver konfigurálását, javítását és telepítését.

Az Azure virtuális gépek különféle módon használhatóak. Néhány példa:

* **Fejlesztés és tesztelés** – Az Azure virtuális gépek gyors és egyszerű módot biztosítanak az alkalmazások programozásához és teszteléséhez szükséges adott konfigurációjú számítógépek létrehozásához.
* **Felhőbeli alkalmazások** – Mivel az alkalmazások iránti igény ingadozhaz, gazgaságosabb lehet őket egy virtuális gépen futtatni az Azure szolgáltatásban. A további virtuális gépekért csak akkor kell fizetnie, amikor szüksége van rájuk, amikor pedig nincs, akkor leállíthatja őket.
* **Bővített adatközpont** – Az Azure virtuális hálózatokon futó virtuális gépek könnyedén összekapcsolhatók a szervezet hálózatához.

Az alkalmazás által használt virtuális gépek száma az Ön igényei szerint skálázható.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Mit kell átgondolnom egy virtuális gép létrehozása előtt?
Az Azure-ban futó alkalmazás-infrastruktúrák kiépítésekor mindig számos [kialakítási szempontot](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) kell figyelembe venni. A kezdés előtt a virtuális gépek következő tulajdonságait fontos átgondolni:

* Az alkalmazás erőforrásainak nevei
* Az erőforrások tárolásának helye
* A virtuális gép mérete
* A létrehozható virtuális gépek maximális száma
* A virtuális gépen futó operációs rendszer
* A virtuális gép indítás utáni konfigurációja
* A virtuális gép által igényelt kapcsolódó erőforrások

### <a name="locations"></a>Helyek
Az Azure-ban létrehozott minden erőforrás világszerte több [földrajzi régió](https://azure.microsoft.com/regions/) között oszlik meg. A virtuális gépek létrehozásakor a régiót általában **helynek** nevezik. A virtuális gépek esetében a hely adja meg, hogy a virtuális merevlemezek hol tárolódnak.

Az alábbi táblázatban az elérhető helyek listájának megismeréséhez olvasható néhány módszer.

| Módszer | Leírás |
| --- | --- |
| Azure Portal |Egy virtuális gép létrehozásakor válasszon egy helyet a listából. |
| Azure PowerShell |Használja a [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) parancsot. |
| REST API |Használja a [Helyek listázása](https://docs.microsoft.com/rest/api/resources/subscriptions) műveletet. |
| Azure CLI |Használja az [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest) műveletet. |

## <a name="availability"></a>Rendelkezésre állás
Az Azure bejelentett egy iparágvezető, 99,9%-os elérhetőséget biztosító egypéldányos virtuálisgép-szolgáltatói szerződést, amelynek az a feltétele, hogy az üzembe helyezett virtuális gép összes lemezén prémium szintű tárolást használjon.  Ahhoz, hogy az üzembe helyezett példány megfeleljen a standard 99,95%-os virtuálisgép-szolgáltatói szerződésnek, legalább még két virtuális gépet kell üzembe helyeznie a számítási feladatok futtatásához egy rendelkezésre állási csoporton belül. A rendelkezésre állási csoport biztosítja, hogy a virtuális gépek több tartalék tartomány között legyenek elosztva az Azure-adatközpontokban, valamint az őket futtató gazdagépeknek különböző karbantartási időszakaik legyenek. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.


## <a name="vm-size"></a>Virtuális gép mérete
A használt virtuális gép [mérete](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a futtatni kívánt számítási feladatok mennyiségétől függ. A választott méret határoz meg olyan tényezőket, mint a feldolgozókapacitás, a memória és a tárolókapacitás. Az Azure számos különböző méretet kínál különféle felhasználási módokhoz.

Az Azure [óradíjat](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) számít fel a virtuális gép méretétől és az operációs rendszertől függően. Nem egész órák esetében az Azure csak a használt perceket számlázza. A tárhely árazása és felszámítása külön történik.

## <a name="vm-limits"></a>A virtuális gépekre korlátai
Minden előfizetésre alapértelmezett [kvótakorlátozások](../../azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak, amelyek akadályt jelenthetnek, ha a projektjéhez nagy számú virtuális gépet szeretne üzembe helyezni. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép. A határértékek megemelhetők [egy emelést kérvényező támogatási jegy benyújtásával](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>Operációsrendszer-lemezek és -rendszerképek
A virtuális gépek [virtuális merevlemezeket (VHD-ket)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használnak az operációs rendszer (os) és az adattárolók tárolásához. A VHD-ken találhatók az operációs rendszer telepítéséhez kiválasztható rendszerképek is. 

Az Azure számos, a [piactéren elérhető rendszerképet](https://azure.microsoft.com/marketplace/virtual-machines/) kínál használatra, amelyek a Windows Server operációs rendszer különböző verzióit és típusait tartalmazzák. A piactérről származó rendszerképek azonosítása a rendszerkép közzétevője, ajánlat, termékváltozat és verzió alapján lehetséges (a verzió általában mint „legfrissebb” van megadva). Kizárólag a 64 bites operációs rendszerek támogatottak. A támogatott vendég operációs rendszerekkel, szerepkörökkel és funkciókkal kapcsolatos további információkért tekintse meg [a Microsoft Azure-beli virtuális gépek Microsoft kiszolgálószoftveres támogatását](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) ismertető cikket.

Az alábbi tábla bemutat néhány módszert, amelyekkel egy rendszerképről több információ is szerezhető.

| Módszer | Leírás |
| --- | --- |
| Azure Portal |Az értékek a rendszerképek kiválasztásakor automatikusan megjelennek. |
| Azure PowerShell |[Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) – hely *helye*<BR>[Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) – hely *helye* – közzétevő *közzétevő neve*<BR>[Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) – hely *helye* – közzétevő *közzétevő neve* – ajánlat *offerName* |
| REST API-k |[Rendszerkép-közzétevők listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Rendszerkép-ajánlatok listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Rendszerkép-termékváltozatok listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az VM rendszerkép listázása-Publishers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --Location *Location*<BR>[az VM rendszerkép listázása-ajánlatok](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --Location *Location* --Publisher *közzétevő neve*<BR>[az VM rendszerkép List-SKU](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest) --Location *Location* --Publisher *közzétevő neve* --ajánlat *offerName*|

Lehetősége van egy [saját rendszerképek feltöltésére és használatára](upload-generalized-managed.md) is, ebben az esetben a közzétevő neve, az ajánlat és a termékváltozat adatok nem használatosak.

### <a name="extensions"></a>Bővítmények
A virtuális gépek [bővítményei](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) további hozzáadott képességekkel ruházzák fel a virtuális gépeket az üzembe helyezést követő konfigurálás és automatizált feladatok útján.

A bővítményekkel a következő gyakori feladatok végezhetők el:

* **Egyéni szkriptek futtatása** – A [Custom Script bővítmény](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) segít a virtuális gép számítási feladatainak konfigurálásában azáltal, hogy a virtuális gép üzembe helyezésekor lefuttatja az Ön szkriptjét.
* **Konfigurációk üzembe helyezése és kezelése** – A [PowerShell Célállapot konfigurálása (DSC) bővítmény](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) segítségével beállíthatja egy virtuális gépen a DSC-t a konfigurációk és környezetek kezeléséhez.
* **Diagnosztikai adatok gyűjtése** – Az [Azure Diagnostics bővítményével](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a virtuális gép diagnosztikai adatok gyűjtésére konfigurálható, amelyek felhasználhatók az alkalmazás állapotának figyelésére.

### <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)
Az alábbi táblázat a virtuális gép által használt erőforrásokat tartalmazza, amelyeknek létezniük kell a virtuális gép létrehozásakor, vagy akkor kell őket létrehozni.

| Erőforrás | Kötelező | Leírás |
| --- | --- | --- |
| [Erőforráscsoport](../../azure-resource-manager/management/overview.md) |Igen |A virtuális gépnek egy erőforráscsoport tagjának kell lennie. |
| [Tárfiók](../../storage/common/storage-create-storage-account.md) |Igen |A virtuális gépnek szüksége van egy tárfiókra a virtuális merevlemezek tárolásához. |
| [Virtuális hálózat](../../virtual-network/virtual-networks-overview.md) |Igen |A virtuális gépnek egy virtuális hálózat tagjának kell lennie. |
| [Nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Nem |A virtuális gép rendelkezhet hozzárendelt nyilvános IP-címmel, hogy távolról is el lehessen érni. |
| [Hálózati adapter](../../virtual-network/virtual-network-network-interface.md) |Igen |A virtuális gépnek szüksége van a hálózati illesztőre a hálózattal való kommunikációhoz. |
| [Adatlemezek](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Nem |A virtuális gépek a tárolókapacitást bővítő adatlemezeket is tartalmazhatnak. |

## <a name="next-steps"></a>További lépések

Hozza létre az első virtuális gépet!

- [Portál](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Azure CLI](quick-create-cli.md)

