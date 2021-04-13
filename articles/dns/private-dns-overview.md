---
title: Mi az Azure Private DNS?
description: Ebből a cikkből megtudhatja, hogyan tekintheti át a Microsoft Azure privát DNS-üzemeltetési szolgáltatásának áttekintését.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 560a88c973d71b3e2c6533e05e4f374f9a5bcd8f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311481"
---
# <a name="what-is-azure-private-dns"></a>Mi az Azure Private DNS?

A tartománynévrendszer vagy a DNS felelős a szolgáltatás nevének egy IP-címhez való lefordításához (vagy feloldásához).  Azure DNS egy szolgáltatói szolgáltatás a tartományok számára, és a Microsoft Azure infrastruktúra használatával biztosítja a névfeloldást. Azure DNS nem csak az internetre irányuló DNS-tartományokat támogatja, hanem támogatja a magánhálózati DNS-zónákat is.

Az Azure saját DNS megbízható és biztonságos DNS-szolgáltatást biztosít a virtuális hálózat számára. Az Azure saját DNS egyéni DNS-megoldás konfigurálása nélkül kezeli és feloldja a tartományneveket a virtuális hálózaton. Privát DNS-zónák használatával az üzembe helyezés során az Azure által biztosított nevek helyett saját egyéni tartománynevet is használhat. Ha egyéni tartománynevet használ, a virtuális hálózati architektúrát a szervezet igényeinek megfelelően testre szabhatja. A virtuális hálózatokon és a csatlakoztatott virtuális hálózatokon belüli virtuális gépek (VM-EK) elnevezési feloldását teszi lehetővé. Emellett a zónák nevét is konfigurálhatja egy osztott horizontú nézettel, amely lehetővé teszi, hogy a privát és a nyilvános DNS-zónák megosszák a nevet.

Ha egy magánhálózati DNS-zóna rekordjait szeretné feloldani a virtuális hálózatról, a virtuális hálózatot a zónával kell összekapcsolni. A csatolt virtuális hálózatok teljes hozzáféréssel rendelkeznek, és a magánhálózati zónában közzétett összes DNS-rekordot feloldják. Egy virtuális hálózati kapcsolaton is engedélyezheti az automatikus regisztrációt. Ha engedélyezi az automatikus regisztrációt egy virtuális hálózati kapcsolaton, a virtuális hálózatban lévő virtuális gépek DNS-rekordjait a rendszer regisztrálja a privát zónában. Ha engedélyezve van az automatikus regisztráció, Azure DNS frissíti a zóna rekordját, amikor létrejön egy virtuális gép, megváltoztatja az IP-címét, vagy törölve lesz.

