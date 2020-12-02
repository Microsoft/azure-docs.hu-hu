---
title: Útválasztási beállítások az Azure-ban
description: Ismerje meg, hogy miként választhatja ki, hogy az Azure és az Internet közötti forgalom hogyan legyen átirányítva az útválasztási beállításokkal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 3f5653e4c184e88bce86026681dde1aee9e14d49
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491417"
---
# <a name="what-is-routing-preference-preview"></a>Mi az útválasztási preferencia (előzetes verzió)?

Az Azure-útválasztási beállítások lehetővé teszik, hogy kiválassza, hogyan irányítja át a forgalmat az Azure és az internet között. Megadhatja, hogy a forgalmat a Microsoft hálózatán vagy az INTERNETSZOLGÁLTATÓ hálózatán (a nyilvános interneten) keresztül irányítsa-e. Ezek a beállítások a következő néven is ismertek: *hideg burgonya-útválasztás* és *forró burgonya-útválasztás* . A kimenő adatforgalom díjszabása az Útválasztás kiválasztása alapján változhat. A nyilvános IP-cím létrehozásakor megadhatja az útválasztási beállítást. A nyilvános IP-cím olyan erőforrásokhoz társítható, mint a virtuális gép, a virtuálisgép-méretezési csoportok, az internetre irányuló terheléselosztó stb. Beállíthatja az Azure Storage-erőforrások útválasztási beállításait, például a blobokat, a fájlokat, a webes és az Azure-DataLake is. Alapértelmezés szerint a rendszer az összes Azure-szolgáltatáshoz a Microsoft globális hálózatán keresztül irányítja a forgalmat.

> [!IMPORTANT]
> Az útválasztási preferencia jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="routing-via-microsoft-global-network"></a>Útválasztás a Microsoft globális hálózatán keresztül

Ha a *Microsoft globális hálózatán* keresztül irányítja át a forgalmat, a forgalom a világ egyik legnagyobb hálózatán keresztül történik, amely több mint 160 000 km-re halad át, és több mint 165 Edge jelenléti ponttal (POP) rendelkezik. A hálózat jól kiépíthető több redundáns szál elérési úttal, így biztosítva a rendkívül magas megbízhatóságot és rendelkezésre állást. A Traffic Engineering egy olyan, a szoftver által definiált WAN-vezérlő által felügyelt, amely biztosítja a forgalom kis késleltetésű elérési útját, és a prémium szintű hálózati teljesítményt kínálja.

