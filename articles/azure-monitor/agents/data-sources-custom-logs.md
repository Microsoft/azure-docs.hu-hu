---
title: Egyéni naplók gyűjtése Log Analytics-ügynökkel az Azure Monitorban
description: A Azure Monitor a Windows és Linux rendszerű számítógépeken is gyűjthet eseményeket a szöveges fájlokból.  Ez a cikk azt ismerteti, hogyan határozható meg az új egyéni napló és a Azure Monitorban létrehozott rekordok részletei.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: b66011ff41e1dd4a7439105862ca61355a95bd71
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042708"
---
# <a name="collect-custom-logs-with-log-analytics-agent-in-azure-monitor"></a>Egyéni naplók gyűjtése Log Analytics-ügynökkel az Azure Monitorban

A Azure Monitor Log Analytics ügynökének egyéni naplói adatforrása lehetővé teszi, hogy a Windows és a Linux rendszerű számítógépeken is gyűjtsön eseményeket a szöveges fájlokból. Számos alkalmazás a szabványos naplózási szolgáltatások, például a Windows-Eseménynapló vagy a syslog helyett szöveges fájlokba naplóz adatokat. Az adatgyűjtés után elemezheti az adatokat a lekérdezések egyes mezőibe, vagy kinyerheti az adatokat a gyűjteménybe az egyes mezőkbe.

> [!IMPORTANT]
> Ez a cikk az egyéni naplók összegyűjtését ismerteti a [log Analytics ügynökkel](./log-analytics-agent.md) , amely a Azure monitor által használt ügynökök egyike. Más ügynökök különböző adatokat gyűjtenek, és eltérően vannak konfigurálva. A rendelkezésre álló ügynökök és az összegyűjtött adatok listáját lásd: [Azure monitor ügynökök áttekintése](../agents/agents-overview.md) .

![Egyéni naplók gyűjtése](media/data-sources-custom-logs/overview.png)

A gyűjteni kívánt naplófájloknak meg kell egyezniük a következő feltételekkel.

- A naplónak soronként vagy egyetlen bejegyzéssel kell rendelkeznie, vagy az egyes bejegyzések elején az alábbi formátumok valamelyikének megfelelő időbélyeget kell használnia.

    ÉÉÉÉ-HH-NN ÓÓ: PP: MM<br>M/D/ÉÉÉÉ ÓÓ: PP: SS AM/DU<br>Mon DD, éééé óó: PP: SS<br />ÉÉHHNN óó: PP: mm<br />nnhhéé óó: PP: mm<br />MMM d óó: PP: SS<br />dd/MMM/éééé: óó: PP: SS ZZZ<br />éééé-hh-NNTóó: PP: ssK

- A naplófájl nem engedheti meg a körkörös naplózást vagy a napló elforgatását, ahol a fájl felülírása új bejegyzésekkel történik.
- A naplófájlnak ASCII vagy UTF-8 kódolást kell használnia.  Más formátumok, például az UTF-16 nem támogatottak.
- A Linux esetében az időzóna-átalakítás nem támogatott a naplók időbélyegzői esetében.

>[!NOTE]
> Ha duplikált bejegyzések vannak a naplófájlban, akkor a Azure Monitor begyűjti őket. A lekérdezés eredményei azonban inkonzisztensek lesznek, ha a szűrő eredményei több eseményt mutatnak, mint az eredmények száma. Fontos, hogy ellenőrizze a naplót annak megállapításához, hogy az azt létrehozó alkalmazás okozza-e ezt a viselkedést, és ha lehetséges, adja meg az egyéni napló-gyűjtemény definíciójának létrehozása előtt.  
>

>[!NOTE]
> A Log Analytics munkaterület a következő korlátokat támogatja:
> 
> * Csak 500 egyéni napló hozható létre.
> * A táblák legfeljebb 500 oszlopot támogatnak. 
> * Az oszlopnév legfeljebb 500 karakterből állhat. 
>

>[!IMPORTANT]
>Az egyéni naplók gyűjteménye megköveteli, hogy az alkalmazás a naplófájlt írásban kiürítse a napló tartalmát a lemezre. Ennek az az oka, hogy az egyéni napló-gyűjtemény a nyomon követett naplófájl fájlrendszer-változási értesítéseire támaszkodik.

