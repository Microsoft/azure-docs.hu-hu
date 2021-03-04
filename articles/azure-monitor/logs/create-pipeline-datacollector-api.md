---
title: Adatfolyamatok létrehozása a adatgyűjtő API használatával
description: A Azure Monitor HTTP-adatgyűjtő API-val hozzáadhat JSON-adatok a Log Analytics munkaterülethez bármely olyan ügyfélről, amely képes meghívni a REST API. Ez a cikk a fájlokban tárolt adatok automatizált módon történő feltöltését ismerteti.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: ab2e9c691f17b8f0891ecbc82ff42cd3529a1328
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031191"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Adatfolyamat létrehozása az adatgyűjtő API-val

A [Azure monitor adatgyűjtő API](data-collector-api.md) lehetővé teszi, hogy az egyéni naplókat a Azure monitor log Analytics munkaterületére importálja. Az egyetlen követelmény, hogy az adat JSON-formátumú, és 30 MB vagy kevesebb szegmensre legyen felosztva. Ez egy teljesen rugalmas mechanizmus, amely többféleképpen is csatlakoztatható: az alkalmazásból közvetlenül elküldött adatokból az egyszeri, alkalmi feltöltésekhez. Ez a cikk a gyakori forgatókönyvek egyes kiindulási pontjait vázolja fel: a fájlokban tárolt adatok rendszeres, automatizált módon történő feltöltésének szükségessége. Habár az itt bemutatott folyamat nem a legnagyobb teljesítményű, vagy más módon optimalizált, az a célja, hogy kiindulási pontként szolgáljon a saját termelési folyamatának kiépítése felé.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Probléma – példa
A cikk további részében megvizsgáljuk az oldal nézetének a Application Insights. A feltételezett forgatókönyvben a Application Insights SDK által alapértelmezés szerint gyűjtött földrajzi adatokat szeretnénk összekapcsolni a világ minden országa/régiója között található egyéni adatokkal, azzal a céllal, hogy meghatározza, hol kell költeni a legtöbb marketing dollárt. 

