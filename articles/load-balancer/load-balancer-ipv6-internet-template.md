---
title: Internetkapcsolattal rendelkező Load-Balancer üzembe helyezése IPv6-Azure-sablonnal
titleSuffix: Azure Load Balancer
description: Ismerje meg, hogyan telepíthet IPv6-támogatást Azure Load Balancer és elosztott terhelésű virtuális gépekhez egy Azure-sablon használatával.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, Dual stack, nyilvános IP-cím, natív IPv6, mobil, IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 415c95a441ac0cc6ed2dbf2d6a37f57d7a9e7341
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90562519"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Internetkapcsolattal rendelkező terheléselosztó megoldás üzembe helyezése IPv6-sablonnal sablon használatával

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Sablon](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>Ez a cikk egy bevezető IPv6-szolgáltatást ismertet, amely lehetővé teszi, hogy az alapszintű Load Balancer IPv4-és IPv6-kapcsolatot is biztosítson. A teljes körű IPv6-kapcsolat mostantól elérhető az [IPv6 for Azure virtuális hálózatok](../virtual-network/ipv6-overview.md) , amely integrálja az IPv6-kapcsolatot a virtuális hálózatokkal, és olyan kulcsfontosságú funkciókat tartalmaz, mint például az IPv6 hálózati biztonsági csoportra vonatkozó szabályok, az IPv6 felhasználói útválasztás, az IPv6 alapszintű és a standard szintű terheléselosztás, valamint egyebek.  Az IPv6 az Azure virtuális hálózatok az Azure-beli IPv6-alkalmazások esetében ajánlott szabvány. Lásd: [IPv6 az Azure VNET PowerShell üzembe helyezéséhez](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A terheléselosztó a felhőszolgáltatások vagy virtuális gépek kifogástalan állapotú szolgáltatási példányai között osztja meg a bejövő forgalmat egy terheléselosztói készletben, és ezáltal biztosítja a magas rendelkezésre állást. Az Azure Load Balancer a szolgáltatásokat több portra vagy több IP-címre, illetve portokra és IP-címekre egyaránt továbbíthatja.

## <a name="example-deployment-scenario"></a>Példa telepítési forgatókönyv

A következő ábra azt szemlélteti, hogy a cikkben ismertetett példa sablonnal milyen terheléselosztási megoldás van üzembe helyezve.

![Ábrán látható példa az ebben a cikkben használt forgatókönyvre, beleértve az interneten keresztül Azure Load Balancer csatlakoztatott munkaállomás-ügyfelet, amely két virtuális géphez csatlakozik.](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Ebben a forgatókönyvben a következő Azure-erőforrásokat fogja létrehozni:

* virtuális hálózati adapter minden virtuális GÉPHEZ, amelyhez IPv4-és IPv6-címek vannak hozzárendelve
* egy internetre irányuló Load Balancer IPv4-és IPv6-alapú nyilvános IP-címmel
* két terheléselosztási szabály a nyilvános VIP-címek a privát végpontokra való leképezéséhez
* a két virtuális gépet tartalmazó rendelkezésre állási csoport
* két virtuális gép (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>A sablon üzembe helyezése a Azure Portal használatával

Ez a cikk az [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) galériájában közzétett sablonra hivatkozik. A sablont letöltheti a katalógusból, vagy közvetlenül a katalógusból is elindíthatja az üzembe helyezést az Azure-ban. Ez a cikk azt feltételezi, hogy letöltötte a sablont a helyi számítógépre.

1. Nyissa meg a Azure Portalt, és jelentkezzen be egy olyan fiókkal, amely jogosult virtuális gépek és hálózati erőforrások létrehozására egy Azure-előfizetésen belül. Emellett, ha nem használ meglévő erőforrásokat, a fióknak engedéllyel kell rendelkeznie az erőforráscsoport és a Storage-fiók létrehozásához.
2. Kattintson az "+ új" gombra a menüből, majd írja be a "sablon" kifejezést a keresőmezőbe. A keresési eredmények közül válassza a "Template deployment" lehetőséget.

    ![Képernyőfelvétel: a Azure Portal új és Template deployment kiválasztva.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Az összes panelen kattintson a "Template deployment" elemre.

    ![A képernyőfelvételen a piactéren Template deployment látható.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Kattintson a Létrehozás gombra.

    ![A képernyőfelvételen a piactér Template deployment leírását láthatja.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Kattintson a "sablon szerkesztése" elemre. Törölje a meglévő tartalmakat, és másolja/illessze be a sablonfájl teljes tartalmát (a kezdő és a befejező {} érték belefoglalásához), majd kattintson a Save (Mentés) gombra.

    > [!NOTE]
    > Ha a Microsoft Internet Explorert használja, a beillesztéskor egy párbeszédpanel jelenik meg, amely arra kéri, hogy engedélyezze a hozzáférést a Windows vágólaphoz. Kattintson a hozzáférés engedélyezése lehetőségre.

    ![A képernyőképen egy egyéni központi telepítés Firest lépése látható, amely a sablon szerkesztése.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Kattintson a paraméterek szerkesztése elemre. A parameters (paraméterek) panelen adja meg az értékeket a sablon paramétereinek szakaszban, majd kattintson a Save (Mentés) gombra a paraméterek panel bezárásához. Az egyéni üzembe helyezés panelen válassza ki az előfizetést, egy meglévő erőforráscsoportot, vagy hozzon létre egyet. Ha létrehoz egy erőforráscsoportot, válassza ki az erőforráscsoport helyét. Ezután kattintson a **jogi feltételek** elemre, majd a jogi feltételek **megvásárlása** elemre. Az Azure megkezdi az erőforrások üzembe helyezését. Az összes erőforrás üzembe helyezése több percet vesz igénybe.

    ![Képernyőfelvétel: az egyéni üzembe helyezés lépései, a sablon paramétereinek megadásával kezdődően.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    A paraméterekkel kapcsolatos további információkért tekintse meg a cikk későbbi, a [sablon paramétereinek és változóinak](#template-parameters-and-variables) című szakaszát.

7. A sablon által létrehozott erőforrások megtekintéséhez kattintson a Tallózás gombra, görgessen le a listára, amíg meg nem jelenik az "erőforráscsoportok", majd kattintson rá.

    ![A képernyőképen a kiválasztott tallózási és erőforráscsoportok Azure Portal látható.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Az erőforráscsoportok panelen kattintson a 6. lépésben megadott erőforráscsoport nevére. Ekkor megjelenik az összes telepített erőforrás listája. Ha minden rendben volt, azt az "utolsó üzembe helyezés" szakasz "sikeres" értékének kell megjelennie. Ha nem, győződjön meg arról, hogy a használt fiók rendelkezik a szükséges erőforrások létrehozásához szükséges engedélyekkel.

    ![A képernyőképen az erőforráscsoport utolsó üzemelő példányának állapota látható, ebben a példában a sikeres.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Ha a 6. lépés befejezése után azonnal böngészhet az erőforráscsoportok között, a "legutóbbi üzembe helyezés" a "telepítés" állapotot jeleníti meg, miközben az erőforrások üzembe helyezése történik.

9. Kattintson a "myIPv6PublicIP" elemre az erőforrások listájában. Láthatja, hogy az IP-cím alatt IPv6-címmel rendelkezik, és a DNS-neve a 6. lépésben a dnsNameforIPv6LbIP paraméterhez megadott érték. Ez az erőforrás a nyilvános IPv6-cím és az internetes ügyfelek számára elérhető állomásnév.

    ![A képernyőképen az IPv6 nyilvános címe látható.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A sablon sikeres üzembe helyezése után a következő feladatok végrehajtásával ellenőrizheti a kapcsolatot:

1. Jelentkezzen be a Azure Portalba, és kapcsolódjon a sablon telepítése által létrehozott összes virtuális géphez. Ha Windows Server rendszerű virtuális gépet telepített, futtassa az ipconfig/all parancsot egy parancssorból. Láthatja, hogy a virtuális gépek IPv4-és IPv6-címekkel is rendelkeznek. Ha Linux rendszerű virtuális gépeket telepített, akkor konfigurálnia kell a Linux operációs rendszert, hogy fogadja a dinamikus IPv6-címeket a Linux-disztribúcióhoz megadott utasítások alapján.
2. Egy IPv6-alapú internetkapcsolattal rendelkező ügyfélről kezdeményezzen kapcsolatot a terheléselosztó nyilvános IPv6-címeként. Annak ellenőrzéséhez, hogy a terheléselosztó egyensúlyba kerüljön-e a két virtuális gép között, telepíthet egy webkiszolgálót, például a Microsoft Internet Information Services (IIS) szolgáltatást mindegyik virtuális gépre. Az egyes kiszolgálók alapértelmezett weblapjai a "Server0" vagy a "Kiszolgáló1" szöveget is tartalmazhatják az egyedi azonosításhoz. Ezután nyisson meg egy Internet-böngészőt egy IPv6-alapú internetkapcsolattal rendelkező ügyfélen, és keresse meg a terheléselosztó dnsNameforIPv6LbIP paraméteréhez megadott állomásnevet, hogy erősítse meg az egyes virtuális gépek végpontok közötti IPv6-kapcsolatát. Ha csak egyetlen kiszolgálóról látja a weblapot, előfordulhat, hogy törölnie kell a böngésző gyorsítótárát. Nyisson meg több privát böngészési munkamenetet. Az egyes kiszolgálók válaszait kell megjelennie.
3. Egy IPv4-alapú internethez csatlakozó ügyfélről kezdeményezzen kapcsolatot a terheléselosztó nyilvános IPv4-címeként. Annak megerősítéséhez, hogy a terheléselosztó terheléselosztást végez a két virtuális gép között, tesztelheti az IIS-t a 2. lépésben részletezett módon.
4. Az egyes virtuális gépekről kezdeményezzen kimenő kapcsolatot egy IPv6-vagy IPv4-kapcsolattal rendelkező Internet-eszközzel. A célként megadott eszköz által látott forrás IP-cím mindkét esetben a terheléselosztó nyilvános IPv4-vagy IPv6-címe.

> [!NOTE]
> Az ICMP az IPv4 és az IPv6 esetében is le van tiltva az Azure-hálózaton. Ennek eredményeképpen az olyan ICMP-eszközök, mint a pingelés, mindig sikertelenek lesznek. A kapcsolat teszteléséhez használjon olyan TCP-alternatívát, mint a TCPing vagy a PowerShell Test-NetConnection parancsmag. Vegye figyelembe, hogy a diagramon megjelenő IP-címek például az esetlegesen megjelenő értékek. Mivel az IPv6-címek hozzárendelése dinamikusan történik, a fogadott címek eltérőek lehetnek, és régiónként változhatnak. Az is gyakori, hogy a terheléselosztó nyilvános IPv6-címéhez a háttérbeli készletben lévő magánhálózati IPv6-címektől eltérő előtagot kell kezdeni.

## <a name="template-parameters-and-variables"></a>Sablon paraméterei és változói

Egy Azure Resource Manager sablon több változót és paramétert tartalmaz, amelyeket testreszabhat igényei szerint. A változók olyan rögzített értékekhez használatosak, amelyeket nem kíván módosítani a felhasználó. A paraméterek olyan értékekhez használatosak, amelyeket a felhasználónak meg kell adnia a sablon telepítésekor. A példa sablon a jelen cikkben ismertetett forgatókönyvhöz van konfigurálva. Ezt testreszabhatja a környezet igényei szerint.

A cikkben használt példa sablon a következő változókat és paramétereket tartalmazza:

| Paraméter/változó | Jegyzetek |
| --- | --- |
| adminUsername |Adja meg a virtuális gépekre való bejelentkezéshez használt rendszergazdai fiók nevét. |
| adminPassword |A alkalmazásban a virtuális gépekre való bejelentkezéshez használt rendszergazdai fiók jelszavának megadása. |
| dnsNameforIPv4LbIP |Adja meg a terheléselosztó nyilvános neveként hozzárendelni kívánt DNS-állomásnév nevét. Ez a név a terheléselosztó nyilvános IPv4-címeként lett feloldva. A névnek kisbetűnek kell lennie, és meg kell egyeznie a reguláris kifejezéssel: ^ [a-z] [a-Z0-9-] {1,61} [a-Z0-9] $. |
| dnsNameforIPv6LbIP |Adja meg a terheléselosztó nyilvános neveként hozzárendelni kívánt DNS-állomásnév nevét. Ez a név a terheléselosztó nyilvános IPv6-címeként van feloldva. A névnek kisbetűnek kell lennie, és meg kell egyeznie a reguláris kifejezéssel: ^ [a-z] [a-Z0-9-] {1,61} [a-Z0-9] $. Ez az IPv4-címként megegyező név lehet. Amikor egy ügyfél DNS-lekérdezést küld az Azure-nak, az a és az AAAA rekordot is visszaadja, ha a név meg van osztva. |
| vmNamePrefix |Adja meg a virtuális gép nevének előtagját. A sablon hozzáfűz egy számot (0, 1 stb.) a virtuális gépek létrehozásakor a névhez. |
| nicNamePrefix |Adja meg a hálózati adapter nevének előtagját. A sablon hozzáfűz egy számot (0, 1 stb.) a nevet a hálózati adapterek létrehozásakor. |
| storageAccountName |Adja meg egy meglévő Storage-fiók nevét, vagy adja meg a sablon által létrehozandó új nevet. |
| availabilitySetName |Adja meg a virtuális gépekhez használni kívánt rendelkezésre állási csoport nevét. |
| addressPrefix |A Virtual Network címtartomány definiálásához használt címzési előtag |
| subnetName |A VNet létrehozott alhálózat neve |
| subnetPrefix |Az alhálózat Címtartomány-tartományának definiálásához használt címzési előtag |
| vnetName |Adja meg a virtuális gépek által használt VNet nevét. |
| ipv4PrivateIPAddressType |A magánhálózati IP-címhez használt kiosztási módszer (statikus vagy dinamikus) |
| ipv6PrivateIPAddressType |A magánhálózati IP-címhez (dinamikus) használt kiosztási módszer. Az IPv6 csak a dinamikus foglalást támogatja. |
| numberOfInstances |A sablon által üzembe helyezett elosztott terhelésű példányok száma |
| ipv4PublicIPAddressName |Adja meg a terheléselosztó nyilvános IPv4-címeként való kommunikációhoz használni kívánt DNS-nevet. |
| ipv4PublicIPAddressType |A nyilvános IP-címhez használt kiosztási módszer (statikus vagy dinamikus) |
| Ipv6PublicIPAddressName |Adja meg a terheléselosztó nyilvános IPv6-címeként való kommunikációhoz használni kívánt DNS-nevet. |
| ipv6PublicIPAddressType |A nyilvános IP-címhez (dinamikus) használt kiosztási módszer. Az IPv6 csak a dinamikus foglalást támogatja. |
| lbName |Adja meg a terheléselosztó nevét. Ez a név jelenik meg a portálon, vagy a parancssori felülettel vagy PowerShell-paranccsal való hivatkozáskor használatos. |

A sablon többi változója olyan származtatott értékeket tartalmaz, amelyek akkor vannak hozzárendelve, amikor az Azure létrehozza az erőforrásokat. Ne módosítsa ezeket a változókat.

## <a name="next-steps"></a>Következő lépések

A sablonban lévő terheléselosztó JSON-szintaxisához és tulajdonságaihoz lásd: [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
