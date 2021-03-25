---
title: Gyakran ismételt kérdések az Azure Cloud Services (bővített támogatás)
description: Gyakran ismételt kérdések az Azure Cloud Services (bővített támogatás)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4db23982dfaed1447023edb417a3732dac94bdc4
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025237"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Az Azure Cloud Services (kiterjesztett támogatás) szolgáltatással kapcsolatos gyakori kérdések
Ez a cikk az Azure Cloud Servicesokkal kapcsolatos gyakori kérdéseket ismerteti (kiterjesztett támogatás).

## <a name="general"></a>Általános kérdések

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Mi az erőforrás neve Cloud Services (klasszikus) & Cloud Services (bővített támogatás)?
- Cloud Services (klasszikus): `microsoft.classiccompute/domainnames`
- Cloud Services (kiterjesztett támogatás): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Milyen helyszínek érhetők el a Cloud Services üzembe helyezéséhez (kiterjesztett támogatás)?
A Cloud Services (kiterjesztett támogatás) minden nyilvános Felhőbeli régióban elérhető.

### <a name="how-does-my-quota-change"></a>Hogyan változik a kvóta? 
Az ügyfeleknek a többi Azure Resource Manager termékkel megegyező folyamatokkal kell megadniuk a kvótát. Azure Resource Manager a kvóta regionális, és az egyes régiókban külön kvóta-kérelemre lesz szükség.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Miért nem látok éles & átmeneti tárolóhelyet?
Cloud Services (kiterjesztett támogatás) nem támogatja az üzemeltetett szolgáltatás logikai fogalmát, amely két tárolóhelyet tartalmaz (éles & előkészítés). Mindegyik üzemelő példány egy független felhőalapú szolgáltatás (kiterjesztett támogatás). Egy felhőalapú szolgáltatás új kiadásának teszteléséhez és előkészítéséhez, a Cloud Service (kiterjesztett támogatás) üzembe helyezéséhez és a címkeként való megjelöléséhez, amely egy másik felhőalapú szolgáltatással (kiterjesztett támogatással), VIP-ként cserélhető.

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Miért nem tudok többé üres felhőalapú szolgáltatást létrehozni?
Az üzemeltetett szolgáltatások neveinek fogalma már nem létezik, ezért nem hozható létre üres felhőalapú szolgáltatás (kiterjesztett támogatás).

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Támogatja a Cloud Services (kiterjesztett támogatás) Resource Health-ellenőrzési (RHC) támogatását?
Nem, Cloud Services (kiterjesztett támogatás) nem támogatja Resource Health-ellenőrzési (RHC) használatát.

### <a name="how-are-role-instance-metrics-changing"></a>Hogyan változnak a szerepkör-példány metrikái?
A szerepkör-példány metrikái nem módosultak. 

### <a name="how-are-web--worker-roles-changing"></a>Hogyan változnak a web & feldolgozói szerepkörök?
A webes és a feldolgozói szerepkörök kialakítását, architektúráját vagy összetevőit nem változtatja meg. 

### <a name="how-are-role-instances-changing"></a>Hogyan változnak a szerepkör-példányok?
A szerepkör-példányok kialakítása, architektúrája vagy összetevői nem változnak. 

### <a name="how-will-guest-os-updates-change"></a>Hogyan változnak a vendég operációs rendszer frissítései?
 A bevezetési módszer nem módosul. A Cloud Services (klasszikus) és Cloud Services (bővített támogatás) ugyanazokat a frissítéseket kapja meg.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>A Cloud Services (kiterjesztett támogatás) támogatja a leállított, leállított és leállított állapotú állapotokat?

