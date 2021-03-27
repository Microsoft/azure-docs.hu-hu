---
title: A Windows rendszerű virtuális asztali előnézet szószedetének figyelése – Azure
description: A Windows rendszerű virtuális asztali Azure Monitorhez kapcsolódó feltételek és fogalmak glosszáriuma.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 3/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7b824bc13bc4f553d22358b69237173effb51594
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627132"
---
# <a name="azure-monitor-for-windows-virtual-desktop-preview-glossary"></a>Azure Monitor a Windows rendszerű virtuális asztali (előzetes verzió) szószedethez

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali Azure Monitor jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk a Windows rendszerű virtuális asztalok (előzetes verzió) Azure Monitorával kapcsolatos főbb fogalmakat és fogalmakat sorolja fel és röviden ismerteti.

## <a name="alerts"></a>Riasztások

Az előfizetésben konfigurált és a [súlyossági szint 0](#severity-0-alerts) besorolású aktív Azure monitor riasztások az Áttekintés lapon jelennek meg. A riasztások beállításával kapcsolatos további információkért tekintse meg a [Azure monitor riasztásokkal kapcsolatos események megválaszolásával foglalkozó](../azure-monitor/alerts/tutorial-response.md)témakört.

## <a name="available-sessions"></a>Elérhető munkamenetek

Az elérhető munkamenetek a gazdagépen rendelkezésre álló munkamenetek számát jelenítik meg. A szolgáltatás kiszámítja ezt a számot a virtuális gépek (VM-EK) számának a virtuális gépen engedélyezett maximális számával, majd az összes munkamenet kivonásával.

## <a name="connection-success"></a>Sikeres csatlakoztatás

Ez az objektum a kapcsolatok állapotát mutatja. A "kapcsolat sikeressége" azt jelenti, hogy a kapcsolat elérheti a gazdagépet, ahogyan azt a virtuális gép vereme is megerősítette. A sikertelen kapcsolat azt jelenti, hogy a kapcsolat nem tudta elérni a gazdagépet.

## <a name="daily-active-users-dau"></a>Napi aktív felhasználók (DAU)

A munkamenetet elindító felhasználók teljes száma az elmúlt 24 órában.

## <a name="daily-alerts"></a>Napi riasztások

Az egyes napokon kiváltott riasztások teljes száma.

## <a name="daily-connections-and-reconnections"></a>Napi csatlakozások és újracsatlakozások

Az elmúlt 24 órában elindított vagy befejezett kapcsolatok és újrakapcsolódások teljes száma.

## <a name="daily-connected-hours"></a>Napi csatlakozások időtartama órákban

A felhasználók által az elmúlt 24 órában egy munkamenethez csatlakoztatott órák teljes száma.

## <a name="diagnostics-and-errors"></a>Diagnosztika és hibák

Ha egy hiba vagy riasztás jelenik meg a Windows rendszerű virtuális asztali Azure Monitorban, három dolog kategorizálja:

- Tevékenységtípus: Ez a kategória azt szemlélteti, hogy a Windows virtuális asztali diagnosztika hogyan kategorizálta a hibát. A kategóriák a felügyeleti tevékenységek, a hírcsatornák, a kapcsolatok, a gazdagépek regisztrációja, a hibák és az ellenőrzőpontok. További információ ezekről a kategóriákról [log Analytics a diagnosztikai szolgáltatás használatához](diagnostics-log-analytics.md).

- Típus: Ez a kategória a hiba helyét mutatja. 

     - "Szolgáltatás" vagy "ServiceError = TRUE" jelölésű hibák történtek a Windows rendszerű virtuális asztali szolgáltatásban.
     - Az "üzembe helyezés" vagy a "ServiceError = FALSE" címkével jelölt hibák a Windows rendszerű virtuális asztali szolgáltatáson kívül történtek.
     - További információ a ServiceError címkéről: [gyakori hibák](diagnostics-role-service.md#common-error-scenarios).

- Forrás: Ez a kategória részletesebb leírást ad a hiba helyéről.

     - Diagnosztika: a szolgáltatás figyelésére és jelentéskészítésére szolgáló szolgáltatási szerepkör, amely lehetővé teszi a felhasználók számára az üzembe helyezési problémák megfigyelését és diagnosztizálását.

     - RDBroker: a telepítési tevékenységek előkészítésére, az objektumok állapotának fenntartására, a hitelesítés ellenőrzésére és egyebekre vonatkozó szolgáltatási szerepkör.

     - RDGateway: a végfelhasználók és a virtuális gépek közötti hálózati kapcsolat kezeléséért felelős szolgáltatási szerepkör.

     - RDStack: a virtuális gépekre telepített szoftver-összetevő, amely lehetővé teszi a kommunikációt a Windows Virtual Desktop szolgáltatással.

     - Client: a végfelhasználói gépen futó szoftver, amely a Windows rendszerű virtuális asztali szolgáltatás felületét biztosítja. Megjeleníti a közzétett erőforrások listáját, és a kijelölés után a Távoli asztal-kapcsolatban tárolja a kapcsolatokat.

Minden diagnosztikai probléma vagy hiba tartalmaz egy üzenetet, amely elmagyarázza, hogy mi volt a baj. További információ a hibák elhárításáról: a [Windows rendszerű virtuális asztali problémák azonosítása és diagnosztizálása](diagnostics-role-service.md).

## <a name="input-delay"></a>Bemeneti késés

A Windows rendszerű virtuális asztali Azure Monitorban a "bemeneti késleltetés" érték azt jelenti, hogy az egyes munkamenetek esetében az összes folyamat teljesítményszámláló a bemeneti késleltetés. A [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)címen található gazdagép teljesítmény lapján ez a teljesítményszámláló úgy van konfigurálva, hogy 30 másodpercenként egyszer küldjön jelentést a szolgáltatásnak. A 30 másodperces intervallumok neve "Samples", és a jelentés az adott ablakban a legrosszabb ESET. A medián és a p95 érték az összes minta középértékét és 95. százalékát tükrözi.

A **gazdagép által megadott bemeneti késleltetés** alatt kiválaszthat egy munkamenet-gazdagépet, amely a lapon lévő összes többi vizualizációt szűrni tudja. Azt is megteheti, hogy a folyamat nevét kiválasztva kiszűri a medián bemeneti késleltetését az idődiagramon.

A következő kategóriákban késedelmeket teszünk:

- Jó: 150 ezredmásodperc alatt.
- Elfogadható: 150-500 ezredmásodperc.
- Gyenge: 500 – 2000 ezredmásodperc (2 másodperc alatt).
- Helytelen: több mint 2 000 ezredmásodperc (2 másodperc és fel).

Ha többet szeretne megtudni a bemeneti késleltetési számláló működéséről, tekintse meg a [felhasználói bemeneti késleltetési](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)teljesítményszámlálók című témakört.

##  <a name="monthly-active-users-mau"></a>Havi aktív felhasználók (MAU)

Azon felhasználók teljes száma, akik az elmúlt 28 napban megkezdték a munkamenetet. Ha 30 vagy kevesebb napig tárolja az adatait, akkor előfordulhat, hogy alacsonyabb, mint a várt MAU és a kapcsolatok értéke az időszakokban, ahol kevesebb, mint 28 nap áll rendelkezésre.

## <a name="performance-counters"></a>Teljesítményszámlálók

A teljesítményszámlálók megjelenítik a hardver-összetevők, az operációs rendszerek és az alkalmazások teljesítményét.

A következő táblázat a Windows rendszerű virtuális asztali Azure Monitor által használt ajánlott teljesítményszámlálókat és időintervallumokat sorolja fel:

|Teljesítményszámláló neve|Időintervallum|
|---|---|
|Logikai lemez (C:) \\ átlagos Lemezvezérlő-várólista hossza|30 másodperc|
|Logikai lemez (C:) \\ átlagos műveleti idő (mp/átvitel)|60 másodperc|
|Logikai lemez (C:) a \\ lemez aktuális várólistájának hossza|30 másodperc|
|Memória ( \* ) \\ elérhető MB-ban|30 másodperc|
|Memória ( \* ) \\ laphibák másodpercenként|30 másodperc|
|Memória ( \* ) \\ lapok másodpercenként|30 másodperc|
|Memória ( \* ) \\ %-ban előjegyzett bájtok használatban|30 másodperc|
|Fizikai \* lemez () átlagos lemezvezérlő- \\ várólista hossza|30 másodperc|
|Fizikai \* lemez () \\ átlagos olvasási idő (mp/olvasás)|30 másodperc|
|Fizikai lemez () átlagos műveleti idő ( \* \\ mp/átvitel)|30 másodperc|
|Fizikai lemez ( \* ) \\ átlagos írási idő (mp/írás)|30 másodperc|
|Processzor adatai (_Total) \\ processzoridő%-ban|30 másodperc|
|Terminal Services ( \* ) \\ aktív munkamenetek|60 másodperc|
|Terminal Services ( \* ) \\ inaktív munkamenetek|60 másodperc|
|Terminal Services ( \* ) \\ összes munkamenete|60 másodperc|
|\*Felhasználói bevitel késleltetése folyamat szerint ( \* ) \\ maximális bemeneti késleltetés|30 másodperc|
|\*Felhasználói bemeneti késleltetés/munkamenet ( \* ) \\ maximális bemeneti késleltetése|30 másodperc|
|Távoli hálózat ( \* ) \\ jelenlegi TCP-RTT|30 másodperc|
|Távoli hálózat ( \* ) \\ jelenlegi UDP-sávszélesség|30 másodperc|

További információ a teljesítményszámlálók olvasásáról: [teljesítményszámlálók konfigurálása](../azure-monitor/agents/data-sources-performance-counters.md).

A bemeneti késleltetési teljesítményszámlálók részletes ismertetését lásd: [felhasználói bemeneti késleltetési](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)teljesítményszámlálók.

## <a name="potential-connectivity-issues"></a>Lehetséges kapcsolódási problémák

A lehetséges kapcsolódási problémák a gazdagépeket, a felhasználókat, a közzétett erőforrásokat és az ügyfeleket magas csatlakozási hibák arányban jelenítik meg. Miután kiválasztotta a "jelentés alapja" szűrőt, kiértékelheti a probléma súlyosságát az alábbi oszlopok értékeinek ellenőrzésével:

- Kísérletek (csatlakozási kísérletek száma)
- Erőforrások (közzétett alkalmazások vagy asztali számítógépek száma)
- Gazdagépek (virtuális gépek száma)
- Ügyfelek

Ha például a **felhasználói** szűrő lehetőséget választja, akkor a **kísérletek** oszlopban megtekintheti az egyes felhasználók csatlakozási kísérleteit.

Ha észreveszi, hogy a probléma több gazdagépre, felhasználóra, erőforrásra vagy ügyfélre is kiterjed, valószínű, hogy a probléma a teljes rendszerre hatással van. Ha nem, az alacsonyabb prioritású problémát jelent.

A bejegyzéseket a további információk megtekintéséhez is kiválaszthatja. Megtekintheti, hogy mely gazdagépek, erőforrások és ügyfél-verziók érintettek a problémával. A kijelzõn a kapcsolati kísérletek során jelentett hibák is megjelennek.

## <a name="round-trip-time-rtt"></a>Oda-és visszaút ideje (RTT)

Az oda-és visszautazási idő (RTT) a kapcsolat időkorlátja a végfelhasználó helye és a munkamenet-állomás Azure-régiója között. Ha szeretné megtekinteni, hogy mely helyek rendelkeznek a legjobb késéssel, keresse meg a kívánt helyet a [Windows rendszerű virtuális asztali élmény kalkulátor eszközben](https://azure.microsoft.com/services/virtual-desktop/assessment/).

## <a name="session-history"></a>Munkamenet előzményei

A **munkamenetek** elem megjeleníti az összes munkamenet állapotát, a csatlakoztatott és a leválasztott kapcsolatot. Az **üresjárati munkamenetek** csak a leválasztott munkameneteket jelenítik meg.

## <a name="severity-0-alerts"></a>0 súlyosságú riasztások

A legsürgősebb elemek, amelyekre azonnal ügyelnie kell. Ha nem oldja meg ezeket a problémákat, előfordulhat, hogy a Windows rendszerű virtuális asztali környezet működése leáll.

## <a name="time-to-connect"></a>Csatlakozási idő

A kapcsolódáshoz szükséges idő az az idő, amikor egy felhasználó elindítja a munkamenetet, és amikor bejelentkeznek a szolgáltatásba. Az új kapcsolatok létrehozása általában hosszabb időt is igénybe venni, mint a meglévő kapcsolatok helyreállítása.

## <a name="user-report"></a>Felhasználói jelentés

A felhasználói jelentés lapon megtekintheti egy adott felhasználó kapcsolatainak előzményeit és a diagnosztikai adatokat. Minden felhasználói jelentés a használati mintákat, a felhasználói visszajelzéseket és a felhasználók által a munkamenetek során észlelt hibákat jeleníti meg. A legtöbb kisebb probléma feloldható a felhasználói visszajelzéssel. Ha mélyebbre van szüksége, akkor egy adott kapcsolódási AZONOSÍTÓval vagy időtartammal is szűrheti az adatokat.

## <a name="users-per-core"></a>Felhasználók/mag

Az egyes virtuálisgép-mag felhasználóinak száma. Ha nyomon szeretné követni a felhasználók maximális számát az idő múlásával, akkor könnyebben azonosíthatja, hogy a környezet konzisztensen fut-e magas, alacsony vagy ingadozó számú felhasználó számára egy mag alapján. Annak ismerete, hogy az aktív felhasználók száma milyen mértékben segíti a környezet hatékony erőforrás-és méretezését.

## <a name="windows-event-logs"></a>Windows-eseménynaplók

A Windows-eseménynaplók olyan adatforrások, amelyeket Log Analytics ügynökök gyűjtenek a Windows rendszerű virtuális gépeken. Eseményeket gyűjthet a szabványos naplókból, például a rendszerből és az alkalmazásból, valamint a figyelni kívánt alkalmazások által létrehozott egyéni naplókból.

A következő táblázat felsorolja a Windows rendszerű virtuális asztali Azure Monitorhoz szükséges Windows-eseménynaplókat:

|Esemény neve|Eseménytípus|
|---|---|
|Alkalmazás|Hiba és figyelmeztetés|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin|Hiba, figyelmeztetés és információ|
|Microsoft-Windows-TerminalServices-LocalSessionManager/Operational|Hiba, figyelmeztetés és információ|
|Rendszer|Hiba és figyelmeztetés|
| Microsoft-FSLogix – alkalmazások/működési|Hiba, figyelmeztetés és információ|
|Microsoft-FSLogix-alkalmazások/rendszergazda|Hiba, figyelmeztetés és információ|

A Windows-eseménynaplókkal kapcsolatos további tudnivalókért tekintse meg a [Windows-események rekordjainak tulajdonságai](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)című témakört.

## <a name="next-steps"></a>Következő lépések

A Windows rendszerű virtuális asztalok Azure Monitorének megkezdéséhez tekintse meg a következő cikkeket:

- [Az üzembe helyezés figyelése a Windows rendszerű virtuális asztali Azure Monitor használatával](azure-monitor.md)
- [Azure Monitor a Windows rendszerű virtuális asztali hibaelhárításhoz](troubleshoot-azure-monitor.md)

A Azure Advisor is beállíthatja, hogy segítsen a gyakori problémák megoldásában vagy megelőzésében. További információ: [Azure Advisor használata a Windows rendszerű virtuális asztallal](azure-advisor.md).

Ha segítségre van szüksége, vagy kérdése van, tekintse meg a közösségi erőforrásokat:

- Kérdéseket tehet fel, vagy javaslatokat tehet a Közösségnek a [Windows rendszerű virtuális asztali TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
   
- A visszajelzések elküldésével kapcsolatos további információkért lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás a Windows rendszerű virtuális asztalokhoz](troubleshoot-set-up-overview.md#report-issues).

- A Windows rendszerű virtuális asztal visszajelzéseit is elhagyhatja a [Windows rendszerű virtuális asztali visszajelzési központban](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
