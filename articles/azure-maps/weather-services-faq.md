---
title: Microsoft Azure Maps időjárási szolgáltatások – gyakran ismételt kérdések (GYIK)
description: Választ kaphat a Azure Maps időjárási szolgáltatások adatait és funkcióit érintő gyakori kérdésekre.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/04/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 2a5a58c1515c647bb76bf35f3a5eaade3d00588a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747324"
---
# <a name="azure-maps-weather-services-frequently-asked-questions-faq"></a>Azure Maps időjárási szolgáltatások – gyakran ismételt kérdések (GYIK)

Ez a cikk a [Azure Maps időjárási szolgáltatások](https://docs.microsoft.com/rest/api/maps/weather) adatait és funkcióit érintő gyakori kérdésekre ad választ. A következő témákat tárgyalja:

* Adatforrások és adatmodellek
* Időjárási szolgáltatások lefedettsége és rendelkezésre állása
* Adatfrissítés gyakorisága
* Fejlesztés Azure Maps SDK-val
* Időjárási adatmegjelenítési lehetőségek, beleértve a Microsoft Power BI integrációját

## <a name="data-sources-and-data-models"></a>Adatforrások és adatmodellek

**Hogyan működik Azure Maps a forrás időjárási adatvédelme?**

A Azure Maps világszínvonalú mobilitási és helymeghatározási technológiai partnerekkel, például az előrejelzéssel, amely a mögöttes időjárási adatokat biztosítja. Ha olvasni szeretné a Azure Maps "az előrejelzéssel való együttműködésről szóló bejelentését, lásd [: Rain vagy Shine: Azure Maps az időjárási szolgáltatások bepillantást nyerhetnek a vállalatba](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

Az AccuWeather valós idejű időjárási és környezeti információkat kínál a világ bármely pontján, nagyrészt a számos nemzeti kormányzati időjárási Ügynökséggel és más tulajdonosi szerződésekkel való együttműködésük miatt. Az alábbi alapvető információk listáját alább találja.

* Nyilvánosan elérhető globális felszíni megfigyelések kormányzati szervektől
* Saját felszíni megfigyelési adatkészletek a kormányoktól és a magánvállalkozásoktól
* Nagy felbontású radar-adat több mint 40 országban/régióban
* Valós idejű globális villám-adatelemzés
* Kormány által kiadott időjárási figyelmeztetések több mint 60 ország/régió és terület esetében
* Az egész világra kiterjedő, geostacionárius időjárási műholdakból származó műhold-adatok
* Több mint 150 számszerű előrejelzési modell, többek között belső, szabadalmaztatott modellezés, kormányzati modellek, például az Egyesült Államok globális előrejelzési rendszere (GFS) és a magánvállalatok által biztosított egyedi downscaled-modellek
* Levegőminőség-megfigyelések
* A közlekedési részlegek észrevételei

A felhasználók számára elérhető aktuális feltételek több tízezer felszíni megfigyeléssel és más adatmennyiséggel vannak beépítve. Ez nem csak a szabadon elérhető szabványos adatkészleteket foglalja magában, hanem a nemzeti meteorológiai szolgáltatásokból beszerzett egyedi észrevételeket is számos országban/régióban, például Indiában, Brazíliában és Kanadában, valamint más tulajdonosi adatbeviteleken. Ezek az egyedi adatkészletek megnövelik a felhasználók aktuális feltételi adatainak térbeli és időbeli feloldását. 

Ezeket az adatkészleteket valós időben vizsgálják felül a digitális előrejelzési rendszer pontossága érdekében, amely az AccuWeather szabadalmaztatott mesterséges intelligencia algoritmusait használja az előrejelzések folyamatos módosítására, így biztosítva, hogy mindig a legfrissebb adatokat használják, és így maximalizálják a folyamatos pontosságot.

**Milyen modellek hoznak létre időjárás-előrejelzési adatgyűjtést?**

A globális előrejelzések megfogalmazásához számos időjárás-előrejelző útmutatást használó rendszer van használatban. Minden nap több mint 150 numerikus előrejelzési modellt használ a külső és belső adatkészletek esetében is. Ide tartoznak a kormányzati modellek, például az európai központ ECMWF és az Egyesült Államok globális előrejelzési rendszere (GFS). Emellett az AccuWeather olyan szabadalmaztatott, nagyfelbontású modelleket is tartalmaz, amelyek adott helyszínekre és stratégiai regionális tartományokra vonatkozó előrejelzéseket downscale, hogy az időjárás előrejelzése további pontossággal történjen. A AccuWeather egyedi keverési és súlyozási algoritmusai az elmúlt néhány évtizedben lettek kifejlesztve. Ezek az algoritmusok optimálisan kihasználják a számos előrejelzési bemenetet, így nagyon pontos előrejelzéseket biztosítanak.

## <a name="weather-services-coverage-and-availability"></a>Időjárási szolgáltatások lefedettsége és rendelkezésre állása

**Milyen típusú lefedettség várható a különböző országokban/régiókban?**

Az időjárási szolgáltatások lefedettsége ország/régió szerint változik. Minden szolgáltatás nem érhető el minden országban vagy régióban. További információkért lásd a [lefedettségi dokumentációt](https://docs.microsoft.com/azure/azure-maps/weather-coverage).

## <a name="data-update-frequency"></a>Adatfrissítés gyakorisága

**Milyen gyakran frissülnek az aktuális feltételek?**

A jelenlegi állapotok körülbelül legalább egyszer frissülnek, de a gyorsan változó feltételekkel (például a nagy hőmérséklet-változásokkal, a Sky-feltételek változásaival, a csapadék változásaival stb.) gyakrabban frissülnek. A legtöbb megfigyelő állomás a világ minden pontján a feltételek változása után óránként többször jelent meg. Néhány terület azonban továbbra is csak egyszer, kétszer, vagy négyszer egy órával az ütemezett időközönként frissül.  

A Azure Maps legfeljebb 10 percig gyorsítótárazza az aktuális feltételeket, így az adatmennyiség közel valós idejű frissítési gyakorisága rögzíthető. Ha szeretné megtekinteni, hogy a gyorsítótárazott válasz mikor jár le, és nem jeleníti meg az elavult adatokat, kihasználhatja a lejárati fejléc információit a Azure Maps API-válasz HTTP-fejlécében.

**Milyen gyakran frissülnek a napi és az óránkénti előrejelzési adatfrissítések?**

A napi és az óránkénti előrejelzési adat naponta többször frissül, mivel a rendszer frissített megfigyeléseket fogad.  Ha például az előrejelzett magas/alacsony hőmérséklet meghaladja a határértéket, a következő frissítési ciklusban az előrejelzési adatértékek is módosulnak. Ez különböző intervallumokban fordulhat elő, de általában egy órán belül történik. A hirtelen időjárási körülmények sok időt okozhatnak az előrejelzési adatváltozások esetén. Például egy forró nyári délutánon egy elszigetelt zivatar hirtelen kialakulhat, ami nagy mennyiségű Felhőbeli lefedettséget és csapadékot jelent. Az izolált Storm gyakorlatilag 10 fokos mértékben képes a hőmérséklet csökkenésére. Ez az új hőmérsékleti érték befolyásolja az óránkénti és a napi előrejelzést a nap hátralevő részében, és így az adatkészletekben is frissülni fog.

Azure Maps előrejelzési API-k akár 30 percig is gyorsítótárazva vannak. Ha szeretné megtekinteni, hogy a gyorsítótárazott válasz mikor jár le, és nem jeleníti meg az elavult adatokat, kihasználhatja a lejárati fejléc információit a Azure Maps API-válasz HTTP-fejlécében. Azt javasoljuk, hogy szükség szerint frissítsen egy adott termék-használati eset és felhasználói felület (felhasználói felület) alapján.

## <a name="developing-with-azure-maps-sdks"></a>Fejlesztés Azure Maps SDK-val

**A Azure Maps web SDK natív módon támogatja az időjárási szolgáltatások integrációját?**

A Azure Maps web SDK egy Services modult biztosít. A Services modul egy segítő könyvtár, amely megkönnyíti a Azure Maps REST-szolgáltatások használatát a webes vagy Node.js alkalmazásokban. JavaScript vagy írógéppel használatával. Első lépésként tekintse meg a [dokumentációt](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module).

**A Azure Maps Android SDK natív módon támogatja az időjárási szolgáltatások integrációját?**

A Azure Maps Android SDK-k támogatják a Mercator csempe-rétegeket, amelyek x/y/nagyítási jelöléssel, quad Key jelöléssel vagy EPSG 3857-alapú határolókeret-jelöléssel rendelkezhetnek.

Azt tervezzük, hogy a web SDK-modulhoz hasonló Java/Android Services-modult hozzunk létre. Az Android-szolgáltatások modul megkönnyíti az összes Azure Maps-szolgáltatás elérését egy Java-vagy Android-alkalmazásban.  

## <a name="data-visualizations"></a>Adatmegjelenítések  

**Azure Maps Power BI vizuális támogatás Azure Maps Weather csempe?**

Igen. Ha szeretné megtudni, hogyan telepítse át a radar és az infravörös műhold csempéit a Microsoft Power BI-vizualizációba, tekintse meg [a csempék hozzáadása Power bi a vizualizációhoz](https://docs.microsoft.com/azure/azure-maps/power-bi-visual-add-tile-layer)című témakört. 

**Hogyan a radar és a műholdas csempék színének értelmezését?**

A Azure Maps [időjárási koncepciója című cikk](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#radar-and-satellite-imagery-color-scale) egy útmutatót tartalmaz a radar és a műholdas csempék színeinek értelmezéséhez. A cikk a színes mintákat és a HEXADECIMÁLIS színkódokat tartalmazza.
 
**Létrehozhatok radar-és műholdas csempéket is?**

Igen. A valós idejű radar-és műholdas csempén kívül Azure Maps ügyfelek a térképes átfedésekkel rendelkező adatvizualizációk fejlesztéséhez a múltbeli és a jövőbeli csempéket is kérhetik. Ezt a [Get Map csempe v2 API](https://aka.ms/AzureMapsWeatherTiles ) közvetlen hívásával vagy a csempék Azure Maps web SDK-n keresztül történő igénylésével teheti meg. A radar csempéi a múltban akár 1,5 óráig, illetve a későbbiekben akár 2 óráig is elérhetők. A csempék 5 percenként érhetők el. Az infravörös csempék a múltban akár 3 óráig is rendelkezésre állnak, és 10 perces időközönként érhetők el. További információ: nyílt forráskódú időjárási csempe animációs [kód mintája](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer).  

**Különböző időjárási feltételekhez tartozó ikonokat kínál?**

Igen. [Itt](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#weather-icons)megtalálhatja a megfelelő ikonokat és a hozzájuk tartozó kódokat. Figyelje meg, hogy csak néhány az időjárási szolgáltatás API-jai, például a  [jelenlegi feltételek API beolvasása](https://aka.ms/azuremapsweathercurrentconditions), a válasz *iconCode* visszaadása. További információkért tekintse meg az aktuális WeatherConditions nyílt forráskódú [kódú mintát](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location).

## <a name="next-steps"></a>További lépések

Ha ez a GYIK nem válaszol a kérdésére, az alábbi csatornákon keresztül léphet velünk kapcsolatba:

* A cikk megjegyzések szakasza.
* [MSFT Q&a Azure Maps oldalát](https://docs.microsoft.com/answers/topics/azure-maps.html).
* Microsoft ügyfélszolgálata. Új támogatási kérelem létrehozásához a [Azure Portal](https://portal.azure.com/)Súgó lapján kattintson a **Súgó +** támogatás gombra, majd válassza az **új támogatási kérelem** lehetőséget.
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) küldéséhez.

Ismerje meg, hogyan kérhet valós idejű és előre jelzett időjárási információkat Azure Maps időjárási szolgáltatások használatával:
> [!div class="nextstepaction"]
> [Valós idejű időjárási adatgyűjtés kérése ](how-to-request-weather-data.md)

Azure Maps Weather Services – fogalmakat ismertető cikk:
> [!div class="nextstepaction"]
> [Időjárási szolgáltatások – fogalmak](weather-coverage.md)

Ismerje meg a Azure Maps Weather Services API dokumentációját:

> [!div class="nextstepaction"]
> [Azure Maps időjárási szolgáltatások](/rest/api/maps/weather)
