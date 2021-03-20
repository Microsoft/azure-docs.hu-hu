---
title: Nyilvános adatkészletek az Azure analyticshez
description: Ismerje meg az Azure Analytics-szolgáltatások és-megoldások prototípusához és teszteléséhez használható nyilvános adatkészleteket.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 2e836a175b45c6fca46f9c76760e1cbaef305a47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792582"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Nyilvános adatkészletek teszteléshez és prototípusokhoz
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Tallózással keresse meg a nyilvános adatkészletek azon listáját, amely használható a tárolási és elemzési szolgáltatások és megoldások prototípusára és tesztelésére.

## <a name="us-government-and-agency-data"></a>Az Egyesült Államok kormányzati szerveinek adatai

| Adatforrás | Tudnivalók az adatokról | Tudnivalók a fájlokról |
|---|---|---|
| [USA kormányzati adatszolgáltatások](https://catalog.data.gov/dataset) | Több mint 250 000 adathalmaz, amely a mezőgazdaság, az éghajlat, a fogyasztó, az ökoszisztémák, az oktatás, az energia, a pénzügy, az egészségügy, a helyi kormányzat, a gyártás, a tengerészeti, az óceán, a közbiztonság, valamint az Egyesült Államok területén | Különböző formátumú fájlok különböző formátumokban, például HTML, XML, CSV, JSON, Excel és sok más méretben. A rendelkezésre álló adatkészleteket fájlformátum alapján szűrheti. |
| [Egyesült államokbeli népszámlálási adatai](https://www.census.gov/data.html) | Az Egyesült Államok lakosságának statisztikai adatainak ismertetése | Az adatkészletek különböző formátumokban vannak. |
| [A NASA Earth Science-adatai](https://earthdata.nasa.gov/) | Több mint 32 000 adatgyűjtemény a mezőgazdaság, a környezet, a bioszféra, az éghajlat, a cryosphere, az emberi méretek, a hidroszféra, a földterületek, az óceánok, a Sun-Earth interakciók és egyebek terén. | Az adatkészletek különböző formátumokban vannak. |
| [Repülőjegy-repülési késések és egyéb szállítási adatszolgáltatások](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Az Egyesült Államok Közlekedési Minisztériuma (DOT) Bureau of közlekedési statisztikája (BTS) nyomon követi a nagyméretű légifuvarozók által üzemeltetett belföldi járatok időbeli teljesítményét. Megjelenik az időponthoz, a késleltetett, a megszakított és a lefordított járatok számával kapcsolatos összegző információ... a webhelyen közzétett összefoglaló táblákban. | A fájlok CSV formátumúak. |
| [Forgalomra vonatkozó halálesetek – US Fatality Analysis Reporting System (a szolgáltatás)](https://www.nhtsa.gov/FARS) | "A NHTSA-t, a Kongresszust és az amerikai közelmúltbeli adatokat, amelyek a gépjárművek forgalmának összeomlása miatt elszenvedett végzetes sérülésekkel kapcsolatosak." | "Hozzon létre saját halálozási adatait online a szolgáltatással a (z) a (z) rendszerhez. Vagy töltse le az 1975-ből származó összes, az FTP-helyről beérkező összes szolgáltatás adatait. " |
| [Mérgező kémiai adatszolgáltatások – EPA toxicitás-előrejelző (ToxCast &trade; )](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "Az EPA legutóbb frissített, nyilvánosan elérhető, nagy adatátvitelt okozó toxicitási adatok több ezer vegyi anyagban. Ezeket az adatmennyiséget az EPA ToxCast kutatási tevékenysége hozza létre. | Az adatkészletek különböző formátumokban érhetők el, többek között a táblázatok, az R-csomagok és a MySQL-adatbázisfájlok. |
| [Mérgező vegyianyag-adat – NIH Tox21-adatkihívás 2014](https://tripod.nih.gov/tox21) | "A 2014 Tox21 adatra vonatkozó kihívás úgy lett kialakítva, hogy segítse a tudósokat abban, hogy a 21. századi kezdeményezésben a toxikológiai módszerekkel tesztelt vegyi anyagok és összetevők milyen hatással lehetnek a toxikus hatásokra." | Az adathalmazok a SMILEs és az SDF formátumokban érhetők el. Az adatelemzési tevékenységek és a kémiai struktúrák a ~ 10 000-es vegyületek Tox21-gyűjteményében (Tox21 10K) is elérhetők. " |
| [Biotechnológiai és genom-adatok a NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Több adathalmaz, amely a génekre, a genomokra és a fehérjékre terjed ki. | Az adathalmazok szöveg, XML, BLAST és más formátumúak. A BLAST-alkalmazás elérhető. |

## <a name="other-statistical-and-scientific-data"></a>Egyéb statisztikai és tudományos adatszolgáltatások

| Adatforrás | Tudnivalók az adatokról | Tudnivalók a fájlokról |
|---|---|---|
| [New York-i taxi-adatgyűjtés](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "A taxiban nyilvántartott rekordok közé tartoznak a pick-up és a lemorzsolódási dátum/idő, a pick-up és a lemorzsolódási Locations, az utazási távolságok, a részletezett viteldíjak, a díjszabási típusok, a fizetési típusok és a járművezető által jelentett utasok száma." | Az adatkészletek havonta CSV-fájlokban vannak. |
| [Microsoft kutatási adatkészletek – "adatelemzés kutatási célokra"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Több adathalmaz, amely az emberi számítógépekkel való interakciót, hang/videó, adatbányászat/információ beolvasását, földrajzi elhelyezkedését, a természetes nyelvi feldolgozást, valamint a robotika/számítógép jövőképét fedi le. | Az adathalmazok formátuma különböző formátumú, a tömörített fájlok letölthetők. |
| [A Science Cloud-adatfelhőbeli adatfeldolgozás megnyitása](https://www.opensciencedatacloud.org/projects/) | "A nyílt tudományos adatfelhő biztosítja a tudományos közösség számára a terabájt és petabyte szintű tudományos adatkészletek tárolásához, megosztásához és elemzéséhez szükséges erőforrásokat."| Az adatkészletek különböző formátumokban vannak. |
| [Globális klíma-WorldClim](https://worldclim.org/) | "A WorldClim a globális éghajlati rétegek (kockás Climate-adathalmazok) készlete, amelynek térbeli felbontása körülbelül 1 km2. Ezek az adatleképezések és a térbeli modellezések is használhatók. " | Ezek a fájlok térinformatikai adatkészletet tartalmaznak. További információ: [adatformátum](https://worldclim.org/formats1). |
| [Az emberi társadalommal kapcsolatos információk – a GDELT projekt](https://www.gdeltproject.org/data.html) | "A GDELT projekt az emberi társadalom legnagyobb, legátfogóbb és legnagyobb felbontású nyílt adatbázisa, amelyet valaha hoztak létre." | A nyers adatfájlok CSV formátumúak. |
| [Reklám – a Criteo származó gépi tanulásra vonatkozó előrejelzési adatok](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "A legnagyobb valaha nyilvánosan kiadott ML-adatkészlet." További információ: [Criteo 1 TB Click Jóslás DataSet](/archive/blogs/machinelearning/now-available-on-azure-ml-criteos-1tb-click-prediction-dataset). | |
| [ClueWeb09 a maki projektből](https://www.lemurproject.org/clueweb09.php/) | "A ClueWeb09 adatkészlet azért jött létre, hogy támogassa az információk lekérésével és a kapcsolódó emberi nyelvi technológiákkal kapcsolatos kutatásokat. A szolgáltatás körülbelül 1 000 000 000 weboldalt tartalmaz, amelyek 10 nyelven, a januári és február 2009-ban gyűjtöttek. " | Lásd: [adatkészlet adatai](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Online szolgáltatási adatszolgáltatások

| Adatforrás | Tudnivalók az adatokról | Tudnivalók a fájlokról |
|---|---|---|
| [GitHub-Archívum](https://www.githubarchive.org/) | "A GitHub Archive egy olyan projekt, amely rögzíti a nyilvános GitHub-idővonalat [az események], archiválja, és könnyen elérhetővé teszi a további elemzéseket." | A JSON-kódolású események archívumait. gz (gzip) formátumban töltheti le egy webes ügyfélprogramból. |
| [A GitHub tevékenységi adatok a GHTorrent projektből](http://ghtorrent.org/) | "A GHTorrent projekt [is] arra törekszik, hogy a GitHub-REST API segítségével méretezhető, lekérdezhető, offline tükrözést hozzon létre. A GHTorrent figyeli a GitHub nyilvános eseményének időpontját. Minden eseménynél lekéri annak tartalmát és függőségeit. " | A MySQL-adatbázis-memóriaképek CSV formátumúak. |
| [Adatmemóriakép Stack Overflow](https://archive.org/details/stackexchange) | "Ez az összes felhasználó által a verem Exchange-hálózaton [beleértve a Stack Overflowokat] is." | "Az egyes helyek [például a Stack Overflow] külön archívumként vannak formázva, amely a 7-zip használatával tömörített XML-fájlokból áll. Minden hely archiválása tartalmazza a bejegyzéseket, a felhasználókat, a szavazatokat, a megjegyzéseket, a PostHistory és a PostLinks. " |