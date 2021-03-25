---
title: Egyéni képességek méretezése és kezelése
titleSuffix: Azure Cognitive Search
description: Ismerje meg azokat az eszközöket és technikákat, amelyekkel hatékonyan méretezhetők a maximális átviteli sebességre vonatkozó egyéni képességek. Az egyéni készségek olyan egyéni AI-modelleket vagy logikai műveleteket indítanak el, amelyeket hozzáadhat egy mesterséges intelligenciával bővített indexelési folyamathoz az Azure Cognitive Searchban.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: ccee8537c2543b67c1bb60711737ec761ebc8cbf
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043070"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Az egyéni képességek hatékony felskálázása

Az egyéni képességek olyan webes API-k, amelyek egy adott felületet implementálnak. Az egyéni képességek bármely nyilvánosan címezhető erőforráson végrehajthatók. Az egyéni képességek leggyakoribb implementációi a következők:
* Azure Functions egyéni logikai ismeretekhez
* Azure webapps az egyszerű, tárolós AI-ismeretekhez
* Az Azure Kubernetes szolgáltatás összetettebb vagy nagyobb szaktudást biztosít.

## <a name="prerequisites"></a>Előfeltételek

+ Tekintse át az [Egyéni ügyességi felületet](cognitive-search-custom-skill-interface.md) az egyéni képességek megvalósításához szükséges bemeneti/kimeneti interfész bevezetéséhez.

+ Állítsa be a környezetet. [Ebből az oktatóanyagból](/python/tutorial-vs-code-serverless-python-01) megkezdheti a kiszolgáló nélküli Azure-függvények beállítását a Visual Studio Code és a Python Extensions használatával.

## <a name="skillset-configuration"></a>Készségkészlet-konfiguráció

Az indexelési folyamat átviteli sebességének maximalizálása érdekében egyéni képességet kell beállítania, és ismernie kell a képességet, az indexelő konfigurációit, valamint azt, hogy a szakértelem hogyan kapcsolódik az egyes dokumentumokhoz. Például a rendszer a szaktudás meghívásának számát jeleníti meg a dokumentumon, és a meghívások várható időtartamát.

### <a name="skill-settings"></a>Szaktudás beállításai

Az [egyéni képességek](cognitive-search-custom-skill-web-api.md) beállításnál állítsa be a következő paramétereket.

1. Állítsa be az `batchSize` Egyéni képességet, hogy a szaktudás egyetlen meghívásával konfigurálja a képességek számára eljuttatott rekordok számát.

2. Állítsa be az `degreeOfParallelism` -t az egyidejű kérések számának kalibrálásához, amelyet az indexelő tesz a szakértelemre.

3. Állítsa `timeout` olyan értékre, amely elegendő ahhoz, hogy a képesség érvényes válaszra válaszoljon.

4. A `indexer` definícióban állítsa be [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) az adatforrásból beolvasni kívánt dokumentumok számát, és bővítse egyidejűleg.

### <a name="considerations"></a>Megfontolandó szempontok

Ha ezeket a változókat úgy állítja be, hogy optimalizálja az indexelő teljesítményét, meg kell határoznia, hogy a készség jobban teljesít-e sok egyidejű kis kérés vagy kevesebb nagy kérés esetén. Néhány megfontolandó kérdés:

* Mi a szaktudás meghívása? A jártasság minden dokumentum esetében egyszer lefut, például a dokumentum besorolási képességeire, vagy a szaktudás többször is végrehajtható a dokumentumban, a bekezdések besorolási képességeivel?

* Átlagosan hány dokumentumot kell beolvasni az adatforrásból egy skill-kérelem kitöltéséhez a képzettségi köteg mérete alapján? Ideális esetben ez kevesebb, mint az indexelő kötegének mérete. Ha a kötegelt méretek 1-nél nagyobbak, a szakértelem több forrásból is fogadhat rekordokat. Ha például az indexelő kötegek száma 5, és a képzettségi kötegek száma 50, és az egyes dokumentumok csak öt rekordot generálnak, az indexelő több indexelő kötegben kell kitöltenie az egyéni képességre vonatkozó kérést.

* Az indexelő köteg által létrehozható kérések átlagos száma optimális beállítást biztosít a párhuzamossági fok számára. Ha a képességet üzemeltető infrastruktúra nem támogatja az adott szintű párhuzamosságot, érdemes lehet lehívni a párhuzamosság mértékét. Ajánlott eljárásként tesztelje a konfigurációt néhány dokumentummal, hogy érvényesítse a paramétereket.