## <a name="defining-a-custom-log"></a>Egyéni napló definiálása
Egyéni naplófájl definiálásához használja az alábbi eljárást.  Görgessen a cikk végére egy egyéni napló hozzáadására szolgáló minta áttekintéséhez.

### <a name="step-1-open-the-custom-log-wizard"></a>1. lépés Az egyéni napló varázsló megnyitása
Az egyéni napló varázsló a Azure Portal fut, és lehetővé teszi, hogy megadjon egy új egyéni naplót a gyűjtéshez.

1. A Azure Portal válassza a **log Analytics munkaterületek** > a munkaterület > **Speciális beállítások** lehetőséget.
2. Kattintson az   >  **Egyéni adatnaplók** elemre.
3. Alapértelmezés szerint a rendszer az összes konfigurációs módosítást automatikusan leküldi az összes ügynöknek. Linux-ügynökök esetében a rendszer egy konfigurációs fájlt küld a Fluent-adatgyűjtőnek.
4. Kattintson a **Hozzáadás +** elemre az egyéni napló varázsló megnyitásához.

### <a name="step-2-upload-and-parse-a-sample-log"></a>2. lépés Minta napló feltöltése és elemzése
Először töltse fel az egyéni napló mintáját.  A varázsló elemzi és megjeleníti a fájlban szereplő bejegyzéseket az érvényesítéshez.  Azure Monitor a megadott határolójelet fogja használni az egyes rekordok azonosításához.

Az **új sor** az alapértelmezett elválasztó, és a naplófájlok, amelyek soronként egy bejegyzést használnak.  Ha a sor a rendelkezésre álló formátumok egyikében egy dátummal és időponttal kezdődik, akkor megadhat egy **időbélyeg** -elválasztó karaktert, amely támogatja a több sorban átnyúló bejegyzéseket.

Időbélyeg-elválasztó használata esetén a Azure Monitorban tárolt egyes rekordok TimeGenerated tulajdonsága a naplófájlban a bejegyzéshez megadott dátummal és idővel lesz feltöltve.  Ha új elválasztó sort használ, akkor a TimeGenerated a bejegyzést Azure Monitor begyűjtött dátummal és időponttal tölti fel.

1. Kattintson a **Tallózás** gombra, és tallózással keresse meg a mintát.  Vegye figyelembe, hogy előfordulhat, hogy a gomb címkéje egyes böngészőkben a **fájl kiválasztása** .
2. Kattintson a **Tovább** gombra.
3. Az egyéni napló varázsló feltölti a fájlt, és felsorolja az általa azonosított rekordokat.
4. Módosítsa az új rekord azonosításához használt elválasztó karaktert, és válassza ki a naplófájlban a rekordokat legjobban azonosító elválasztót.
5. Kattintson a **Tovább** gombra.

### <a name="step-3-add-log-collection-paths"></a>3. lépés Napló-gyűjtemény elérési útjának hozzáadása
Meg kell adnia egy vagy több elérési utat az ügynökön, ahol megtalálhatja az egyéni naplót.  Megadhat egy adott elérési utat és nevet a naplófájlhoz, vagy megadhat egy elérési utat, amely helyettesítő karaktert tartalmaz a névhez. Ez olyan alkalmazásokat támogat, amelyek naponta új fájlt hoznak létre, vagy amikor egy fájl elér egy adott méretet. Egyetlen naplófájlhoz több elérési utat is megadhat.

Előfordulhat például, hogy egy alkalmazás minden nap létrehoz egy naplófájlt a névben szereplő dátummal együtt log20100316.txt. Az ilyen naplók mintája lehet a *log \* . txt* fájl, amely az alkalmazás elnevezési sémáját követő minden naplófájlra vonatkozni fog.

A következő táblázat példákat tartalmaz a különböző naplófájlok megadására szolgáló érvényes mintákra.

