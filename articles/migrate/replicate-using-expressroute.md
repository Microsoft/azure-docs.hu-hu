---
title: Adatreplikálás ExpressRoute keresztül Azure Migrate kiszolgáló áttelepítése
description: Az Azure ExpressRoute használata replikációhoz Azure Migrate kiszolgáló áttelepítésével
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: b52d61c2828ddf5c04ab943d73964d236c9017c1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098842"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Adatreplikálás ExpressRoute keresztül a Azure Migrate: kiszolgáló áttelepítése

Ebből a cikkből megtudhatja, hogyan konfigurálhatja [Azure Migrate: a kiszolgáló áttelepítését](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) , hogy ExpressRoute áramkörön keresztül replikálja az információkat a kiszolgálók Azure-ba való áttelepítése során.

## <a name="understand-azure-expressroute-circuits"></a>Az Azure ExpressRoute-áramkörök ismertetése
A ExpressRoute (ER) áramkör csatlakoztatja a helyszíni infrastruktúrát a Microsofthoz egy kapcsolati szolgáltatón keresztül. A ExpressRoute-áramköröket úgy lehet konfigurálni, hogy privát, Microsoft-társas vagy mindkettőt használjanak. Tekintse át a [ExpressRoute-áramkörökről](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) szóló cikket, és ismerkedjen meg a ExpressRoute-mel elérhető különböző társ-beállítási lehetőségekkel.

A Azure Migrate kiszolgáló áttelepítési eszköze segíti a helyszíni kiszolgálók és kiszolgálók áttelepítését más felhőből az Azure Virtual Machines szolgáltatásba. Az eszköz úgy működik, hogy egy folyamatos replikálási adatfolyamot állít be, amely az Azure-előfizetésben lévő felügyelt lemezekre áttelepíteni kívánt kiszolgálók adatait replikálja. Amikor készen áll a kiszolgálók áttelepítésére, a rendszer az Azure-ban replikált, a kiszolgálók áttelepítésére szolgáló adatfájlokat használja.

A helyszíni kiszolgálókról replikált adatok konfigurálhatók úgy, hogy az interneten keresztül (biztonságos titkosított kapcsolaton keresztül) vagy egy ExpressRoute-kapcsolaton keresztül is elküldhetők legyenek az Azure-előfizetésre. Ha nagy számú kiszolgálót szeretne áttelepíteni, a ExpressRoute használatával történő replikáláshoz a ExpressRoute-áramkörhöz elérhető kiépített sávszélesség használatával hatékonyabban áttelepítheti a kiszolgálókat.

Ebből a cikkből megtudhatja, hogyan
> [!div class="checklist"]
>
> * Az adatreplikálás ExpressRoute-áramkör használatával privát társas kapcsolattal.
> * Adatreplikálás ExpressRoute-áramkör használatával Microsoft-partneri kapcsolattal.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Adatreplikálás ExpressRoute-áramkör használatával privát kapcsolattal

