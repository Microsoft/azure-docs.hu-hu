---
title: Azure-Szószedet – Azure-szótár | Microsoft Docs
description: Az Azure-Szószedet használatával megismerheti az Azure platform Felhőbeli terminológiáját. Ez a rövid Azure-szótár az Azure általános Felhőbeli használati feltételeinek definícióit tartalmazza.
keywords: Azure Dictionary, Felhőbeli terminológia, Azure Szószedet, terminológiai definíciók, Felhőbeli feltételek
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 785c87e8136430557af819cd8c7ab1db8f54c060
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507721"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure Szószedet: az Azure platform felhőalapú terminológiájának szótára

A Microsoft Azure Szószedet az Azure platformhoz készült Felhőbeli terminológia rövid szótára. Lásd még:

* [Microsoft Azure és Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) – az Azure-szolgáltatások és az AWS-partnerek meghatározása.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Felhőalapú számítástechnikai feltételek](https://azure.microsoft.com/overview/cloud-computing-dictionary/) – általános iparági Felhőbeli feltételek.

## <a name="account"></a>account
Egy Azure-előfizetés eléréséhez és kezeléséhez használt fiók. Gyakran Azure-fióknak is nevezzük, bár a fiókok bármelyike lehet: egy meglévő munkahelyi, iskolai vagy személyes Microsoft-fiók. Létrehozhat egy fiókot is az Azure-előfizetés kezeléséhez az [ingyenes próbaverzióra](https://azure.microsoft.com)való regisztráció során.  
A bejelentkezéshez használhatja az [Azure-előfizetésre vonatkozó regisztrációt Microsoft 365 fiókját](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md) és [fiókját](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>API-alkalmazás
[App Service alkalmazás](#app-service-app)másik neve.

## <a name="app-service-app"></a>App Service-alkalmazás
A [Azure app Service](app-service/overview.md) a webhelyek vagy webalkalmazások, webes API-k vagy [mobil alkalmazások háttérrendszer](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)üzemeltetéséhez szükséges számítási erőforrások. App Service alkalmazások *app Services*, *webalkalmazások*, *API-alkalmazások* és *Mobile apps* néven is ismertek.

## <a name="availability-set"></a>rendelkezésre állási csoport
Az alkalmazások redundancia és megbízhatósága érdekében együtt kezelt virtuális gépek gyűjteménye. A rendelkezésre állási csoport használata biztosítja, hogy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető legyen.  
Lásd: [a Windows rendszerű virtuális gépek rendelkezésre állásának kezelése](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [a linuxos virtuális gépek rendelkezésre állásának kezelése](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Klasszikus Azure üzembe helyezési modell
Az erőforrások Azure-beli üzembe helyezéséhez használt két [üzembe helyezési modell](./azure-resource-manager/management/deployment-models.md) egyike (az új modell Azure Resource Manager). Bizonyos Azure-szolgáltatások csak a Resource Manager-alapú üzemi modellt támogatják, némelyikük csak a klasszikus üzemi modellt támogatja, és néhány támogatást is. Az egyes Azure-szolgáltatások dokumentációja határozza meg, hogy mely modell (ek) et támogatják.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Azure parancssori felület (CLI)
Parancssori felület, amely az Azure-szolgáltatások Windows, macOS és Linux rendszeren való felügyeletére használható.  Egyes szolgáltatások vagy szolgáltatási funkciók csak a PowerShell vagy a parancssori felület használatával kezelhetők. Lásd: [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Parancssori felület az Azure-szolgáltatások Windows rendszerű számítógépeken keresztüli parancssorból való kezeléséhez. Egyes szolgáltatások vagy szolgáltatási funkciók csak a PowerShell vagy a parancssori felület használatával kezelhetők.
Lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Azure Resource Manager üzembe helyezési modell
A két üzembe helyezési [modell](./azure-resource-manager/management/deployment-models.md) egyike, amely a Microsoft Azure erőforrásainak központi telepítésére szolgál (a másik a klasszikus üzembe helyezési modell). Bizonyos Azure-szolgáltatások csak a Resource Manager-alapú üzemi modellt támogatják, némelyikük csak a klasszikus üzemi modellt támogatja, és néhány támogatást is. Az egyes Azure-szolgáltatások dokumentációja határozza meg, hogy mely modell (ek) et támogatják.

## <a name="fault-domain"></a>tartalék tartomány
Egy rendelkezésre állási csoportba tartozó virtuális gépek gyűjteménye, amelyek valószínűleg sikertelenek lehetnek. Ilyen például a rackben található olyan gépek csoportja, amelyek közös áramforrással és hálózati kapcsolóval rendelkeznek. Az Azure-ban a rendelkezésre állási csoportokban lévő virtuális gépeket a rendszer automatikusan elválasztja több tartalék tartomány között.  
Lásd: [a Windows rendszerű virtuális gépek rendelkezésre állásának kezelése](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [a linuxos virtuális gépek rendelkezésre állásának kezelése](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>georedundáns
Egy meghatározott határ az adattároláshoz, amely általában két vagy több régiót tartalmaz. A határok a nemzeti határokon belül vagy azokon kívül is lehetnek, és az adó szabályozása hatással van rájuk. Minden geo rendelkezik legalább egy régióval. Térségek Ázsia és a Csendes-óceáni térség és Japán. Más néven *Földrajz*.  
Lásd: [Azure-régiók](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>Geo-replikáció
A tartalom (például Blobok, táblák és várólisták) automatikus replikálásának folyamata a regionális párokon belül.  
Lásd: [aktív Geo-Replication Azure SQL Database](./azure-sql/database/auto-failover-group-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Az operációs rendszert és az alkalmazás konfigurációját tartalmazó fájl, amely tetszőleges számú virtuális gép létrehozásához használható. Az Azure-ban kétféle rendszerkép létezik: virtuálisgép-lemezkép és operációsrendszer-rendszerkép. A virtuálisgép-rendszerkép tartalmaz egy operációs rendszert és a virtuális géphez csatlakoztatott összes lemezt a rendszerkép létrehozásakor. Az operációs rendszer lemezképe csak olyan általánosított operációs rendszert tartalmaz, amely nem rendelkezik adatlemez-konfigurációval.  
Lásd: [Windows rendszerű virtuálisgép-rendszerképek navigálása és kiválasztása az Azure-ban a PowerShell vagy a parancssori felület használatával](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>határértékeket
A létrehozható erőforrások száma vagy az elérhető teljesítmény-teljesítményteszt. A korlátokat jellemzően előfizetésekhez, szolgáltatásokhoz és ajánlatokhoz társítjuk.  
Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>terheléselosztó
Olyan erőforrás, amely a bejövő forgalmat a hálózat számítógépei között osztja szét. Az Azure-ban a terheléselosztó a terheléselosztási készletben definiált virtuális gépek forgalmát osztja ki. A [terheléselosztó lehet internetkapcsolattal rendelkező vagy](load-balancer/load-balancer-overview.md) belső is.  

## <a name="mobile-app"></a>Mobile-alkalmazás
[App Service alkalmazás](#app-service-app)másik neve.

## <a name="offer"></a>offer
Az Azure-előfizetésre vonatkozó díjszabás, kreditek és kapcsolódó feltételek.  
Tekintse meg az [Azure-ajánlat részleteit ismertető oldalt](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portál
Az Azure-szolgáltatások üzembe helyezésére és kezelésére szolgáló biztonságos webes portál.

## <a name="region"></a>régió
Egy geo-n belüli, nem határokon átnyúló, és egy vagy több adatközpontot tartalmazó régió. A díjszabás, a regionális szolgáltatások és az ajánlatok típusai a régió szintjén érhetők el. Egy régiót általában egy másik régióval párosítanak, amely akár több száz kilométerre is felhasználható. A regionális párok a vész-helyreállítási mechanizmusként használhatók, és magas rendelkezésre állást biztosítanak. Más néven *hely*.  
Lásd: [Azure-régiók](best-practices-availability-paired-regions.md)

## <a name="resource"></a>erőforrás
Az Azure-megoldás részét képező elem. Minden egyes Azure-szolgáltatás lehetővé teszi különböző típusú erőforrások, például adatbázisok vagy virtuális gépek üzembe helyezését.   
Lásd: [Azure Resource Manager áttekintése](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>erőforráscsoport
Egy olyan tároló a Resource Managerben, amely egy alkalmazás kapcsolódó erőforrásait tárolja. Az erőforráscsoport tartalmazhat egy alkalmazás összes erőforrását, vagy csak azokat az erőforrásokat, amelyek logikailag csoportosítva vannak. Eldöntheti, hogyan kívánja kiosztani az erőforrásokat az erőforráscsoportokba a szervezetének legmegfelelőbb módon.  
Lásd: [Azure Resource Manager áttekintése](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Resource Manager-sablon
Olyan JSON-fájl, amely egy vagy több Azure-erőforrást definiál, és amely az üzembe helyezett erőforrások közötti függőségeket határozza meg. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó.  
Lásd: [Azure Resource Manager sablonok készítése](./azure-resource-manager/templates/template-syntax.md)

## <a name="resource-provider"></a>erőforrás-szolgáltató
A Resource Manageren keresztül üzembe helyezhető és felügyelhető erőforrásokat ellátó szolgáltatás. Mindegyik erőforrás-szolgáltató műveleteket biztosít a telepített erőforrásokkal folytatott munkához. Az erőforrás-szolgáltatók a Azure Portalon, Azure PowerShellon és számos programozási SDK-n keresztül érhetők el.  
Lásd: [Azure Resource Manager áttekintése](azure-resource-manager/management/overview.md)

## <a name="role"></a>szerepkör
A felhasználókhoz, csoportokhoz és szolgáltatásokhoz hozzárendelhető hozzáférés szabályozására szolgáló eszköz. A szerepkörök olyan műveleteket hajthatnak végre, mint például az Azure-erőforrások létrehozása, kezelése és olvasása.  
Lásd [: RBAC: beépített szerepkörök](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>szolgáltatói szerződés (SLA)
A Microsoft által az üzemidőre és a kapcsolatra vonatkozó kötelezettségvállalásokat ismertető szerződés. Minden Azure-szolgáltatáshoz tartozik egy adott SLA.  
Lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>közös hozzáférésű aláírás (SAS)
Egy aláírás, amely lehetővé teszi, hogy korlátozott hozzáférést biztosítson egy erőforráshoz a fiók kulcsa nélkül. Az [Azure Storage](./storage/common/storage-sas-overview.md) például az SAS használatával biztosít ügyfél-hozzáférést az objektumokhoz, például a blobokhoz. A [IOT hub sas](iot-hub/iot-hub-devguide-security.md#security-tokens) használatával engedélyezi az eszközök számára a telemetria küldését.

## <a name="storage-account"></a>tárfiók
Egy olyan fiók, amely hozzáférést biztosít az Azure Storage-ban található Azure Blob, üzenetsor, tábla és Fájlszolgáltatások számára. A Storage-fiók neve határozza meg az Azure Storage-adatobjektumok egyedi névterét.  
Lásd: [Az Azure Storage-fiókok ismertetése](./storage/common/storage-account-create.md)

## <a name="subscription"></a>előfizetést
Az ügyfélnek a Microsofttal kötött szerződése, amely lehetővé teszi számukra az Azure-szolgáltatások beszerzését. Az előfizetés díjszabása és a kapcsolódó feltételek az előfizetéshez választott ajánlat hatálya alá tartoznak.
Lásd: [Microsoft Online előfizetői szerződés](https://azure.microsoft.com/support/legal/subscription-agreement/) és [Az Azure-előfizetések társítása Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>címke
Egy indexelési kifejezés, amely lehetővé teszi az erőforrások kategorizálását a kezelés vagy a számlázás követelményeinek megfelelően. Ha összetett erőforrásokkal rendelkezik, a címkék használatával megjelenítheti ezeket az eszközöket úgy, hogy a lehető legtöbbet hozza meg. Például elláthat címkével olyan erőforrásokat, amelyek hasonló szerepet töltenek be a szervezetben, vagy ugyanahhoz a részleghez tartoznak.  
Lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](./azure-resource-manager/management/tag-resources.md)

## <a name="update-domain"></a>tartomány frissítése
Egy rendelkezésre állási csoportba tartozó virtuális gépek gyűjteménye, amelyek egy időben frissülnek. Az azonos frissítési tartományban lévő virtuális gépeket a rendszer a tervezett karbantartás során együtt újraindítja. Az Azure soha nem indít újra egynél több frissítési tartományt. Más néven frissítési tartomány.  
Lásd: [a Windows rendszerű virtuális gépek rendelkezésre állásának kezelése](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [a linuxos virtuális gépek rendelkezésre állásának kezelése](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>virtuális gép
Operációs rendszert futtató fizikai számítógép szoftveres implementálása. Egyszerre több virtuális gép is futhat ugyanazon a hardveren. Az Azure-ban a virtuális gépek különböző méretekben érhetők el.  
Lásd: [Virtual Machines dokumentáció](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>Virtuálisgép-bővítmény
Olyan erőforrás, amely olyan viselkedést vagy szolgáltatásokat valósít meg, amelyek vagy más programok segítségére vannak, vagy amelyek lehetővé teszik a futó számítógépekkel való kommunikációt. A virtuálisgép-hozzáférési bővítmény használatával például alaphelyzetbe állíthatja vagy módosíthatja az Azure-beli virtuális gépek távelérési értékeit.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Lásd: [a virtuálisgép-bővítmények és-szolgáltatások (Windows)](./virtual-machines/extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [a virtuálisgép-bővítmények és-szolgáltatások (Linux)](./virtual-machines/extensions/features-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertetése

## <a name="virtual-network"></a><a name="vnet"></a>virtuális hálózat
Olyan hálózat, amely az összes többi Azure-bérlőtől elkülönített Azure-erőforrások közötti kapcsolatot biztosít. Az [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) lehetővé teszi a virtuális hálózatok és egy virtuális hálózat és egy helyszíni hálózat közötti kapcsolatok létrehozását. Az IP-címek, a DNS-beállítások, a biztonsági házirendek és a virtuális hálózatokon belüli útválasztási táblák teljes körű szabályozására is lehetőség van.  
Lásd: [Virtual Network áttekintése](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webalkalmazás
[App Service alkalmazás](#app-service-app)másik neve.

## <a name="see-also"></a>Lásd még

* [Bevezetés az Azure használatába](https://azure.microsoft.com/get-started/)
* [Felhőalapú erőforrás-központ](https://azure.microsoft.com/resources/)  
* [Azure vállalati alkalmazásokhoz](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Az Azure az adatközpontban](https://azure.microsoft.com/overview/business-apps-on-azure/)