| Leírás | Elérési út |
|:--- |:--- |
| A *c:\logs mappa* összes fájlja. txt kiterjesztéssel a Windows-ügynökön |C:\logs mappa \\ \* . txt |
| A *c:\logs mappa* összes fájlja a log és a. txt kiterjesztéssel kezdődő névvel a Windows-ügynökön |C:\Logs\log \* . txt |
| A */var/log/audit* összes fájlja. txt kiterjesztéssel a Linux-ügynökön |/var/log/audit/*. txt |
| A */var/log/audit* összes fájlja a log és a. txt kiterjesztéssel kezdődő névvel Linux-ügynökön |/var/log/audit/log \* . txt |

1. Válassza a Windows vagy a Linux lehetőséget a hozzáadni kívánt elérési út megadásához.
2. Írja be az elérési utat, majd kattintson a **+** gombra.
3. Ismételje meg a folyamatot bármilyen további elérési útra.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>4. lépés: Adja meg a napló nevét és leírását
A rendszer a megadott nevet fogja használni a naplózási típushoz a fent leírtak szerint.  A szolgáltatás mindig _CL, hogy egyéni naplóként megkülönböztesse azt.

1. Írja be a napló nevét.  A **\_ CL** -utótagot a rendszer automatikusan megadja.
2. Adjon hozzá egy opcionális **leírást**.
3. Az egyéni napló definíciójának mentéséhez kattintson a **tovább** gombra.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>5. lépés Annak ellenőrzése, hogy az egyéni naplók gyűjtése folyamatban van-e
Akár egy órát is igénybe vehet, hogy egy új egyéni naplóból származó kezdeti adatok megjelenjenek Azure Monitorban.  Megkezdi a bejegyzések gyűjtését az egyéni naplót definiált pontról megadott elérési úton található naplókból.  A rendszer nem őrzi meg az egyéni napló létrehozásakor feltöltött bejegyzéseket, de a már meglévő bejegyzéseket gyűjti a megtalált naplófájlokban.

Miután Azure Monitor elkezdi a gyűjtést az egyéni naplóból, a rekordjai a napló lekérdezésével lesznek elérhetők.  Használja azt a nevet, amelyet az egyéni naplóhoz adott a lekérdezésben szereplő **típusként** .

> [!NOTE]
> Ha a lekérdezésből hiányzik a RawData tulajdonság, előfordulhat, hogy be kell állítania és újra meg kell nyitnia a böngészőt.

### <a name="step-6-parse-the-custom-log-entries"></a>6. lépés Az egyéni naplóbejegyzések értelmezése
A teljes naplóbejegyzés egyetlen, **RawData** nevű tulajdonságban lesz tárolva.  Valószínűleg el szeretné különíteni az egyes bejegyzések különböző adatait az egyes rekordok egyedi tulajdonságaiba. A **RawData** több tulajdonságba való elemzésének lehetőségeiről a [Azure monitorban található szöveges fájlok](../logs/parse-text.md) elemzése című témakörben talál további információt.

## <a name="removing-a-custom-log"></a>Egyéni napló eltávolítása
A Azure Portal az alábbi eljárással távolíthatja el a korábban definiált egyéni naplókat.

1. A munkaterület **speciális beállításainak** **adatok** menüjében válassza az **egyéni naplók** lehetőséget az összes egyéni napló listázásához.
2. Az eltávolításhoz kattintson az egyéni napló melletti **Eltávolítás** elemre.

## <a name="data-collection"></a>Adatgyűjtés
Azure Monitor minden egyéni naplóból körülbelül 5 percenként gyűjt új bejegyzéseket.  Az ügynök rögzíti a helyét minden olyan naplófájlban, amelyről gyűjt.  Ha az ügynök egy ideig offline állapotba kerül, akkor a Azure Monitor begyűjti azokat a bejegyzéseket, amelyekből az utolsó lemaradt, még akkor is, ha az ügynök offline állapotban van.

A naplóbejegyzés teljes tartalma egyetlen, **RawData** nevű tulajdonságba íródik.  Az egyes importált naplóbejegyzések több tulajdonságba való elemzéséhez tekintse meg a [szöveges adat Azure monitorban](../logs/parse-text.md) történő elemzését ismertető témakört.

