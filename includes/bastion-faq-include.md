---
title: fájl belefoglalása
description: fájl belefoglalása
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ca74b6cf63e3c81d830eca76eea6815548faa1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732576"
---
### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Szükség van nyilvános IP-címre a virtuális gépen a virtuális gép Azure Bastion?

Nem. Amikor virtuális géphez csatlakozik a Azure Bastion, nincs szükség nyilvános IP-címre az Azure-beli virtuális gépen, amelyhez csatlakozik. A Bastion szolgáltatás megnyitja az RDP/SSH-munkamenetet/-kapcsolatot a virtuális géphez a virtuális gép magánhálózati IP-címével, a virtuális hálózaton belül.

### <a name="is-ipv6-supported"></a>Támogatott az IPv6?

Az IPv6 jelenleg nem támogatott. Azure Bastion csak az IPv4-et támogatja.

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>Használhatok virtuális Azure Bastion Azure saját DNS zónával?

A virtuális Azure Bastion Azure saját DNS zónákkal való használata jelenleg nem támogatott. Mielőtt üzembe helyez Azure Bastion erőforrást, győződjön meg arról, hogy a gazdagép virtuális hálózata nincs privát DNS-zónához kapcsolva.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>RdP- vagy SSH-ügyfélre van szükségem?

Nem. Nincs szükség RDP- vagy SSH-ügyfélre az RDP/SSH eléréséhez az Azure-beli virtuális géphez a Azure Portal. A [Azure Portal](https://portal.azure.com) segítségével rdP/SSH-hozzáférést kap a virtuális géphez közvetlenül a böngészőben.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Az Azure-beli virtuális gépen futó ügynökre van szükségem?

Nem. Nem kell ügynököt vagy szoftvert telepítenie a böngészőben vagy az Azure-beli virtuális gépen. A Bastion szolgáltatás ügynök nélküli, és nem igényel további szoftvert az RDP/SSH számára.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Hány egyidejű RDP- és SSH-munkamenetet támogatnak Azure Bastion munkamenetek?

Az RDP és az SSH is használatalapú protokoll. A munkamenetek magas kihasználtsága miatt a megerősített gazdagép kevesebb munkamenetet fog támogatni. Az alábbi számok normál napi munkafolyamatokat feltételeznek.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Milyen szolgáltatásokat támogat egy RDP-munkamenet?

Jelenleg csak a szövegek másolása és beillesztése támogatott. Az olyan funkciók, mint a fájlmásoló, nem támogatottak. Az új funkciókkal kapcsolatos visszajelzéseit a Azure Bastion [oldalon küldheti el.](https://feedback.azure.com/forums/217313-networking?category_id=367303)

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Működik a Bastion-megerősített rendszer az AADJ virtuálisgép-bővítményhez csatlakozott virtuális gépekkel?

Ez a funkció nem működik az Azure AD-felhasználókat használó, AADJ vm-bővítményhez csatlakozott gépekkel. További információ: [Windows Azure virtuális gépek és Azure AD.](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements)

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Mely böngészők támogatottak?

A böngészőnek támogatnia kell a HTML 5-öt. Használja a Microsoft Edge böngészőt vagy a Google Chrome-ot Windows rendszeren. Apple Mac esetén használja a Google Chrome böngészőt. Microsoft Edge Chromium Windows és Mac rendszeren is támogatott.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Hol Azure Bastion ügyféladatokat?

Azure Bastion nem helyez át vagy tárol ügyféladatokat az üzembe helyezett régióból.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Szükségesek szerepkörök a virtuális gépek eléréséhez?

A kapcsolat létesítéhez a következő szerepkörök szükségesek:

* Olvasó szerepkör a virtuális gépen
* Olvasó szerepkör a NIC-ben a virtuális gép privát IP-címével
* Olvasó szerepkör az Azure Bastion-erőforrásban

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Mi a díjszabás?

További tájékoztatás a [díjszabási lapon](https://aka.ms/BastionHostPricing) olvasható.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Szükség Azure Bastion azure-RDS CAL virtuális gépeken rendszergazdai célokra?

Nem, a Windows Server rendszerű virtuális gépek Azure Bastion nem igényel [RDS CAL,](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) ha kizárólag felügyeleti célokra használják.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Mely billentyűzetkiosztások támogatottak a bastioni távoli munkamenet során?

Azure Bastion jelenleg az en-us-qwerty billentyűzetkiosztást támogatja a virtuális gépen belül.  A billentyűzetkiosztás egyéb területi beállítása már folyamatban van.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Támogatott a felhasználó által megadott útválasztás (UDR) Azure Bastion alhálózaton?

Nem. Az UDR nem támogatott egy Azure Bastion alhálózaton.

Olyan forgatókönyvek esetén, amelyekben Azure Bastion és Azure Firewall/Hálózati virtuális berendezés (NVA) is található ugyanazon a virtuális hálózaton, nem kell kényszerítenie az Azure Bastion-alhálózatról az Azure Firewall-re a forgalmat, mert az Azure Bastion és a virtuális gépek közötti kommunikáció privát. További információ: A mögöttes virtuális gépek elérése Azure Firewall [Bastion alkalmazással.](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Miért jelenik meg "A munkamenet lejárt" hibaüzenet a Bastion-munkamenet indítása előtt?

A munkamenetet csak a Azure Portal. Jelentkezzen be a Azure Portal, és kezdje újra a munkamenetet. Ha az URL-címet közvetlenül egy másik böngésző-munkamenetből vagy -lapról használja, ez a hiba várható. Segít biztosítani, hogy a munkamenet biztonságosabb legyen, és hogy a munkamenet csak a Azure Portal.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Hogyan az üzembe helyezési hibákat?

Tekintse át a [hibaüzeneteket,](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) és ha szükséges, a Azure Portal támogatási kérést. Az üzembe helyezési hibákat az [Azure-előfizetés korlátai, kvótái és megkötései okozhatják.](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) Pontosabban, az ügyfelek az előfizetésenként engedélyezett nyilvános IP-címek számára vonatkozó korlátot ütközhetnek, amely miatt a Azure Bastion sikertelen lesz.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Hogyan bele Azure Bastion a vészhelyreállítási tervbe?

Azure Bastion virtuális hálózatokon vagy virtuális társhálózaton belül van üzembe helyezni, és egy Azure-régióhoz van társítva. Az Ön feladata, hogy üzembe Azure Bastion vészhelyreállítási (DR) hely virtuális hálózatán. Azure-régió meghibásodása esetén hajtson végre feladatátvételi műveletet a virtuális gépeken a dr. régióba. Ezután a dr. Azure Bastion üzembe helyezett virtuális gépgazda használatával csatlakozzon az ott üzembe helyezett virtuális gépekhez.