Olyan nyilvános adatforrást használunk, mint például az [ENSZ világméretű lakossági kilátásai](https://esa.un.org/unpd/wpp/) erre a célra. Az alábbi egyszerű sémával kell rendelkeznie:

![Példa egyszerű sémára](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

A példánkban feltételezzük, hogy egy új fájlt töltünk fel a legújabb éves adatokkal, amint elérhetővé válnak.

## <a name="general-design"></a>Általános kialakítás
Klasszikus ETL-Type logikát használunk a folyamat megtervezéséhez. Az architektúra a következőképpen fog megjelenni:

![Az adatgyűjtési folyamat architektúrája](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Ez a cikk nem tárgyalja az adatok létrehozását, illetve az [Azure Blob Storage-fiókba való feltöltését](../../storage/blobs/storage-upload-process-images.md). Ehelyett választjuk ki a folyamatot, amint egy új fájlt töltenek fel a blobba. Innen:

1. Egy folyamat fogja észlelni, hogy az új adatfeltöltés megtörtént.  A példában egy [Azure Logic App-alkalmazás](../../logic-apps/logic-apps-overview.md)található, amely egy triggerrel képes észlelni a blobba feltöltött új adatgyűjtést.

2. A processzor beolvassa ezt az új adatokat, és átalakítja a JSON formátumba, a Azure Monitor ebben a példában szereplő formátumot, és egy [Azure-függvényt](../../azure-functions/functions-overview.md) használunk, amely egyszerű és költséghatékony módszer a feldolgozási kód végrehajtásához. A függvényt ugyanazzal a logikai alkalmazással indítjuk el, amelyet az új adat észlelésére használtunk.

3. Végül, ha a JSON-objektum elérhetővé válik, a rendszer elküldi a Azure Monitornak. Ugyanez a logikai alkalmazás elküldi az adatokat Azure Monitor a beépített Log Analytics adatgyűjtő tevékenységgel.

Habár a blob Storage, a Logic app vagy az Azure függvény részletes telepítése nem szerepel ebben a cikkben, a részletes utasítások az adott termékek oldalain érhetők el.

Ennek a folyamatnak a figyeléséhez Application Insights használjuk az Azure-függvény [részleteinek](../../azure-functions/functions-monitoring.md)figyeléséhez, és Azure monitor a logikai alkalmazás [részleteinek](../../logic-apps/monitor-logic-apps-log-analytics.md)figyeléséhez. 

## <a name="setting-up-the-pipeline"></a>A folyamat beállítása
A folyamat beállításához először győződjön meg róla, hogy létrehozta és konfigurálta a BLOB-tárolót. Hasonlóképpen ügyeljen arra, hogy az Log Analytics munkaterületen, ahová el szeretné küldeni az adatküldés létrehozását.

## <a name="ingesting-json-data"></a>JSON-adatfeldolgozás
A JSON-adatfeldolgozás triviális Logic Apps, és mivel nincs szükség átalakításra, a teljes folyamatot egyetlen logikai alkalmazásba is befoglalhatja. Ha a blob-tároló és a Log Analytics munkaterület konfigurálva lett, hozzon létre egy új logikai alkalmazást, és konfigurálja a következő módon:

![Logic apps-munkafolyamat – példa](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Mentse a logikai alkalmazást, és folytassa a teszteléssel.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>XML-, CSV-vagy egyéb adatformátumok beolvasása
Logic Apps ma nem rendelkezik beépített képességekkel az XML-, CSV-vagy más típusok JSON formátumba való egyszerű átalakításához. Ezért a transzformáció befejezéséhez más módszert kell használnia. Ebben a cikkben a Azure Functions kiszolgáló nélküli számítási képességeit használjuk, amely nagyon egyszerű és költséghatékony megoldás. 

Ebben a példában egy CSV-fájlt elemezünk, de bármely más fájltípus hasonlóképpen feldolgozható. Egyszerűen módosítsa az Azure-függvény deszerializáló részét úgy, hogy az tükrözze a megfelelő logikát az adott adattípushoz.

1.  Hozzon létre egy új Azure-függvényt a Function Runtime v1 és a felhasználás alapján, ha a rendszer kéri.  Válassza ki azt a **http-trigger** sablont, amely a C# nyelvre van kiindulási pontként, amely a szükséges kötéseket konfigurálja. 
2.  A jobb oldali ablaktábla **fájlok megtekintése** lapján hozzon létre egy **project.js** nevű új fájlt, és illessze be a következő kódot a használt NuGet-csomagokból:

    ![Példa Azure Functions projektre](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Váltson a **Run. CSX** elemre a jobb oldali ablaktáblán, és cserélje le az alapértelmezett kódot az alábbira. 

    >[!NOTE]
    >A projekt esetében le kell cserélnie a rekord modelljét (a "PopulationRecord" osztályt) a saját adatsémával.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Mentse a függvényt.
5. Tesztelje a függvényt, és győződjön meg arról, hogy a kód megfelelően működik. Váltson a **teszt** lapra a jobb oldali ablaktáblán, és konfigurálja a tesztet az alábbiak szerint. Helyezzen egy blobra mutató hivatkozást a **kérelem törzse** szövegmezőbe. Ha a **Futtatás** gombra kattint, a **kimenet** mezőben a JSON-kimenetnek kell megjelennie:

    ![Function apps-teszt kódja](./media/create-pipeline-datacollector-api/functions-test-01.png)

Most vissza kell lépnie, és módosítania kell a korábban felépített logikai alkalmazást, hogy tartalmazza a betöltött és a JSON formátumba konvertált adatmennyiséget.  A Tervező nézet használatával konfigurálja a következőt, majd mentse a logikai alkalmazást:

![Példa Logic Apps munkafolyamat befejezésére](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>A folyamat tesztelése
Most feltölthet egy új fájlt a korábban konfigurált blobba, és azt a logikai alkalmazás figyeli. Hamarosan látnia kell a logikai alkalmazás új példányát, hívja meg az Azure-függvényt, majd küldje el az adatait Azure Monitorba. 

>[!NOTE]
>Akár 30 percet is igénybe vehet, hogy az információ megjelenjen a Azure Monitor az első alkalommal, amikor új adattípust küld.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>A Log Analytics és Application Insights egyéb adatértékekkel való korrelációja
Az egyéni adatforrásból betöltött népességi adatokkal kapcsolatos Application Insights oldal nézet adatainak korrelációs céljának elvégzéséhez futtassa az alábbi lekérdezést a Application Insights Analytics-ablakban vagy Log Analytics munkaterületen:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

A kimenetnek két adatforrást kell tartalmaznia.  

![A kihagyott adatpontok korrelációja egy keresési eredményben – példa](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Üzemi folyamat javasolt fejlesztései
Ez a cikk egy működő prototípust mutat be, amely a mögöttes logikai megoldásra alkalmazható. Az ilyen éles környezetű megoldások esetében a következő tökéletesítések javasoltak:

* A logikai alkalmazásban és a függvényben adja hozzá a hibakezelés és az újrapróbálkozási logikát.
* Adja hozzá a logikát annak biztosításához, hogy a 30MB/Single Log Analytics betöltési API-hívások korlátja ne lépje túl. Szükség esetén ossza fel az adatmennyiséget kisebb szegmensekre.
* Hozzon létre egy tisztítási szabályzatot a blob Storage-tárolóban. Miután sikeresen elküldte a (z) Log Analytics munkaterületre, hacsak nem szeretné, hogy a nyers adatkészletek archiválási célokra legyenek elérhetők, nincs ok a tárolás folytatására. 
* Ellenőrizze, hogy a figyelés engedélyezve van-e a teljes folyamaton belül, a nyomkövetési pontok és a riasztások szükség szerinti hozzáadásával.
* Használja ki a forráskódot a függvény és a logikai alkalmazás kódjának kezeléséhez.
* Győződjön meg arról, hogy a megfelelő módosítás-kezelési házirendet követik, például ha a séma megváltozik, a függvény és a Logic Apps ennek megfelelően módosulnak.
* Ha több különböző adattípust tölt fel, elkülöníti őket a blob-tárolóban lévő egyes mappákba, és létrehozhatja a logikát az adattípus alapján. 


## <a name="next-steps"></a>Következő lépések
További információ az adatgyűjtő  [API](data-collector-api.md) -ról az adatok log Analytics munkaterületre való írásához bármely REST API ügyfélről.
