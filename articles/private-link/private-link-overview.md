---
title: Mi az az Azure privát kapcsolat?
description: Az Azure Private link funkcióinak, architektúrájának és megvalósításának áttekintése. Ismerje meg, hogyan működik az Azure Private-végpontok és az Azure Private link Service, és hogyan használhatja őket.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 03/15/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 19a4c6697c161a78bfb1425009bd0b142dece876
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057235"
---
# <a name="what-is-azure-private-link"></a>Mi az az Azure privát kapcsolat? 
Az Azure Private link lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett felhasználói/partneri szolgáltatások elérését a virtuális hálózat [privát végpontján](private-endpoint-overview.md) keresztül.

A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át. A szolgáltatás nyilvános internetre való kimutatása már nem szükséges. Létrehozhatja saját [privát kapcsolati szolgáltatását](private-link-service-overview.md) a virtuális hálózatban, és továbbíthatja az ügyfeleknek. Az Azure Private link használatával történő beállítás és felhasználás konzisztens az Azure Pásti, az ügyfél és a megosztott partneri szolgáltatások között.

> [!IMPORTANT]
> Az Azure Private link már általánosan elérhető. A privát végpont és a magánhálózati kapcsolat szolgáltatás (a standard Load Balancer mögötti szolgáltatás) általánosan elérhető. A különböző Azure-beli Pásti különböző időpontokban fog bejelentkezni az Azure Private-hivatkozásba. Tekintse meg a privát [hivatkozás elérhetőségét](availability.md) az Azure Pásti pontos állapotáról a privát hivatkozáson. Az ismert korlátozásokért lásd: [privát végpont](private-endpoint-overview.md#limitations) és [privát kapcsolat szolgáltatás](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Azure Private link Center Azure Portal" border="false":::

## <a name="key-benefits"></a>Főbb előnyök
Az Azure Private link a következő előnyöket biztosítja:  
- **Saját hozzáférésű szolgáltatások az Azure platformon**: virtuális hálózatának összekapcsolása az Azure-ban, nyilvános IP-cím nélkül a forráson vagy a célhelyen. A szolgáltatók saját virtuális hálózatban tehetik a szolgáltatásaikat, és a felhasználók a helyi virtuális hálózatban érhetik el a szolgáltatásokat. A privát kapcsolati platform a fogyasztó és a szolgáltatások közötti kapcsolatot fogja kezelni az Azure-beli gerinc hálózaton. 
 
- Helyszíni és egymással **összekapcsolt hálózatok**: az Azure-ban futó, privát végpontokat használó EXPRESSROUTE, VPN-alagutakra és a virtuális hálózatokra épülő, az Azure-ban futtatott hozzáférési szolgáltatások. A szolgáltatás eléréséhez nincs szükség a ExpressRoute Microsoft-partnerek konfigurálására vagy az Internet bejárására. A privát hivatkozás biztonságos módszert biztosít a számítási feladatok Azure-ba történő átirányításához.
 
- **Védelem az adatszivárgás ellen**: a rendszer a teljes szolgáltatás helyett egy privát végpontot rendel egy Pásti erőforrás egy példányához. A felhasználók csak az adott erőforráshoz tudnak csatlakozni. A szolgáltatás bármely más erőforrásához való hozzáférés le van tiltva. Ez a mechanizmus védelmet nyújt az adatszivárgási kockázatokkal szemben. 
 
- **Globális elérhetőség**: kapcsolódjon a más régiókban futó szolgáltatásokhoz. A fogyasztó virtuális hálózata az A régióba kerülhet, és a B régióban található privát hivatkozás mögött található szolgáltatásokhoz is csatlakozhat.  
 
- **Kiterjesztheti saját szolgáltatásait**: az Azure-beli felhasználók számára is lehetővé teszi, hogy a szolgáltatás magántulajdonban legyen. Ha a szolgáltatást egy szabványos Azure Load Balancer mögött helyezi el, akkor azt privát hivatkozásként is engedélyezheti. A fogyasztó ezután közvetlenül kapcsolódhat a szolgáltatáshoz a saját virtuális hálózatában található privát végpont használatával. A kapcsolatkérelmeket a jóváhagyási hívási folyamat használatával kezelheti. Az Azure Private link a különböző Azure Active Directory bérlők számára tartozó felhasználók és szolgáltatások számára is működik. 

> [!NOTE]
> Az Azure-beli privát kapcsolat az Azure Virtual Network-vel együtt a [Azure Availability Zonesra](../availability-zones/az-overview.md) terjed ki, ezért rugalmasak. Ha magas rendelkezésre állást szeretne biztosítani az Azure-erőforrás számára egy privát végpont használatával, győződjön meg arról, hogy az erőforrás rugalmas zónában van.

## <a name="availability"></a>Rendelkezésre állás 

A privát hivatkozást támogató Azure-szolgáltatásokkal kapcsolatos információkért lásd: az [Azure privát kapcsolatának elérhetősége](availability.md).

A legfrissebb értesítésekért keresse fel az [Azure Private link frissítései lapot](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Az Azure Private-hivatkozás Azure Monitorokkal való integrációt tartalmaz. Ez a kombináció lehetővé teszi a következőket:

 - Naplók archiválása egy Storage-fiókba.
 - Események továbbítása az Event hub-ba.
 - Azure Monitor-naplózás.

Azure Monitor a következő információkat érheti el: 
- **Privát végpont**: 
    - A magánhálózati végpont által feldolgozott adatértékek (be/ki)
 
- **Magánhálózati kapcsolati szolgáltatás**:
    - A privát kapcsolati szolgáltatás által feldolgozott adatértékek (be/ki)
    - NAT-port elérhetősége  
 
## <a name="pricing"></a>Díjszabás   
A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Gyakori kérdések  
Gyakori kérdések: [Azure Private link – gyakori kérdések](private-link-faq.md).
 
## <a name="limits"></a>Korlátok  
A korlátokat lásd: [Azure Private link Limits](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>szolgáltatói szerződés
SLA esetén lásd: [SLA az Azure Private linkhez](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: privát végpont létrehozása Azure Portal használatával](create-private-endpoint-portal.md)
- [Rövid útmutató: privát link szolgáltatás létrehozása a Azure Portal használatával](create-private-link-service-portal.md)