> [!NOTE]
> A privát társítási áramkörön keresztüli replikálás jelenleg csak a [VMWare virtuális gépek ügynök nélküli migrálása](./tutorial-migrate-vmware.md)esetén támogatott az Azure-ban. Az egyéb [replikációs módszerekhez](./migrate-services-overview.md#azure-migrate-server-migration-tool) tartozó privát végpontok támogatása hamarosan elérhető lesz.

A VMware virtuális gépeknek az Azure-ba történő áttelepítésének ügynök nélküli módszerével a Azure Migrate készülék először feltölti a replikálási adatait egy Storage-fiókba (gyorsítótár Storage-fiókba) az előfizetésében. Ezután a rendszer áthelyezi a gyorsítótárból a gyorsítótárazott adatokból a replika által felügyelt lemezeket az előfizetésben a Azure Migrate szolgáltatás. Ha privát társítási áramkört szeretne használni a replikáláshoz, hozzon létre és csatoljon egy privát végpontot a cache Storage-fiók használatához. A privát végpontok egy vagy több magánhálózati IP-címet használnak a virtuális hálózatról (VNet), ami gyakorlatilag a Storage-fiókot az Azure-VNet hozza. A privát végpont lehetővé teszi, hogy az Azure Migrate berendezés a ExpressRoute privát társításával kapcsolódjon a gyorsítótár-fiókhoz, és közvetlenül a magánhálózati IP-címen vigye át az adatátvitelt. <br/>  

![Replikációs folyamat](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - A replikációs adatokon kívül a Azure Migrate berendezés a vezérlési sík tevékenységeivel kommunikál a Azure Migrate szolgáltatással, beleértve a replikációt. A Azure Migrate berendezés és a Azure Migrate szolgáltatás közötti sík kommunikáció vezérlése továbbra is az interneten keresztül történik Azure Migrate szolgáltatás nyilvános végpontján.
> - A Storage-fiók magánhálózati végpontjának elérhetőnek kell lennie azon a hálózaton, amelyen a Azure Migrate berendezés telepítve van.
> - A DNS-t úgy kell konfigurálni, hogy feloldja a DNS-lekérdezéseket a cache Storage-fiók blob Service-végpontja számára a Azure Migrate készülék által a gyorsítótár-tároló fiókhoz csatolt privát végpont magánhálózati IP-címére.
> - A cache Storage-fióknak elérhetőnek kell lennie a nyilvános végpontján. (A Azure Migrate szolgáltatás a gyorsítótár Storage-fiók nyilvános végpontját használja az adatoknak a Storage-fiókból a replika felügyelt lemezekre való áthelyezéséhez.) 


### <a name="1-pre-requisites"></a>1. előfeltételek

A privát végpontot létrehozó Azure-felhasználónak a következő engedélyekkel kell rendelkeznie az erőforráscsoport és a virtuális hálózat számára, amelyeken a magánhálózati végpont létre lesz hozva.

**Használati eset** | **Engedélyek** 
--- | --- 
 Privát végpontok létrehozása és kezelése. | Microsoft. Network/privateEndpoint/írás/művelet<br/>Microsoft. Network/privateEndpoint/olvasás/művelet  
|Magánhálózati végpont csatolása egy VNet/alhálózathoz.<br/>Erre akkor van szükség, ha a virtuális hálózaton létrejön a magánhálózati végpont.| Microsoft. Network/virtualNetworks/alhálózat/csatlakozás/művelet Microsoft. Network/virtualNetworks/JOIN/Action
|Kapcsolja össze a magánhálózati végpontot egy Storage-fiókkal. <br/>| Microsoft. microsoft. Storage/storageAccounts/privateEndpointConnectionApproval/művelet <br/> Microsoft. microsoft. Storage/storageAccounts/privateEndpointConnections/READ
|Hozzon létre egy hálózati adaptert, és csatlakoztassa egy hálózati biztonsági csoporthoz. | Microsoft. Network/networkInterfaces/READ <br/> Microsoft. Network/networkInterfaces/alhálózatok/írás <br/> Microsoft. Network/networkInterfaces/alhálózatok/olvasás<br/> Microsoft. Network/networkSecurityGroups/JOIN/Action (nem kötelező)
Privát DNS-zónák létrehozása és kezelése.| saját DNS zóna közreműködői szerepkör <br/> _Vagy_ <br/> Microsoft. Network/privateDnsZones/A/* <br/>  Microsoft. Network/privateDnsZones/Write Microsoft. Network/privateDnsZones/READ <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Write <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/READ <br/> Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write <br/>  Microsoft. Network/privateDnsZones/virtualNetworkLinks/READ <br/> Microsoft. Network/virtualNetworks/csatlakozás/művelet 

### <a name="2-identify-the-cache-storage-account"></a>2. a gyorsítótár Storage-fiókjának azonosítása 
 
A Azure Migrate automatikusan létrehoz egy gyorsítótár-tárolási fiókot, amikor egy Azure Migrate projektben először konfigurálja a virtuális gép replikációját (a Azure Portal élmény használatával). A Storage-fiók ugyanabban az előfizetésben és erőforráscsoporthoz jön létre, amelyben a Azure Migrate projektet hozta létre a alkalmazásban.

A Storage-fiók létrehozása és megkeresése:

1. A Azure Migrate Azure Portal-felületének használatával replikálhat egy vagy több virtuális gépet a projektben.
2. Navigáljon a Azure Migrate projekt erőforráscsoporthoz.
3. Keresse meg a cache Storage-fiókot az **"LSA"** előtag azonosításával a Storage-fiók nevében.

![Erőforráscsoport nézet](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> Ha egynél több Storage-fiókja van az erőforráscsoport **"LSA"** előtaggal, a Storage-fiók ellenőrzéséhez navigáljon a projektben lévő replikáló virtuális gépek replikációs beállítások és cél konfigurációja menüjébe. <br/> 
> ![A replikációs beállítások áttekintése](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. frissítse a gyorsítótár Storage-fiókját általános célú v2-re 

Saját végpontokat csak általános célú v2 (GPv2) Storage-fiókban hozhat létre. Ha a cache Storage-fiók nem GPv2, frissítse a GPv2 a következő lépések végrehajtásával:

1. Nyissa meg a tárfiókot.
2. Válassza a **Konfiguráció** lehetőséget.
3. A **Fiók típusa** területen válassza a **frissítés** lehetőséget.
4. A **Frissítés megerősítése** területen írja be a fiók nevét.
5. Válassza a **frissítés** elemet az oldal alján.

![Storage-fiók frissítése](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. hozzon létre egy privát végpontot a Storage-fiókhoz

1. Nyissa meg a Storage-fiókját, válassza a bal oldali menü **hálózatkezelés** elemét, és válassza a **privát végponti kapcsolatok** fület.  
2. Válassza a **+ privát végpont** lehetőséget.

    a. A **privát végpont létrehozása** ablakban – válassza ki az **előfizetést** és az **erőforráscsoportot**. Adja meg a privát végpont nevét, és válassza ki a Storage-fiók régióját.  
    ![PE konfigurációs ablak](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. Az **erőforrás** lapon adja meg annak az **előfizetési névnek a nevét** , amelyben a Storage-fiók található. Válassza ki a **Microsoft. Storage/StorageAccounts** **erőforrás típusát**. Az **erőforrás** mezőben adja meg a GPv2 típusú replikációs tároló fiók nevét. Válassza a **blob** lehetőséget **célként szolgáló alerőforrásként**.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. A **konfiguráció** lapon válassza ki a **virtuális hálózatot** és az **alhálózatot** a Storage-fiók privát végpontja számára.  

    > [!Note]
    > A virtuális hálózatnak tartalmaznia kell az ExpressRoute-átjáró végpontját, vagy csatlakoznia kell a virtuális hálózathoz a ExpressRoute-átjáróval. 

    Az **saját DNS integráció** szakaszban válassza az **Igen** lehetőséget, és INTEGRÁLJA a saját DNS-zónával. Az **Igen** lehetőség kiválasztásával automatikusan összekapcsolja a DNS-zónát a kiválasztott virtuális hálózattal, és hozzáadja azokat a DNS-rekordokat, amelyek szükségesek az új IP-címek és a magánhálózati végponthoz létrehozott teljes tartománynevek DNS-feloldásához. További információ a [privát DNS-zónákról.](https://docs.microsoft.com/azure/dns/private-dns-overview)

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. Hozzáadhat **címkéket** is a privát végponthoz.  

    e. Folytassa a **felülvizsgálat + létrehozás** után a részletek beírásával. Ha az ellenőrzés befejeződött, válassza a **Létrehozás** lehetőséget a privát végpont létrehozásához.

    > [!Note]
    > Ha a magánhálózati végpontot létrehozó felhasználó egyben a Storage-fiók tulajdonosa is, a magánhálózati végpontot a rendszer automatikusan jóváhagyja. Ellenkező esetben a tulajdonosnak jóvá kell hagynia a privát végpontot a használathoz. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Saját DNS-zónák létrehozása és a DNS-rekordok manuális hozzáadása (nem kötelező)

Ha nem jelölte be a magánhálózati DNS-zónával való integráció lehetőségét a magánhálózati végpont létrehozásakor, kövesse az ebben a szakaszban ismertetett lépéseket a saját DNS-zónák manuális létrehozásához. 

> [!Note]
> Ha az **Igen** lehetőséget választotta egy privát DNS-zónába való integráláshoz, akkor kihagyhatja ezt a szakaszt. 

1. Hozzon létre egy privát DNS-zónát. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a.  Az **saját DNS zónák** lapon kattintson a **+ Hozzáadás** gombra az új zóna létrehozásának megkezdéséhez.  
    b.  A **saját DNS-zóna létrehozása** lapon adja meg a szükséges adatokat. Adja meg a saját DNS-zóna nevét _privatelink_. blob.Core.Windows.net néven. c. A DNS-zóna áttekintéséhez és létrehozásához folytassa a **felülvizsgálat + létrehozás** lapon.

2. Kapcsolja össze a magánhálózati DNS-zónát a virtuális hálózattal.  

    A fent létrehozott privát DNS-zónának ahhoz a virtuális hálózathoz kell tartoznia, amelyhez a magánhálózati végpont csatolva van.

    a. Nyissa meg az előző lépésben létrehozott privát DNS-zónát, és navigáljon a lap bal oldalán található virtuális hálózati kapcsolatokhoz. Kattintson a **+ Hozzáadás** gombra.   
    b. Adja meg a szükséges adatokat. Az **előfizetés** és a **virtuális hálózat** mezőket meg kell adni annak a virtuális hálózatnak a megfelelő részleteivel, amelyben a privát végpont csatolva van. A többi mező is maradhat.

3. A következő lépés a DNS-rekordok hozzáadása a DNS-zónához. Adjon hozzá egy bejegyzést a Storage-fiók teljes tartománynevéhez a saját DNS-zónájában.

    a. Nyissa meg a saját DNS-zónáját, és lépjen az oldal bal oldalán található **Áttekintés** szakaszra. Válassza a **+** rekordhalmaz lehetőséget a rekordok hozzáadásának megkezdéséhez.  

    b. A **rekordazonosító hozzáadása** lapon adjon hozzá egy bejegyzést a teljes tartománynév és a magánhálózati IP-cím típusú rekordként.

> [!Important]
> További DNS-beállításokat igényelhet a Storage-fiók privát végpontjának magánhálózati IP-címének feloldásához a forrás környezetből. [Tekintse át ezt a cikket](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) a szükséges DNS-konfiguráció megismeréséhez.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Adatreplikálás ExpressRoute-áramkör használatával Microsoft-partneri kapcsolattal

Az alábbi ábrán látható módon a Microsoft-társ vagy egy meglévő nyilvános tartomány (az új ExpressRoute-kapcsolatok esetében elavult) használatával irányíthatja át a replikációs forgalmat egy ExpressRoute-áramkörön keresztül.
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Még a Microsoft által kezelt áramkörön áthaladó replikációs adatokkal is szüksége lesz az internetkapcsolatra a helyszíni helyről más kommunikációhoz (vezérlési síkon) a Azure Migrate szolgáltatással. Vannak olyan további URL-címek, amelyek nem érhetők el a ExpressRoute-on keresztül, hogy a replikációs berendezésnek/Hyper-V-gazdagépnek hozzá kell férnie a replikálási folyamat előkészítéséhez. Az URL-követelményeket áttekintheti az áttelepítési forgatókönyv, a [VMware ügynök nélküli áttelepítés](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) vagy az [ügynök-alapú áttelepítés](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance)alapján.  

Ha a helyszíni telephelyen proxyt használ, és a ExpressRoute szeretné használni a replikálási forgalomhoz, konfigurálnia kell egy proxy megkerülést a kapcsolódó URL-címekhez a helyszíni készüléken. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Proxy-megkerülési szabályok konfigurálása a Azure Migrate berendezésen (VMware ügynök nélküli Migrálás esetén)

1. Jelentkezzen be (távoli asztal) a Azure Migrate készülékre.   
2. Nyissa meg a C:/ProgramData/MicrosoftAzure/config/appliance.jsfájlt a Jegyzettömb használatával.
3. A fájlban módosítsa a "EnableProxyBypassList" (hamis) kifejezést a "EnableProxyBypassList": "true" (igaz) értékre. Mentse a módosításokat, és indítsa újra a készüléket.
4. Az újraindítás után a készülék Configuration Manager megnyitásakor megtekintheti a proxy megkerülése lehetőséget a webalkalmazás felhasználói felületén. Adja hozzá az alábbi URL-címeket a proxy-megkerülési listához.   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Proxy-megkerülési szabályok konfigurálása a replikációs berendezésen (ügynök-alapú áttelepítések esetén)

Kövesse az alábbi lépéseket a proxy-megkerülési lista konfigurálásához a konfigurációs kiszolgálón és a folyamat-kiszolgálókon:

1. [Töltse le a PsExec eszközt](https://docs.microsoft.com/sysinternals/downloads/psexec) a rendszer felhasználói környezetének eléréséhez.
2. Nyissa meg az Internet Explorert a rendszerfelhasználó környezetben a következő parancssori PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe" parancs futtatásával
3. Proxybeállítások megadása az IE-ben.
4. A Mellőzés listából adja hozzá az Azure Storage URL-címét. *. blob. Core. Windows. net.  

A fenti megkerülési szabályok biztosítják, hogy a replikálási forgalom áthaladjon a ExpressRoute-on keresztül, miközben a felügyeleti kommunikáció áthaladhat a proxyn az interneten.  

Emellett a következő BGP-Közösségek útvonal-szűrőben lévő útvonalakat is meg kell hirdetnie, hogy a Azure Migrate replikációs forgalom az Internet helyett egy ExpressRoute-áramkörön haladjon át.  

- Regionális BGP-Közösség az Azure-régió forrásához (Azure Migrate projekt régiója)
- Regionális BGP-Közösség a cél Azure-régióhoz (Migrálás régiója)
- BGP-Közösség Azure Active Directory (12076:5060)

További információ az [útválasztási szűrőkről](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) és a [ExpressRoute BGP-Közösségek](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp)listájáról. 

## <a name="next-steps"></a>Következő lépések

- További információ az [ExpressRoute-áramkörökről](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings).
- További információ az [ExpressRoute-útválasztási tartományokról](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare).
- További információ a [privát végpontokról](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).