## <a name="custom-log-record-properties"></a>Egyéni naplózási rekord tulajdonságai
Az egyéni naplók egy olyan típussal rendelkeznek, amely tartalmazza a megadott napló nevét, valamint az alábbi táblázatban szereplő tulajdonságokat.

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated |A rekord Azure Monitor általi gyűjtésének dátuma és időpontja.  Ha a napló időalapú határolójelet használ, akkor ez a bejegyzésből begyűjtött idő. |
| SourceSystem |Az ügynök típusa, amelyet a rendszer a rekordból gyűjtött. <br> OpsManager – Windows-ügynök, közvetlen kapcsolat vagy System Center Operations Manager <br> Linux – minden Linux-ügynök |
| RawData |Az összegyűjtött bejegyzés teljes szövege. Valószínűleg ezeket az adatelemzéseket az [egyes tulajdonságokkal szeretné elemezni](../logs/parse-text.md). |
| ManagementGroupName |A System Center-műveletek felügyeleti csoportjának neve, ügynökök kezelése.  Más ügynökök esetében ez az AOI-\<workspace ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Példa egyéni napló hozzáadására
Az alábbi szakasz végigvezeti egy egyéni napló létrehozásának példáján.  A begyűjtött minta naplója egyetlen bejegyzést tartalmaz minden egyes sorban egy dátummal és időponttal kezdődően, majd vesszővel tagolt mezőket a kód, az állapot és az üzenet számára.  Alább több minta bejegyzés látható.

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>Minta napló feltöltése és elemzése
A naplófájlok egyikét biztosítjuk, és láthatjuk, hogy milyen események lesznek összegyűjtve.  Ebben az esetben az új sor megfelelő elválasztó karakter.  Ha a napló egyetlen bejegyzése több sorra is terjedhet, akkor az időbélyeg-elválasztót is használni kell.

![Minta napló feltöltése és elemzése](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Napló-gyűjtemény elérési útjának hozzáadása
A naplófájlok a *C:\MyApp\Logs*-ben lesznek elhelyezve.  Minden nap egy új fájl jön létre, amelynek a neve tartalmazza a *appYYYYMMDD. log* nevű mintát.  A naplóhoz elegendő minta lenne a *C:\MyApp\Logs \\ \* . log*.

![Napló gyűjteményének elérési útja](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Adja meg a napló nevét és leírását
Az *MyApp_CL* nevet használjuk, és a **leírást** beírhatjuk.

![Napló neve](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Annak ellenőrzése, hogy az egyéni naplók gyűjtése folyamatban van-e
Az összegyűjtött naplóból származó összes rekord visszaküldéséhez a *MyApp_CL* egyszerű lekérdezését használjuk.

![Egyéni mezők nélküli naplózási lekérdezés](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Egyéni naplók alternatívái
Az egyéni naplók akkor hasznosak, ha az adatai megfelelnek a fent felsorolt feltételeknek, például a következő esetekben, amikor szüksége van egy másik stratégiára:

- Az adat nem felel meg a szükséges szerkezetnek, például az időbélyeg eltérő formátumú.
- A naplófájl nem felel meg a követelményeknek, például a fájlok kódolásának vagy a nem támogatott mappák struktúrájának.
- Az adatgyűjtési folyamat előtt az adatfeldolgozásra vagy-szűrésre van szükség. 

A következő alternatív stratégiákat kell figyelembe venni abban az esetben, ha az adatok nem gyűjthetők össze az egyéni naplókkal:

- Egyéni parancsfájl vagy más módszer használatával adatokat írhat a Windows- [eseményekre](data-sources-windows-events.md) vagy a [syslog](data-sources-syslog.md) -re, amelyeket a Azure monitor gyűjt. 
- Az adatküldés közvetlenül a Azure Monitor a http-adatgyűjtő [API](../logs/data-collector-api.md)használatával. 

## <a name="next-steps"></a>Következő lépések
* Az egyes importált naplóbejegyzések több tulajdonságba való elemzéséhez tekintse meg a [szöveges adat Azure monitorban](../logs/parse-text.md) történő elemzését ismertető témakört.
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../logs/log-query-overview.md) .