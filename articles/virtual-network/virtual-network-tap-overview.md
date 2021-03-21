---
title: Azure Virtual Network – KOPPINTson az Áttekintés | Microsoft Docs
description: További tudnivalók a virtuális hálózatok KOPPINTÁSáról. A virtuális hálózat KOPPINTÁSsal a virtuális gép hálózati forgalmának részletes másolatát nyújtja, amely továbbítható egy csomag gyűjtője számára.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 6160dd09edc57f2f52306d4dad0dde413fff0616
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617182"
---
# <a name="virtual-network-tap"></a>Virtuális hálózat TAP-jai
> [!IMPORTANT]
> A Virtual Network TAP Preview jelenleg minden Azure-régióban megtartható. Az előfizetés-AZONOSÍTÓval e-mailben elküldheti az <azurevnettap@microsoft.com> adatait, és értesítjük Önt az előzetes verzióval kapcsolatos jövőbeli Frissítésekről is. Az ideiglenes verzióban olyan ügynök-alapú vagy NVA-megoldásokat használhat, amelyek az [Azure Marketplace-ajánlatokban](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners)elérhető [Packet Broker-partneri megoldásokkal](#virtual-network-tap-partner-solutions) biztosítják a TAP/Network láthatósági funkcióit.

Az Azure Virtual Network (terminál-hozzáférési pont) funkció lehetővé teszi a virtuális gép hálózati forgalmának folyamatos továbbítását egy hálózati csomag gyűjtője vagy analitikai eszköze számára. A gyűjtő vagy az elemzési eszközt egy [hálózati virtuális berendezési](https://azure.microsoft.com/solutions/network-appliances/) partner kapja meg. A virtuális hálózati KOPPINTÁSsal való együttműködéshez ellenőrzött partneri megoldások listáját a [partneri megoldások](#virtual-network-tap-partner-solutions)című témakörben találja.
Az alábbi képen látható, hogyan működik a virtuális hálózati KOPPINTÁS. A virtuális hálózatban üzembe helyezett virtuális gépekhez csatlakoztatott [hálózati adapterekre](virtual-network-network-interface.md) koppinthat. A cél egy virtuális hálózati IP-cím ugyanabban a virtuális hálózatban, mint a figyelt hálózati adapter vagy egy [társ virtuális](virtual-network-peering-overview.md) hálózat. A virtuális hálózatra KOPPINTó gyűjtő megoldás a magas rendelkezésre állás érdekében üzembe helyezhető egy Azure belső terheléselosztó mögött.
![A virtuális hálózat KOPPINTÁSának működése](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Előfeltételek

A virtuális hálózati KOPPINTÁS létrehozása előtt meg kell kapnia egy megerősítő e-mailt, amelyet Ön az előzetes verzióban regisztrált, és egy vagy több, [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) üzemi modellel és egy partneri megoldással létrehozott virtuális géppel rendelkezik, amely az ugyanazon az Azure-régióban található TAP-forgalmat összesíti. Ha nem rendelkezik partneri megoldással a virtuális hálózaton, tekintse meg a következő témakörben található [partneri megoldásokat](#virtual-network-tap-partner-solutions) :. Ugyanazt a virtuális hálózati KOPPINTÁS erőforrást használhatja az azonos vagy különböző előfizetésekben található több hálózati adapterről érkező forgalom összesítésére. Ha a figyelt hálózati adapterek különböző előfizetésekben találhatók, az előfizetéseket ugyanahhoz a Azure Active Directory bérlőhöz kell társítani. Emellett a figyelt hálózati adapterek és a TAP-forgalom összesítéséhez használt cél végpont is lehet ugyanabban a régióban lévő, egymástól független virtuális hálózatokban. Ha ezt a telepítési modellt használja, ellenőrizze, hogy a virtuális [hálózat](virtual-network-peering-overview.md) társítása engedélyezve van-e a virtuális hálózati koppintás konfigurálása előtt.

## <a name="permissions"></a>Engedélyek

A hálózati adapterekre vonatkozó KOPPINTó beállítások alkalmazásához használt fiókokat hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely a következő táblázat szükséges műveleteihez van rendelve:

| Művelet | Name |
|---|---|
| Microsoft. Network/virtualNetworkTaps/* | Virtuális hálózati KOPPINTó erőforrás létrehozásához, frissítéséhez, olvasásához és törléséhez szükséges. |
| Microsoft. Network/networkInterfaces/READ | A hálózati adapter azon erőforrásának olvasásához szükséges, amelyen a KOPPINTÁS konfigurálva lesz |
| Microsoft. Network/tapConfigurations/* | A TAP-konfiguráció hálózati adapteren való létrehozásához, frissítéséhez, olvasásához és törléséhez szükséges. |


## <a name="virtual-network-tap-partner-solutions"></a>Virtuális hálózati KOPPINTson partneri megoldások

### <a name="network-packet-brokers"></a>Hálózati csomagok közvetítői

- [GigaVUE Cloud Suite az Azure-hoz](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prizmák](https://www.nubeva.com/azurevtap)
- [Big Switch – Big monitoring-háló](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Biztonsági elemzés, hálózat/alkalmazás teljesítményének kezelése

- [Biztonság – ébren](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch-felhő](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis kiberbiztonsági](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [NetScout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Névtelen biztonság](https://nonamesecurity.com/)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® platform](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [hozhat létre virtuális hálózatokat](tutorial-tap-virtual-network-cli.md).
