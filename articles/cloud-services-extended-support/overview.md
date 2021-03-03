---
title: Tudnivalók az Azure Cloud Servicesról (bővített támogatás)
description: Ismerje meg a szolgáltatás konfigurációs fájljának alárendelt elemeit, amelyek a Virtual Network és a DNS-értékeket határozzák meg.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8ed21d8689bf5340c1bde0a7f782bb8614f7cf11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700201"
---
# <a name="about-azure-cloud-services-extended-support"></a>Tudnivalók az Azure Cloud Servicesról (bővített támogatás)

> [!IMPORTANT]
> A Cloud Services (bővített támogatás) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Cloud Services (bővített támogatás) az [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) termék új, [Azure Resource Manager](../azure-resource-manager/management/overview.md) alapú üzembe helyezési modellje, amely jelenleg nyilvános előzetes verzióban érhető el. A Cloud Services (kiterjesztett támogatás) elsődleges előnye, hogy regionális rugalmasságot biztosít, valamint az Azure-Service Manager használatával üzembe helyezett Cloud Services Azure-beli szolgáltatások paritását. Emellett olyan ARM-képességeket is kínál, mint a szerepköralapú hozzáférés és vezérlés (RBAC), címkék, szabályzatok és a telepítési sablonok támogatása.  

Ezzel a módosítással a Cloud Services Azure Service Manager-alapú üzemi modellje át lesz nevezve [Cloud Services (klasszikus)](../cloud-services/cloud-services-choose-me.md). Megtarthatja a webes és felhőalapú alkalmazások és szolgáltatások felépítésének és gyors üzembe helyezésének képességét. A Cloud Services-infrastruktúrát a jelenlegi igény alapján méretezheti, és gondoskodhat arról, hogy az alkalmazások teljesítménye a költségek csökkentése mellett is megmaradjon.  

## <a name="what-does-not-change"></a>Mi nem változik 
- Létrehozhatja a kódot, definiálhatja a konfigurációkat, és üzembe helyezheti azt az Azure-ban. Az Azure beállítja a számítási környezetet, futtatja a kódot, majd figyeli és karbantartja azt.
- A Cloud Services (bővített támogatás) a szerepkörök, a [webes és a feldolgozók](../cloud-services/cloud-services-choose-me.md)két típusát is támogatja. A webes és a feldolgozói szerepkörök kialakítását, architektúráját vagy összetevőit nem változtatja meg. 
- A felhőalapú szolgáltatás három összetevője, a szolgáltatás definíciója (. csdef), a szolgáltatás konfigurációja (. cscfg) és a szolgáltatáscsomag (. cspkg) továbbítása megtörténik, és a [formátumuk](cloud-services-model-and-package.md)nem változik. 
- Nem szükséges módosítani a futásidejű programkódot, mert az adatsík ugyanaz, és a vezérlő síkja csak változik. 
- Az Azure GuestOS kiadásai és a hozzájuk tartozó frissítések Cloud Services (klasszikus) összhangban vannak.
- Az alapul szolgáló frissítési folyamat a frissítési tartományok tekintetében, a frissítés menetének, visszaállításának és az engedélyezett szolgáltatások változásainak a frissítés során nem változik

## <a name="changes-in-deployment-model"></a>Változások a telepítési modellben

Minimális változtatásokra van szükség a szolgáltatás-konfiguráció (. cscfg) és a Service Definition (. csdef) fájljai számára a Cloud Services telepítéséhez (kiterjesztett támogatás). A futásidejű kód módosítása nem szükséges. Az üzembe helyezési parancsfájlokat azonban frissíteni kell az új Azure Resource Manager-alapú API-k meghívásához. 

:::image type="content" source="media/overview-image-1.png" alt-text="A rendszerkép a klasszikus felhőalapú szolgáltatás konfigurációját jeleníti meg a sablon hozzáadásával. ":::

Az üzembe helyezésre vonatkozó Cloud Services (klasszikus) és Cloud Services (kiterjesztett támogatás) közötti fő különbségek a következők: 

- Azure Resource Manager üzemelő példányok [ARM-sablonokat](../azure-resource-manager/templates/overview.md) használnak, amely egy JavaScript Object Notation (JSON) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon olyan deklaratív szintaxist használ, amellyel anélkül határozhatja meg, hogy mit szeretne üzembe helyezni, hogy ehhez programozási parancsok sorozatát kellene megírnia. A szolgáltatás konfigurálásához és a szolgáltatás-definíciós fájlhoz konzisztensnek kell lennie az [ARM-sablonnal](../azure-resource-manager/templates/overview.md) Cloud Services (kiterjesztett támogatás) telepítésekor. Ezt az [ARM-sablon manuális létrehozásával](deploy-template.md) vagy a [PowerShell](deploy-powershell.md), a [portál](deploy-portal.md) és a [Visual Studio](deploy-visual-studio.md)használatával lehet megvalósítani.  

