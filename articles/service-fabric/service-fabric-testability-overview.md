---
title: A hiba-elemzési szolgáltatás áttekintése
description: Ez a cikk a hiba-elemzési szolgáltatást ismerteti Service Fabric a hibák kiváltásához és a szolgáltatásokkal kapcsolatos tesztelési forgatókönyvek futtatásához.
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 4e879b0b39d58f115b4661d47cf2ae564ba14e5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531377"
---
# <a name="introduction-to-the-fault-analysis-service"></a>A hiba-elemzési szolgáltatás bemutatása
A hiba-elemzési szolgáltatás a Microsoft Azure Service Fabricra épülő szolgáltatások tesztelésére szolgál. A hiba-elemzési szolgáltatással értelmes hibákat válthat ki, és teljes körű tesztelési forgatókönyveket futtathat az alkalmazásain. Ezek a hibák és forgatókönyvek a számos államot, valamint a szolgáltatás teljes élettartama során felmerülő, ellenőrzött, biztonságos és konzisztens módon működtetett állapotokat gyakorolják és érvényesítik.

A műveletek a szolgáltatás tesztelésére szolgáló egyéni hibák. A szolgáltatás-fejlesztő ezeket a építőelemeket összetett forgatókönyvek írásához használhatja. Például:

* Indítsa újra a csomópontot, hogy tetszőleges számú helyzetet Szimuláljon, ha a gép vagy a virtuális gép újraindul.
* Helyezzen át egy replikát az állapot-nyilvántartó szolgáltatásból a terheléselosztás, a feladatátvétel vagy az alkalmazás frissítésének szimulálása érdekében.
* Állapot-nyilvántartó szolgáltatás kvórum elvesztésének meghívásával olyan helyzetet hozhat létre, amelyben az írási műveletek nem folytatódnak, mert nincs elég "biztonsági másolat" vagy "másodlagos" replika az új adatok elfogadásához.
* Egy állapot-nyilvántartó szolgáltatás adatvesztésének meghívásával olyan helyzetet hozhat létre, amelyben az összes memóriában tárolt állapot teljesen törlődik.

A forgatókönyvek egy vagy több műveletből álló összetett műveletek. A hiba-elemzési szolgáltatás két beépített teljes forgatókönyvet biztosít:

* Káosz forgatókönyv
* Feladatátvételi forgatókönyv

## <a name="testing-as-a-service"></a>Szolgáltatásként történő tesztelés
A hiba-elemzési szolgáltatás egy Service Fabric rendszerszolgáltatás, amely automatikusan elindul egy Service Fabric-fürttel. Ez a szolgáltatás gazdagépként működik a hibák befecskendezése, a tesztelési forgatókönyvek végrehajtása és az állapot elemzése során. 

![Hiba-elemzési szolgáltatás][0]

Ha egy hiba vagy tesztelési forgatókönyvet kezdeményeznek, a rendszer egy parancsot kap a hiba-elemzési szolgáltatásnak a művelet vagy tesztelési forgatókönyv futtatásához. A hiba-elemzési szolgáltatás állapotú, hogy megbízhatóan futtasson hibákat és forgatókönyveket, és érvényesítse az eredményeket. Egy hosszan futó tesztelési forgatókönyv esetében például a hiba-elemzési szolgáltatás megbízhatóan képes végrehajtani. Mivel a teszteket a fürtön belül hajtják végre, a szolgáltatás megvizsgálja a fürt és a szolgáltatások állapotát, hogy részletesebb információkat nyújtson a hibákról.

## <a name="testing-distributed-systems"></a>Elosztott rendszerek tesztelése
A Service Fabric jelentősen megkönnyíti az elosztott méretezhető alkalmazások írását és kezelését. A hiba-elemzési szolgáltatás hasonló módon teszi lehetővé az elosztott alkalmazások tesztelését. A tesztelés során három fő problémát kell megoldani:

