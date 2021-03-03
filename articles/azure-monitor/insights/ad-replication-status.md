---
title: Active Directory replikáció állapotának figyelése
description: A Active Directory Replication Status Solution Pack minden replikációs hiba esetén rendszeresen figyeli a Active Directory környezetet.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: c99ad16e119c4262aa6d9d645b2457cdd46061b2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700677"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Active Directory replikáció állapotának figyelése Azure Monitor

![AD Replication Status szimbólum](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory a vállalati informatikai környezet kulcsfontosságú összetevője. A magas rendelkezésre állás és a nagy teljesítmény biztosítása érdekében minden tartományvezérlő saját másolattal rendelkezik a Active Directory adatbázisáról. A tartományvezérlők egymással replikálódnak, hogy propagálják a módosításokat a vállalaton belül. A replikálási folyamat hibái számos problémát okozhatnak a vállalaton belül.

A AD Replication Status megoldás rendszeresen figyeli a Active Directory környezetet bármilyen replikációs hiba esetén.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

### <a name="prerequisites"></a>Előfeltételek

* A AD Replication Status megoldáshoz a .NET-keretrendszer 4.6.2 vagy újabb verziójának támogatott verzióját kell telepítenie minden olyan számítógépen, amelyen telepítve van a Windows Log Analytics ügynöke (más néven a Microsoft monitoring Agent (MMA)).  Az ügynököt a System Center 2016-Operations Manager, Operations Manager 2012 R2 és Azure Monitor használja.
* A megoldás támogatja a Windows Server 2008 és a 2008 R2, a Windows Server 2012 és az 2012 R2, valamint a Windows Server 2016 rendszert futtató tartományvezérlőket.
* Log Analytics munkaterület, amely az Azure Portal Azure Marketplace-en adja hozzá a Active Directory Health-ellenőrzési megoldást. Nincs szükség további konfigurációra.


### <a name="install-agents-on-domain-controllers"></a>Ügynökök telepítése tartományvezérlőkön
Az ügynököket olyan tartományvezérlőkön kell telepíteni, amelyek kiértékelése a tartomány tagjai. Másik lehetőségként telepítenie kell az ügynököket a tagkiszolgálókon, és konfigurálnia kell az ügynököket, hogy az AD-replikációs adatszolgáltatásokat a Azure Monitor küldje el. A Windows rendszerű számítógépek Azure Monitorhoz való kapcsolódásának megismeréséhez lásd: [Windows rendszerű számítógépek Összekötése Azure monitorhoz](../agents/agent-windows.md). Ha a tartományvezérlő már része egy meglévő System Center Operations Manager-környezetnek, amelyhez csatlakozni szeretne Azure Monitorhoz, tekintse meg a [Operations Manager Azure monitorhoz való kapcsolódását](../agents/om-agents.md)ismertető témakört.

### <a name="enable-non-domain-controller"></a>Nem tartományvezérlő engedélyezése
Ha nem kívánja közvetlenül csatlakoztatni a tartományvezérlőket a Azure Monitorhoz, a tartomány bármely más számítógépét is csatlakoztathatja a Azure Monitorhoz, hogy összegyűjtse az AD Replication Status megoldási csomag adatait, és küldje el az adatokat.

1. Győződjön meg arról, hogy a számítógép a AD Replication Status megoldás használatával figyelni kívánt tartomány tagja.
2. Ha még nincs csatlakoztatva, [csatlakoztassa a Windows rendszerű számítógépet Azure monitor](../agents/om-agents.md) vagy [csatlakoztassa a meglévő Operations Manager-környezettel Azure monitorhoz](../agents/om-agents.md).
3. A számítógépen állítsa be a következő beállításkulcsot:<br>Kulcs: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName> \Solutions\ADReplication**<br>Érték: **IsTarget**<br>Érték: **true**

   > [!NOTE]
   > Ezek a módosítások addig nem lépnek érvénybe, amíg újra nem indítja a Microsoft monitoring Agent szolgáltatást (HealthService.exe).
   > ### <a name="install-solution"></a>Megoldás telepítése
   > A **Active Directory Replication status** megoldás a log Analytics-munkaterülethez való hozzáadásához kövesse a [figyelési megoldás telepítése](solutions.md#install-a-monitoring-solution) című témakörben ismertetett eljárást. Nincs szükség további konfigurációra.


## <a name="ad-replication-status-data-collection-details"></a>AD Replication Status adatgyűjtési adatok
Az alábbi táblázat az adatgyűjtés módszereit és a AD Replication Status adatok gyűjtésének egyéb részleteit mutatja be.

| platform | Közvetlen ügynök | SCOM-ügynök | Azure Storage | SCOM szükséges? | A felügyeleti csoporton keresztül elküldett SCOM-ügynök | gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |minden öt nap |



## <a name="understanding-replication-errors"></a>Replikációs hibák ismertetése

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

A AD Replication Status csempe megjeleníti, hogy hány replikációs hiba van jelenleg. A **kritikus replikálási hibák** olyan hibák, amelyek a Active Directory erdő [törlésre kijelölt élettartamának](/previous-versions/windows/it-pro/windows-server-2003/cc784932(v=ws.10)) 75%-ában vagy annál újabbnál nagyobbak.

![AD Replication Status csempe](./media/ad-replication-status/oms-ad-replication-tile.png)

A csempére kattintva további információkat jeleníthet meg a hibákról.
![AD Replication Status irányítópult](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Célkiszolgáló állapota és forráskiszolgáló állapota
Ezek az oszlopok a replikációs hibákat észlelő célkiszolgáló és forráskiszolgáló állapotát jelenítik meg. Az egyes tartományvezérlők neve utáni szám jelzi a replikálási hibák számát a tartományvezérlőn.

A rendszer a célkiszolgáló és a forráskiszolgáló hibáit is megjeleníti, mivel bizonyos problémák könnyebben orvosolhatók a forráskiszolgáló szemszögéből és másokkal a célkiszolgáló szemszögéből.

Ebben a példában láthatja, hogy sok célkiszolgáló nagyjából azonos számú hibát tartalmaz, de van egy forráskiszolgáló (ADDC35), amely sokkal több hibát tartalmaz, mint az összes többi. Valószínű, hogy probléma merült fel a ADDC35, ami miatt a rendszer nem tudja elküldeni az adatküldést a replikációs partnereinek. A ADDC35 kapcsolatos problémák elhárítása megoldhatja a célkiszolgáló területén megjelenő hibák nagy részét.

### <a name="replication-error-types"></a>Replikációs hibák típusai
Ez a terület információt nyújt a vállalaton belül észlelt hibák típusairól. Minden egyes hiba egyedi numerikus kóddal és egy olyan üzenettel rendelkezik, amely segíthet a hiba kiváltó okának meghatározásában.

A felső fánkon látható, hogy a környezetben milyen hibák jelennek meg gyakrabban és ritkábban.

Akkor jelenik meg, ha több tartományvezérlő ugyanazt a replikációs hibát tapasztalja. Ebben az esetben előfordulhat, hogy az egyik tartományvezérlőn képes felderíteni vagy azonosítani a megoldást, majd megismételni a műveletet más, ugyanezen hiba által érintett tartományvezérlőkön.

### <a name="tombstone-lifetime"></a>Törlés kijelölésének élettartama
A törlésre kijelöltség élettartama határozza meg, hogy a törölt objektumokat, amelyeket törlésre jelöl, a rendszer megőrzi a Active Directory adatbázisban. Amikor egy törölt objektum átadja a törlésre kijelölt időpontot, a rendszer automatikusan eltávolítja a szemetet a Active Directory adatbázisból.

A törlés alapértelmezett élettartama 180 nap a legújabb Windows-verziók esetében, de a régebbi verziókban 60 nap volt, és a Active Directory rendszergazdája explicit módon megváltoztathatja.

Fontos tudni, hogy vannak-e olyan replikációs hibák, amelyek közelednek vagy a törlésre kijelölt élettartamon túl vannak. Ha két tartományvezérlő olyan replikációs hibát észlel, amely nem őrzi meg a törlés utáni élettartamot, a replikálás le lesz tiltva a két tartományvezérlő között, még akkor is, ha az alapul szolgáló replikációs hiba kijavítva van.

A törlésre kijelölt élettartam területe segít azonosítani azokat a helyeket, ahol a letiltott replikáció veszélyben van. A **több mint 100% TSL** kategória minden hibája olyan partíciót jelöl, amely nem replikálódott a forrás-és a célkiszolgáló között legalább az erdő törlésre kijelölt élettartamára.

Ebben az esetben a replikálási hiba kijavítása nem lesz elég. A replikáció újraindítása előtt legalább manuálisan meg kell vizsgálnia a hosszadalmas objektumok azonosítását és tisztítását. Szükség lehet egy tartományvezérlő leszerelésére is.

Azon replikációs hibák azonosításán kívül, amelyek megtartották a törlés utáni élettartamot, a **50-75% TSL** vagy a **75-100% TSL** kategóriába tartozó hibákért is érdemes figyelni.

Ezek olyan hibák, amelyek egyértelműen kitartanak, nem átmenetiek, így valószínűleg beavatkozásra van szükségük a megoldás feloldásához. A jó hír az, hogy még nem érte el a törlésre való kijelölés élettartamát. Ha ezt a problémát azonnal kijavítja, *mielőtt* elérné a törlésre kijelölt élettartamot, a replikálás a minimális manuális beavatkozással is újraindulhat.

Amint azt korábban említettük, a AD Replication Status-megoldás irányítópult-csempe a környezetben a *kritikus* replikációs hibák számát jeleníti meg, amely a törlésre kijelölt élettartam 75%-ában (beleértve a TSL 100%-át) megjelenő hibákat. Törekedjen a 0 érték megtartására.

> [!NOTE]
> Az összes törlésre kijelölt élettartam százalékos számításai a Active Directory erdő tényleges törlésre kijelölt élettartamán alapulnak, így biztos lehet benne, hogy a százalékos értékek pontosak, még akkor is, ha egyéni törlésre vonatkozó élettartam-érték van beállítva.
>
>

### <a name="ad-replication-status-details"></a>AD-replikálási állapot részletei
Ha az egyik listában rákattint bármelyik elemre, további részleteket láthat a napló lekérdezésével kapcsolatban. A rendszer szűri az eredményeket, hogy csak az adott elemmel kapcsolatos hibákat jelenítse meg. Ha például a **célkiszolgáló állapota (ADDC02)** szakaszban felsorolt első tartományvezérlőre kattint, a lekérdezési eredmények szűrve jelennek meg, hogy megjelenjenek-e az adott tartományvezérlővel jelzett hibák a célkiszolgálón:

![Az AD-replikáció állapotával kapcsolatos hibák a lekérdezés eredményeiben](./media/ad-replication-status/oms-ad-replication-search-details.png)

Innen további szűrést végezhet, módosíthatja a napló lekérdezését, és így tovább. További információ a Azure Monitorban található naplók használatával kapcsolatban: a [naplózási adatok elemzése Azure monitorokban](../logs/log-query-overview.md).

A **HelpLink** mező a TechNet-oldal URL-címét jeleníti meg az adott hibával kapcsolatos további részletekkel. A hivatkozást a böngészőablakba másolhatja és beillesztheti a hibaelhárítással és a hiba elhárításával kapcsolatos információk megjelenítéséhez.

Az **Exportálás** gombra kattintva is exportálhatja az eredményeket az Excelbe. Az adatexportálással bármilyen módon megjelenítheti a replikációs hibákat.

![az Active Directory replikációs állapotával kapcsolatos hibák az Excelben](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD Replication Status GYIK
**K: milyen gyakran frissülnek az AD-replikációs állapotadatok?**
A: az adatok öt naponta frissülnek.

**K: van lehetőség arra, hogy beállítsa az Adatfrissítés gyakoriságát?**
A: jelenleg nem.

**K: az összes saját tartományvezérlőt hozzá kell adni az Log Analytics munkaterülethez a replikálási állapot megtekintéséhez?**
A: nem, csak egyetlen tartományvezérlőt kell hozzáadnia. Ha több tartományvezérlővel rendelkezik a Log Analytics munkaterületen, az összes közül az adatok Azure Monitorba lesznek küldve.

**K: nem kívánok tartományvezérlőket hozzáadni a Log Analytics munkaterülethez. Továbbra is használhatom a AD Replication Status megoldást?**

V: Igen. Megadhatja a beállításkulcs értékét az engedélyezéséhez. Lásd: a [nem tartományvezérlő engedélyezése](#enable-non-domain-controller).

**K: Mi az adatgyűjtés folyamatának neve?**
A: AdvisorAssessment.exe

**K: mennyi időt vesz igénybe az adatok gyűjtése?**
A: az adatgyűjtési idő a Active Directory környezet méretétől függ, de általában kevesebb, mint 15 percet vesz igénybe.

**K: milyen típusú adatokat gyűjt a rendszer?**
A: a replikációs információk gyűjtése az LDAP használatával történik.

**K: konfigurálható az adatok gyűjtésének módja?**
A: jelenleg nem.

**K: milyen engedélyekre van szükségem az adatok gyűjtéséhez?**
A: a Active Directoryhoz tartozó normál felhasználói engedélyek elegendőek.

## <a name="troubleshoot-data-collection-problems"></a>Az adatgyűjtési problémák elhárítása
Az adatok gyűjtéséhez a AD Replication Status Solution Pack használatához legalább egy tartományvezérlőnek csatlakoznia kell a Log Analytics munkaterülethez. Amíg nem kapcsolódik tartományvezérlőhöz, megjelenik egy üzenet, amely jelzi, hogy az **adatok gyűjtése még folyamatban van**.

Ha segítségre van szüksége a tartományvezérlők csatlakoztatásához, megtekintheti a dokumentációt a [Windows rendszerű számítógépek csatlakoztatása Azure monitor](../agents/om-agents.md). Azt is megteheti, hogy ha a tartományvezérlő már csatlakoztatva van egy meglévő System Center Operations Manager-környezethez, megtekintheti a dokumentációt a [Connect System Center Operations Manager a Azure monitor](../agents/om-agents.md).

Ha nem szeretne közvetlenül a tartományvezérlőhöz csatlakozni Azure Monitor vagy a System Center Operations Managerhoz, tekintse meg a [nem tartományvezérlő engedélyezése](#enable-non-domain-controller)című témakört.

## <a name="next-steps"></a>Következő lépések
* A [Azure monitorban található naplók](../logs/log-query-overview.md) használatával megtekintheti a Active Directory replikációs állapotadatok részletes állapotát.