- Az ügyfeleknek [Azure Key Vault](../key-vault/general/overview.md) kell használniuk a [tanúsítványok Cloud Services (kiterjesztett támogatás) való kezeléséhez](certificates-and-key-vault.md). A Azure Key Vault lehetővé teszi az alkalmazások hitelesítő adatainak, például a titkok, kulcsok és tanúsítványok biztonságos tárolását és kezelését egy központi és biztonságos Felhőbeli tárházban. Az alkalmazások a hitelesítő adatok lekéréséhez futtatás közben Key Vault a hitelesítést. 

- A [Azure Resource Manageron](../azure-resource-manager/templates/overview.md) központilag telepített összes erőforrásnak egy virtuális hálózaton belül kell lennie. A virtuális hálózatok és alhálózatok a Azure Resource Manager meglévő Azure Resource Manager API-k használatával jönnek létre, és a. cscfg NetworkConfiguration szakaszában kell hivatkozni a Cloud Services telepítésekor (kiterjesztett támogatás).   

- Minden felhőalapú szolgáltatás (kiterjesztett támogatás) egyetlen független üzembe helyezés. A Cloud Services (bővített támogatás) nem támogatja több tárolóhely használatát egyetlen felhőalapú szolgáltatáson belül.  
    - A VIP swap <sup>*</sup> funkció felhasználható két felhőalapú szolgáltatás (kiterjesztett támogatás) közötti váltásra. Egy felhőalapú szolgáltatás új kiadásának teszteléséhez és előkészítéséhez, a Cloud Service (kiterjesztett támogatás) üzembe helyezéséhez és a címkeként való megjelöléséhez, amely egy másik felhőalapú szolgáltatással (kiterjesztett támogatással), VIP-ként cserélhető.  

- A tartománynév-szolgáltatás (DNS) címkéje nem kötelező a Cloud Service-hez (kiterjesztett támogatás). Azure Resource Manager a DNS-címke a felhőalapú szolgáltatáshoz társított nyilvános IP-erőforrás tulajdonsága. 


<sup>*</sup> A nyilvános előzetes verzióban nem érhető el a Cloud Services VIP-swap (bővített támogatás).  

## <a name="migration-to-azure-resource-manager"></a>Áttelepítés Azure Resource Managerre

Cloud Services (kiterjesztett támogatás) két elérési utat biztosít az Azure- [Service Managerról](/powershell/azure/servicemanagement/overview?preserve-view=true&view=azuresmps-4.0.0) a [Azure Resource Managerre](../azure-resource-manager/management/overview.md)való Migrálás során. 
1) Az ügyfelek közvetlenül a Azure Resource Manager telepítik a Cloud Services szolgáltatást, majd törlik a régi Cloud Service-t az Azure Service Managerban. 
2) A helyben történő áttelepítés lehetővé teszi Cloud Services (klasszikus) áttelepítését, amely minimálisan leállást eredményez Cloud Services (kiterjesztett támogatás). 

### <a name="additional-migration-options"></a>További áttelepítési lehetőségek

A Cloud Services (klasszikus) rendszerről Cloud Servicesra (bővített támogatás) való áttelepítési tervek kiértékelése során érdemes lehet olyan további Azure-szolgáltatásokat vizsgálni, mint például a következők: [Virtual Machine Scale sets](../virtual-machine-scale-sets/overview.md), [app Service](../app-service/overview.md), [azure Kubernetes Service](../aks/intro-kubernetes.md)és [Azure Service Fabric](../service-fabric/service-fabric-overview.md). Ezek a szolgáltatások továbbra is további képességeket biztosítanak, míg a Cloud Services (kiterjesztett támogatás) elsősorban a Cloud Services (klasszikus) szolgáltatás-paritást fogja fenntartani. 

Az alkalmazástól függően Cloud Services (kiterjesztett támogatás) a többi lehetőséghez képest lényegesen kevesebb erőfeszítést igényel a Azure Resource Manager való áttéréshez. Ha az alkalmazás nem fejlődik, Cloud Services (kiterjesztett támogatás) egy életképes megoldás, amely gyors áttelepítési útvonalat biztosít. Ezzel szemben, ha az alkalmazása folyamatosan fejlődik, és modernebb szolgáltatáskészlet szükséges, a többi Azure-szolgáltatás megismerésével hatékonyabban kezelheti aktuális és jövőbeli követelményeit. 

## <a name="next-steps"></a>Következő lépések
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).