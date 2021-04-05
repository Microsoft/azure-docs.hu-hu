---
title: Azure Application Insights tranzakciós diagnosztika | Microsoft Docs
description: Application Insights végpontok közötti tranzakciós diagnosztika
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 7623b7131e6344a67c468d0436884ebfef9b0058
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96746097"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Egyesített ágazatközi tranzakciós diagnosztika

Az egyesített diagnosztika felhasználói felülete automatikusan összekapcsolja az összes Application Insights figyelt összetevőből származó kiszolgálóoldali telemetria egyetlen nézetbe. Nem számít, hogy van-e több, különálló rendszerállapot-kulccsal rendelkező erőforrás. Application Insights észleli a mögöttes kapcsolatot, és lehetővé teszi, hogy egyszerűen diagnosztizálja az alkalmazás összetevőjét, függőségét vagy kivételét, amely a tranzakció lelassulását vagy meghibásodását okozta.

## <a name="what-is-a-component"></a>Mi az összetevő?

Az összetevők egymástól függetlenül telepíthetők a Distributed/-Services-alkalmazás részeként. A fejlesztőknek és az operatív csapatoknak kód szintű láthatósággal vagy az alkalmazás-összetevők által generált telemetria való hozzáféréssel kell rendelkezniük.

* Az összetevők különböznek a "megfigyelt" külső függőségek, például az sqltól, a EventHub stb., amelyek nem férhetnek hozzá a csapatához vagy szervezetéhez (kód vagy telemetria).
* Az összetevők tetszőleges számú kiszolgáló/szerepkör/tároló példányon futnak.
* Az összetevők különálló Application Insights rendszerállapot-kulcsok (még akkor is, ha az előfizetések eltérőek) vagy a különböző szerepkörök, amelyek egyetlen Application Insights kialakítási kulcsnak vannak kialakítva. Az új felület az összes összetevő részleteit jeleníti meg, függetlenül attól, hogy milyen beállítások vannak beállítva.

