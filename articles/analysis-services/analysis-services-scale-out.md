---
title: Azure Analysis Services horizontális felskálás| Microsoft Docs
description: Replikálja Azure Analysis Services kiszolgálókat horizontális felskálával. Az ügyféllekérdezések ezután több lekérdezésreplika között oszthatók el egy horizontálisan felskálált lekérdezéskészletben.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2385cb811e322bd44daefa03d821b2ae47e0652
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769254"
---
# <a name="azure-analysis-services-scale-out"></a>Az Azure Analysis Services horizontális felskálázása

A horizontális felskálával az ügyféllekérdezések egy lekérdezéskészlet több lekérdezésreplikába *is eloszthatók,* ami csökkenti a válaszidőt a magas lekérdezési feladatok során.  A feldolgozást el is különítheti a lekérdezéskészlettől, így biztosítva, hogy a feldolgozási műveletek ne befolyásolják hátrányosan az ügyféllekérdezéseket. A horizontális felskálasztás konfigurálható a Azure Portal vagy a Analysis Services REST API.

A horizontális felskálás a Standard tarifacsomagban található kiszolgálókhoz érhető el. Minden lekérdezésreplikának ugyanaz a számlázása, mint a kiszolgálónak. Az összes lekérdezésreplika ugyanabban a régióban jön létre, mint a kiszolgáló. A konfigurálható lekérdezésreplikák száma a kiszolgáló régiójától függően korlátozott. További információ: Rendelkezésre állás [régiónként.](analysis-services-overview.md#availability-by-region) A horizontális felskálás nem növeli a kiszolgáló számára rendelkezésre álló memória mennyiségét. A memória növeléséhez frissítenie kell a tervet. 

## <a name="why-scale-out"></a>Miért érdemes horizontális felskálát?

Egy tipikus kiszolgálótelepítésben az egyik kiszolgáló feldolgozó kiszolgálóként és lekérdezési kiszolgálóként is szolgál. Ha a kiszolgálón található modelleken futtatott ügyféllekérdezések száma meghaladja a kiszolgáló tervéhez szükséges lekérdezésfeldolgozó egységek (QPU) számát, vagy a modell feldolgozása a magas lekérdezési számítási feladatokkal egy időben történik, a teljesítmény csökkenhet. 

A horizontális felskálával létrehozhat egy lekérdezéskészletet, amely legfeljebb hét további lekérdezésreplikával rendelkezik (összesen nyolc erőforrással, az elsődleges *kiszolgálóval együtt).* A lekérdezéskészletben található replikák számát a kritikus időpontokban a QPU-igényeknek megfelelően skálázhatja, és bármikor elválaszthat egy feldolgozókiszolgálót a lekérdezéskészlettől. 

A lekérdezési készletben található lekérdezésreplikák számától függetlenül a számítási feladatok nincsenek elosztva a lekérdezési replikák között. Az elsődleges kiszolgáló szolgál feldolgozó kiszolgálóként. A lekérdezésreplikák csak az elsődleges kiszolgáló és a lekérdezéskészlet egyes replikái között szinkronizált modelladatbázisok lekérdezéseit szolgálják ki. 

A felméretezés akár öt percet is igénybe vehet, amíg az új lekérdezésreplikák fokozatosan bekerülnek a lekérdezéskészletbe. Ha az összes új lekérdezési replika működik, az új ügyfélkapcsolatok terheléselosztása a lekérdezéskészlet erőforrásai között történik. A meglévő ügyfélkapcsolatok nem módosulnak ahhoz az erőforráshoz, amelyhez jelenleg csatlakoznak. A leméretezéskor a lekérdezéskészletből eltávolított lekérdezéskészlet-erőforráshoz meglévő ügyfélkapcsolatok leállnak. Az ügyfelek újracsatlakozhatnak egy másik lekérdezéskészlet-erőforráshoz.

## <a name="how-it-works"></a>Működés

A horizontális felskálazás első konfigurálásakor az elsődleges  kiszolgálón található modelladatbázisok automatikusan szinkronizálódnak az új lekérdezéskészlet új replikáival. Az automatikus szinkronizálás csak egyszer történik meg. Az automatikus szinkronizálás során a rendszer átmásolja az elsődleges kiszolgáló (blobtárolóban tárolt, titkosítva tárolt) adatfájlokat egy második helyre, amely szintén titkosítva van a blobtárolóban. A lekérdezéskészlet replikái ezután *a* második fájlkészletből származó adatokkal vannak hidratálva. 

Bár az automatikus szinkronizálás csak akkor történik meg, amikor először skáláz fel horizontálisan egy kiszolgálót, manuális szinkronizálást is végezhet. A szinkronizálás biztosítja, hogy a lekérdezéskészlet replikáiban található adatok megegyeznek az elsődleges kiszolgáló replikáival. Az elsődleges kiszolgálón a feldolgozási (frissítési) modellek feldolgozásakor szinkronizálást kell végezni a *feldolgozási* műveletek befejezése után. Ez a szinkronizálás átmásolja a frissített adatokat az elsődleges kiszolgáló blobtárolóban lévő fájljaiból a második fájlkészletbe. A lekérdezéskészlet replikái ezután a blobtároló második fájlkészletének frissített adataival vannak hidratálva. 

Egy későbbi felskálás művelet végrehajtásakor, például a lekérdezéskészletben lévő replikák számának kettőről ötre való növelésekor az új replikák a blobtároló második fájlkészletének adataival vannakhidratálva. Nincs szinkronizálás. Ha a felméretezés után szinkronizálást hajt végre, a lekérdezéskészletben található új replikák kétszer lesznek rehidratálva – redundáns rehidratálást eredményeznek. Egy későbbi felskálásos művelet végrehajtásakor fontos szem előtt tartani a következőt:

* Végezzen szinkronizálást a *felskálás* művelet előtt, hogy elkerülje a hozzáadott replikák redundáns rehidratálását. Az egyidejűleg futó szinkronizációs és horizontális felskálás műveletek nem engedélyezettek.

* A feldolgozási és  a horizontális felskálásos műveletek automatizálása során fontos, hogy először az elsődleges kiszolgálón feldolgozzunk adatokat, majd szinkronizálást hajtsunk végre, majd végrehajtsunk egy felskálás műveletet. Ez a sorozat minimális hatással van a QPU- és memória-erőforrásokra.

* A felskálásos műveletek során a lekérdezéskészletben található összes kiszolgáló, az elsődleges kiszolgálót is beleértve, átmenetileg offline állapotban van.

* A szinkronizálás akkor is engedélyezett, ha a lekérdezéskészletben nincsenek replikák. Ha nulláról egy vagy több olyan replikára skáláz, amely az elsődleges kiszolgálón található feldolgozási műveletből származó új adatokat tartalmaz, először hajtsa végre a szinkronizálást úgy, hogy a lekérdezéskészletben nincsenek replikák, majd horizontális felskálás. A felméretezés előtti szinkronizálással elkerülhető az újonnan hozzáadott replikák redundáns rehidratációja.

* Amikor töröl egy modelladatbázist az elsődleges kiszolgálóról, az nem törlődik automatikusan a lekérdezéskészlet replikáiból. Szinkronizálási műveletet kell végrehajtania a [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell-paranccsal, amely eltávolítja az adatbázis/fájlokat a replika megosztott blobtároló helyéről, majd törli a modelladatbázist a lekérdezéskészlet replikáin. Annak megállapításához, hogy a modelladatbázis létezik-e a lekérdezéskészlet  replikáiban, de az elsődleges kiszolgálón nem, győződjön meg arról, hogy a Feldolgozó kiszolgáló a készlet lekérdezéstől beállítása **Igen.** Ezután az SSMS használatával csatlakozzon az elsődleges kiszolgálóhoz a minősítővel, és nézze `:rw` meg, hogy létezik-e az adatbázis. Ezután csatlakozzon a lekérdezéskészlet replikáihoz úgy, hogy a minősítő nélkül csatlakozik, hogy lássa, létezik-e ugyanaz az `:rw` adatbázis. Ha az adatbázis létezik a lekérdezéskészlet replikáiban, de az elsődleges kiszolgálón nem, futtatassa a szinkronizálási műveletet.   

* Amikor az elsődleges kiszolgálón található adatbázist névre ad, egy további lépésre van szükség annak biztosításához, hogy az adatbázis megfelelően legyen szinkronizálva a replikákra. Az átnevezés után végezzen szinkronizálást a [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) paranccsal, és adja meg a paramétert `-Database` a régi adatbázisnévvel. Ez a szinkronizálás eltávolítja az adatbázist és a régi nevű fájlokat az összes replikából. Ezután hajtson végre egy másik szinkronizálást, és adja `-Database` meg a paramétert az új adatbázisnévvel. A második szinkronizálás átmásolja az újonnan elnevezett adatbázist a második fájlkészletbe, éshidratálja az összes replikát. Ezek a szinkronizálások nem hajthatóak végre a modell szinkronizálása paranccsal a portálon.

### <a name="synchronization-mode"></a>Szinkronizálási mód

Alapértelmezés szerint a lekérdezésreplikák teljes mértékben rehidratálva vannak, nem növekményesen. A rehidratálás szakaszokban történik. A rendszer leválasztja őket, és egyszerre kettőhöz csatolja őket (feltéve, hogy legalább három replika van), hogy legalább egy replika mindig online állapotban legyen a lekérdezésekhez. Bizonyos esetekben előfordulhat, hogy a folyamat során az ügyfeleknek újra kell csatlakozniuk az egyik online replikához. A **ReplicaSyncMode beállítással** megadhatja, hogy a lekérdezésreplikák szinkronizálása párhuzamosan történik. A párhuzamos szinkronizálás a következő előnyöket biztosítja: 

- Jelentős csökkenés a szinkronizálási időben. 
- A replikákon lévő adatok nagyobb valószínűséggel lesznek konzisztensek a szinkronizálási folyamat során. 
- Mivel az adatbázisok a szinkronizálási folyamat során az összes replikán online vannak, az ügyfeleknek nem kell újracsatlakozni. 
- A memóriában tárolt gyorsítótár növekményesen frissül csak a módosított adatokkal, ami gyorsabb lehet, mint a modell teljes rehidratálása. 

#### <a name="setting-replicasyncmode"></a>A ReplicaSyncMode beállítása

Az SSMS használatával állítsa be a ReplicaSyncMode tulajdonságot a Speciális tulajdonságok között. Lehetséges értékek: 

- `1` (alapértelmezett): Teljes replikaadatbázis-rehidratálás szakaszokban (növekményes). 
- `2`: Optimalizált szinkronizálás párhuzamosan. 

![RelicaSyncMode beállítás](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

A **ReplicaSyncMode=2** beállításakor a lekérdezésreplikák a gyorsítótár frissítésének mértékétől függően további memóriát is igénybehetnek. Ahhoz, hogy az adatbázis elérhető maradjon a lekérdezésekhez, attól függően, hogy  az adatok mekkora része változott, a művelethez akár meg is duplázhatja a replikán található memóriát, mivel a régi és az új szegmensek is egyszerre vannak a memóriában. A replikacsomópontok memóriafoglalása megegyezik az elsődleges csomópont memóriakiosztásának megegyezik, és általában további memória áll az elsődleges csomóponton a frissítési műveletekhez, ezért nem valószínű, hogy a replikák elfogynak a memóriából. Emellett a gyakori forgatókönyv szerint az adatbázis növekményesen frissül az elsődleges csomóponton, ezért a memória kétszeresére vonatkozó követelmény szokatlan lehet. Ha a szinkronizálási művelet memória nélkül hibát jelez, újrapróbálkozás az alapértelmezett technikával (egyszerre kettő csatolása/leválasztása). 

### <a name="separate-processing-from-query-pool"></a>A feldolgozás elkülönül a lekérdezéskészlettől

A feldolgozási és lekérdezési műveletek maximális teljesítménye érdekében választhat, hogy elkülöníti a feldolgozó kiszolgálót a lekérdezéskészlettől. A rendszer az új ügyfélkapcsolatokat csak a lekérdezéskészletben található lekérdezésreplikákhoz rendeli hozzá. Ha a feldolgozási műveletek csak egy rövid ideig tart, dönthet úgy, hogy csak a feldolgozási és szinkronizálási műveletek végrehajtásához szükséges ideig választja el a feldolgozó kiszolgálót a lekérdezéskészlettől, majd visszaveszi a lekérdezéskészletbe. Ha elkülöníti a feldolgozó kiszolgálót a lekérdezéskészlettől, vagy újra hozzáadja a lekérdezéskészlethez, a művelet befejezése akár öt percet is igénybe vehet.

## <a name="monitor-qpu-usage"></a>QPU-használat figyelése

Annak megállapításához, hogy szükség van-e a kiszolgáló horizontális felméretére, [](analysis-services-monitor.md) a Metrikák használatával Azure Portal a kiszolgálót. Ha a QPU rendszeresen túllépi a maximális korlátot, az azt jelenti, hogy a modellekkel kapcsolatos lekérdezések száma meghaladja a csomag QPU-korlátját. A Lekérdezéskészlet feladatsorának hossza metrika akkor is nő, ha a lekérdezési szálkészlet várólistájának lekérdezései száma meghaladja a rendelkezésre álló QPU-t. 

Egy másik jó mérőszám, amelyet figyelni lehet, az átlagos QPU ServerResourceType szerint. Ez a metrika az elsődleges kiszolgáló átlagos QPU-ját hasonlítja össze a lekérdezéskészletével. 

![Lekérdezési horizontális felskálás metrika](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**QPU konfigurálása ServerResourceType szerint**

1. A Metrika vonaldiagramon kattintson a **Metrika hozzáadása elemre.** 
2. Az **ERŐFORRÁS** mezőben válassza ki a kiszolgálót, majd a METRIKA NÉVTERÉBEN válassza Analysis Services standard metrikákat, majd **a METRIKA** mezőben válassza a **QPU** lehetőséget, majd az ÖSSZESÍTÉS mezőben **válassza** az Átlag **lehetőséget.** 
3. Kattintson **a Felosztás alkalmazása elemre.** 
4. A **VALUES (ÉRTÉKEK)** mezőben válassza a **ServerResourceType (Kiszolgálóforrástípus) lehetőséget.**  

### <a name="detailed-diagnostic-logging"></a>Részletes diagnosztikai naplózás

A Azure Monitor a horizontálisan felskálált kiszolgálói erőforrások részletesebb diagnosztikákhoz való használatával. A naplók segítségével Log Analytics-lekérdezésekkel kiszolgáló és replika szerint használhatja a QPU-t és a memóriát. További tudnivalókért tekintse meg a diagnosztikai naplózással kapcsolatos [Analysis Services példát.](analysis-services-logging.md#example-queries)


## <a name="configure-scale-out"></a>Horizontális felskálázás konfigurálása

### <a name="in-azure-portal"></a>A Azure Portal

1. A portálon kattintson a **Horizontális felskálás elemre.** A csúszkával válassza ki a lekérdezésreplikák kiszolgálóinak számát. A replikák választott száma a meglévő kiszolgálón felüli.  

2. A **Separate the processing server from the querying pool**(A feldolgozó kiszolgáló és a lekérdezési készlet elválasztása a lekérdezési készlettől) beállításban válassza az Igen lehetőséget, hogy kizárja a feldolgozó kiszolgálót a lekérdezési kiszolgálókból. Az [alapértelmezett kapcsolati](#connections) sztringet használó (nélküli) ügyfélkapcsolatok a lekérdezéskészlet `:rw` replikáira vannak átirányítva. 

   ![Felskálás csúszka](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Az **új lekérdezésreplikák** kiszolgálóinak üzembe mentéséhez kattintson a Mentés gombra. 

Amikor első alkalommal konfigurál felméretezést egy kiszolgálóhoz, az elsődleges kiszolgálón található modellek automatikusan szinkronizálódnak a lekérdezéskészlet replikáival. Az automatikus szinkronizálás csak egyszer történik meg, amikor először konfigurálja a horizontális felskálát egy vagy több replikára. Az ugyanazon a kiszolgálón található replikák számának későbbi módosításai nem indítják el *az újabb automatikus szinkronizálást.* Az automatikus szinkronizálás nem történik meg újra, még akkor sem, ha a kiszolgálót nulla replikára állítja, majd ismét bármilyen számú replikára méretez. 

## <a name="synchronize"></a>Szinkronizálás 

A szinkronizálási műveleteket manuálisan kell végrehajtani, vagy a REST API.

### <a name="in-azure-portal"></a>A Azure Portal

A **Modell szinkronizálása** > áttekintési > **oldalon.**

![Szinkronizálás ikon](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

Használja a **szinkronizálási** műveletet.

#### <a name="synchronize-a-model"></a>Modell szinkronizálása   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Szinkronizálási állapot lekért állapota  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Visszatérési állapotkódok:


|Code  |Description  |
|---------|---------|
|-1     |  Érvénytelen       |
|0     | Replikáló        |
|1     |  Rehidratálás       |
|2     |   Befejeződött       |
|3     |   Sikertelen      |
|4     |    Véglegesítése     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell használata előtt telepítse vagy frissítse a [legújabb Azure PowerShell modult.](/powershell/azure/install-az-ps) 

A szinkronizálás futtatásához használja a [Sync-AzAnalysisServicesInstance et.](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)

A lekérdezésreplikák számának beállítására használja a [Set-AzAnalysisServicesServer parancsot.](/powershell/module/az.analysisservices/set-azanalysisservicesserver) Adja meg a választható `-ReadonlyReplicaCount` paramétert.

A feldolgozó kiszolgáló lekérdezéskészlettől való elválasztához használja a [Set-AzAnalysisServicesServer parancsot.](/powershell/module/az.analysisservices/set-azanalysisservicesserver) Adja meg a használni `-DefaultConnectionMode` kívánt opcionális `Readonly` paramétert.

További információ: [Egyszerű szolgáltatás használata az Az.AnalysisServices modullal.](analysis-services-service-principal.md#azmodule)

## <a name="connections"></a>Kapcsolatok

A kiszolgáló Áttekintés lapján két kiszolgálónév található. Ha még nem konfigurált horizontális felskálát egy kiszolgálóhoz, mindkét kiszolgálónév ugyanúgy működik. Miután konfigurálta a horizontális felskálát egy kiszolgálóhoz, a kapcsolat típusától függően meg kell adnia a megfelelő kiszolgálónevet. 

A végfelhasználói ügyfélkapcsolatok, például a Power BI Desktop, az Excel és az egyéni alkalmazások esetében használja a **Kiszolgálónév nevet.** 

Az SSMS, Visual Studio és kapcsolati sztringek esetében a PowerShellben, az Azure-függvényalkalmazásokban és az AMO-ban használja a **felügyeleti kiszolgáló nevét.** A felügyeleti kiszolgáló neve tartalmaz egy speciális `:rw` (írási/olvasási) minősítőt. Minden feldolgozási művelet az (elsődleges) felügyeleti kiszolgálón történik.

![Kiszolgálónevek](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Horizontális felskálás, leskálás és horizontális felskálás

A több replikával rendelkező kiszolgálók tarifacsomagját módosíthatja. Ugyanaz a tarifacsomag vonatkozik az összes replikára. A skálázási művelet először egyszerre le fogja lehívni az összes replikát, majd az összes replikát az új tarifacsomagban.

## <a name="troubleshoot"></a>Hibaelhárítás

**Probléma:** A felhasználók "ReadOnly" kapcsolati módban nem találják a kiszolgáló **\<Name of the server> példányát hibaüzenetet.**

**Megoldás:** Amikor a **Feldolgozó** kiszolgáló elválasztása a lekérdezési készlettől lehetőséget választja, az alapértelmezett kapcsolati sztringet (anélkül) használó ügyfélkapcsolatok át vannak irányítva a lekérdezéskészlet `:rw` replikáira. Ha a lekérdezéskészletben található replikák még nincsenek online, mert a szinkronizálás még nem fejeződött be, az átirányított ügyfélkapcsolatok meghiúsulhatnak. A sikertelen kapcsolatok elkerülése érdekében legalább két kiszolgálónak kell lennie a lekérdezéskészletben szinkronizálás végrehajtásakor. A rendszer minden kiszolgálót egyenként szinkronizál, míg mások online állapotban maradnak. Ha a feldolgozás során úgy dönt, hogy nem adja hozzá a feldolgozó kiszolgálót a lekérdezéskészlethez, eltávolíthatja a készletből feldolgozásra, majd a feldolgozás befejezése után, de szinkronizálás előtt hozzáadhatja a készlethez. A szinkronizálási állapot figyelése memória- és QPU-metrikák használatával.



## <a name="related-information"></a>Kapcsolódó információk

[Kiszolgálómetrikák figyelése](analysis-services-monitor.md)   
[A Azure Analysis Services](analysis-services-manage.md)
