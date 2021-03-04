---
title: Egyéni mezők a Azure Monitorban (előzetes verzió) | Microsoft Docs
description: A Azure Monitor egyéni mezők funkciója lehetővé teszi, hogy saját kereshető mezőket hozzon létre egy olyan Log Analytics-munkaterületen lévő rekordokból, amelyek egy összegyűjtött rekord tulajdonságaihoz vannak hozzáadva.  Ez a cikk az egyéni mezők létrehozásának folyamatát ismerteti, és részletes útmutatót nyújt egy adott eseményről.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: e7ddeaf1273a9e3b5608336b24c07fad3c3f4525
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031038"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Egyéni mezők létrehozása Log Analytics munkaterületen Azure Monitor (előzetes verzió)

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan elemezheti a szöveges adatokat egy Log Analytics-munkaterületen a gyűjtött adatok alapján. Azt javasoljuk, hogy a szöveges adatok elemzését a lekérdezési szűrőben a következő témakörben ismertetett útmutatást követve olvassa el: [Azure monitor](./parse-text.md). Számos előnyt biztosít az egyéni mezők használata során.

> [!IMPORTANT]
> Az egyéni mezők növelik a Log Analytics munkaterületen összegyűjtött adatok mennyiségét, ami növelheti a költségeket. A részletekért lásd: [a használat és a költségek kezelése Azure monitor naplókkal](./manage-cost-storage.md#pricing-model) .

A Azure Monitor **Egyéni mezők** funkciója lehetővé teszi, hogy a saját kereshető mezőinek hozzáadásával kiterjessze a log Analytics munkaterületen meglévő rekordokat.  Az egyéni mezők automatikusan ki lesznek töltve az ugyanabban a rekordban lévő más tulajdonságokból kinyert adatokból.

![Az ábrán egy Log Analytics munkaterület módosított rekordjához társított eredeti rekord látható, amely a módosított rekordban szereplő eredeti tulajdonsághoz hozzáadott tulajdonság-érték párokat tartalmazza.](media/custom-fields/overview.png)

Az alábbi minta rekord például hasznos adatokkal van elárasztva az esemény leírásában. Az adatok egy külön tulajdonságba való kinyerése lehetővé teszi, hogy a rendezési és szűrési műveletekhez elérhető legyen.

![Minta kibontása](media/custom-fields/sample-extract.png)

> [!NOTE]
> Az előzetes verzióban a munkaterületen legfeljebb 100 egyéni mező lehet.  Ezt a korlátot a rendszer kibontja, ha a funkció eléri az általános elérhetőséget.

## <a name="creating-a-custom-field"></a>Egyéni mező létrehozása
Egyéni mező létrehozásakor Log Analytics meg kell értenie, hogy mely adatokat kell használni az érték feltöltéséhez.  A FlashExtract nevű Microsoft Research technológiáját használja az adatok gyors azonosítására.  Ahelyett, hogy explicit utasításokat kellene megadnia, Azure Monitor megismerheti az Ön által megadott példákból kinyerni kívánt adatokkal kapcsolatos információkat.

A következő szakaszokban megtekintheti az egyéni mezők létrehozásának eljárását.  A cikk alján egy minta-kinyerési útmutató látható.

> [!NOTE]
> Az egyéni mező a megadott feltételeknek megfelelő rekordokkal van feltöltve a Log Analytics munkaterületre, így csak az egyéni mező létrehozása után gyűjtött rekordokban fog megjelenni.  Az egyéni mező nem lesz hozzáadva olyan rekordokhoz, amelyek már szerepelnek az adattárban a létrehozásakor.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>1. lépés – az egyéni mezőt tartalmazó rekordok azonosítása
Az első lépés az egyéni mezőt lekérdező rekordok azonosítása.  Első lépésként egy [standard szintű log-lekérdezést](./log-query-overview.md) kell választania, majd ki kell választania egy olyan rekordot, amely a Azure monitor által megtanulni kívánt modell.  Ha megadja, hogy az adatok kinyerése egy egyéni mezőbe történik, megnyílik a **mező kibontása varázsló** , ahol érvényesítheti és pontosíthatja a feltételeket.

1. Nyissa meg a **naplókat** , és használjon egy lekérdezést az egyéni mezőt tartalmazó [rekordok lekéréséhez](./log-query-overview.md) .
2. Válassza ki azt a rekordot, amelyet a Log Analytics az adatok kinyerésére szolgáló modellként fog működni az egyéni mező feltöltéséhez.  Azonosíthatja a rekordból kiolvasni kívánt adatokat, és a Log Analytics ezeket az információkat fogja használni az egyéni mezőnek az összes hasonló rekordhoz való feltöltéséhez.
3. Bontsa ki a rekord tulajdonságait, kattintson a rekord legfelső tulajdonságának bal oldalán található ellipszisre, majd válassza a **mezők kinyerése a következőből** lehetőséget.
4. Megnyílik a **mező kibontása varázsló** , és a kiválasztott rekord a **fő példa** oszlopban jelenik meg.  Az egyéni mező a kiválasztott tulajdonságok azonos értékeivel lesz definiálva.  
5. Ha a kijelölés nem pontosan az, amit szeretne, válassza a további mezők lehetőséget a feltételek szűkítéséhez.  Ha módosítani szeretné a feltételekhez tartozó mezőértékeket, meg kell szakítania, és ki kell választania egy másik, a kívánt feltételeknek megfelelő rekordot.

### <a name="step-2---perform-initial-extract"></a>2. lépés – kezdeti Kibontás végrehajtása.
Miután azonosította az egyéni mezőt tartalmazó rekordokat, azonosítsa a kiolvasni kívánt adatokat.  A Log Analytics ezeket az információkat a hasonló rekordokban lévő hasonló minták azonosítására fogja használni.  A lépés után érvényesítheti az eredményeket, és további részleteket adhat meg a Log Analytics számára az elemzésében való használathoz.

1. Jelölje ki a minta rekordban azt a szöveget, amelyet fel szeretne tölteni az egyéni mezővel.  Ekkor megjelenik egy párbeszédpanel, ahol megadhatja a mező nevét és adattípusát, és elvégezheti a kezdeti kinyerést.  A rendszer automatikusan hozzáfűzi a **\_ CF** karaktereket.
2. Az összegyűjtött rekordok elemzéséhez kattintson a **Kibontás** elemre.  
3. Az **Összefoglalás** és a **keresési eredmények** szakaszban a kivonat eredményei láthatók, így ellenőrizheti annak pontosságát.  Az **Összefoglalás** megjeleníti a rekordok azonosítására szolgáló feltételeket és az azonosított adatértékek számát.  A **keresési eredmények** a feltételnek megfelelő rekordok részletes listáját jelenítik meg.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>3. lépés – a kinyerés pontosságának ellenőrzése és egyéni mező létrehozása
Miután elvégezte a kezdeti kibontást, Log Analytics megjeleníti az eredményeket a már összegyűjtött adatok alapján.  Ha az eredmények pontosak, akkor a további munka nélkül hozhatja létre az egyéni mezőt.  Ha nem, akkor pontosíthatja az eredményeket, így a Log Analytics javíthatja a logikáját.

1. Ha a kezdeti kivonat bármely értéke nem megfelelő, kattintson a nem pontos rekord melletti **Szerkesztés** ikonra, és válassza a **kiemelés módosítása** lehetőséget a kijelölés módosításához.
2. A bejegyzést a rendszer a **fő példa** alatti **További példák** szakaszra másolja.  A kiemelés itt állítható be, hogy könnyebben Log Analytics a kiválasztást.
3. Kattintson a **Kibontás** elemre az új információk használatához az összes meglévő rekord kiértékeléséhez.  Az eredmények módosíthatók az új intelligencián alapuló, az imént módosított rekordokon kívül is.
4. Folytassa a javítások hozzáadását, amíg a kivonat összes rekordja helyesen azonosítja az adatokat az új egyéni mező feltöltéséhez.
5. Kattintson a **Kibontás mentése** gombra, ha elégedett az eredményekkel.  Az egyéni mező már definiálva van, de még nem lesz hozzáadva a rekordokhoz.
6. Várjon, amíg a megadott feltételeknek megfelelő új rekordok begyűjthetők, majd futtassa újra a naplóbeli keresést. Az új rekordoknak az egyéni mezővel kell rendelkezniük.
7. Használja az egyéni mezőt, mint bármely más rekord tulajdonságot.  Felhasználhatja az adatokat összesíteni és csoportosítani, és még az új elemzések létrehozásához is használhatja azt.

## <a name="viewing-custom-fields"></a>Egyéni mezők megtekintése
A felügyeleti csoport összes egyéni mezőjének listáját a Azure Portal Log Analytics munkaterület **Speciális beállítások** menüjében tekintheti meg.  Válassza az **adat** , majd az **Egyéni mezők** lehetőséget a munkaterület összes egyéni mezőjének listájához.  

![Egyéni mezők](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Egyéni mező eltávolítása
Az egyéni mezők két módon távolíthatók el.  Az első az **Eltávolítás** lehetőség az egyes mezőknél a teljes lista megtekintésekor a fent leírtak szerint.  A másik módszer egy rekord beolvasása, majd a mező bal oldalán lévő gombra kattintva.  A menüben lehetősége lesz eltávolítani az egyéni mezőt.

## <a name="sample-walkthrough"></a>Mintaútmutató
Az alábbi szakasz végigvezeti az egyéni mezők létrehozásának teljes példáján.  Ez a példa Kinyeri a szolgáltatás nevét a Windows eseményeiben, amelyek a szolgáltatás változó állapotát jelzik.  Ez a Windows rendszerű számítógépek indításakor a szolgáltatásvezérlő által létrehozott eseményekre támaszkodik.  Ha ezt a példát szeretné követni, [a rendszernaplóhoz kapcsolódó információs eseményeket kell összegyűjtenie](../agents/data-sources-windows-events.md).

A következő lekérdezéssel adja vissza az összes olyan eseményt a szolgáltatásvezérlő kezelőjéből, amelynek 7036-as AZONOSÍTÓJÚ eseménye a szolgáltatás elindítását vagy leállítását jelző esemény.

![A képernyőképen egy eseményforrás és egy azonosító lekérdezése látható.](media/custom-fields/query.png)

Ezután kiválasztunk minden olyan rekordot, amely a 7036-es AZONOSÍTÓJÚ eseménnyel rendelkezik.

![Forrásoldali rekord](media/custom-fields/source-record.png)

Az egyéni mezőket a Top tulajdonság melletti ellipszisre kattintva definiáljuk.

![Mezők kinyerése](media/custom-fields/extract-fields.png)

Megnyílik a **mező kibontása varázsló** , és az **Eseménynapló** és a **Napszállta** mezők ki vannak választva a **fő példa** oszlopban.  Ez azt jelzi, hogy az egyéni mező a rendszernaplóban az 7036-es azonosítójú eseményekhez lesz definiálva.  Ez elegendő, ezért nem kell kijelölni más mezőket.

![Fő példa](media/custom-fields/main-example.png)

Kiemeljük a szolgáltatás nevét a **RenderedDescription** tulajdonságban, és a **szolgáltatás használatával** azonosítjuk a szolgáltatás nevét.  Az egyéni mező neve **Service_CF** lesz. Ebben az esetben a mező típusa karakterlánc, így változatlan marad.

![Mező címe](media/custom-fields/field-title.png)

Láthatjuk, hogy a szolgáltatás neve megfelelően van azonosítva egyes rekordokhoz, de mások számára nem.   A **keresési eredmények** azt mutatják, hogy a **WMI-teljesítmény adapter** neve nem lett kiválasztva.  Az **Összefoglalás** azt mutatja, hogy egy rekordban azonosított **modulok telepítője** a **Windows-modulok telepítője** helyett.  

![Képernyőfelvétel: a keresési eredmények ablaktáblájában Kiemelt szolgáltatásnév, valamint az összegzésben az egyik helytelen szolgáltatásnév látható.](media/custom-fields/search-results-01.png)

Először a **WMI Performance adapter** rekordját használjuk.  A Szerkesztés ikonra kattintunk, majd **módosítjuk ezt a kiemelést**.  

![Kiemelés módosítása](media/custom-fields/modify-highlight.png)

Növeljük a kiemelést, hogy belefoglalja a Word **WMI** -t, majd futtassa újra a kinyerést.  

![További példa](media/custom-fields/additional-example-01.png)

Láthatjuk, hogy a **WMI-teljesítmény adapterhez** tartozó bejegyzések ki lettek javítva, és log Analytics is használták ezeket az információkat, hogy javítsa a **Windows-modul telepítőjének** rekordjait.

![Képernyőfelvétel: a keresési eredmények ablaktáblán Kiemelt teljes szolgáltatásnév, valamint az összegzésben Kiemelt helyes szolgáltatásnév.](media/custom-fields/search-results-02.png)

Most már futtathatunk egy lekérdezést, amely ellenőrzi, hogy létrejött-e a **Service_CF** , de még nincs hozzáadva rekordokhoz. Ennek az az oka, hogy az egyéni mező nem működik a meglévő rekordokkal szemben, ezért meg kell várni az új rekordok gyűjtését.

![Kezdeti darabszám](media/custom-fields/initial-count.png)

Némi idő elteltével a rendszer begyűjti az új eseményeket, láthatjuk, hogy a **Service_CF** mező már hozzá van adva a feltételnek megfelelő rekordokhoz.

![Végső eredmények](media/custom-fields/final-results.png)

Mostantól az egyéni mezőt is használhatja, mint bármely más rekord tulajdonságot.  Ennek szemléltetéséhez hozzunk létre egy lekérdezést, amely az új **Service_CF** mező alapján csoportosítja a legaktívabb szolgáltatásokat.

![Csoportosítás lekérdezés szerint](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Következő lépések
* További információ a [lekérdezések létrehozásáról a](./log-query-overview.md) lekérdezésekhez egyéni mezők használatával a feltételekhez.
* Egyéni mezők használatával elemzett [Egyéni naplófájlok](../agents/data-sources-custom-logs.md) figyelése.