![Útválasztás a Microsoft globális hálózatán keresztül](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Bejövő forgalom:** A globális BGP-ra vonatkozó bejelentése biztosítja, hogy a bejövő forgalom a felhasználóhoz legközelebb eső Microsoft-hálózatba kerül. Ha például egy Szingapúrból álló felhasználó hozzáfér a Chicago-ban üzemeltetett Azure-erőforrásokhoz, akkor az USA-beli forgalom bekerül a Microsoft globális hálózatba a szingapúri Edge POP-ba, és a Microsoft hálózatban utazik a Chicago-ban üzemeltetett szolgáltatásba.

**Kimenő forgalom:** A kimenő forgalom ugyanazon elv alapján történik. A forgalom nagy részét a Microsoft globális hálózatára utazik, és a felhasználóhoz legközelebb kilép. Ha például az Azure Chicago-ról érkező forgalom a szingapúri felhasználóra van szánva, akkor a forgalom a Chicago-ról Szingapúrba utazik, és kilép a Microsoft hálózattal a szingapúri Edge POP-ban.

Mind a bejövő, mind a kimenő forgalom nagyban marad a Microsoft globális hálózatának utazási forgalmán. Ez más néven a *hideg burgonya-útválasztás*.


## <a name="routing-over-public-internet-isp-network"></a>Útválasztás nyilvános interneten keresztül (ISP-hálózat)

Az új útválasztási választás *internetes útválasztása* minimálisra csökkentheti a Microsoft globális hálózatának utazását, és a tranzit ISP-hálózatot használja a forgalom irányítására. Ez a költséghatékony útválasztási lehetőség a más felhőalapú szolgáltatókhoz hasonló hálózati teljesítményt nyújt.

![Útválasztás nyilvános interneten keresztül](media/routing-preference-overview/route-via-isp-network.png)

**Bejövő forgalom:** A bejövő forgalom elérési útja a *forró burgonya-útválasztást* használja, ami azt jelenti, hogy a forgalom belép az üzemeltetett szolgáltatási régióhoz legközelebb eső Microsoft-hálózatba. Ha például egy Szingapúrból álló felhasználó hozzáfér a Chicago-ban üzemeltetett Azure-erőforrásokhoz, akkor a forgalom a nyilvános interneten halad át, és belép a Microsoft globális hálózatba Chicagóban.

**Kimenő forgalom:** A kimenő forgalom ugyanazon elv alapján történik. A forgalom kilép a Microsoft hálózatból abban a régióban, ahol a szolgáltatás fut. Ha például az Azure Chicago szolgáltatásból érkező forgalom a szingapúri felhasználóra van szánva, akkor a forgalom kilép a Chicago-beli Microsoft-hálózattal, és a nyilvános interneten keresztül utazik a Szingapúrban lévő felhasználó felé.

## <a name="supported-services"></a>Támogatott szolgáltatások

A "Microsoft Global Network" nevű útválasztási beállításokat tartalmazó nyilvános IP-címek bármely Azure-szolgáltatáshoz társíthatók. Azonban a nyilvános IP-cím és az útválasztási beállítások megválasztása **Internet** a következő Azure-erőforrásokhoz is társítható:

* Virtuális gép
* Virtuálisgép-méretezési csoport
* Azure Kubernetes Service (AKS)
* Internetkapcsolattal rendelkező Load Balancer
* Application Gateway
* Azure Firewall

A tároláshoz az elsődleges végpontok mindig a **Microsoft globális hálózatot** használják. A másodlagos végpontokat az **internettel** engedélyezheti, ha a forgalom útválasztását választja. A támogatott tárolási szolgáltatások a következők:

* Blobok
* Fájlok
* Web
* Azure DataLake

## <a name="pricing"></a>Díjszabás
A két lehetőség közötti árkülönbség az internetes kimenő adatforgalom díjszabása szerint jelenik meg. Az Útválasztás a **Microsoft globális hálózati** adatátviteli díjszabása szerint megegyezik az aktuális internetes forgalom díjszabásával. A legfrissebb díjszabási információkért tekintse meg az [Azure sávszélesség-díjszabási oldalát](https://azure.microsoft.com/pricing/details/bandwidth/) . 

## <a name="availability"></a>Rendelkezésre állás

Az útválasztási beállítások támogatása a következő régiókban érhető el olyan szolgáltatások esetében, mint például a virtuális gép és az internetre irányuló terheléselosztó, amely nyilvános IP-címet használ az internetes forgalomhoz – Észak-Európa, Nyugat-Európa, Dél-Franciaország, Egyesült Királyság déli régiója, Nyugat-Svájc Középnyugat-Németország USA keleti régiója

A Storage-fiók útválasztási preferencia-támogatása a következő Azure-régiókban érhető el: az USA északi középső régiója, az USA nyugati középső régiója, az USA déli középső régiója, az USA keleti régiója, az USA nyugati régiója, Észak-Európa, Dél-Franciaország, Középnyugat-Németország, Nyugat-Svájc, déli Kelet-Ázsia, Kelet-Japán
## <a name="limitations"></a>Korlátozások

* Az útválasztási beállítás csak a nyilvános IP-cím szabványos SKU-jának megfelelően kompatibilis. A nyilvános IP-cím alapszintű SKU-jának használata nem támogatott.
* Az útválasztási beállítások jelenleg csak IPv4 nyilvános IP-címeket támogatnak. Az IPv6 nyilvános IP-címek nem támogatottak.
* A több hálózati adapterrel rendelkező virtuális gépek esetében csak egy típusú útválasztási beállítás lehet.


## <a name="next-steps"></a>További lépések

* [Útválasztási beállítások konfigurálása virtuális géphez a Azure PowerShell használatával](configure-routing-preference-virtual-machine-powershell.md)
* [Útválasztási beállítások konfigurálása virtuális géphez az Azure CLI használatával](configure-routing-preference-virtual-machine-cli.md)