1. Valós helyzetekben előforduló hibák szimulálása/generálása: a Service Fabric egyik fontos aspektusa, hogy az elosztott alkalmazások a különböző hibákból való helyreállítást lehetővé teszik. Azonban annak ellenőrzéséhez, hogy az alkalmazás képes-e helyreállítani ezeket a hibákat, szükségünk van egy mechanizmusra, amellyel szimulálhatja vagy előállíthatja ezeket a valós hibákat egy ellenőrzött tesztkörnyezetben.
1. Korrelált hibák hozhatók létre: a rendszer alapvető meghibásodásai, például a hálózati hibák és a gépi meghibásodások egyszerűen előállíthatók egyenként. Ha jelentős számú forgatókönyvet generálnak, amelyek a valós világban történnek az egyes hibák interakciója miatt, nem triviálisak.
1. Egységes felhasználói élmény a különböző fejlesztési és üzembe helyezési szinteken: számos hibás befecskendező rendszer létezik, amely különböző típusú hibákat tehet elérhetővé. Ugyanakkor az is előfordulhat, hogy az egyablakos fejlesztői forgatókönyvek esetében a tapasztalatok mindegyike gyenge, hogy az éles tesztelési környezetekben is ugyanezeket a teszteket használja.

Habár számos mechanizmust kell megoldani ezen problémák megoldására, egy olyan rendszerre, amely a szükséges garanciákkal megegyezően működik – mindezt egy egymezős fejlesztői környezetből az üzemi fürtök tesztelésére – hiányzik. A hiba-elemzési szolgáltatás segítségével az alkalmazások fejlesztői az üzleti logikájuk tesztelésére koncentrálnak. A hiba-elemzési szolgáltatás biztosítja az összes olyan funkciót, amely a szolgáltatás interakciójának teszteléséhez szükséges a mögöttes elosztott rendszerrel.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Valós idejű meghibásodási forgatókönyvek szimulálása/generálása
Az elosztott rendszer meghibásodások elleni stabilitásának teszteléséhez olyan mechanizmusra van szükség, amely hibákat eredményez. Elméletileg egy olyan hiba jön létre, mint egy csomópont, amely úgy tűnik, hogy egyszerű, és ugyanazokat a konzisztencia-problémákat indítja el, amelyeket a Service Fabric megpróbál megoldani. Ha például egy csomópontot szeretnénk leállítani, a szükséges munkafolyamat a következő:

1. Adja ki az ügyféltől a leállítási csomópont kérését.
1. Küldje el a kérést a jobb oldali csomópontnak.
   
    a. Ha a csomópont nem található, a művelet sikertelen lesz.
   
    b. Ha a csomópont található, csak akkor kell visszaadnia, ha a csomópont le van állítva.

A tesztelési perspektívából érkező hibák ellenőrzéséhez a tesztnek tudnia kell, hogy a hiba kiváltása esetén a hiba ténylegesen megtörténik. A Service Fabric által biztosított garancia az, hogy vagy a csomópont leáll, vagy már leállt, amikor a parancs elérte a csomópontot. A tesztnek mindkét esetben képesnek kell lennie arra, hogy megfelelően indokolja az állapotot, és sikeres vagy sikertelen legyen az érvényesítése. A Service Fabricon kívül megvalósított rendszerek számos hálózati, hardver-és szoftverbeli problémát okozhatnak, ami megakadályozza az előző garanciák megadását. A korábban említett problémák előfordulásakor a Service Fabric újrakonfigurálja a fürt állapotát, hogy megkerülje a problémákat, és így a hiba-elemzési szolgáltatás továbbra is a megfelelő garanciákat fogja biztosítani.

### <a name="generating-required-events-and-scenarios"></a>Szükséges események és forgatókönyvek létrehozása
A valós idejű hibák következetes szimulálása azonban nehéz, hogy a korrelált hibák is keményebbek legyenek. Az adatvesztés például egy állapot-nyilvántartó megőrzött szolgáltatásban történik, ha a következő dolgok történnek:

1. A replikálás csak a replikák írási kvórumát kapja meg. Az összes másodlagos replika az elsődleges mögött marad.
1. Az írási kvórum leáll, mert a replikák le lesznek mentve (egy kód-csomag vagy egy csomópont leállása miatt).
1. Az írási kvórum nem tud biztonsági másolatot készíteni, mert a replikák adatvesztése (a lemez sérülése vagy a gép újratelepítése miatt) elvész.