> [!NOTE]
> * **Hiányzik a kapcsolódó elemek hivatkozásai?** Az összes kapcsolódó telemetria a bal oldalon a [felső](#cross-component-transaction-chart) és az [alsó](#all-telemetry-with-this-operation-id) részen található. 

## <a name="transaction-diagnostics-experience"></a>Tranzakciós diagnosztikai felület
Ez a nézet négy fő részből áll: az eredmények listájából, egy több összetevőből álló tranzakciós diagramból, a művelethez kapcsolódó összes telemetria idősorozat-listáját, valamint a bal oldali kiválasztott telemetria elem részletek ablaktábláját.

![Kulcsfontosságú részek](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Több összetevőt tartalmazó tranzakciós diagram

Ez a diagram a kérelmek és az összetevők közötti függőségek időtartamára vízszintes sávokkal rendelkező ütemtervet biztosít. A begyűjtött kivételek szintén az idősoron vannak megjelölve.

* A diagram legfelső sora a belépési pontot, az első összetevőnek a tranzakcióban meghívott bejövő kérelmét jelképezi. Az időtartam a tranzakció befejezéséhez szükséges teljes idő.
* A külső függőségek felé irányuló hívások egyszerű, nem összecsukható sorok, a függőség típusát jelképező ikonokkal.
* A más összetevőkre irányuló hívások összecsukható sorok. Minden sor egy adott műveletnek felel meg, amelyet az összetevő meghívott.
* Alapértelmezés szerint a kiválasztott kérelem, függőség vagy kivétel a jobb oldalon jelenik meg.
* Válassza ki bármelyik sort a [jobb oldalon látható Részletek](#details-of-the-selected-telemetry)megjelenítéséhez. 

> [!NOTE]
> A más összetevőkre irányuló hívásoknak két sora van: az egyik sor a hívó összetevőtől érkező kimenő hívást (függőséget) jelenti, a másik sor pedig a hívott összetevő bejövő kérelmének felel meg. A vezető ikon és az időtartam-sávok eltérő stílusa segít különbséget tenni a kettő között.

## <a name="all-telemetry-with-this-operation-id"></a>A művelet-azonosítóval rendelkező összes telemetria

Ez a szakasz a tranzakcióhoz kapcsolódó összes telemetria idősorában jeleníti meg az egyszerű listanézet-nézetet. Emellett a tranzakciós diagramon nem megjelenő egyéni eseményeket és nyomkövetéseket is megjeleníti. Ezt a listát egy adott összetevő vagy hívás által generált telemetria szűrheti. A listában bármelyik telemetria kiválaszthatja [a megfelelő részletek](#details-of-the-selected-telemetry)megjelenítéséhez.

![Az összes telemetria időszakasza](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>A kiválasztott telemetria részletei

Ez az összecsukható ablaktábla a tranzakciós diagram vagy a lista bármely kiválasztott elemének részletes adatait jeleníti meg. Az "összes megjelenítése" listában az összes összegyűjtött szabványos attribútum szerepel. Minden egyéni attribútum külön szerepel a szabványos készlet alatt. Kattintson a "..." gombra. a verem nyomkövetési ablaka alatt a nyomkövetés másolására szolgáló lehetőséget kaphat. "A Profiler-Nyomkövetések megnyitása" vagy "a hibakeresési pillanatkép megnyitása" a megfelelő részletes ablaktáblákban megjeleníti a kód szintű diagnosztikát.

![Kivétel részletei](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Keresési eredmények

Ez az összecsukható ablaktábla a szűrési feltételeknek megfelelő egyéb eredményeket jeleníti meg. Kattintson bármelyik találatra a fent felsorolt 3 szakaszban található megfelelő részletek frissítéséhez. Megpróbáljuk megtalálni azokat a mintákat, amelyeknek a legvalószínűbb, hogy az összes összetevőről elérhetők legyenek a részletek, még akkor is, ha a mintavételezés bármelyikben érvényben van. Ezek "javasolt" mintákként jelennek meg.

![Keresési eredmények](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>A Profiler és a Snapshot Debugger

A [Application Insights Profiler](./profiler.md) vagy a [Snapshot Debugger](snapshot-debugger.md) segítséget nyújt a teljesítménnyel és a hibákkal kapcsolatos hibák diagnosztizálására. Ezzel a megoldással a Profiler-nyomkövetéseket és-pillanatképeket bármely összetevőből megtekintheti egyetlen kattintással.

Ha nem tudja beolvasni a Profilert, forduljon a **serviceprofilerhelp \@ Microsoft.com**

Ha nem Snapshot Debugger munkát, forduljon a **snapshothelp \@ Microsoft.com**

![Profiler-integráció](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>GYIK

*Egyetlen összetevőt látok a diagramon, a többi pedig csak külső függőségeket mutat, az ezen összetevőkön belül történtek részletessége nélkül.*

Lehetséges okok:

* A többi összetevő a Application Insights?
* A legújabb STABLE Application Insights SDK-t használják?
* Ha ezek az összetevők külön Application Insights erőforrások, szükséges a [hozzáférés](resources-roles-access-control.md) , ha rendelkezik hozzáféréssel, és az összetevők a legújabb Application Insights SDK-val vannak ellátva, tudassa velünk a jobb felső visszajelzési csatornán keresztül.

*Ismétlődő sorok jelennek meg a függőségekhez. Ez várható?*

Ekkor a kimenő függőségi hívás a bejövő kérelemtől elkülönítve jelenik meg. A két hívás általában megegyezik azzal, hogy csak a hálózati kör miatti időtartam értéknek kell megegyeznie. A vezető ikon és az időtartam-sávok eltérő stílusa segít különbséget tenni a kettő között. Az adatzavar az adatelemzést mutatja? Küldje el visszajelzését!

*Mi a helyzet a különböző összetevő-példányok közötti időeltérésekkel?*

Az időkeretek a tranzakciós diagramon az időeltérésekhez vannak igazítva. A pontos időbélyegeket a részletek ablaktáblán vagy az Analytics használatával tekintheti meg.

*Miért hiányzik az új felület a kapcsolódó elemek legtöbb lekérdezésével?*

Ez az elvárt működés. Az összes összetevőhöz kapcsolódó összes elem már elérhető a bal oldalon (a felső és az alsó részen). Az új felület két kapcsolódó elemet tartalmaz, amelyeket a bal oldalon nem fed le: az esemény előtt és után az összes telemetria öt perc múlva és a felhasználói idővonalon.

