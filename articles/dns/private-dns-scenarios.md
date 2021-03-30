---
title: Privát zónák forgatókönyvei – Azure DNS
description: Ebben a cikkben megismerheti a Azure DNS Private Zones használatának gyakori forgatókönyveit.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76939343"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS privát zónák forgatókönyvei

Azure DNS Private Zones a névfeloldást a virtuális hálózaton belül, valamint a virtuális hálózatok között. Ebben a cikkben megvizsgáljuk azokat a gyakori forgatókönyveket, amelyeket a funkció használatával lehet megvalósítani.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Forgatókönyv: névfeloldás hatóköre egyetlen virtuális hálózatra
Ebben az esetben egy Azure-beli virtuális hálózattal rendelkezik, amely számos Azure-erőforrással rendelkezik, beleértve a virtuális gépeket is. Egy adott tartománynév (DNS-zóna) segítségével szeretné feloldani az erőforrásokat a virtuális hálózaton belül, és a névfeloldásnak magánjellegűnek kell lennie, és nem érhető el az internetről. Emellett a VNET belüli virtuális gépek esetében az Azure-ra is szüksége lesz a DNS-zónába való automatikus regisztráláshoz. 

Ez a forgatókönyv az alábbi ábrán látható. Az "A" nevű Virtual Network két virtuális gépet tartalmaz (TÁRSVISZONYBAN áll-VM1 és TÁRSVISZONYBAN áll-VM2). Ezek mindegyike saját IP-címmel van társítva. Miután létrehozta a contoso.com nevű privát zónát, és a virtuális hálózatot regisztrációs virtuális hálózatként kapcsolja össze, Azure DNS automatikusan két rekordot hoz létre a zónában ábrázolt módon. A TÁRSVISZONYBAN áll-VM1 által a VNETA-VM2.contoso.com feloldására irányuló DNS-lekérdezések egy DNS-választ kapnak, amely tartalmazza a TÁRSVISZONYBAN áll-VM2 magánhálózati IP-címét. Továbbá a TÁRSVISZONYBAN áll-VM2 által kiadott TÁRSVISZONYBAN áll-VM1 (10.0.0.1) magánhálózati IP-címéhez fordított DNS-lekérdezés (PTR) egy DNS-választ kap, amely a várt módon tartalmazza a TÁRSVISZONYBAN áll-VM1 nevét. 

![Egyetlen virtuális hálózat feloldása](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Forgatókönyv: névfeloldás a virtuális hálózatok között

Ez a forgatókönyv a leggyakoribb eset, amikor több virtuális hálózattal kell hozzárendelnie egy privát zónát. Ez a forgatókönyv olyan architektúrákat is képes kiszolgálni, mint a sugaras modell, ahol egy központi hub virtuális hálózat található, amelyhez több más küllős virtuális hálózat kapcsolódik. A központi hub virtuális hálózat regisztrálható virtuális hálózatként egy privát zónához, a küllős virtuális hálózatok pedig feloldási virtuális hálózatokként kapcsolhatók össze. 

Az alábbi ábrán a forgatókönyv egy egyszerű verziója látható, ahol csak két virtuális hálózat létezik – A és A B. A a regisztrációs virtuális hálózatként van megjelölve, a B pedig feloldási virtuális hálózatként van megjelölve. A cél az, hogy mindkét virtuális hálózat közös zóna-contoso.com osszon meg. A zóna létrehozásakor és a megoldási és regisztrációs virtuális hálózatok a zónához való csatolásakor az Azure automatikusan regisztrálja a virtuális gépek (TÁRSVISZONYBAN áll-VM1 és TÁRSVISZONYBAN áll-VM2) DNS-rekordjait a virtuális hálózatról. A DNS-rekordokat manuálisan is hozzáadhatja a zónához a (B) feloldási virtuális hálózatban lévő virtuális gépek zónájában. Ezzel a beállítással a következő viselkedést fogja figyelembe venni a továbbítási és a fordított DNS-lekérdezések esetében:
* A b-VM1 DNS-lekérdezése a (VNETA-VM1.contoso.com) B megoldási virtuális hálózatban egy DNS-választ fog kapni, amely a TÁRSVISZONYBAN áll-VM1 magánhálózati IP-címét tartalmazza.
* Egy fordított DNS-(PTR-) lekérdezés a b-VM2-ben a "B" megoldási virtuális hálózatban a 10.1.0.1 esetében egy DNS-választ kap, amely tartalmazza a teljes tartománynevet VNETB-VM1.contoso.com.  
* A b-VM3 fordított DNS-(PTR-) lekérdezése a 10.0.0.1-ben a "B" megoldási virtuális hálózatban NXDOMAIN fog kapni. Ennek az az oka, hogy a fordított DNS-lekérdezések csak ugyanarra a virtuális hálózatra terjednek ki. 


![Több virtuális hálózati megoldás](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Forgatókönyv: Split-Horizon funkció

Ebben a forgatókönyvben olyan használati esettel rendelkezik, amelyben eltérő DNS-feloldási viselkedést kíván megvalósítani attól függően, hogy az ügyfél hol ül (az Azure-ban vagy az interneten), ugyanarra a DNS-zónára. Előfordulhat például, hogy az alkalmazás olyan magán-és nyilvános verziója van, amely különböző funkciókkal vagy viselkedéssel rendelkezik, de mindkét verzióhoz ugyanazt a tartománynevet kívánja használni. Ez a forgatókönyv egy nyilvános DNS-zóna és egy azonos nevű privát zóna létrehozásával valósítható meg Azure DNS.

A következő ábra ezt a forgatókönyvet ábrázolja. Rendelkezik egy olyan virtuális hálózattal, amely két virtuális géppel (TÁRSVISZONYBAN áll-VM1 és TÁRSVISZONYBAN áll-VM2) rendelkezik, és amelyek magánhálózati IP-címekkel és nyilvános IP-címekkel rendelkeznek. Hozzon létre egy contoso.com nevű nyilvános DNS-zónát, és regisztrálja a virtuális gépek nyilvános IP-címeit a zónán belüli DNS-rekordokként. Emellett létrehoz egy saját DNS zónát is, amely contoso.com néven is megadhatja a regisztrációs virtuális hálózatot. Az Azure automatikusan regisztrálja a virtuális gépeket a privát zónában lévő saját IP-címekre mutató rekordként.

Most, amikor egy internetes ügyfél DNS-lekérdezést bocsát ki a VNETA-VM1.contoso.com kereséséhez, az Azure a nyilvános zónából fogja visszaadni a nyilvános IP-rekordot. Ha ugyanezt a DNS-lekérdezést egy másik virtuális gépről (például: TÁRSVISZONYBAN áll-VM2) is kiállítják, akkor az Azure a magánhálózati IP-rekordot adja vissza a privát zónából. 

![Feldarabolt Brian-feloldás](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Következő lépések
További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md).

Megtudhatja, hogyan [hozhat létre saját DNS-zónát](./private-dns-getstarted-powershell.md) a Azure DNSban.

A DNS-zónák és-rekordok megismerése látogasson el ide: [DNS-zónák és-rekordok áttekintése](dns-zones-records.md).

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.