* A dokumentumok kisebb mintájának tesztelésével kiértékelheti a szakértelem végrehajtási idejét a dokumentumok részhalmazának feldolgozásához szükséges teljes időre. Az indexelő több időt vesz fel egy köteg összeállítására, vagy a készségre vár választ? 

* Tekintse át a párhuzamosságok felsőbb rétegbeli következményeit. Ha az egyéni képességek bemenete egy korábbi szakértelmet eredményez, a készségkészlet összes szaktudása hatékonyan méretezhető a késés csökkentése érdekében?

## <a name="error-handling-in-the-custom-skill"></a>Hibakezelés az egyéni szakértelemben

Az egyéni szaktudásnak a HTTP 200 sikerességi állapotkódot kell visszaadnia, ha a szaktudás sikeresen befejeződik. Ha egy köteg egy vagy több rekordja hibákat eredményez, érdemes lehet visszaadnia a 207-as többszörös állapotkódot. A rekordhoz tartozó hibák vagy figyelmeztetések listájának tartalmaznia kell a megfelelő üzenetet.

A kötegben lévő minden olyan elem, amely hibát jelez, a megfelelő dokumentumot fogja eredményezni. Ha a sikeres dokumentumra van szüksége, egy figyelmeztetést ad vissza.

A rendszer a 299-nál több állapotkódot is kiértékeli, és az összes dúsítás meghiúsult. 

### <a name="common-error-messages"></a>Gyakori hibaüzenetek

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Állítsa be a képességek időtúllépési paraméterét, hogy a hosszabb végrehajtási időtartamot engedélyezzen.

* `Could not execute skill because Web Api skill response is invalid.` A szaktudás nem ad vissza üzenetet az egyéni képességi válasz formátumában. Ez lehet egy nem kezelt kivétel eredménye a szakértelemben.

* `Could not execute skill because the Web Api request failed.` Valószínűleg az engedélyezési hibák vagy kivételek okozzák.

* `Could not execute skill.` Általában a dokumentum-hierarchiában egy meglévő tulajdonságra leképezett ügyességi válasz eredménye.

## <a name="testing-custom-skills"></a>Egyéni képességek tesztelése

Első lépésként tesztelje egyéni képességeit egy REST API-ügyféllel az ellenőrzéshez:

* A jártasság a kérések és válaszok egyéni képességi felületét implementálja

* A szakértelem érvényes JSON-t ad vissza a `application/JSON` MIME-típussal

* Érvényes HTTP-állapotkódot ad vissza.

Hozzon létre egy [hibakeresési munkamenetet](cognitive-search-debug-session.md) , és adja hozzá a készségét a készségkészlet, és győződjön meg arról, hogy az érvényes dúsítást eredményez. Míg a hibakeresési munkamenet nem teszi lehetővé a szaktudás teljesítményének finomhangolását, lehetővé teszi, hogy a szakértelem érvényes értékekkel legyen konfigurálva, és visszaadja a várt dúsított objektumokat.

## <a name="best-practices"></a>Ajánlott eljárások

* Míg a képességek elfogadják és visszaadhatják a nagyobb hasznos adatokat, érdemes lehet a 150 MB-ra vagy annál kisebbre korlátozni a JSON-t.

* Érdemes lehet úgy beállítani a Batch-méretet az indexelő és a szakértelem beállításával, hogy az egyes adatforrás-kötegek teljes adattartalmat hozzanak létre a szakértelemhez.

* A hosszú ideig futó feladatok esetében állítsa az időtúllépést elég magas értékre, hogy az indexelő ne legyen hiba a dokumentumok párhuzamos feldolgozásakor.

* Optimalizálhatja az indexelő köteg méretét, a képzettségi köteg méretét és a párhuzamossági fokot a készséggel várt terhelési minta, kevesebb nagy kérés vagy sok kis kérés létrehozásához.

* A hibák részletes naplófájljaival figyelheti az egyéni képességeket, mivel olyan forgatókönyvekkel rendelkezhet, amelyekben az adatok változékonysága miatt az adott kérések következetesen meghiúsulnak.


## <a name="next-steps"></a>Következő lépések
Gratulálunk! Az egyéni képesség mostantól az indexelő teljesítményének maximalizálására van kibővítve. 

+ [Energiaellátási készségek: az egyéni képességek tárháza](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Egyéni képesség hozzáadása egy mesterséges intelligencia-bővítési folyamathoz](cognitive-search-custom-skill-interface.md)
+ [Azure Machine Learning-képesség hozzáadása](./cognitive-search-aml-skill.md)
+ [A módosítások teszteléséhez használjon hibakeresési munkameneteket](./cognitive-search-debug-session.md)