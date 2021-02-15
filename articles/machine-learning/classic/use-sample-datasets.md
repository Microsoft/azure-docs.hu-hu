---
title: 'ML Studio (klasszikus): a minta adatkészletek használata – Azure'
description: A Machine Learning Studio (klasszikus) részét képező minta modellekben használt adatkészletek leírása. Ezeket a minta adatkészleteket használhatja a kísérletekhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 8a848f6589902c2eabf5b4e14a4d000dc4ed081e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518436"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>A minta adatkészletek használata Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


[top]: #machine-learning-sample-datasets

Ha Azure Machine Learning Studio (klasszikus) új munkaterületet hoz létre, a rendszer alapértelmezés szerint több minta adatkészletet és kísérletet is tartalmaz. A minta-adathalmazok nagy részét a [Azure AI Galleryban](https://gallery.azure.ai/)szereplő minták használják. Mások például a gépi tanulásban jellemzően használt különböző típusú adattípusokra mutatnak.

Ezen adatkészletek némelyike elérhető az Azure Blob Storage-ban. Ezen adatkészletek esetében a következő táblázat egy közvetlen hivatkozást tartalmaz. Ezeket az adatkészleteket a kísérletekben az [adat importálása][import-data] modul használatával használhatja.

Ezen minta-adatkészletek további része a munkaterületen a **mentett adatkészletek** területen érhető el. Ez a modul paletta bal oldalán található, Machine Learning Studio (klasszikus).
A saját kísérletekben bármelyik adatkészletet használhatja a kísérlet vászonra húzva.

## <a name="datasets"></a>Adathalmazok

<table>

<tr>
  <th>Adatkészlet neve</th>
  <th>Adatkészlet leírása</th>
</tr>

<tr>
  <td>Felnőtt népszámlálás jövedelme bináris besorolási adatkészlet</td>
  <td>
Az 1994-es népszámlálási adatbázis egy részhalmaza, amely a 16. korban dolgozó felnőtteket használja > 100 korrigált bevételi indexszel.
<p></p>
<b>Használat:</b> A demográfiai adatokkal rendelkező személyek besorolása annak előrejelzéséhez, hogy egy személy évente több mint 50 000-et keres.
<p></p>
<b>Kapcsolódó kutatás:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Repülőtéri kódok adatkészlete</td>
  <td>
Egyesült államokbeli repülőtéri kódok.
<p></p>
Ez az adatkészlet minden Egyesült államokbeli repülőtérhez tartalmaz egy sort, amely a repülőtéri azonosító számát és nevét adja meg a hely városával és állapotával együtt.
  </td>
</tr>

<tr>
  <td>Az autók árát (nyers)</td>
  <td>
Információk a márka és a modell alapján, beleértve a díjszabást, az olyan szolgáltatásokat, mint például a hengerek száma és az MPG, valamint a biztosítási kockázati pontszám.
<p></p>
A kockázati pontszám kezdetben az automatikus árral van társítva. Ezt követően a rendszer a tényleges kockázatokat egy, a biztosítási matematikusok ismert folyamat során helyesbíti. A + 3 érték azt jelzi, hogy az automatikus kockázatos, a-3 érték pedig valószínűleg biztonságos.
<p></p>
<b>Használat:</b> A funkciók kockázati pontszámának előrejelzése regresszió vagy többváltozós besorolás használatával. 
<p></p>
<b>Kapcsolódó kutatás:</b> Schlimmer, J.C. (1987). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Bike Rental UCI-adatkészlet</td>
  <td>
Az UCI Bike Rental adatkészlete, amely a Bikeshare vállalat valódi adatán alapul, és egy Bike Rental hálózatot tart fenn Washingtonban.
<p></p>
Az adatkészlet a 2011-es és 2012-as nap minden órájának egy sorával rendelkezik, összesen 17 379 sorra. Az óradíjas kerékpár-kölcsönzések tartománya 1 és 977 között van.

  </td>
</tr>

<tr>
  <td>Bill Gates RGB-képe</td>
  <td>
Nyilvánosan elérhető képfájl CSV-adatokra konvertálva.
<p></p>
A rendszerkép átalakítására szolgáló kód a <strong>"K" kifejezéssel a fürtözési</strong> modell részletes lapja.
  </td>
</tr>

<tr>
  <td>Véradási adatgyűjtés</td>
  <td>
A Hsin-Chu City, Tajvan vérképzőszervi transzfúziós szolgáltatásának központjának Blood adományozó adatbázisából származó adatok egy részhalmaza.
<p></p>
A donori adatmennyiség tartalmazza a legutóbbi adományozás óta eltelt hónapokat, valamint a gyakoriságot, vagy az adományok teljes számát, a legutóbbi adományozás óta eltelt időt és a felhasznált vért.
<p></p>
<b>Használat:</b> A cél az, hogy megjósolja az osztályozást, hogy a donor a 2007 márciusában adományozta-e a vért, ahol az 1 egy donort jelez a megcélzott időszakban, és 0 a nem adományozó. 
<p></p>
<b>Kapcsolódó kutatás:</b> Yeh, I.C., (2008). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science
<p></p>
Yeh, I-Cheng, Yang, King-Jang és Ting, Tao-Ming, "Knowledge Discovery for RFM Model a Bernoulli Sequence használatával" szakértői rendszerek alkalmazásokkal, 2008, <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Mellrák-adatfeldolgozás</td>
  <td>
Az Onkológiai Intézet által a gépi tanulási irodalomban gyakran megjelenő három daganattal kapcsolatos adatkészlet egyike. A diagnosztikai adatokat a 300-es szöveti minták laboratóriumi elemzésével kapcsolatos funkciókkal ötvözi.
<p></p>
<b>Használat:</b> A rák típusának osztályozása 9 attribútum alapján, amelyek némelyike lineáris, némelyik pedig kategorikus. 
<p></p>
<b>Kapcsolódó kutatás:</b> Wohlberg, W.H., utca, W.N., & Mangasarian, O.L. (1995). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>A mellrák funkciói <td>
Az adatkészlet a 102K gyanús régiói (jelöltek) adatait tartalmazza, amelyeket az 117-funkciók ismertetnek. A funkciók védettek, és a jelentésük nem derül ki az adatkészlet létrehozói (Siemens Healthcare) számára. 
  </td>
</tr>

<tr>
  <td>Mellrák adatai</td>
  <td>
Az adatkészlet további információkat tartalmaz az X-ray rendszerképek gyanús régióiról. Mindegyik példa információkat (például a címkét, a beteg AZONOSÍTÓját, a javítás koordinátáit a teljes képhez viszonyítva) tartalmazza a mellrák funkcióinak adatkészletében található megfelelő sorszámmal kapcsolatban. Mindegyik páciens több példát is tartalmaz. A rákos megbetegedések esetében néhány példa pozitív, néhány pedig negatív. A daganatos betegeknél az összes példa negatív. Az adatkészlet 102K-példákat tartalmaz. Az adatkészlet elfogult, a pontok 0,6%-a pozitív, a többi pedig negatív. Az adathalmazt a Siemens Healthcare tette elérhetővé.
  </td>
</tr>

<tr>
  <td>Megosztott CRM szolgáltatóváltást-Címkék</td>
  <td>
A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. labels</a>) címkéi.
  </td>
