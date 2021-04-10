---
title: DNS Analytics megoldás a Azure Monitorban | Microsoft Docs
description: A Azure Monitor DNS Analytics megoldásának beállítása és használata a DNS-infrastruktúra biztonságával, teljesítményével és műveleteivel kapcsolatos információk gyűjtésére.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: f01a1204aefbcaabe0ddac254b24bc014c3d5a64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654526"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>A DNS-infrastruktúrával kapcsolatos információk gyűjtése a DNS Analytics előzetes verziójának megoldásával

![DNS Analytics szimbólum](./media/dns-analytics/dns-analytics-symbol.png)

Ez a cikk azt ismerteti, hogyan állíthatja be és használhatja a Azure Monitor Azure DNS elemzési megoldását, hogy betekintést gyűjtsön a DNS-infrastruktúrába a biztonság, a teljesítmény és a műveletek terén.

A DNS Analytics a következőket teszi lehetővé:

- Azonosítsa azokat az ügyfeleket, akik kártékony tartományneveket próbálnak megoldani.
- Elavult erőforrásrekordok azonosítása.
- Azonosítsa a gyakran lekérdezett tartományneveket és a beszédes DNS-ügyfeleket.
- A kérések terhelésének megtekintése a DNS-kiszolgálókon.
- Dinamikus DNS-regisztráció meghibásodásának megtekintése.

A megoldás gyűjti, elemzi és korrelálja a DNS-kiszolgálókkal kapcsolatos Windows DNS analitikai és naplózási naplókat, valamint az egyéb kapcsolódó adatokat.