Ezek a korrelált hibák a valós világban történnek, de nem olyan gyakran, mint az egyéni hibák. Az ilyen forgatókönyvek tesztelésének lehetősége az éles környezetben való működésük előtt kritikus fontosságú. Még ennél is fontosabb, hogy képes legyen szimulálni ezeket a forgatókönyveket a termelési számítási feladatokkal az ellenőrzött körülmények között (a nap közepén a pakliban található összes mérnök esetében). Ez sokkal jobb, mint az első alkalommal az éles üzemben, 2:00 órakor

### <a name="unified-experience-across-different-environments"></a>Egységes felhasználói élmény különböző környezetekben
A gyakorlat hagyományosan három különböző felhasználói felületet hozott létre, egyet a fejlesztési környezethez, egyet a tesztekhez, egyet pedig az éles környezetben. A modell a következőket használta:

1. A fejlesztési környezetben olyan állapot-átállásokat hozhat létre, amelyek lehetővé teszik az egyes metódusok egységes tesztelését.
1. A tesztkörnyezetben olyan hibák hozhatók létre, amelyek lehetővé teszik a különböző meghibásodási forgatókönyveket használó teljes körű tesztek elvégzését.
1. Tartsa érintetlenül az éles környezetben a nem természetes meghibásodásokat, és győződjön meg arról, hogy rendkívül gyors emberi reagálási hiba történt.

Service Fabric a hiba-elemzési szolgáltatáson keresztül azt javasoljuk, hogy kapcsolja be ezt a módszert, és használja ugyanezt a módszertant a fejlesztői környezetből a gyártásba. A következő két módon valósítható meg:

1. Az ellenőrzött hibák kiváltásához használja a hiba-elemzési szolgáltatás API-jait egy egymezős környezetből, egészen a termelési fürtökig.
1. Annak érdekében, hogy a fürt olyan láz legyen, amely a hibák automatikus leállítását okozza, a hiba-elemzési szolgáltatással automatikus hibákat állíthat elő. A hibák sebességének szabályozása a konfiguráción keresztül lehetővé teszi, hogy ugyanazt a szolgáltatást különböző környezetekben különbözőképpen tesztelje.

A Service Fabric, bár a hibák skálája eltérő lehet a különböző környezetekben, a tényleges mechanizmusok azonosak lesznek. Ez egy sokkal gyorsabb, a központi telepítéshez szükséges folyamat, valamint a szolgáltatások valós terhelésekkel való tesztelésének lehetősége.

## <a name="using-the-fault-analysis-service"></a>A hiba-elemzési szolgáltatás használata
**C#**

A hiba-elemzési szolgáltatás funkciói a System. Fabric névtérben találhatók a Microsoft. ServiceFabric NuGet-csomagban. A hiba-elemzési szolgáltatás funkcióinak használatához adja meg a nuget csomagot hivatkozásként a projektben.

**PowerShell**

A PowerShell használatához telepítenie kell a Service Fabric SDK-t. Az SDK telepítése után a ServiceFabric PowerShell-modul automatikusan betöltődik a használatára.

## <a name="next-steps"></a>Következő lépések
A valóban felhőbe méretezhető szolgáltatások létrehozásához elengedhetetlen, hogy az üzembe helyezés előtt és után is gondoskodjon arról, hogy a szolgáltatások valós hibákkal bírjanak. A szolgáltatások világában ma már nagyon fontos a gyors innováció és a kód gyors mozgatása az éles környezetbe. A hiba-elemzési szolgáltatás segít a szolgáltatás fejlesztői számára, hogy pontosan ezt végezzék.

Megkezdheti az alkalmazások és szolgáltatások tesztelését a beépített [tesztelési forgatókönyvek](service-fabric-testability-scenarios.md)használatával, vagy saját tesztelési forgatókönyvek létrehozásával a hiba-elemzési szolgáltatás által biztosított [hibákkal](service-fabric-testability-actions.md) .

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