![A DNS áttekintése](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Ajánlott eljárásként ne használjon *. local* tartományt a saját DNS-zónájához. Nem minden operációs rendszer támogatja ezt.

## <a name="benefits"></a>Előnyök

Az Azure saját DNS a következő előnyöket biztosítja:

* **Eltávolítja az egyéni DNS-megoldások szükségességét**. Korábban számos ügyfél hozott létre egyéni DNS-megoldásokat a virtuális hálózat DNS-zónáinak kezeléséhez. Mostantól a natív Azure-infrastruktúrával kezelheti a DNS-zónákat, ami eltávolítja az egyéni DNS-megoldások létrehozásának és kezelésének terheit.

* **Használja az összes általános DNS-rekord típust**. Azure DNS támogatja A, AAAA, CNAME, MX, PTR, SOA, SRV és TXT rekordokat.

* **Automatikus állomásnév-nyilvántartás kezelése**. Az egyéni DNS-rekordok üzemeltetése mellett az Azure automatikusan a megadott virtuális hálózatokban lévő virtuális gépek gazdagép-rekordjait is fenntartja. Ebben az esetben optimalizálhatja a használt tartományneveket anélkül, hogy egyéni DNS-megoldásokat kellene létrehoznia, vagy alkalmazásokat kellene módosítania.

* **Állomásnév feloldása a virtuális hálózatok között**. Az Azure által biztosított állomásnevektól eltérően a magánhálózati DNS-zónák megoszthatók a virtuális hálózatok között. Ez a funkció egyszerűsíti a hálózatok közötti és a szolgáltatás-felderítési forgatókönyveket, például a virtuális hálózati társítást.

* **Ismerős eszközök és felhasználói élmény**. A tanulási görbe csökkentése érdekében a szolgáltatás jól bevált Azure DNS-eszközöket (Azure Portal, Azure PowerShell, Azure CLI, Azure Resource Manager sablonokat és a REST API) használ.

* **Felosztási horizontú DNS-támogatás**. A Azure DNS használatával olyan zónákat hozhat létre, amelyek ugyanazzal a névvel rendelkeznek, mint a virtuális hálózaton belüli és a nyilvános internetről érkező különböző válaszokra. A megosztott horizontú DNS tipikus forgatókönyve, hogy a szolgáltatás dedikált verzióját adja meg a virtuális hálózaton belüli használatra.

* **Minden Azure-régióban elérhető**. Az Azure DNS Private Zones szolgáltatás az Azure nyilvános felhőben található összes Azure-régióban elérhető.

## <a name="capabilities"></a>Képességek

Azure DNS a következő képességeket biztosítja:

* **Virtuális gépek automatikus regisztrálása egy olyan virtuális hálózatból, amely egy privát zónához van csatolva, és engedélyezve van az automatikus regisztráció**. A virtuális gépek a magánhálózati IP-címekre mutató rekordokként regisztrálják magukat a privát zónában. Ha az automatikus regisztrációval rendelkező virtuális hálózati kapcsolaton belül egy virtuális gép törölve lesz, Azure DNS automatikusan eltávolítja a megfelelő DNS-rekordot a társított privát zónából.

* **A továbbítási DNS-feloldás a privát zónához csatolt virtuális hálózatok között támogatott**. A virtuális hálózatok közötti DNS-feloldáshoz nincs olyan explicit függőség, amely a virtuális hálózatokat egymáshoz társítja. Előfordulhat azonban, hogy más forgatókönyvek (például HTTP-forgalom) esetében is szeretne egyenrangú virtuális hálózatokat használni.

* **A fordított DNS-keresés a virtuális hálózat hatókörén belül támogatott**. A privát zónákhoz rendelt magánhálózati IP-címek fordított DNS-címkeresés olyan teljes tartománynevet ad vissza, amely tartalmazza a gazdagép/rekord nevét és a zóna nevét utótagként.

## <a name="other-considerations"></a>További szempontok

A Azure DNS a következő korlátozásokkal rendelkezik:

* Egy adott virtuális hálózat csak egy privát zónához kapcsolható, ha engedélyezve van a virtuális gépek DNS-rekordjainak automatikus regisztrálása. Azonban több virtuális hálózatot is csatolhat egyetlen DNS-zónához.
* A fordított DNS csak a társított virtuális hálózat magánhálózati IP-címére működik
* A társított virtuális hálózat magánhálózati IP-címei fordított DNS-je a `internal.cloudapp.net` virtuális gép alapértelmezett utótagja lesz. Az automatikus regisztrációt engedélyező privát zónához kapcsolt virtuális hálózatok esetében a magánhálózati IP-címek fordított DNS-je két teljes tartománynevet ad vissza: egyet az utótaggal `internal.cloudapp.net` , és egy másikat a privát zóna utótagja alapján.
* A feltételes továbbítás jelenleg nem támogatott natív módon. Az Azure és a helyszíni hálózatok közötti megoldás engedélyezéséhez lásd [a virtuális gépek és a szerepkör-példányok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)névfeloldását ismertető témakört.
 
## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért tekintse meg a [Azure DNS díjszabását](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan hozhat létre saját zónát Azure DNSban [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* További információ [a privát zónák](./private-dns-scenarios.md) Azure DNS-beli privát zónákkal való használatával kapcsolatban.

* A Azure DNS privát zónákkal kapcsolatos gyakori kérdések és válaszok: [saját DNS GYIK](./dns-faq-private.md).

* A DNS-zónák és-rekordok megismerése a [DNS-zónák és-rekordok áttekintésével](dns-zones-records.md).

* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
