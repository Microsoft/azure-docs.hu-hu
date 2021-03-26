---
title: 'ML Studio (klasszikus): áttelepítés Azure Machine Learning'
description: Migrálás a studióból (klasszikus) Azure Machine Learning egy modernizált adatelemzési platformra.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 7cdf016d7de66c33973ec7818fa92df28c4639dd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566955"
---
# <a name="migrate-to-azure-machine-learning"></a>Migrálás az Azure Machine Learningbe

Ebből a cikkből megtudhatja, hogyan telepítheti át a Studio (klasszikus) eszközöket Azure Machine Learningba. Jelenleg az erőforrások áttelepíthetők, manuálisan kell újraépíteni a kísérleteket.

A Azure Machine Learning egy modernizált adatelemzési platformot biztosít, amely a kód nélküli és a kód első megközelítését ötvözi. Ha többet szeretne megtudni a Studio (klasszikus) és a Azure Machine Learning közötti különbségekről, tekintse meg a [Azure Machine learning értékelése](#step-1-assess-azure-machine-learning) című szakaszt.


## <a name="recommended-approach"></a>Ajánlott megközelítés

A Azure Machine Learningra való Migrálás során a következő módszert javasoljuk:

> [!div class="checklist"]
> * 1. lépés: Azure Machine Learning értékelése
> * 2. lépés: áttelepítési terv létrehozása
> * 3. lépés: kísérletek és webszolgáltatások újraépítése
> * 4. lépés: ügyfélalkalmazások integrálása
> * 5. lépés: a Studio (klasszikus) eszközök tisztítása


## <a name="step-1-assess-azure-machine-learning"></a>1. lépés: Azure Machine Learning értékelése
1. A [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/)megismerése az előnyök, a költségek és az architektúra.

1. Hasonlítsa össze a Azure Machine Learning és a Studio (klasszikus) képességeit.

    >[!NOTE]
    > A Azure Machine Learning **Designer** funkciója hasonló húzási élményt biztosít a Studio (klasszikus) számára. Ugyanakkor a Azure Machine Learning robusztus, [kód-első munkafolyamatokat](../concept-model-management-and-deployment.md) is biztosít Alternatív megoldásként. Ez az áttelepítési sorozat a tervezőre összpontosít, mivel a Studio (klasszikus) felületéhez hasonló.

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Ellenőrizze, hogy a kritikus Studio (klasszikus) modulok támogatottak-e a Azure Machine Learning Designerben. További információkért lásd a [Studio (klasszikus) és a Designer modul-leképezési](#studio-classic-and-designer-module-mapping) táblázatot alább.

4. [Hozzon létre egy Azure Machine learning munkaterületet](../how-to-manage-workspace.md?tabs=azure-portal).

## <a name="step-2-create-a-migration-plan"></a>2. lépés: áttelepítési terv létrehozása

1. Azonosítsa az áttelepíteni kívánt Studio (klasszikus) **adatkészleteket**, **modelleket** és **webszolgáltatásokat** .

1. Állapítsa meg, hogy az áttelepítés milyen hatással lesz a vállalatra.

1. Hozzon létre egy áttelepítési tervet.

## <a name="step-3-rebuild-experiments-and-web-services"></a>3. lépés: kísérletek és webszolgáltatások újraépítése

1. [Adatkészletek migrálása a Azure Machine Learningba](migrate-register-dataset.md).
1. A Designer használatával [újraépítheti a kísérleteket](migrate-rebuild-experiment.md).
1. A Designer használatával telepítse újra a [webszolgáltatásokat](migrate-rebuild-web-service.md).

    >[!NOTE]
    > A Azure Machine Learning támogatja az [adatkészletek](../how-to-create-register-datasets.md), a [képzések](../how-to-set-up-training-targets.md)és a [központi telepítés](../how-to-deploy-and-where.md)kódjának első munkafolyamatait is.

## <a name="step-4-integrate-client-apps"></a>4. lépés: ügyfélalkalmazások integrálása

1. A Studio (klasszikus) webszolgáltatásokat meghívó ügyfélalkalmazások módosítása az új [Azure Machine learning-végpontok](migrate-rebuild-integrate-with-client-app.md)használatára.

## <a name="step-5-cleanup-studio-classic-assets"></a>5. lépés: a Studio (klasszikus) eszközök tisztítása

1. [Törölje a Studio (klasszikus) eszközöket](export-delete-personal-data-dsr.md) a további költségek elkerülése érdekében. Előfordulhat, hogy vissza kívánja tartani az eszközöket tartalékként, amíg meg nem érvényesítette Azure Machine Learning munkaterheléseket.

## <a name="studio-classic-and-designer-module-mapping"></a>Studio (klasszikus) és Designer modul – leképezés

A következő táblázatból megtudhatja, hogy mely modulokat kell használni a Studio (klasszikus) kísérletek a tervezőben való újraépítésekor.


> [!IMPORTANT]
> A tervező a C#-csomagok, például a Studio (klasszikus) helyett nyílt forráskódú Python-csomagokon keresztül valósítja meg a modulokat. Ennek a különbségnek a következtében a Designer modulok kimenete a Studio (klasszikus) partnereitől némileg eltérő lehet.


|Kategória|Studio (klasszikus) modul|Helyettesítő Designer modul|
|--------------|----------------|--------------------------------------|
|Adatbemenet és -kimenet|– Manuálisan adja meg az adatbevitelt </br> – Az adatexportálás </br> – Az adatimportálás </br> -Betanított modell betöltése </br> -Tömörített adatkészletek kicsomagolása|– Manuálisan adja meg az adatbevitelt </br> – Az adatexportálás </br> – Az adatimportálás|
|Adatformátum-konverziók|– Konvertálás CSV-re </br> – Átalakítás adatkészletbe </br> – Konvertálás ARFF </br> – Konvertálás SVMLight </br> – Váltás TSV formátumra|– Konvertálás CSV-re </br> – Átalakítás adatkészletbe|
|Adatátalakítás – manipuláció|-Oszlopok hozzáadása</br> – Sorok hozzáadása </br> -SQL-transzformáció alkalmazása </br> -Hiányzó Adattisztítás </br> – Váltás a kijelző értékeire </br> -Metaadatok szerkesztése </br> – Az Adategyesítés </br> – Ismétlődő sorok eltávolítása </br> -Oszlopok kiválasztása az adatkészletben </br> -Oszlopok átalakításának kiválasztása </br> – ARCUL ütötte </br> -Csoport kategorikus értékei|-Oszlopok hozzáadása</br> – Sorok hozzáadása </br> -SQL-transzformáció alkalmazása </br> -Hiányzó Adattisztítás </br> – Váltás a kijelző értékeire </br> -Metaadatok szerkesztése </br> – Az Adategyesítés </br> – Ismétlődő sorok eltávolítása </br> -Oszlopok kiválasztása az adatkészletben </br> -Oszlopok átalakításának kiválasztása </br> – ARCUL ütötte|
|Adatátalakítás – méretezés és csökkentés |– Klip értékei </br> -Adatcsoportosítás a rekeszekben </br> – Az adatnormalizálás </br>– Elsődleges összetevő elemzése |– Klip értékei </br> -Adatcsoportosítás a rekeszekben </br> – Az adatnormalizálás|
|Adatátalakítás – minta és felosztás|– Partíció és minta </br> – Az adatfelosztás|– Partíció és minta </br> – Az adatfelosztás|
|Adatátalakítás – szűrő |-Szűrő alkalmazása </br> -FENYŐ szűrő </br> -IIR szűrő </br> – Medián szűrő </br> – Átlagos szűrő áthelyezése </br> -Küszöbérték szűrő </br> – Felhasználó által definiált szűrő||
|Adatátalakítás – megtanulás a Counts szolgáltatással |– Leltár átalakításának létrehozása </br> -Export Count tábla </br> – Importálások száma tábla </br> – Egyesítési szám átalakítása</br>  -A Count tábla paramétereinek módosítása||
|Szolgáltatás kiválasztása |– Szűrésen alapuló funkció kiválasztása </br> -Fisher lineáris diszkrimináns elemzése  </br> -Permutáció funkció fontossága |– Szűrésen alapuló funkció kiválasztása </br>  -Permutáció funkció fontossága|
| Modell besorolása| – Többosztályos döntési erdő </br> – Többosztályos döntési dzsungel  </br> – Többosztályos logisztikai regresszió  </br>– Többosztályos neurális hálózat  </br>– One-vs-All Multiclass </br>– Two-Class átlagos Perceptron </br>-Two-Class Bayes pont gép </br>– Two-Class megnövelt döntési fa  </br> -Two-Class döntési erdő  </br> – Two-Class döntési dzsungel  </br> – Two-Class Locally-Deep SVM </br> – Two-Class logisztikai regresszió  </br> -Two-Class neurális hálózat </br> -Two-Class támogatási vektoros gép  | – Többosztályos döntési erdő </br>  – Többosztályos kiemelés döntési fája  </br> – Többosztályos logisztikai regresszió </br> – Többosztályos neurális hálózat </br> – One-vs-All Multiclass  </br> – Two-Class átlagos Perceptron  </br> – Two-Class megnövelt döntési fa  </br> -Two-Class döntési erdő </br>– Two-Class logisztikai regresszió </br> -Two-Class neurális hálózat </br>-Two-Class támogatási vektoros gép  |
| Modell – fürtözés| -K – fürtözést jelent| -K – fürtözést jelent|
| Modell – regresszió| – Bayes lineáris regresszió  </br> – A döntési fa regressziójának megerősítése  </br>-Döntési erdő regressziója  </br> -Gyors erdő Quantile-regresszió  </br> -Lineáris regresszió </br> -Neurális hálózat regressziója </br> -Sorszám regresszió Poisson-regresszió| – A döntési fa regressziójának megerősítése  </br>-Döntési erdő regressziója  </br> -Gyors erdő Quantile-regresszió </br> -Lineáris regresszió  </br> -Neurális hálózat regressziója </br> – Poisson-regresszió|
| Model – anomáliák észlelése| – One-Class SVM  </br> -PCA-Based anomáliák észlelése | -PCA-Based anomáliák észlelése|
| Machine Learning – Értékelés  | -Cross validate Model  </br>-Modell kiértékelése  </br>-Az ajánló kiértékelése | -Cross validate Model  </br>-Modell kiértékelése </br> -Az ajánló kiértékelése|
| Machine Learning – betanítás| – Fürtözés megtakarítása  </br> -Rendellenesség-észlelési modell tanítása </br>-Fürtszolgáltatási modell betanítása  </br> -Train Matchbox ajánló –</br> Modell betanítása  </br>– Modell Hiperparaméterek beállítása hangolása| -Rendellenesség-észlelési modell tanítása  </br> -Fürtszolgáltatási modell betanítása </br> -Train Model –</br> -PyTorch-modell betanítása  </br>-A Train SVD ajánló  </br>– Széles és mély ajánló </br>– Modell Hiperparaméterek beállítása hangolása|
| Machine Learning – pontszám| – Átalakítás alkalmazása  </br>-Az adathalmazok társítása  </br>– Pontszám Matchbox ajánló </br> – Pontszám modell|– Átalakítás alkalmazása  </br> -Az adathalmazok társítása </br> -Pontszám képmodell  </br> – Pontszám modell </br>– Pontszám SVD ajánló </br> -Pontszám széles és mély ajánló|
| OpenCV függvénytár-modulok| – Lemezképek importálása </br>-Előre betanított lépcsőzetes képosztályozás | |
| Python nyelvi modulok| – Python-szkript végrehajtása| – Python-szkript végrehajtása  </br> – Python-modell létrehozása |
| R nyelvi modulok  | -R-szkript végrehajtása  </br> – R-modell létrehozása| -R-szkript végrehajtása|
| Statisztikai függvények | – Matematikai művelet alkalmazása </br>-Számítási elemi statisztikák  </br>-Számítási lineáris korreláció  </br>– A valószínűségi függvény kiértékelése  </br>– Különálló értékek cseréje  </br>– Az adatösszesítés  </br>-Teszt hipotézis a t-test használatával| – Matematikai művelet alkalmazása  </br>– Az adatösszesítés|
| Text Analytics| – Nyelvek felismerése  </br>– Billentyűparancsok kinyerése szövegből  </br>– N-Gram-funkciók kinyerése szövegből  </br>-Szolgáltatások kivonatolása </br>– Látens Dirichlet kiosztása  </br>-Nevesített entitások felismerése </br>-Előfeldolgozási szöveg  </br>-Score Vowpal Wabbit 7-10-es verziójának modellje  </br>-Pontszám Vowpal Wabbit 8-as verziójú modell </br>– A Vowpal Wabbit 7-10-es verziójának modellje  </br>-A Vowpal Wabbit 8-as verziójú modellje |– A Word konvertálása vektorba </br> – N-Gram-funkciók kinyerése szövegből </br>-Szolgáltatások kivonatolása  </br>– Látens Dirichlet kiosztása </br>-Előfeldolgozási szöveg  </br>-Score Vowpal Wabbit-modell </br> -Vowpal Wabbit-modell betanítása|
| Idősorozat| -Idősorozat-anomáliák észlelése | |
| Webszolgáltatás | – Bemenet </br> -Kimenet | – Bemenet </br>  - Kimenet|
| Computer Vision| | – Rendszerkép-átalakítás alkalmazása </br> – Konvertálás rendszerkép-könyvtárba </br> -Init rendszerkép-átalakítás </br> -Képek felosztása könyvtár  </br> -DenseNet-rendszerkép besorolása   </br>-ResNet-rendszerkép besorolása |

A Designer modulok egyéni használatáról további információt a [Designer modul referenciája](../algorithm-module-reference/module-reference.md)tartalmaz.

### <a name="what-if-a-designer-module-is-missing"></a>Mi a teendő, ha a tervező modul hiányzik?

A Azure Machine Learning Designer a Studio (klasszikus) legnépszerűbb moduljait tartalmazza. Emellett olyan új modulokat is tartalmaz, amelyek kihasználják a legújabb gépi tanulási technikákat. 

Ha az áttelepítés a tervezőben hiányzó modulok miatt le van tiltva, vegye fel velünk [a kapcsolatot támogatási jegy létrehozásával](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="example-migration"></a>Példa áttelepítésre

A következő kísérlet áttelepítése kiemeli a Studio (klasszikus) és a Azure Machine Learning közötti különbségeket.

### <a name="datasets"></a>Adathalmazok

A Studióban (klasszikus) az **adathalmazokat** a munkaterületen mentik, és csak a Studio (klasszikus) használatával lehet használni.

![személygépkocsi – ár – klasszikus – adatkészlet](./media/migrate-overview/studio-classic-dataset.png)

A Azure Machine Learningban az **adatkészletek** regisztrálva vannak a munkaterületen, és az összes Azure Machine learning használhatók. További információ a Azure Machine Learning adatkészletek előnyeiről: az [adathozzáférés biztonságossá tétele](../concept-data.md#reference-data-in-storage-with-datasets).

![személygépkocsi – ár – pénzmosás – adatkészlet](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>Folyamat

A Studióban (klasszikus) a **kísérletek** tartalmazzák a munkához szükséges feldolgozási logikát. A kísérleteket a fogd és vidd modulokkal hozta létre.


![személygépkocsi – ár – klasszikus – kísérlet](./media/migrate-overview/studio-classic-experiment.png)

Azure Machine Learningban a **folyamatok** a munkához szükséges feldolgozási logikát tartalmazzák. A folyamatokat húzással is létrehozhatja, vagy kódot írhat.

![személygépkocsi – ár – pénzmosás – folyamat](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Webszolgáltatás végpontja

A Studio (klasszikus) esetében a **kérelem/válasz API** a valós idejű előrejelzéshez lett használva. A Batch- **végrehajtási API** -t a kötegelt előrejelzéshez vagy újraképzéshez használták.

![személygépkocsi-ár-klasszikus – webszolgáltatás](./media/migrate-overview/studio-classic-web-service.png)

A Azure Machine Learning **valós** idejű előrejelzéseket használunk. A **folyamat végpontjai** a kötegelt előrejelzéshez vagy az átképzéshez használatosak.

![személygépkocsi – ár-pénzmosás – végpont](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogy milyen magas szintű követelményeket kell áttelepíteni a Azure Machine Learningre. A részletes lépésekért tekintse meg a Studio (klasszikus) áttelepítési sorozat további cikkeit:

1. **Áttelepítési áttekintés**.
1. [Adatkészlet Átmigrálása](migrate-register-dataset.md).
1. [Építse újra a Studio (klasszikus) betanítási folyamatát](migrate-rebuild-experiment.md).
1. [Építse újra a Studio (klasszikus) webszolgáltatást](migrate-rebuild-web-service.md).
1. [Azure Machine learning webszolgáltatások integrálása az ügyfélalkalmazások alkalmazásával](migrate-rebuild-integrate-with-client-app.md).
1. [Telepítse át az R-szkriptet](migrate-execute-r-script.md).