---
title: 'K – fürtözés: modulok leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning a fürtözési modelleket a fürtözési modellek betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/04/2020
ms.openlocfilehash: 7e0b61c1ca6ae30044e4c9d4705bdce01eac1942
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93323685"
---
# <a name="module-k-means-clustering"></a>Modul: K – fürtözést jelenti

Ez a cikk azt ismerteti, hogyan használható a *"k-meaning" típusú fürtözési* modul a Azure Machine learning Designerben egy nem betanított K – fürtözési modell létrehozásához. 
 
A K-means az egyik legegyszerűbb és legismertebb, nem *felügyelt* tanulási algoritmus. Az algoritmus számos gépi tanulási feladathoz használható, például: 

* [Rendellenes adatelemzések észlelése](/archive/msdn-magazine/2013/february/data-clustering-detecting-abnormal-data-using-k-means-clustering).
* Fürtözött szöveges dokumentumok.
* Adatkészletek elemzése más besorolási vagy regressziós módszerek használata előtt. 

A fürtszolgáltatási modell létrehozásához a következőket kell tennie:

* Adja hozzá ezt a modult a folyamathoz.
* Adatkészlet csatlakoztatása.
* Állítsa be a paramétereket, például a várt fürtök számát, a fürtök létrehozásához használandó távolsági metrikát és így tovább. 
  
A modul hiperparaméterek beállítása konfigurálása után a nem betanított modellt a [vonat-fürtszolgáltatási modellhez](train-clustering-model.md)kell kötni. Mivel a K-means algoritmus egy nem felügyelt tanulási módszer, a Label (címke) oszlop nem kötelező. 

+ Ha az adatok címkét tartalmaznak, a címke értékeit követve megtekintheti a fürtök kijelölését, és optimalizálhatja a modellt. 

+ Ha az adatai nem rendelkeznek címkével, az algoritmus kizárólag az adatain alapuló, lehetséges kategóriákat jelképező fürtöket hoz létre.  

##  <a name="understand-k-means-clustering"></a>A K értelmezése: fürtözés
 
Általánosságban elmondható, hogy a fürtözés iterációs technikákat használ az adatkészletekben lévő esetek csoportosítására a hasonló tulajdonságokkal rendelkező fürtökön. Ezek a Csoportosítások hasznosak az adatelemzéshez, az adatrendellenességek azonosításához, és végül az előrejelzések készítéséhez. A fürtözési modellek segíthetnek az adatkészletben lévő olyan kapcsolatok azonosításában is, amelyeket nem lehet logikailag a tallózással vagy egyszerű megfigyeléssel származtatni. Ezen okok miatt a fürtözést gyakran használják a gépi tanulási feladatok korai fázisában, az adatok feltárására és a váratlan korrelációk felderítésére.  
  
 Ha a K-Mean metódus használatával konfigurál egy fürtszolgáltatási modellt, meg kell adnia a *centroids* *kívánt számát a* modellben. A középpontját az egyes fürtökre jellemző pont. A K-azt jelenti, hogy az algoritmus az egyes bejövő adatpontokat az egyik fürthöz rendeli úgy, hogy minimalizálja a mezőkön belüli összegét. 
 
Amikor feldolgozza a betanítási adatmennyiséget, a K-means algoritmus a véletlenszerűen kiválasztott centroids kezdeti készletével kezdődik. A Centroids kiindulási pontként szolgálnak a fürtökhöz, és a Lloyd algoritmust alkalmazva iteratív a helyüket. A K-means algoritmus a fürtök kiépítése és finomítása miatt leáll, ha az megfelel a következő feltételek közül:  
  
-   A centroids stabilizálása, ami azt jelenti, hogy az egyes pontokhoz tartozó fürt-hozzárendelések már nem változnak, és az algoritmus egy megoldáshoz konvergált.  
  
