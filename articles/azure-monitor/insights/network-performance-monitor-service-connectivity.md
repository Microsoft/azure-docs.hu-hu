---
title: Network Performance Monitor megoldás-szolgáltatás kapcsolata – Azure Log Analytics
description: A Network Performance Monitor Service connectivity monitor funkciójának használatával figyelheti a hálózati kapcsolatot bármely olyan végponthoz, amely nyitott TCP-porttal rendelkezik.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: a5b42e61af398e111b2014f3734a0011a4bee778
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734162"
---
# <a name="service-connectivity-monitor"></a>Szolgáltatáskapcsolati monitor

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási [feladatokhoz telepítse át a teszteket Network Performance monitorról az](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) Azure Network Watcher új, 2024. február 29. előtti kapcsolódási figyelője felé.

A [Network Performance monitor](network-performance-monitor.md) Service connectivity monitor funkciójának használatával figyelheti a hálózati kapcsolatot bármely olyan végponthoz, amely nyitott TCP-porttal rendelkezik. Ilyen végpontok közé tartoznak a webhelyek, az SaaS-alkalmazások, a Pásti-alkalmazások és az SQL-adatbázisok. 

A szolgáltatás-kapcsolódási figyelővel a következő függvényeket végezheti el: 

- Az alkalmazásokhoz és a hálózati szolgáltatásokhoz való hálózati kapcsolat figyelése több fiókirodából vagy helyről. Az alkalmazások és a hálózati szolgáltatások közé tartoznak a Microsoft 365, a Dynamics CRM, a belső üzletági alkalmazások és az SQL-adatbázisok.
- A Microsoft 365 és a Dynamics 365-végpontok hálózati kapcsolatának figyeléséhez használjon beépített teszteket. 
- A végponthoz való csatlakozáskor a válaszidő, a hálózati késés és a csomagok elvesztésének meghatározása.
- Annak megállapítása, hogy a gyenge alkalmazások teljesítménye a hálózat vagy az alkalmazás-szolgáltató végén lévő probléma miatt történt-e.
- Azonosítsa a hálózatban lévő olyan gyors helyeket, amelyek az alkalmazások gyenge teljesítményét okozhatják, ha megtekinti a topológiai Térkép egyes ugrásai által biztosított késést.