Cloud Services (bővített támogatás) a központi telepítés csak a leállított, leállított állapotot támogatja a Azure Portalban. A leállított kiosztott állapot nem támogatott. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>A Cloud Services (kiterjesztett támogatás) központi telepítések támogatják a fürtök, a rendelkezésre állási zónák és a régiók méretezését?
Cloud Services (kiterjesztett támogatás) központi telepítések nem méretezhetők több fürt, rendelkezésre állási zóna és régió között. 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Van-e díjszabási különbség a Cloud Services (klasszikus) és a Cloud Services (bővített támogatás) között?
Cloud Services (kiterjesztett támogatás) Azure Key Vault és alapszintű (ARM) nyilvános IP-címeket használ.A tanúsítványokat igénylő ügyfeleknek Azure Key Vault kell használniuk a Tanúsítványkezelők[számára (További információ a](https://azure.microsoft.com/pricing/details/key-vault/) Azure Key Vault díjszabásáról.)   A Cloud Services (kiterjesztett támogatás) minden nyilvános IP-címét külön számítjuk[fel (További információ a](https://azure.microsoft.com/pricing/details/ip-addresses/) nyilvános IP-címek díjszabásáról.) 
## <a name="resources"></a>Források 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Milyen erőforrásokhoz kell kapcsolódnia egy Cloud Serviceshoz (kiterjesztett támogatás)?
A terheléselosztó, a hálózati biztonsági csoportok és az útválasztási tábláknak ugyanabban a régióban és erőforráscsoporthoz kell rendelkezniük. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Milyen erőforrásokhoz kapcsolódó Cloud Services (kiterjesztett támogatás) üzemelő példánynak ugyanabban a régióban kell élnie?
A Key Vault, a virtuális hálózat, a nyilvános IP-címek, a hálózati biztonsági csoportok és az útválasztási tábláknak ugyanabban a régióban kell lenniük.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Milyen erőforrásokhoz kell kapcsolódnia egy Cloud Serviceshoz (kiterjesztett támogatás) egy virtuális hálózaton?
A nyilvános IP-címek, a terheléselosztó, a hálózati biztonsági csoportok és az útválasztási tábláknak ugyanabban a virtuális hálózatban kell lenniük. 

## <a name="deployment-files"></a>Központi telepítési fájlok 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Hogyan használhatok sablont az üzembe helyezés üzembe helyezéséhez vagy kezeléséhez?
A sablon és a paraméter fájljai a REST, a PowerShell és a parancssori felület használatával adhatók át paraméterként. A Azure Portal használatával is feltölthetők.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>Most már négy fájlt kell fenntartani? (sablon, paraméter, csdef, cscfg)
A sablon és a paraméter fájljai csak az üzembe helyezés automatizálásához használatosak. Hasonlóan Cloud Services (klasszikus), manuálisan is létrehozhatja a függő erőforrásokat, majd egy Cloud Services (kiterjesztett támogatás) üzembe helyezését a PowerShell, a CLI-parancsok vagy a portálon a meglévő csdef, a cscfg használatával.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Hogyan változik az alkalmazás kódja Cloud Services (kiterjesztett támogatás)
A cspkg-ben csomagolt alkalmazás kódjában nincs szükség módosításra. A meglévő alkalmazásai továbbra is ugyanúgy működnek, mint korábban. 

### <a name="does-cloud-services-extended-support-allow-ctp-package-format"></a>Cloud Services (kiterjesztett támogatás) engedélyezi a CTP-csomagok formátumát?
A CTP-csomag formátuma Cloud Services (kiterjesztett támogatás) nem támogatott. Ez azonban lehetővé teszi a 800 MB-os bővített csomag méretének korlátozását

## <a name="migration"></a>Migrálás

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>A Cloud Services (kiterjesztett támogatás) csökkenti a foglalási hibák miatti hibákat?
Nem, a Cloud Service (bővített támogatás) üzemelő példányok olyan fürthöz vannak kötve, mint a Cloud Services (klasszikus). Ezért a foglalási hibák továbbra is fennállnak, ha a fürt megtelt. 

### <a name="when-do-i-need-to-migrate"></a>Mikor kell migrálni? 
A szükséges idő becslése és a bonyolultsági áttelepítés számos változótól függ. A tervezés a leghatékonyabb lépés a munka, a blokkolók és a Migrálás összetettségének megismeréséhez.

## <a name="networking"></a>Hálózatkezelés

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Miért nem hozható létre virtuális hálózat nélküli központi telepítés?
A virtuális hálózatok a Azure Resource Manager összes telepítéséhez szükséges erőforrások. A Cloud Services (kiterjesztett támogatás) telepítésének virtuális hálózaton belül kell lennie. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Miért látok ennyi hálózati erőforrást? 
A Azure Resource Manager a Cloud Services (kiterjesztett támogatás) összetevőit a jobb láthatóság és jobb szabályozás érdekében erőforrásként teszik elérhetővé. Ugyanazokat a típusú erőforrásokat használták Cloud Services (klasszikus), de csak rejtettek. Egy ilyen erőforrás például a nyilvános Load Balancer, amely mostantól a platform által automatikusan létrehozott explicit "írásvédett" erőforrást eredményezi.

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Milyen korlátozások vonatkoznak a megfelelő Cloud Services (bővített támogatással) rendelkező alhálózatokra?
Cloud Services (kiterjesztett támogatású) központi telepítéseket tartalmazó alhálózat nem osztható meg más számítási termékekből, például Virtual Machinesból, Virtual Machines méretezési csoportokból, Service Fabric stb.).

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Milyen IP-kiosztási módszereket támogat a Cloud Services (bővített támogatás)?
Cloud Services (kiterjesztett támogatás) támogatja a dinamikus & statikus IP-kiosztási módszereit. A statikus IP-címek a cscfg fájlban fenntartott IP-címekként vannak hivatkozva.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>Miért kell díjat fizetni az IP-címekért?
Az ügyfelek számlázása az IP-cím Cloud Services (kiterjesztett támogatás) alapján történik, ugyanúgy, mint a virtuális gépekhez társított IP-címek számlázása. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Használhatok DNS-nevet Cloud Services (kiterjesztett támogatással)? 
Igen. A Cloud Services (kiterjesztett támogatás) DNS-nevet is kaphat. A Azure Resource Manager a DNS-címke a felhőalapú szolgáltatáshoz hozzárendelt nyilvános IP-cím opcionális tulajdonsága. A Azure Resource Manager alapú központi telepítések DNS-nevének formátuma a következő: `<userlabel>.<region>.cloudapp.azure.com`

## <a name="certificates--key-vault"></a>Tanúsítványok & Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Miért van szükség a tanúsítványok Cloud Services (kiterjesztett támogatás) kezeléséhez?
Cloud Services (kiterjesztett támogatás) ugyanezt a folyamatot fogadta el, mint más számítási ajánlatokat, ahol a tanúsítványok az ügyfél által felügyelt Kulcstartókban találhatók. Ez lehetővé teszi, hogy az ügyfelek teljes körűen szabályozhatják a titkokat & a tanúsítványokat. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>Használhatok egy Key Vault az összes saját üzembe helyezéséhez az összes régióban?
Nem. Key Vault regionális erőforrás, és az ügyfeleknek egy Key Vault kell rendelkezniük az egyes régiókban. Az adott régióban lévő összes központi telepítéshez azonban egy Key Vault is használható.

## <a name="next-steps"></a>Következő lépések
A Cloud Services használatának megkezdéséhez tekintse meg [a Cloud Service (kiterjesztett támogatás) üzembe helyezése a PowerShell használatával](deploy-powershell.md) című témakört.