-   Az algoritmus a megadott számú iteráció futtatásával fejeződött be.  
  
 Miután elvégezte a betanítási fázist, az [adatgyűjtés a fürtökhöz](assign-data-to-clusters.md) modul használatával új eseteket rendelhet hozzá a K-means algoritmus használatával talált fürtök valamelyikéhez. A fürt kiosztását az új eset és az egyes fürtök középpontját közötti távolság kiszámításával végezheti el. Minden új eset a legközelebbi középpontját rendelkező fürthöz van rendelve.  

## <a name="configure-the-k-means-clustering-module"></a>A K-Meaning-fürtszolgáltatási modul konfigurálása
  
1.  Adja hozzá a **K-means fürtszolgáltatási** modult a folyamathoz.  
  
2.  A modell képzésének megadásához válassza az **oktatói mód létrehozása** lehetőséget.  
  
    -   **Egyetlen paraméter**: Ha ismeri a fürtözési modellben használni kívánt pontos paramétereket, megadhatja az értékek egy adott halmazát argumentumként.  
  
3.  A **Centroids száma** mezőben adja meg, hogy hány fürtből kell kezdeni az algoritmust.  
  
     A modell nem garantált, hogy pontosan ezt a számú fürtöt hozza létre. Az algoritmus ezzel a számú adatponttal és iterációval kezdődik az optimális konfiguráció megtalálásához. A [sklearn forráskódját](https://github.com/scikit-learn/scikit-learn/blob/fd237278e/sklearn/cluster/_kmeans.py#L1069)a következő címen tekintheti meg:.
  
4.  A tulajdonságok **inicializálásával** adhatja meg a fürt kezdeti konfigurációjának definiálásához használt algoritmust.  
  
    -   **Első N**: egyes adatpontok kezdeti száma az adatkészletből van kiválasztva, és a kezdeti módon használatos. 
    
         Ezt a metódust *hamis metódusnak* is nevezzük.  
  
    -   **Véletlenszerű**: az algoritmus véletlenszerűen elhelyez egy adatpontot a fürtben, majd kiszámítja a kezdeti középértéket, hogy a fürt véletlenszerűen hozzárendelt pontjainak középpontját legyen. 

         Ezt a metódust *véletlenszerű partíciós* metódusnak is nevezzük.  
  
    -   **K-azt jelenti + +**: ez az alapértelmezett módszer a fürtök inicializálásához.  
  
         A **k-= + +** algoritmust a 2007-as, David Arthur és Szergej Vassilvitskii javasolta, hogy elkerülje a standard k-means algoritmus általi gyenge fürtözést. A **k-azt jelenti** , hogy a + + és a standard szintű k-t is javítja, ha más módszert használ a kezdeti fürtcsomópontok kiválasztásához.  
  
    
5.  A **véletlenszerű számú magok** esetében szükség esetén adja meg a fürt inicializálásának magjaként használandó értéket. Ez az érték jelentős hatással lehet a fürt kiválasztására.  
  
6.  A **metrika** mezőben válassza ki a fürt vektorai közötti távolság, illetve az új adatpontok és a véletlenszerűen kiválasztott középpontját közötti távolság méréséhez használni kívánt függvényt. Azure Machine Learning a következő szektorcsoport-távolsági metrikákat támogatja:  
  
    -   **Euklideszi**: a euklideszi távolságot általában a fürtök szórásának mértékéhez használják, amely a fürtözést jelenti. Ez a mérőszám előnyben részesített, mert a lekicsinyíti a pontok és a centroids közötti átlagos távolságot.
  
7.  Az **Ismétlések** mezőben adja meg, hogy az algoritmus hány alkalommal ismételje meg a betanítási adatmennyiséget, mielőtt véglegesíti a centroids kiválasztását.  
  
     Ezt a paramétert beállíthatja úgy, hogy a pontosságot kiegyensúlyozza a képzési idő alapján.  
  
8.  A **címke-hozzárendelési mód** beállításnál válassza ki azt a lehetőséget, amely meghatározza, hogy a felirat oszlop hogyan legyen kezelve az adatkészletben.  
  
     Mivel a K-azt jelenti, hogy a fürtözés nem felügyelt gépi tanulási módszer, a címkék megadása nem kötelező. Ha azonban az adatkészlet már rendelkezik címke oszloppal, ezeket az értékeket használhatja a fürtök kijelölésének irányításához, vagy megadhatja, hogy a rendszer figyelmen kívül hagyja-e az értékeket.  
  
    -   **Címke oszlopának mellőzése**: a címke oszlopban szereplő értékek figyelmen kívül lesznek hagyva, és nem használatosak a modell felépítésekor.
  
    -   **Hiányzó értékek kitöltése**: a címke oszlop értékei a fürtök létrehozásához használható funkciókként használatosak. Ha bármelyik sorban hiányzik egy címke, az érték más funkciók használatával lesz beszámítva.  
  
    -   **Felülírás a legközelebb a középponthoz**: a felirat oszlopainak értékei az aktuális középpontját legközelebb eső pont címkéjével lesznek lecserélve.  

8.  Válassza a **funkciók normalizálása** lehetőséget, ha a betanítás előtt szeretné normalizálni a szolgáltatásokat.
  
     Ha a normalizálás alkalmazása előtt betanítást alkalmaz, az adatpontokat a MinMaxNormalizer szerint normalizálja a rendszer `[0,1]` .

10. A modell betanítása.  
  
    -   Ha úgy állítja be az **oktatói módot** , hogy **egyetlen paramétert** adjon meg, vegyen fel egy címkézett adatkészletet, és adja ki a modellt a [vonat-fürtszolgáltatási modell](train-clustering-model.md) modullal.  
  
## <a name="results"></a>Results (Eredmények)

Miután befejezte a modell konfigurálását és betanítását, rendelkezik egy olyan modellel, amelynek használatával pontszámokat lehet előállítani. A modell tanítása több módon is elvégezhető, és az eredmények több módon is megtekinthetők és használhatók: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>A modell pillanatképének rögzítése a munkaterületen

Ha a vonat- [fürtszolgáltatási modell](train-clustering-model.md) modult használta:

1. Válassza ki a **vonat-fürtszolgáltatási modell** modult, és nyissa meg a jobb oldali panelt.

2. Válassza a **kimenetek** fület. Válassza az **adatkészlet regisztrálása** ikont a betanított modell másolatának mentéséhez.

A mentett modell a modell mentésekor a betanítási adategységeket jelöli. Ha később frissíti a folyamat során használt betanítási adattípust, nem frissíti a mentett modellt. 

### <a name="see-the-clustering-result-dataset"></a>Tekintse meg a fürtözési eredmény adatkészletét 

Ha a vonat- [fürtszolgáltatási modell](train-clustering-model.md) modult használta:

1. Kattintson a jobb gombbal a **vonat-fürtszolgáltatási modell** modulra.

2. Válassza a **Megjelenítés** lehetőséget.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tippek a legjobb fürtözési modell létrehozásához  

Ismeretes, hogy a fürtözés során használt *előkészítési* folyamat jelentős hatással lehet a modellre. A beültetés azt jelenti, hogy a pontok kezdeti elhelyezése lehetséges centroids.
 
Ha például az adatkészlet sok kiugró értéket tartalmaz, és a rendszer kiugróan kiválasztja a fürtöket, akkor más adatpontok nem illenek jól az adott fürthöz, és a fürt lehet egypéldányos. Ez azt okozhatja, hogy csak egy ponttal rendelkezhet.  
  
Ezt a problémát több módon is elkerülheti:  
  
-   Módosítsa a centroids számát, és próbálkozzon több kezdőtőke értékkel.  
  
-   Hozzon létre több modellt, amely változó a metrikát vagy az iterációt.  
  
A fürtözési modellek esetében általánosságban lehetséges, hogy bármely adott konfiguráció a fürtök helyileg optimalizált halmazát fogja eredményezni. Más szóval a modell által visszaadott fürtök halmaza csak az aktuális adatpontokat, és nem általánosítható más adatértékekhez. Ha más kezdeti konfigurációt használ, a K-Mean metódus egy másik, magasabb szintű konfigurációt is talál. 

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .