---
title: Az Azure Spring Cloud metrikáinak ismertetése
description: Ismerje meg, hogyan tekintheti át a metrikákat az Azure Spring Cloud-ban
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 8c375c3dc9f4c5fdb20270797eb55b3a20a6e214
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698280"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Az Azure Spring Cloud metrikáinak ismertetése

Az Azure Metrics Explorer a Microsoft Azure Portal egy olyan összetevője, amely lehetővé teszi a diagramok ábrázolását, a trendek vizuális korrelációját, valamint a tüskék és a kiugró adatok kivizsgálása mérőszámokban. Az erőforrások állapotának és kihasználtságának vizsgálatához használja a metrikák Explorert. 

Az Azure Spring Cloud-ban két nézőpont látható a metrikák esetében.
* Diagramok az egyes alkalmazások áttekintő oldalán
* Gyakori metrikák lap

 ![Metrikák diagramjai](media/metrics/metrics-1.png)

Az alkalmazások **áttekintésében** szereplő diagramok gyors állapot-ellenőrzéseket biztosítanak az egyes alkalmazásokhoz. A gyakori **mérőszámok** lap az összes rendelkezésre álló metrikát tartalmazza. Saját diagramokat hozhat létre a közös mérőszámok oldalon, és rögzítheti őket az irányítópulton.

## <a name="application-overview-page"></a>Alkalmazás – Áttekintés lap
Válasszon ki egy alkalmazást az **alkalmazásokban** a diagramok kereséséhez az Áttekintés oldalon.  

 ![Alkalmazás-metrikák kezelése](media/metrics/metrics-2.png)

Az egyes alkalmazások **alkalmazás-áttekintő** lapja egy mérőszámokat tartalmazó diagramot jelenít meg, amely lehetővé teszi az alkalmazás gyors állapot-ellenőrzésének elvégzését.  

 ![Az alkalmazás metrikáinak áttekintése](media/metrics/metrics-3.png)

Az Azure Spring Cloud az alábbi öt diagramot biztosítja a percenként frissülő metrikákkal:

* **Http-kiszolgálóhiba: hibák** száma az alkalmazás http-kéréseinél
* **Adat**: az alkalmazás által fogadott bájtok száma
* **Kimenő adatmennyiség**: az alkalmazás által elküldett bájtok száma
* **Kérelmek**: az alkalmazás által fogadott kérelmek
* **Átlagos válaszidő**: az alkalmazás átlagos válaszideje

A diagram esetében egy óra és hét nap közötti időtartományt választhat.

## <a name="common-metrics-page"></a>Gyakori metrikák lap

A bal oldali navigációs panelen a **metrikák** a közös mérőszámok lapra mutatnak.

Először válassza ki a megtekinteni kívánt metrikákat:

![Metrika nézet kiválasztása](media/metrics/metrics-4.png)

Az összes metrikai beállítás részletei az alábbi [részben](#user-metrics-options) találhatók.

Ezután válassza ki az egyes metrikák összesítési típusát:

![Metrika összesítése](media/metrics/metrics-5.png)

Az összesítési típus azt jelzi, hogyan összesíthető a diagram metrikai pontjainak ideje. Percenként egy nyers metrikai pont található, és egy percen belül előre definiált összesítési típust a metrikák típusa határozza meg.
* Sum: az összes értéket célként megadott kimenetként összesíti.
* Átlag: az időszakban az átlagos értéket célként megadott kimenetként használja.
* Max/min: használja a maximális/legkisebb értéket az időszakban célként megadott kimenetként.

Az időtartomány az elmúlt 30 percről az utolsó 30 napra vagy egy egyéni időtartományra is beállítható.

![Metrika módosítása](media/metrics/metrics-6.png)

Az alapértelmezett nézet magában foglalja az Azure Spring Cloud Service összes application's metrikáját. Egy alkalmazás vagy példány metrikája szűrhető a kijelzőn.  Kattintson a **szűrő hozzáadása** gombra, állítsa a tulajdonságot **alkalmazás** értékre, majd válassza ki a figyelni kívánt célalkalmazás **értékét az értékek** szövegmezőben. 

Kétféle szűrőt használhat (Tulajdonságok):
* Alkalmazás: szűrés az alkalmazás neve szerint
* Példány: szűrés alkalmazás-példány alapján

![Metrikai szűrők](media/metrics/metrics-7.png)

Használhatja a **felosztás alkalmazása** lehetőséget is, amely egy alkalmazás több sorát készíti el:

![Metrika felosztása](media/metrics/metrics-8.png)

>[!TIP]
> Saját diagramokat hozhat létre a metrikák lapon, és rögzítheti őket az **irányítópulton**. Először nevezze el a diagramot.  Ezután válassza **a rögzítés az irányítópulton lehetőséget a jobb felső sarokban**. Mostantól a portál **irányítópultján** is megtekintheti az alkalmazását.

## <a name="user-metrics-options"></a>Felhasználói mérőszámok beállításai

Az alábbi táblázatokban az elérhető metrikák és részletek láthatók.

### <a name="error"></a>Hiba
>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| tomcat. Global. error | tomcat. Global. error | Darabszám | A feldolgozott kérelmekben előforduló hibák száma |

### <a name="performance"></a>Teljesítmény
>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| System. CPU. használat | System. CPU. használat | Százalék | A legújabb CPU-használat a teljes rendszer számára (elavult, és nem javasolt használni). Ez az érték a [0.0, 1.0] intervallumban megjelenő Double. A 0,0 érték azt jelenti, hogy az összes CPU tétlen volt a legutóbbi megfigyelt időszakban, míg a 1,0 érték azt jelenti, hogy minden CPU aktívan futtatta az idő 100%-át az elmúlt időszak során.|
>| Process. CPU. használat | Alkalmazás CPU-kihasználtságának százalékos aránya | Százalék | A Java virtuális gép folyamat legutóbbi CPU-használata (elavult, és nem javasolt használni). Ez az érték a [0.0, 1.0] intervallumban megjelenő Double. A 0,0 érték azt jelenti, hogy egyik processzor sem futtatott szálakat a JVM folyamat során a legutóbbi megfigyelt időszakban, míg a 1,0 érték azt jelenti, hogy minden CPU aktívan futtatta a szálakat a JVM 100%-ában az elmúlt időszak során. A JVM található szálak közé tartoznak az alkalmazási szálak, valamint a JVM belső szálak.|
>| AppCpuUsage | Alkalmazás CPU-használata | Százalék | A JVM folyamat legutóbbi CPU-használata az alkalmazáshoz lefoglalt PROCESSZORon, dupla típusú érték [0.0, 1.0] között. A 0,0 érték azt jelenti, hogy egyik processzor sem futtatott szálakat a JVM folyamat során a legutóbbi megfigyelt időszakban, míg a 1,0 érték azt jelenti, hogy minden CPU aktívan futtatta a szálakat a JVM 100%-ában az elmúlt időszak során. A JVM található szálak közé tartoznak az alkalmazási szálak, valamint a JVM belső szálak.|
>| JVM. Memory. véglegesített | JVM. Memory. véglegesített | Bájt | A JVM számára elérhetővé tett memória mennyiségét jelöli. Előfordulhat, hogy a JVM memóriát szabadít fel a rendszeren, és a véglegesítése kisebb lehet, mint az init. a véglegesítés mindig nagyobb vagy egyenlő, mint a használat. |
>| JVM. Memory. felhasználva | JVM. Memory. felhasználva | Bájt | A bájtban jelenleg használt memória mennyiségét jelöli. |
>| JVM. Memory. max | JVM. Memory. max | Bájt | A memória-kezeléshez használható maximális memóriát jelöli. A felhasznált és az előjegyzett memória mennyisége mindig kisebb vagy egyenlő, ha a maximális érték meg van adva. Előfordulhat, hogy a memória kiosztása meghiúsul, ha a használt memóriát úgy próbálja megjavítani, hogy az <= Max használata esetén is igaz legyen (például ha a rendszer kevés a virtuális memóriában), ha a használni próbált >. |
>| JVM. GC. max. az adatméret | JVM. GC. max. az adatméret | Bájt | A régi generációs memória maximális kihasználtsága a Java virtuális gép elindítása óta. |
>| JVM. GC. Live. Resize | JVM. GC. Live. Resize | Bájt | A régi generációs memória-készlet mérete teljes GC után. |
>| JVM. GC. Memory. előléptetve | JVM. GC. Memory. előléptetve | Bájt | A régi generációs memória-készlet méretének pozitív növekedésének száma, mielőtt a GC-t a GC után. |
>| JVM. GC. Memory. lefoglalt | JVM. GC. Memory. lefoglalt | Bájt | Növekszik a fiatal generációs memória-készlet méretének növekedésével, miután az egyik GC a következőre kerül. |
>| JVM. GC. pause. Total. Count | JVM. GC. pause (összesen-Count) | Darabszám | A JMV elindítását követő összes GC-szám, beleértve a fiatal és a régi GC-t. |
>| JVM. GC. pause. Total. Time | JVM. GC. pause (teljes idő) | Ezredmásodpercben | A JMV elindítását követően felhasznált teljes GC-idő, beleértve a fiatal és a régi GC-t. |

### <a name="performance-net"></a>Teljesítmény (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|------|-----------------------------|------|---------|
>| Processzorhasználat       | CPU – használat      | Százalék      | A folyamat CPU-használatának százaléka az összes rendszerprocesszor-erőforráshoz viszonyítva [0-100]. |
>| Munkakészlet     | munkakészlet    | Megabájtban    | A folyamat által használt munkakészlet mennyisége. |
>| GC-halom mérete    | GC-heap-size   | Megabájtban    | A szemetet gyűjtő által jelentett teljes halom mérete. |
>| 0. generációs GC-darabszám  | Gen-0-GC-darabszám | Darabszám        | A 0. generációs Garbage gyűjtemények másodpercenkénti száma. |
>| 1. generációs GC-darabszám  | Gen-1 – GC-darabszám | Darabszám        | Az 1. generációs Garbage gyűjtemények másodpercenkénti száma |
>| 2. generációs GC-darabszám  | Gen-2 – GC – darabszám | Darabszám        | A 2. generációs Garbage gyűjtemények másodpercenkénti száma. |
>| Idő a GC-ben      | timein – GC      | Százalék      | A szemét-gyűjteményben az utolsó betakarítás óta eltelt idő százalékos aránya. |
>| 0. generációs halom mérete | Gen-0 – méret     | Bájt        | 0. generációs halom mérete. |
>| 1. generációs halom mérete | 1. generációs méret     | Bájt        | 1. generációs halom mérete |
>| 2. generációs halom mérete | 2. generációs méret     | Bájt        | 2. generációs halom mérete. |
>| Halmozott halom mérete   | Kissné méret       | Bájt        | Nagyméretű objektum halomba rakásának mérete. |
>| Foglalási sebesség | foglalási – arány     | Bájt        | A lefoglalt bájtok másodpercenkénti száma |
>| Szerelvények száma  | szerelvény – darabszám | Darabszám        | A betöltött szerelvények száma. |
>| Kivételek száma | kivételek száma | Darabszám       | A kivételek másodpercenkénti száma. |
>| Szálak készletének szálak száma      | szálkészlet munkaszála belépett – szálak száma              | Darabszám | Szálak készletének száma |
>| A zárolási tartalom számának figyelése | figyelő – zárolási tartalom – darabszám        | Darabszám | A figyelő zárolásának megkísérlése során a másodpercenkénti számú alkalommal történt a verseny. |
>| Szál-készlet várólistájának hossza      | szálkészlet munkaszála belépett – várólista hossza              | Darabszám | Szál készletének munkaelem-várólistájának hossza |
>| Szál készletének befejezett elemeinek száma | szálkészlet munkaszála belépett – befejezett elemek – darabszám | Darabszám | A szál készlete befejezte a munkaelemek darabszámát. |
>| Aktív Időzítők száma               | aktív – időzítő – darabszám               | Darabszám | A jelenleg aktív Időzítők száma. Az aktív időzítő olyan, amely a jövőben egy adott időpontban van regisztrálva, és még nem lett megszakítva. |

További információ: DotNet- [számlálók](/dotnet/core/diagnostics/dotnet-counters).

### <a name="request"></a>Kérés
>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| tomcat. Global. elküldve | tomcat. Global. elküldve | Bájt | Elküldve a Tomcat-webkiszolgálók mennyisége |
>| tomcat. Global. Received | tomcat. Global. Received | Bájt | A lekéréses tomcat-webkiszolgáló mennyisége |
>| tomcat. Global. Request. Total. Count | tomcat. Global. Request (összesen-Count) | Darabszám | A Tomcat webkiszolgáló által feldolgozott kérelmek teljes száma |
>| tomcat. Global. Request. max | tomcat. Global. Request. max | Ezredmásodpercben | A Tomcat-webkiszolgáló maximális ideje a kérelem feldolgozására |

### <a name="request-net"></a>Kérelem (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|------|-----------------------------|------|---------|
>| Kérések száma másodpercenként | kérelmek/másodperc | Darabszám | Kérelmek gyakorisága. |
>| Összes kérelem | kérelmek összesen | Darabszám | Kérelmek száma összesen |
>| Aktuális kérések | aktuális – kérelmek | Darabszám | A jelenlegi kérések száma. |
>| Sikertelen kérelmek | Sikertelen – kérelmek | Darabszám | Sikertelen kérelmek száma. |

További információ: DotNet- [számlálók](/dotnet/core/diagnostics/dotnet-counters).

### <a name="session"></a>Munkamenet
>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| tomcat. Sessions. Active. max | tomcat. Sessions. Active. max | Darabszám | Egy időben aktív munkamenetek maximális száma |
>| tomcat. Sessions. Alive. max | tomcat. Sessions. Alive. max | Ezredmásodpercben | A lejárt munkamenet élettartamának leghosszabb ideje (másodpercben) |
>| tomcat. Sessions. created | tomcat. Sessions. created | Darabszám | A létrehozott munkamenetek száma |
>| tomcat. Sessions. lejárt | tomcat. Sessions. lejárt | Darabszám | Lejárt munkamenetek száma |
>| tomcat. Sessions. elutasítva | tomcat. Sessions. elutasítva | Darabszám | Azon munkamenetek száma, amelyek nem lettek létrehozva, mert elérte az aktív munkamenetek maximális számát. |
>| tomcat. Sessions. Active. Current | tomcat. Sessions. Active. Current | Darabszám | Tomcat-munkamenet aktív száma |

## <a name="see-also"></a>Lásd még

* [Gyors útmutató: Azure Spring Cloud-alkalmazások figyelése naplókkal, metrikákkal és nyomkövetéssel](spring-cloud-quickstart-logs-metrics-tracing.md)

* [Bevezetés az Azure Metrikaböngésző használatába](../azure-monitor/essentials/metrics-getting-started.md)

* [Naplók és mérőszámok elemzése diagnosztikai beállításokkal](./diagnostic-services.md)

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: tavaszi Felhőbeli erőforrások figyelése riasztások és műveleti csoportok használatával](./spring-cloud-tutorial-alerts-action-groups.md)

* [Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz](./spring-cloud-quotas.md)