</tr>

<tr>
  <td>Megosztott CRM-adatforgalom-Címkék</td>
  <td>
A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. labels</a>) címkéi.
  </td>
</tr>

<tr>
  <td>Megosztott CRM-adatkészlet</td>
  <td>
Ezek az adatok a KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívásról (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>) származnak.
<p></p>
Az adatkészlet 50 000 ügyfelet tartalmaz a francia távközlési cég Orange-től. Minden ügyfél 230-es anonim funkciókkal rendelkezik, amelyek 190 a numerikus és a 40-as számú adat kategorikus. A funkciók nagyon ritkák.
  </td>
</tr>

<tr>
  <td>Megosztott CRM-Címkék</td>
  <td>
A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. labels</a>) címkéi.
  </td>
</tr>

<tr>
  <td>Regressziós adatEnergy-Efficiency</td>
  <td>
Szimulált energia-profilok gyűjteménye 12 különböző építési alakzat alapján. Az épületeket nyolc funkció különbözteti meg. Ebbe beletartozik az üvegezés terület, az üvegezési terület eloszlása és a tájolás.
<p></p>
<b>Használat:</b> A regresszió vagy a besorolás használatával előre jelezhető az energiahatékonysági minősítés a két valós értékű válasz egyike alapján. A többosztályos besorolás esetében a válasz változó a legközelebbi egész számra van kerekítve. 
<p></p>
<b>Kapcsolódó kutatás:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Repülési késések adatvédelme</td>
  <td>