![Szolgáltatáskapcsolati monitor](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguráció 
A Network Performance Monitor konfigurációjának megnyitásához nyissa meg a [Network Performance monitor megoldást](network-performance-monitor.md) , és válassza a **Konfigurálás** lehetőséget.

![A Network Performance Monitor konfigurálása](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Log Analytics-ügynökök konfigurálása figyeléshez
Engedélyezze a következő tűzfalszabályok használatát a figyeléshez használt csomópontokon, hogy a megoldás képes legyen felderíteni a csomópontok topológiáját a szolgáltatási végpontra: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Szolgáltatás-kapcsolódási figyelő tesztek létrehozása 

Kezdje el a tesztek létrehozását a szolgáltatási végpontokhoz való hálózati kapcsolat figyelésére.

1. Válassza ki a **szolgáltatás-kapcsolódási figyelő** lapot.
2. Válassza a **teszt hozzáadása** lehetőséget, és adja meg a teszt nevét és leírását. Munkaterületen legfeljebb 450 tesztet hozhat létre. 
3. Válassza ki a teszt típusát:<br>

    * Válassza a **web** lehetőséget egy olyan szolgáltatáshoz való kapcsolódás figyelésére, amely válaszol a http/S kérelmekre, például outlook.office365.com vagy Bing.com.<br>
    * Válassza a **hálózat** lehetőséget a TCP-kérelmekre válaszoló szolgáltatáshoz való kapcsolódás figyeléséhez, de nem válaszol a http/S kérelmekre, például az SQL Serverre, az FTP-kiszolgálóra vagy az SSH-portra. 
    * Például: webteszt létrehozása blob Storage-fiókba válassza a **web** lehetőséget, és adja meg a TARGET értéket *yourstorageaccount*. blob.Core.Windows.net néven. Hasonlóképpen létrehozhat teszteket más Table Storage-hoz, üzenetsor-tároláshoz és Azure Files [a hivatkozás használatával.](../../storage/common/storage-account-overview.md#storage-account-endpoints)
4. Ha nem szeretne hálózati méréseket végrehajtani, például a hálózati késést, a csomagok elvesztését és a topológia felderítését, törölje a jelet a **hálózati mérések végrehajtása** jelölőnégyzetből. Hagyja kiválasztva, hogy a képességből maximális előnyt kapjon. 
5. A **cél** mezőben adja meg azt az URL/FQDN/IP-címet, amelyre figyelni szeretné a hálózati kapcsolatot.
6. A **portszám** mezőben adja meg a cél szolgáltatás portszámát. 
7. A **tesztelési gyakoriság** mezőben adjon meg egy értéket, hogy milyen gyakran szeretné futtatni a tesztet. 
8. Válassza ki azokat a csomópontokat, amelyeken figyelni szeretné a szolgáltatással létesített hálózati kapcsolatot. Győződjön meg arról, hogy a tesztek által hozzáadott ügynökök száma kisebb, mint 150. Bármelyik ügynök legfeljebb 150 végpontot/ügynököt tud tesztelni.

    >[!NOTE]
    > A Windows Server-alapú csomópontok esetében a képesség TCP-alapú kérelmeket használ a hálózati mérések végrehajtásához. A Windows ügyfél-alapú csomópontok esetében a képesség ICMP-alapú kérelmeket használ a hálózati mérések végrehajtásához. Bizonyos esetekben a célalkalmazás blokkolja a bejövő ICMP-alapú kérelmeket, amikor a csomópontok Windows ügyfél-alapúak. A megoldás nem tud hálózati méréseket végrehajtani. Javasoljuk, hogy ilyen esetekben a Windows Server-alapú csomópontokat használja. 

9. Ha nem szeretne állapotadatokat létrehozni a kiválasztott elemekhez, törölje **az állapot-figyelés engedélyezése beállítást a teszt által érintett célokban**. 
10. Válassza a figyelési feltételek lehetőséget. A küszöbértékek megadásával egyéni küszöbértékeket állíthat be az állapot-események generálásához. Ha a feltétel értéke a kiválasztott hálózati vagy alhálózati pár esetén a kiválasztott küszöbérték fölé esik, a rendszer egy állapottal kapcsolatos eseményt generál. 
11. A konfiguráció mentéséhez kattintson a **Mentés** gombra. 

    ![Szolgáltatás-csatlakozási figyelő – tesztelési konfigurációk](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Útmutatás 

Lépjen a Network Performance Monitor irányítópult nézetre. A különböző létrehozott tesztek állapotának összefoglalásához tekintse meg a **szolgáltatás-kapcsolódási figyelő** lapot. 

![Szolgáltatás-kapcsolódási figyelő lapja](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Válassza ki a csempét a **tesztek lapon a tesztek** részleteinek megtekintéséhez. A bal oldali táblázatban megtekintheti a szolgáltatás válaszideje, a hálózati késés és a csomagok elvesztésének időpontját az összes tesztnél. A hálózati állapot-rögzítő vezérlőelem használatával megtekintheti a hálózati pillanatképet a múltban egy másik időpontban. Válassza ki a vizsgálni kívánt táblát. A jobb oldali ablaktáblán található diagramoknál megtekintheti a veszteség, a késés és a válaszidő értékeinek korábbi tendenciáit. Az egyes csomópontok teljesítményének megtekintéséhez kattintson a **teszt részletei** hivatkozásra.

![Szolgáltatás-kapcsolódási figyelő tesztek](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

A **csomópontok tesztelése** nézetben megfigyelheti az egyes csomópontok hálózati kapcsolatait. Válassza ki a teljesítmény romlását okozó csomópontot. Ez az a csomópont, ahol a rendszer lassú működést észlelt az alkalmazásban.

Annak megállapítása, hogy a gyenge alkalmazások teljesítménye a hálózat vagy az alkalmazás szolgáltatójának hibája miatt történt-e, ha megfigyeli az alkalmazás válaszideje és a hálózati késés közötti korrelációt. 

* **Alkalmazási probléma:** A válaszidő, de a hálózati késésben lévő konzisztencia azt sugallja, hogy a hálózat megfelelően működik, és a probléma oka az alkalmazás végén fellépő probléma lehet. 

    ![Szolgáltatás-kapcsolódási figyelő alkalmazásával kapcsolatos probléma](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Hálózati probléma:** A hálózati késésben lévő megfelelő tüske kíséretében a válaszidő azt jelzi, hogy a válaszidő növekedésének oka a hálózati késés növekedése lehet. 

    ![Szolgáltatás-kapcsolódási figyelő hálózati problémája](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Miután meghatározta, hogy a probléma a hálózat miatt van-e, válassza a **topológia** nézet hivatkozását, és azonosítsa a topológiai Térkép kellemetlen ugrását. Az alábbi képen egy példa látható. A csomópont és az alkalmazás végpontja közötti 105 – MS teljes késés miatt a 96 MS az Ugrás vörös színnel van megjelölve. A fáradságos ugrás azonosítása után elvégezheti a javítási műveleteket. 

![Szolgáltatás-kapcsolódási figyelő végpontjának topológiája](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnosztika 

Ha rendellenesség figyelhető meg, kövesse az alábbi lépéseket:

* Ha a szolgáltatás válaszideje, a hálózati veszteség és a késés a NA-ban jelenik meg, a következő okok közül egy vagy több oka lehet:

    - Az alkalmazás nem érhető el.
    - A szolgáltatáshoz való hálózati kapcsolat ellenőrzéséhez használt csomópont nem érhető el.
    - A teszt konfigurációjában megadott cél helytelen.
    - A csomópontnak nincs hálózati kapcsolata.

* Ha megjelenik egy érvényes szolgáltatás válaszideje, de a hálózati veszteség, valamint a késés a NA-ban jelenik meg, a következő okok közül egy vagy több oka lehet:

    - Ha a szolgáltatáshoz való hálózati kapcsolat ellenőrzéséhez használt csomópont egy Windows-ügyfélszámítógép, vagy a célként megadott szolgáltatás blokkolja az ICMP-kéréseket, vagy a hálózati tűzfal blokkolja a csomóponttól származó ICMP-kérelmeket.
    - A **hálózati mérések elvégzése** jelölőnégyzet üres a tesztelési konfigurációban. 

* Ha a szolgáltatás válaszideje NA, de a hálózati veszteség, valamint a késés érvényes, akkor előfordulhat, hogy a célként megadott szolgáltatás nem webalkalmazás. Szerkessze a teszt konfigurációját, és válassza ki a teszt típusát a **web** helyett **hálózatként** . 

* Ha az alkalmazás lassan fut, állapítsa meg, hogy a gyenge alkalmazások teljesítménye a hálózat vagy az alkalmazás szolgáltatójának végén található-e.

## <a name="gcc-office-urls-for-us-government-customers"></a>GCC Office-URL-címek az USA kormányzati ügyfelei számára
Az USA-beli kormányzati Virginia régió esetében csak a DOD URL-címek beépített NPM. A GCC URL-címeket használó ügyfeleknek egyéni teszteket kell létrehozniuk, és egyenként hozzá kell adni az egyes URL-címeket.

| Mező | GCC |
|:---   |:--- |
| Office 365-portál és megosztott | portal.apps.mil |
| Office 365-hitelesítés és-identitás | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS-csapatok | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Következő lépések
[Keresési naplók](../logs/log-query-overview.md) a hálózati teljesítményadatok részletes rekordjainak megtekintéséhez.