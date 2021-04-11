---
title: Az Azure Cloud Services-re való áttelepítéshez szükséges technikai részletek és követelmények (kiterjesztett támogatás)
description: Technikai részleteket és követelményeket biztosít az Azure Cloud Services (klasszikus) és az Azure Cloud Services (bővített támogatás) áttelepítéséhez
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286908"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Az Azure Cloud Servicesba való Migrálás technikai részletei (kiterjesztett támogatás)   

Ez a cikk az áttelepítési eszközre vonatkozó technikai részleteket ismerteti Cloud Services (klasszikus). 

> [!IMPORTANT]
> Az áttelepítési eszköz jelenleg nyilvános előzetes verzióban érhető el a Cloud Services (klasszikus) rendszerről a Cloud Servicesra (kiterjesztett támogatás). Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Az áttelepítéshez támogatott funkciók/forgatókönyvek részletei 

### <a name="extensions-and-plugin-migration"></a>Bővítmények és beépülő modulok migrálása 
- Az összes engedélyezett és támogatott bővítmény migrálva lesz. 
- A letiltott bővítmények nem lesznek áttelepítve. 
- A beépülő modulok egy örökölt fogalom, amelyet az áttelepítés előtt el kell távolítani. Ezeket a rendszer áttelepítéshez, de az áttelepítés után is támogatja, ha a bővítményt engedélyezni kell, először el kell távolítani a beépülő modult a bővítmény telepítése előtt. Ez a távoli asztali bővítmények és bővítmények többségét érinti.   
 
### <a name="certificate-migration"></a>Tanúsítvány áttelepítése
- Cloud Services (bővített támogatás) esetén a tanúsítványok tárolása egy Key Vault történik. A Migrálás részeként létrejön egy Key Vault a Cloud Service-nevet használó ügyfelek számára, és az Azure Service Manager összes tanúsítványát át kell vinni Key Vault. 
- A Key Vaultre mutató hivatkozás a sablonban van megadva, vagy a PowerShell vagy az Azure CLI használatával lett átadva. 