Az Amerikai Egyesült államokbeli Közlekedési Minisztérium TranStats adatgyűjtési szolgáltatásában<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">(az időpontnál</a>) üzemelő személyszállító repülési adatok.
<p></p>
Az adatkészlet April-October 2013 időszakra vonatkozik. Azure Machine Learning Studio (klasszikus) feltöltésének megkezdése előtt az adatkészlet a következőképpen lett feldolgozva:
<ul>
  <li>Az adatkészlet úgy lett szűrve, hogy csak a 70-es legforgalmasabb repülőtérre vonatkozzon az USA kontinentális területén</li>
  <li>A megszakított járatok több mint 15 perc késéssel lettek megjelölve</li>
  <li>A rendszer kiszűrte a lefordított járatok kiszűrését</li>
  <li>A következő oszlopok lettek kiválasztva: év, hónap, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, megszakítva</li>
</ul>
</td>
</tr>

<tr>
  <td>Repülési idejű teljesítmény (nyers)</td>
  <td>
A repülőgép-repülési beérkezések és a távozások nyilvántartása Egyesült Államok október 2011.
<p></p>
<b>Használat:</b> Repülési késések előrejelzése. 
<p></p>
<b>Kapcsolódó kutatás:</b> Az USA- <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a> beli közlekedési részlegtől.
  </td>
</tr>

<tr>
  <td>Erdőtüzek – adatfeldolgozás</td>
  <td>
Időjárási jellegű, például hőmérsékleti és páratartalom-indexeket tartalmaz. Az adatok az Északkelet-portugáliai területről származnak, amely az erdőtüzek rekordjaival együtt történik.
<p></p>
<b>Használat:</b> Ez egy nehéz regressziós feladat, amelynek célja, hogy előre megjósolja az erdőtüzek kiégett területét. 
<p></p>
<b>Kapcsolódó kutatás:</b> Cortez, P., & Morais, A. (2008). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science
<p></p>
[Cortez és Morais, 2007] P. Cortez és A. Morais. Adatbányászati megközelítés az erdőtüzek előrejelzéséhez meteorológiai adatmennyiség használatával. J. neves, M. F. Santos és J. Machado EDS., a mesterséges intelligencia új trendjei, a 13. EPIA 2007 – portugál konferencia a mesterséges intelligencia, december, Guimarães, Portugália, PP. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. A következő címen érhető el: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a> .
  </td>
</tr>

<tr>
  <td>Német hitelkártya UCI-adatkészlet</td>
  <td>