## <a name="connected-sources"></a>Összekapcsolt források

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| **Csatlakoztatott forrás** | **Támogatás** | **Leírás** |
| --- | --- | --- |
| [Windows-ügynökök](../agents/agent-windows.md) | Yes | A megoldás DNS-információkat gyűjt a Windows-ügynököktől. |
| [Linux-ügynökök](../vm/quick-collect-linux-computer.md) | No | A megoldás nem gyűjt DNS-adatokat a közvetlen Linux-ügynököktől. |
| [System Center Operations Manage felügyeleti csoport](../agents/om-agents.md) | Yes | A megoldás a DNS-információkat a csatlakoztatott Operations Manager felügyeleti csoportban lévő ügynököktől gyűjti. Nem szükséges közvetlen kapcsolódás a Operations Manager ügynöktől a Azure Monitorhoz. Az adatok továbbítása a felügyeleti csoportból a Log Analytics munkaterületre történik. |
| [Azure Storage-fiók](../essentials/resource-logs.md#send-to-log-analytics-workspace) | No | A megoldás nem használja az Azure Storage-t. |

### <a name="data-collection-details"></a>Adatgyűjtés részletei

A megoldás a DNS-leltár és a DNS-eseményekkel kapcsolatos adatokat gyűjti azokról a DNS-kiszolgálókról, amelyeken a Log Analytics-ügynök telepítve van. Ezt követően a rendszer feltölti ezeket az adatAzure Monitor, és megjeleníti a megoldás irányítópultján. A rendszer a leltárral kapcsolatos adatokat, például a DNS-kiszolgálók, zónák és erőforrásrekordok számát gyűjti össze a DNS PowerShell-parancsmagok futtatásával. Az Adatfrissítés két naponként történik. Az eseményekkel kapcsolatos adatokat a rendszer valós időben gyűjti a továbbfejlesztett DNS-naplózás és diagnosztika által biztosított [analitikai és naplózási naplókból](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) a Windows Server 2012 R2 rendszerben.

## <a name="configuration"></a>Konfiguráció

A megoldás konfigurálásához használja a következő információkat:

- A figyelni kívánt összes DNS-kiszolgálón [Windows](../agents/agent-windows.md) vagy [Operations Manager](../agents/om-agents.md) ügynöknek kell lennie.
- Az [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace)-en felveheti a DNS Analytics megoldást az log Analytics-munkaterületre. Használhatja a [Solutions Gallery Azure monitor-megoldások hozzáadása](solutions.md)című részében ismertetett eljárást is.

A megoldás további konfigurálás nélkül elindítja az adatok gyűjtését. A következő konfigurációval azonban testre szabhatja az adatgyűjtés beállításait.

### <a name="configure-the-solution"></a>A megoldás konfigurálása

A Azure Portal Log Analytics munkaterületen válassza a **munkaterület összefoglalása** elemet, majd kattintson a **DNS Analytics** csempére. A megoldás Irányítópultján kattintson a **konfiguráció** elemre a DNS Analytics konfiguráció lap megnyitásához. A következő két típusú konfigurációs módosítást teheti:

- **Allowlisted tartománynevek**. A megoldás nem dolgozza fel az összes keresési lekérdezést. A tartománynév-utótagok engedélyezési tartja karban. A megoldás nem dolgozza fel azokat a keresési lekérdezéseket, amelyek az ebben a engedélyezési található tartománynév-utótagoknak megfelelő tartományneveket oldják fel. A allowlisted-tartománynevek feldolgozása nem segít optimalizálni a Azure Monitorba elküldett adatgyűjtést. Az alapértelmezett engedélyezési olyan népszerű nyilvános tartományneveket tartalmaz, mint például a www.google.com és a www.facebook.com. A teljes alapértelmezett listát görgetve tekintheti meg.

  A lista módosításával bármely olyan tartománynév-utótagot hozzáadhat, amelyben meg szeretné tekinteni a keresési elemzéseket. Eltávolíthat olyan tartománynév-utótagot is, amelyről nem kívánja megtekinteni a keresési elemzéseket.

- **Beszédes ügyfél küszöbértéke**. Azok a DNS-ügyfelek, amelyek túllépik a keresési kérelmek számának küszöbértékét, a **DNS-ügyfelek** panelen vannak kiemelve. Az alapértelmezett küszöbérték 1 000. Módosíthatja a küszöbértéket.

    ![Allowlisted tartománynevek](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Felügyeleti csomagok

Ha a Microsoft monitoring agentet használja a Log Analytics munkaterülethez való kapcsolódáshoz, a következő felügyeleti csomag van telepítve:

- Microsoft DNS-adatgyűjtő Intelligence Pack (Microsoft. IntelligencePacks. DNS)

Ha a Operations Manager felügyeleti csoport csatlakozik a Log Analytics munkaterülethez, a megoldás hozzáadásakor a következő felügyeleti csomagok lesznek telepítve a Operations Manager. A következő felügyeleti csomagok nem szükségesek a konfiguráláshoz vagy karbantartáshoz:

- Microsoft DNS-adatgyűjtő Intelligence Pack (Microsoft. IntelligencePacks. DNS)
- Microsoft System Center Advisor DNS Analytics konfiguráció (Microsoft.IntelligencePack.Dns.Configszülő)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../agents/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>A DNS Analytics megoldás használata

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


A DNS-csempe tartalmazza azon DNS-kiszolgálók számát, amelyeken az adatok gyűjtése történik. Emellett az ügyfelek által az elmúlt 24 órában kártékony tartományok feloldására irányuló kérések számát is tartalmazza. A csempére kattintva megnyílik a megoldás irányítópultja.

![DNS Analytics csempe](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>A megoldás irányítópultja

A megoldás irányítópultja a megoldás különböző szolgáltatásaihoz tartozó összesített információkat jeleníti meg. Emellett a kriminalisztika elemzésére és diagnosztizálására szolgáló részletes nézetre mutató hivatkozásokat is tartalmaz. Alapértelmezés szerint az elmúlt hét napban megjelennek az adathalmazok. A dátum-és időtartomány a **dátum-idő kijelölési vezérlőelem** használatával módosítható, ahogy az alábbi képen is látható:

![Idő kiválasztása vezérlő](./media/dns-analytics/dns-time.png)

A megoldás irányítópultja a következő lapokat jeleníti meg:

**DNS-biztonság**. A kártékony tartományokkal kommunikálni próbáló DNS-ügyfeleket jelenti. A Microsoft Threat Intelligence-hírcsatornák használatával a DNS Analytics képes észlelni a kártékony tartományokhoz hozzáférni próbáló ügyfelek IP-címeit. A kártevők tartománynevének feloldásával sok esetben a kártevővel fertőzött eszközök kitárcsázása a kártékony tartomány "Command and Control" központjába.

![DNS-biztonság panel](./media/dns-analytics/dns-security-blade.png)

Ha a listában egy ügyfél IP-címére kattint, megnyílik a naplóbeli keresés, és megjelenik a megfelelő lekérdezés keresési adatai. A következő példában a DNS Analytics észlelte, hogy a kommunikáció egy [IRCBot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621)történt:

![A IRCBot mutató keresési eredmények naplózása](./media/dns-analytics/ircbot.png)

Az információ segítségével azonosíthatja a következőket:

- Az ügyfél IP-címe, amely kezdeményezte a kommunikációt.
- A kártékony IP-címhez feloldani kívánt tartománynév.
- Azok az IP-címek, amelyekre a tartománynév fel lett oldva.
- Rosszindulatú IP-cím.
- A probléma súlyossága.
- A kártékony IP-blocklisting oka.
- Észlelési idő.

**Lekérdezett tartományok**. A a leggyakoribb tartományneveket adja meg a környezetben lévő DNS-ügyfelek által lekérdezett lekérdezésekben. Megtekintheti a lekérdezett tartománynevek listáját. A naplóbeli keresés során megtekintheti egy adott tartománynév keresési kérelmének részleteit is.

![Lekérdezett tartományok panel](./media/dns-analytics/domains-queried-blade.png)

**DNS-ügyfelek**. Azt jelenti, hogy az ügyfelek *megszegik a* kiválasztott időszakban a lekérdezések számának küszöbértékét. Megtekintheti az összes DNS-ügyfél listáját, valamint a naplózott keresés során általuk végzett lekérdezések részleteit.

![DNS-ügyfelek panel](./media/dns-analytics/dns-clients-blade.png)

**Dinamikus DNS-regisztrációk**. A jelentések nevének regisztrációja sikertelen. A cím- [erőforrásrekordok](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (a és AAAA típus) összes regisztrációs hibája ki van emelve a regisztrációs kérelmeket használó ügyfél IP-címeivel együtt. Ezt az információt ezután a következő lépésekkel keresheti meg a regisztrációs hiba kiváltó okát:

1. Keresse meg azt a zónát, amely mérvadó ahhoz a névhez, amelyet az ügyfél frissíteni próbál.

1. A megoldás segítségével keresse meg a zóna leltározási adatait.

1. Ellenőrizze, hogy a zóna dinamikus frissítése engedélyezve van-e.

1. Győződjön meg arról, hogy a zóna biztonságos dinamikus frissítésre van-e konfigurálva.

    ![Dinamikus DNS-regisztrációk panel](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Név-regisztrációs kérelmek**. A felső csempe a sikeres és sikertelen DNS dinamikus frissítési kérelmek trendvonalát mutatja. Az alsó csempe felsorolja az első 10 ügyfelet, amely sikertelen DNS-frissítési kérelmeket küld a DNS-kiszolgálókra, a hibák száma alapján rendezve.

![Regisztrációs kérelmek neve panel](./media/dns-analytics/name-reg-req-blade.png)

**Példa DDI Analytics-lekérdezésekre**. Azokat a leggyakoribb keresési lekérdezéseket tartalmazza, amelyek közvetlenül beolvasják a nyers elemzési adatforrásokat.


![Mintalekérdezések](./media/dns-analytics/queries.png)

Ezeket a lekérdezéseket kiindulási pontként használhatja a testreszabott jelentéskészítéshez használható saját lekérdezések létrehozásához. A lekérdezések a DNS Analytics napló keresési oldalára mutatnak, ahol az eredmények megjelennek:

- **A DNS-kiszolgálók listája**. Az összes olyan DNS-kiszolgáló listáját jeleníti meg, amelyek a hozzájuk társított teljes tartománynevet, tartománynevet, erdő nevét és kiszolgálói IP-címeket használják.
- **DNS-zónák listája**. Megjeleníti az összes DNS-zóna listáját a társított zóna nevével, a dinamikus frissítési állapottal, a névkiszolgálók és a DNSSEC aláírási állapotával.
- Nem **használt erőforrásrekordok**. A fel nem használt/Elavult erőforrásrekordok listáját jeleníti meg. Ez a lista tartalmazza az erőforrásrekord nevét, az erőforrásrekord típusát, a társított DNS-kiszolgálót, a rekord létrehozásának idejét és a zóna nevét. Ezt a listát használhatja azon DNS-erőforrásrekordok azonosítására, amelyek már nincsenek használatban. Ezen információk alapján eltávolíthatja ezeket a bejegyzéseket a DNS-kiszolgálókról.
- **DNS-kiszolgálók lekérdezési terhelése**. Megjeleníti a DNS-kiszolgálók DNS-terhelésére vonatkozó perspektívát. Ezek az információk segíthetnek a kiszolgálók kapacitásának megtervezésében. A **metrikák** lapon megnyithatja a nézetet grafikus megjelenítésre. Ez a nézet segít megérteni, hogyan oszlik meg a DNS-terhelés a DNS-kiszolgálók között. A DNS-lekérdezés gyakoriságának tendenciáit mutatja az egyes kiszolgálókon.

    ![DNS-kiszolgálók lekérdezési naplójának keresési eredményei](./media/dns-analytics/dns-servers-query-load.png)

- **DNS-zónák lekérdezés betöltése**. A DNS-zóna a megoldás által felügyelt DNS-kiszolgálókon lévő összes zónájának lekérdezése. Kattintson a **mérőszámok** lapra, hogy a részletes rekordok nézetét az eredmények grafikus megjelenítésére módosítsa.
- **Konfigurációs események**. Megjeleníti az összes DNS-konfigurációs módosítási eseményt és a hozzá tartozó üzeneteket. Ezután szűrheti ezeket az eseményeket az esemény, az eseményazonosító, a DNS-kiszolgáló vagy a feladat kategóriája alapján. Az adatkezelési szolgáltatás az adott DNS-kiszolgálókon megadott időpontokban végrehajtott módosítások naplózását is lehetővé teszi.
- **DNS analitikai napló**. Megjeleníti a megoldás által kezelt DNS-kiszolgálókon található összes analitikai eseményt. Ezután szűrheti ezeket az eseményeket az esemény, az eseményazonosító, a DNS-kiszolgáló, az ügyfél IP-címe és a lekérdezés típusa tevékenység kategóriája alapján. A DNS-kiszolgáló analitikus eseményei lehetővé teszik a tevékenységek nyomon követését a DNS-kiszolgálón. A rendszer minden alkalommal naplóz egy elemzési eseményt, amikor a kiszolgáló elküldi vagy fogadja a DNS-információkat.

### <a name="search-by-using-dns-analytics-log-search"></a>Keresés DNS Analytics naplóbeli keresés használatával

A naplók keresése lapon létrehozhat egy lekérdezést. A keresési eredményeket a Faces vezérlők használatával szűrheti. Emellett speciális lekérdezéseket is létrehozhat az eredmények átalakításához, szűréséhez és jelentéséhez. Kezdje a következő lekérdezések használatával:

1. A **keresési lekérdezés mezőbe** írja be, `DnsEvents` hogy a megoldás által kezelt DNS-kiszolgálók által generált összes DNS-eseményt megtekintse. Az eredmények felsorolják a keresési lekérdezésekkel, a dinamikus regisztrációkkal és a konfigurációs változásokkal kapcsolatos összes esemény naplózási adatát.

    ![DnsEvents-napló keresése](./media/dns-analytics/log-search-dnsevents.png)  

    a. A keresési lekérdezések naplózási adatainak megtekintéséhez válassza a **LookUpQuery** lehetőséget a bal oldali dimenzió vezérlőelemben található **altípus** -szűrőként. Megjelenik egy tábla, amely felsorolja a kijelölt időszakra vonatkozó összes keresési lekérdezési eseményt.

    b. A dinamikus regisztrációk naplózási adatainak megtekintéséhez válassza a **DynamicRegistration** lehetőséget a bal oldali dimenzió vezérlőelemben található **altípus** -szűrőként. Megjelenik egy tábla, amely felsorolja a kijelölt időszakra vonatkozó összes dinamikus regisztrációs eseményt.

    c. A konfigurációs módosítások naplózási adatainak megtekintéséhez válassza a **konfigurációváltozás** lehetőséget a bal oldali dimenzió vezérlőelemben található **altípus** -szűrőként. Megjelenik egy tábla, amely felsorolja a kiválasztott időszak összes konfigurációs módosítási eseményét.

1. A **keresési lekérdezés mezőbe** írja be, `DnsInventory` hogy megtekintse a megoldás által kezelt DNS-kiszolgálók összes DNS-leltárával kapcsolatos adatát. Az eredmények felsorolják a DNS-kiszolgálók, a DNS-zónák és az erőforrásrekordok naplózási adatait.

    ![DnsInventory-napló keresése](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Hibaelhárítás

Gyakori hibaelhárítási lépések:

1. Hiányzó DNS-keresési információ – a probléma megoldásához próbálja meg alaphelyzetbe állítani a konfigurációt, vagy csak egyszer töltse be a konfigurációs oldalt a portálon. Az alaphelyzetbe állításhoz egyszerűen módosítsa a beállítást egy másik értékre, majd állítsa vissza az eredeti értékre, és mentse a konfigurációt.

## <a name="suggestions"></a>Javaslatok

A visszajelzések megadásához látogasson el a [log Analytics UserVoice oldalára](https://aka.ms/dnsanalyticsuservoice) , és tegye fel ötleteit DNS Analytics funkcióinak működéséhez. 

## <a name="next-steps"></a>Következő lépések

[Lekérdezési naplók](../logs/log-query-overview.md) a részletes DNS-naplóbejegyzések megtekintéséhez.