### <a name="service-configuration-and-service-definition-files"></a>Szolgáltatás konfigurációs és szolgáltatás-definíciós fájljai
- A. cscfg és a. csdef fájlokat frissíteni kell Cloud Services (bővített támogatás) kisebb módosításokkal. 
- Az erőforrások, például a virtuális hálózat és a VM SKU neve eltérő. Lásd: [erőforrások fordítása és elnevezési konvenció az áttelepítés után](#translation-of-resources-and-naming-convention-post-migration)
- Az ügyfelek a [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) és a [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/get)használatával kérhetik le az új üzemelő példányokat. 

### <a name="cloud-service-and-deployments"></a>Felhőalapú szolgáltatás és központi telepítések
- Minden Cloud Services (kiterjesztett támogatás) üzembe helyezése egy független felhőalapú szolgáltatás. Az üzembe helyezés már nem egy felhőalapú szolgáltatásba van csoportosítva tárolóhelyek használatával.
- Ha két tárolóhelye van a Cloud Service-ben (klasszikus), törölnie kell egy tárolóhelyet (átmeneti), és az áttelepítési eszköz használatával át kell helyeznie a másik (éles) tárolóhelyet Azure Resource Manager. 
- A felhőalapú szolgáltatás központi telepítésére vonatkozó nyilvános IP-cím a Azure Resource Managerre való Migrálás után változatlan marad, és alapszintű SKU IP-(dinamikus vagy statikus) erőforrásként van kitéve. 
- Az áttelepített felhőalapú szolgáltatás DNS-neve és tartománya (cloudapp.azure.net) változatlan marad. 

### <a name="virtual-network-migration"></a>Virtuális hálózat migrálása
- Ha egy Cloud Services üzemelő példány egy virtuális hálózaton van, akkor az áttelepítés során az összes Cloud Services és a társított virtuális hálózati erőforrások együtt lesznek áttelepítve. 
- Az áttelepítés után a virtuális hálózat egy másik erőforráscsoporthoz kerül, mint a felhőalapú szolgáltatás. 
- A több Cloud Services rendelkező virtuális hálózatok esetében az egyes felhőalapú szolgáltatások a másik után települnek át. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Nem virtuális hálózatban üzemelő példányok áttelepítése
- A 2017-es verzióban az Azure automatikusan elkezdett új központi telepítéseket létrehozni (az ügyfél által megadott virtuális hálózat nélkül) egy "default" virtuális hálózatot létrehozó platformra. Ezek az alapértelmezett virtuális hálózatok rejtve maradnak az ügyfelektől.   
- A Migrálás részeként ez az alapértelmezett virtuális hálózat Azure Resource Manager egyszer elérhetővé válik az ügyfeleknek. A Azure Resource Manager-ben lévő központi telepítés kezeléséhez vagy frissítéséhez az ügyfeleknek hozzá kell adni ezt a virtuális hálózati információt a. cscfg fájl NetworkConfiguration szakaszában.    
- A Azure Resource Managerre áttelepített alapértelmezett virtuális hálózat ugyanabba az erőforráscsoporthoz kerül, mint a Cloud Service.
- A korábban létrehozott Cloud Services nem lesz virtuális hálózatban, és az eszköz használatával nem telepíthető át. Érdemes lehet ezeket a Cloud Servicesokat közvetlenül a Azure Resource Manager-ben telepíteni. 
- Annak ellenőrzéséhez, hogy a központi telepítés jogosult-e az áttelepítésre, futtassa az érvényesítés API-t a központi telepítésben. Az API érvényesítésének eredménye a explicit módon megemlítve hibaüzenetet tartalmaz, ha a központi telepítés jogosult az áttelepítésre.     

### <a name="load-balancer"></a>Load Balancer   
- Nyilvános végpontot használó felhőalapú szolgáltatás esetén a Cloud Service-hez társított platform létrehozott terheléselosztó az ügyfél előfizetésén belül Azure Resource Manager. A Load Balancer egy írásvédett erőforrás, és a frissítések csak a szolgáltatási konfiguráció (. cscfg) és a Service Definition (. csdef) fájlok segítségével vannak korlátozva. 

### <a name="key-vault"></a>Key Vault
- A Migrálás részeként az Azure automatikusan létrehoz egy új Key Vault, és áttelepíti az összes tanúsítványt. Az eszköz nem teszi lehetővé meglévő Key Vault használatát. 
- Cloud Services (kiterjesztett támogatás) az azonos régióban és előfizetésben található Key Vault szükséges. Ez a Key Vault automatikusan létrejön a Migrálás részeként. 


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Az erőforrások és elnevezési konvenciók fordítása az áttelepítés után
A Migrálás részeként módosulnak az erőforrások nevei, és néhány Cloud Services funkció Azure Resource Manager erőforrásként van kitéve. A táblázat összefoglalja Cloud Services áttelepítésre vonatkozó módosításokat.

| Cloud Services (klasszikus) <br><br> Erőforrás neve | Cloud Services (klasszikus) <br><br> Syntax| Cloud Services (kiterjesztett támogatás) <br><br> Erőforrás neve| Cloud Services (kiterjesztett támogatás) <br><br> Syntax | 
|---|---|---|---|
| Felhőszolgáltatás | `cloudservicename` | Nincs társítva| Nincs társítva |
| Üzembe helyezés (portál létrehozva) <br><br> Üzembe helyezés (nem portálon létrehozott)  | `deploymentname` | Cloud Services (kiterjesztett támogatás) | `deploymentname` |  
| Virtual Network | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Nincs társítva |  Virtual Network (a portál nem lett létrehozva) <br><br> Virtual Network (portál létrehozva) <br><br> Virtuális hálózatok (alapértelmezett) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| Nincs társítva | Nincs társítva | Key Vault | `cloudservicename` | 
| Nincs társítva | Nincs társítva | Erőforráscsoport a Cloud Service üzembe helyezéséhez | `cloudservicename-migrated` | 
| Nincs társítva | Nincs társítva | Virtual Network erőforráscsoport | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Nincs társítva | Nincs társítva | Nyilvános IP-cím (dinamikus) | `cloudservicenameContractContract` | 
| Fenntartott IP neve | `reservedipname` | Fenntartott IP (nem portálon létrehozott) <br><br> Fenntartott IP (portál létrehozva) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Nincs társítva| Nincs társítva | Load Balancer | `deploymentname-lb`|



## <a name="migration-issues-and-how-to-handle-them"></a>Áttelepítési problémák és azok kezelése. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>Az áttelepítés hosszú ideig megakadt egy műveletben. 
- A véglegesítés, előkészítés és megszakítás hosszú időt vehet igénybe az üzemelő példányok számától függően. A művelet 24 óra elteltével időtúllépést eredményez.   
- A műveletek végrehajtása, előkészítése és megszakítása idempotens. A legtöbb problémát az Újrapróbálkozással javíthatja. Lehetnek átmeneti hibák, amelyek néhány percen belül eltérhetnek, ezért azt javasoljuk, hogy egy rés után próbálkozzon újra. Ha a Azure Portal használatával végez áttelepítést, és a művelet beragad egy "folyamatban lévő állapotban", a PowerShell használatával próbálja megismételni a műveletet. 
- Lépjen kapcsolatba az ügyfélszolgálattal, hogy segítse az üzemelő példány áttelepítését vagy visszaállítását a háttérből. 

### <a name="migration-failed-in-an-operation"></a>Az áttelepítés sikertelen volt egy műveletben. 
- Ha az ellenőrzés sikertelen, akkor az az oka, hogy a központi telepítés vagy a virtuális hálózat nem támogatott forgatókönyvet/szolgáltatást/erőforrást tartalmaz. A nem támogatott forgatókönyvek listájának használatával megkeresheti a dokumentumokban található munkát.  
- Az előkészítési művelet először érvényesíti az érvényesítést, beleértve néhány költséges érvényességet (az ellenőrzés nem vonatkozik rá). Az előkészítési hiba oka lehet egy nem támogatott forgatókönyv. Keresse meg a forgatókönyvet és a munkát a nyilvános dokumentumokban. A megszakítást vissza kell hívni az eredeti állapotba, és fel kell oldani a frissítések és törlési műveletek központi telepítését.
- Ha a megszakítás sikertelen, próbálja megismételni a műveletet. Ha az újrapróbálkozások sikertelenek, forduljon az ügyfélszolgálathoz.
- Ha nem sikerült véglegesíteni a műveletet, próbálkozzon újra a művelettel. Ha az újrapróbálkozás sikertelen, forduljon az ügyfélszolgálathoz. Még ha véglegesíti a hibát, a telepítéshez nem kell adatsíkokat kiadnia. Az üzemelő példánynak probléma nélkül képesnek kell lennie az ügyfelek forgalmának kezelésére. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>A portál a felkészülés után frissült. Az újraindítás és a végrehajtás vagy a megszakítás többé nem látható. 
- A portál helyileg tárolja az áttelepítési adatokat, és ezért a frissítés után az érvényesítési fázisból indul el, még akkor is, ha a felhőalapú szolgáltatás az előkészítési fázisban van.  
- A portál segítségével áttekintheti az érvényesítési és előkészítési lépéseket, hogy elérhetővé tegye a megszakítás és a végrehajtás gombot. A hiba nem fog okozni.
- Az ügyfelek a PowerShell vagy a REST API használatával megszakítják vagy véglegesítik a műveleteket. 

### <a name="how-much-time-can-the-operations-takebr"></a>Mennyi időt vehet igénybe a műveletek?<br>
Az érvényesítés célja, hogy gyors legyen. A felkészülés a leghosszabb ideig fut, és az áttelepített szerepkör-példányok teljes számától függően időt vesz igénybe. A megszakítás és a végrehajtás is időt vesz igénybe, de a felkészüléshez képest kevesebb időt vesz igénybe. 24 óra elteltével minden művelet időtúllépést eredményez. 