Az UCI statlog (német bankkártya) adatkészlete (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">statlog + német + kredit + adat</a>) a német. adatfájl használatával.
<p></p>
Az adatkészlet az attribútumok egy halmaza által leírtak szerint osztályozza a személyeket, alacsony vagy magas hitelezési kockázatként. Mindegyik példa egy személyt jelöl. A rendszer 20 funkciót használ: számszerű és kategorikus, valamint bináris címkét (a kredit kockázati értékét). A magas kockázatú kreditek címkéje = 2, az alacsony kreditek esetében pedig a Label = 1. Az alacsony kockázatú, például a magas kockázatú példa nem megfelelő besorolásának díja 1, míg a magas kockázatú példa alacsony értékkel való megsértésének díja 5.
  </td>
</tr>

<tr>
  <td>IMDB-mozgóképek címei</td>
  <td>
Az adatkészlet a Twitter tweetekben értékelt filmek információit tartalmazza: IMDB-film azonosítója, film neve, műfaj és termelési év. 17K-filmek vannak az adatkészletben. Az adatkészlet az "S" dokumentumban lett bevezetve. Dooms, T. de Pessemier és L. Martens. MovieTweetings: a Twitteren gyűjtött film minősítési adatkészlet. Workshop a közösségi-on és az emberi számításban az ajánló rendszerekhez, CrowdRec a RecSys 2013-kor. "
  </td>
</tr>

<tr>
  <td>Írisz – kétosztályos adatértékek</td>
  <td>
Ez talán a legismertebb adatbázis, amely a minta felismerési irodalomban található. Az adatkészlet viszonylag kicsi, és 50 példát tartalmaz, amelyek három Iris-típusból álló szirom méréseket tartalmaznak.
<p></p>
<b>Használat:</b> Jósolja meg az írisz típusát a mérések közül.  
<p></p>
<b>Kapcsolódó kutatás:</b> Fisher, Romsics (1988). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Film tweetek</td>
  <td>
Az adatkészlet a Movie tweetek adatkészlet kiterjesztett verziója. Az adatkészlet 170K-minősítésekkel rendelkezik, amelyek a Twitteren jól strukturált tweetekkel lettek kinyerve. Minden példány egy tweetet jelöl, és egy rekord: felhasználói azonosító, IMDB-film azonosítója, minősítés, időbélyeg, kedvencek száma ehhez a tweethez és a tweetek száma. Az adatkészlet elérhetővé tétele a. Said, S. dooms, B. Loni és D. tikk for ajánló Systems Challenge 2014.
  </td>
</tr>

<tr>
  <td>A különböző gépkocsik MPG-adatkészletei</td>
  <td>
Ez az adatkészlet a Carnegie Mellon University StatLib könyvtára által biztosított adatkészlet némileg módosított verziója. Az adathalmazt a 1983-es amerikai statisztikai társulási kiállításon használták.
<p></p>
Az adatmennyiség a különböző személygépkocsik tüzelőanyag-felhasználását sorolja fel, amely mérföld/liter. Olyan információkat is tartalmaz, mint például a hengerek száma, a hajtómű-kihelyezés, a lóerő, a teljes súly és a gyorsítás.
<p></p>
<b>Használat:</b> A tüzelőanyag gazdaságosságának előrejelzése három többértékű diszkrét attribútum és öt folytonos attribútum alapján. 
<p></p>
<b>Kapcsolódó kutatás:</b> StatLib, Carnegie Mellon University, (1993). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Pima indiánok cukorbetegség bináris besorolási adatkészlet</td>
  <td>
Az országos Institute of diabétesz és az emésztési és a vese diseases adatbázisból származó adatok részhalmaza. Az adatkészlet úgy lett szűrve, hogy az indiai örökség Pima a női betegekre koncentráljon. Az adatok olyan orvosi adatokra is kiterjednek, mint például a glükóz és az inzulin szintje, valamint az életmódbeli tényezők.
<p></p>
<b>Használat:</b> Tippelje meg, hogy a tárgy rendelkezik-e cukorbetegséggel (bináris besorolás). 
<p></p>
<b>Kapcsolódó kutatás:</b> Sigillito, V. (1990). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml "</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Az éttermi ügyféladatok</td>
  <td>
Az ügyfelekkel kapcsolatos metaadatok összessége, beleértve a demográfiai adatokat és a preferenciákat.
<p></p>
<b>Használat:</b> Ezt az adatkészletet a másik két éttermi adatkészlettel együtt használva betaníthatja és tesztelheti az Ajánlói rendszereket. 
<p></p>
<b>Kapcsolódó kutatás:</b> BACHE, K. és Licher, M. (2013). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Az éttermi funkciókról</td>
  <td>
Az éttermekkel és azok funkcióival, például élelmiszer-típussal, étkezési stílussal és hellyel kapcsolatos metaadatok halmaza.
<p></p>
<b>Használat:</b> Ezt az adatkészletet a másik két éttermi adatkészlettel együtt használva betaníthatja és tesztelheti az Ajánlói rendszereket. 
<p></p>
<b>Kapcsolódó kutatás:</b> BACHE, K. és Licher, M. (2013). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Éttermi minősítések</td>
  <td>
A felhasználók által az éttermek számára a 0 és 2 közötti skálán megadott minősítéseket tartalmazza.
<p></p>
<b>Használat:</b> Ezt az adatkészletet a másik két éttermi adatkészlettel együtt használva betaníthatja és tesztelheti az Ajánlói rendszereket. 
<p></p>
<b>Kapcsolódó kutatás:</b> BACHE, K. és Licher, M. (2013). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Acél lágyítása többosztályos adatkészlethez</td>
  <td>
Ez az adatkészlet az acél-kiégetési kísérletekből származó rekordokat tartalmaz. Az eredményül kapott acél típusok fizikai attribútumait (szélesség, vastagság, típus (Coil, Sheet stb.) tartalmazza.
<p></p>
<b>Használat:</b> A két numerikus osztály attribútumának előrejelzése keménység vagy szilárdság. Elemezheti az attribútumok közötti korrelációkat is.
<p></p>
Az acélipari osztályok a SAE és más szervezetek által meghatározott szabványos szabványt követik. Egy adott "osztályzat" (osztály változó) értéket keres, és szeretné megismerni a szükséges értékeket. 
<p></p>
<b>Kapcsolódó kutatás:</b> Sterling, D. & Buntine, W. (NA). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, School of Information and Computer Science
<p></p>
A Steel-osztályok hasznos útmutatója itt található: <a href="https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf">https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Távcső-adatkészletek</td>
  <td>
A nagy energiájú gamma-részecskék rögzítése a háttérzajsal együtt szimulált, egy Monte Carlo-folyamattal.
<p></p>
A szimuláció célja, hogy javítsa a terepen alapuló, légköri Cserenkov gamma-távcső pontosságát. Ezt statisztikai módszerekkel végezheti el a kívánt jel (Cserenkov sugárzási zuhanyok) és a háttérzaj (a felső légkörben a kozmikus sugarak által kezdeményezett hadronic-zuhanyok) megkülönböztetése érdekében.
<p></p>
Az adatok előre feldolgozva lettek egy hosszúkás fürt létrehozásához a hosszú tengellyel a kamera középpontja felé. Ennek az ellipszisnek a jellemzői (más néven Hillas paraméterek) a diszkriminációhoz használható képparaméterek közé tartoznak.
<p></p>
<b>Használat:</b> Megjósolhatja, hogy egy zuhanyzó képe jelzi-e a jelet vagy a háttérzajt.
<p></p>
<b>Megjegyzések:</b> Az egyszerű besorolási pontosság nem értelmezhető ezeknél az adatoknál, mert a háttérben futó események besorolása rosszabb, mint a jelzési események háttérként való besorolása. A különböző osztályozók összehasonlításához a ROC-gráfot kell használni. A következő küszöbértékek egyikének kell lennie: 0,01, 0,02, 0,05, 0,1 vagy 0,2.
<p></p>
Azt is vegye figyelembe, hogy a háttérben futó események (h, hadronic-zuhanyzók) száma nem becsülhető meg. A valós mérések során a h vagy a Noise osztály az események többségét jelöli. 
<p></p>
<b>Kapcsolódó kutatás:</b> Bock, polgár (1995). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, információs iskola </td>
</tr>

<tr>
  <td>Időjárási adatkészlet</td>
  <td>
A NOAA-ből származó, óránkénti szárazföld-alapú időjárási megfigyelések (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">201304 és 201310 közötti egyesített adatok</a>).
<p></p>
Az időjárási adatok a repülőtéri meteorológiai állomások megfigyeléseit fedik le, az 2013-as April-October időszakra vonatkozóan. Azure Machine Learning Studio (klasszikus) feltöltésének megkezdése előtt az adatkészlet a következőképpen lett feldolgozva:
<ul>
  <li>A meteorológiai állomás azonosítói a megfelelő repülőtéri azonosítóra vannak leképezve</li>
  <li>Az 70-es legforgalmasabb repülőtérhez nem társított meteorológiai állomások kiszűrése megtörtént</li>
  <li>A Date oszlop külön év, hónap és nap oszlopokra van bontva</li>
  <li>A következő oszlopok lettek kiválasztva: AirportID, év, hónap, nap, idő, időzóna, SkyCondition, láthatóság, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, szélsebesség, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, rekordtípus, HourlyPrecip, magasságmérő</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 adatkészlet</td>
  <td>
Az adatok a Wikipedia ( <a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a> ) alapján jelennek meg, az összes S&P 500 vállalat, amely XML-adatként van tárolva.
<p></p>
Azure Machine Learning Studio (klasszikus) feltöltésének megkezdése előtt az adatkészlet a következőképpen lett feldolgozva:
<ul>
  <li>Szöveges tartalom kinyerése minden egyes vállalatnál</li>
  <li>Wiki formázásának eltávolítása</li>
  <li>Nem alfanumerikus karakterek eltávolítása</li>
  <li>Az összes szöveg konvertálása kisbetűsre</li>
  <li>Ismert vállalati kategóriák lettek hozzáadva</li>
</ul>
<p></p>
Vegye figyelembe, hogy egyes vállalatok esetében nem található cikk, így a rekordok száma kevesebb, mint 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Az adatkészlet ügyféladatokat tartalmaz, és jelzi a közvetlen levelezési kampányra adott válaszokat. Minden sor az ügyfelet jelöli. Az adatkészlet kilenc funkciót tartalmaz a felhasználói demográfiai adatokkal és a múltbeli viselkedéssel kapcsolatban, valamint három címke oszlopot (látogatás, átalakítás és költés).  A Visit egy bináris oszlop, amely azt jelzi, hogy az ügyfél a marketing kampány után látogatta meg. Az átalakítás azt jelzi, hogy egy ügyfél vásárolt valamit. A költés a felhasznált mennyiség.  Az adathalmazt a Kevin Hillstrom tette elérhetővé a MineThatData E-Mail elemzés és az adatbányászati kihívás számára.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
A RCV1-v2 Reuters Hírek adatkészletében található tesztelési példák funkciói. Az adatkészlet 781K, valamint az azonosítójuk (az adatkészlet első oszlopa) szerepel. Minden cikk jogkivonat-, stopworded-és ered. Dávid az adathalmazt adta elérhetővé. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
A betanítási példák funkciói a RCV1-v2 Reuters Hírek adatkészletében. Az adatkészlet 23K, valamint az azonosítójuk (az adatkészlet első oszlopa) szerepel. Minden cikk jogkivonat-, stopworded-és ered. Dávid az adathalmazt adta elérhetővé. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Adathalmaz a KDD Cup 1999 Knowledge Discovery és az adatbányászati eszközök versenyéről (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Az adatkészlet letöltése és tárolása az Azure Blob Storage-ban (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) történik, és a képzési és tesztelési adatkészleteket is tartalmazza. A betanítási adatkészlet körülbelül 126K sort és 43 oszlopot tartalmaz, beleértve a címkéket is. Három oszlop szerepel a címke adatai között, és 40 oszlop, amely a numerikus és a karakterlánc/kategorikus funkciókból áll, és a modell betanítására is használható. A tesztelési adatként körülbelül 22.5 K tesztelési példákat tartalmaz a betanítási adatként megegyező 43 oszlopokkal.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Témakör-hozzárendelések a hírekhez a RCV1-v2 Reuters Hírek adatkészletében. Egy újságcikk több témakörhöz is hozzárendelhető. Az egyes sorok formátuma a " &lt; téma neve &gt; &lt; dokumentum azonosítója &gt; 1". Az adatkészlet 2,6 M témakör-hozzárendeléseket tartalmaz. Dávid az adathalmazt adta elérhetővé. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Ezek az adatok a KDD Cup 2010 Student Performance kiértékelési Challenge (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">Student Performance értékelés</a>) alapján származnak. A felhasznált adathalmaz a Algebra_2008_2009 betanítási készlet (stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, ka (2010). Algebra I 2008-2009. A KDD Cup 2010 oktatási adatbányászati kihívása. Keresse meg a következőt: <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Az adatkészlet letöltése és tárolása az Azure Blob Storage-ban (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) történik, és a tanulói tutori rendszerből származó naplófájlokat tartalmaz. A megadott funkciók közé tartozik a probléma azonosítója és a rövid leírása, a tanuló azonosítója, az időbélyeg, valamint a tanulók által a probléma megoldásához a megfelelő módon történt kísérlet. Az eredeti adatkészlet 8.9 millió rekorddal rendelkezik; ezt az adatkészletet leállították az első 100 000 sorra. Az adatkészlet 23 tabulátorral tagolt oszlopa különböző típusú: numerikus, kategorikus és timestamp.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Példa a kísérletek Kickstart-re](sample-experiments.md)

<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data