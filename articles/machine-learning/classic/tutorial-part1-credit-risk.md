---
title: 'ML Studio (klasszikus) oktatóanyag: hitelkockázat előrejelzése – Azure'
description: Részletes oktatóanyag, amely bemutatja, hogyan hozhat létre a hitelkockázat értékelésére szolgáló prediktív elemzési megoldást Azure Machine Learning Studio (klasszikus). Ez az oktatóanyag egy háromrészes oktatóanyag-sorozat első része.  Bemutatja, hogyan hozhat létre munkaterületet, tölthet fel és hozhat létre egy kísérletet.
keywords: hitelkockázat, prediktív elemzési megoldás,kockázatértékelés
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 571925586bf3b70c38260392bae49c5352f10194
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517518"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>1. Oktatóanyag: hitelkockázat előrejelzése – Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik.  ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus)   ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Ez az oktatóanyag részletesen bemutatja a prediktív elemzési megoldások fejlesztésének folyamatát. Egy egyszerű modellt fejleszt Machine Learning Studio (klasszikus).  Ezután Azure Machine Learning webszolgáltatásként helyezheti üzembe a modellt.  Ez a központilag telepített modell új adatelemzést végezhet az előrejelzésekhez. Ez az oktatóanyag **egy háromrészes oktatóanyag-sorozat első része**.

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

A hitelkockázat-értékelés összetett probléma, de ez az oktatóanyag egy kicsit egyszerűbbé válik. Példaként fogja használni, hogyan hozhat létre prediktív elemzési megoldást Microsoft Azure Machine Learning Studio (klasszikus) használatával. Ehhez a megoldáshoz Azure Machine Learning Studio (klasszikus) és egy Machine Learning webszolgáltatást fog használni.  

Ebben a három részből álló oktatóanyagban nyilvánosan elérhető hitelkockázat-adatforrásokkal kezdheti meg.  Ezután fejlesztheti és betaníthatja a prediktív modellt.  Végül üzembe helyezi a modellt webszolgáltatásként.

Az oktatóanyag ezen részében: 
 
> [!div class="checklist"]
> * Machine Learning Studio (klasszikus) munkaterület létrehozása
> * Meglévő adatok feltöltése
> * Kísérlet létrehozása

Ezután ezt a kísérletet a [2. részben szereplő modellek betanítására](tutorial-part2-credit-risk-train.md) , majd [a 3. részbe történő telepítésére](tutorial-part3-credit-risk-deploy.md)használhatja.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag azt feltételezi, hogy legalább egyszer már használta Machine Learning Studio (klasszikus), és hogy a gépi tanulási fogalmakat is megértette. Az útmutató azonban nem feltételezi, hogy a fent említett területeken szakértő lenne.

Ha még soha nem használta a **Azure Machine learning Studio (klasszikus)** alkalmazást, érdemes elindítania az [első adatelemzési kísérletet Azure Machine learning Studio (klasszikus)](create-experiment.md). A rövid útmutató végigvezeti Machine Learning Studio (klasszikus) az első alkalommal. Bemutatja az alapokat, azt, hogy hogyan húzhat be modulokat a kísérletbe és kapcsolhatja össze azokat, és hogyan futtathatja a kísérletet és tekintheti meg az eredményeket.


> [!TIP] 
> A jelen oktatóanyagban a [Azure AI Galleryban](https://gallery.azure.ai)kifejlesztett kísérlet egy működő példányát is megtalálhatja. Ugrás az **[oktatóanyagra – a hitelkockázat előrejelzése](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** , és a kísérlet egy példányának letöltéséhez kattintson az **Open in Studio (Megnyitás a Studióban** ) lehetőségre a Machine learning Studio (klasszikus) munkaterületre
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Machine Learning Studio (klasszikus) munkaterület létrehozása

Machine Learning Studio (klasszikus) használatához Microsoft Azure Machine Learning Studio (klasszikus) munkaterületre van szükség. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.  

Munkaterület létrehozásához lásd: [Azure Machine learning Studio (klasszikus) munkaterület létrehozása és megosztása](create-workspace.md).

A munkaterület létrehozása után nyissa meg Machine Learning Studio (klasszikus) ( [https://studio.azureml.net/Home](https://studio.azureml.net/Home) ). Ha több munkaterülettel rendelkezik, a munkaterületet az ablak jobb felső sarkában található eszköztárban választhatja ki.

![Munkaterület kiválasztása a Studióban (klasszikus)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Ha Ön a munkaterület tulajdonosa, megoszthatja azokat a kísérleteket, amelyekkel a munkaterületet másokkal meghívja. Ezt megteheti Machine Learning Studio (klasszikus) a **Beállítások** lapon. Az egyes felhasználókhoz csak Microsoft-fiók vagy szervezeti fiókra van szükség.
> 
> A **Beállítások** lapon kattintson a **felhasználók** elemre, majd kattintson az ablak ALJÁn található **további felhasználók meghívása** elemre.
> 

## <a name="upload-existing-data"></a><a name="upload"></a>Meglévő adatok feltöltése

A hitelkockázat-előrejelző modell kifejlesztéséhez olyan adatforrásokra van szükség, amelyeket a modell betanítására és tesztelésére használhat. Ebben az oktatóanyagban az "UCI statlog (német kredit adatok)" adatkészletet fogja használni az UC Irvine Machine Learning adattárból. Itt találja a következőt:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

A **német.** az adatfájlt fogja használni. Töltse le ezt a fájlt a helyi merevlemez-meghajtóra.  

A **német.** adatadatkészlet 20 változóból álló sorokat tartalmaz a 1000-es múltbeli pályázók számára. Ez a 20 változó az adatkészlet azon funkcióinak készletét jelöli (a *szolgáltatás vektora*), amely az egyes kreditek azonosítására szolgáló jellemzőket biztosít. Az egyes sorokban egy további oszlop jelenti a kérelmező számított hitelkockázat-kockázatát, amelynek 700 kérelmezője alacsony hitelkockázat és 300 magas kockázatnak minősül.

Az UCI webhely ismerteti az ehhez az adatszolgáltatási vektorhoz tartozó attribútumok leírását. Ezek az adatok a pénzügyi adatokat, a kreditek előzményeit, a foglalkoztatás állapotát és a személyes adatokat tartalmazzák. Minden egyes kérelmező esetében egy bináris minősítés lett megadva, amely azt jelzi, hogy alacsony vagy magas a hitelkockázat. 

Ezeket az adatkészleteket fogja használni a prediktív elemzési modellek betanításához. Ha elkészült, a modellnek képesnek kell lennie arra, hogy fogadja el a funkció-vektort egy új személy számára, és Tippelje meg, hogy alacsony vagy magas a kredit kockázata.  

Íme egy érdekes csavar.

Az UCI-webhelyen található adatkészlet leírása megemlíti, hogy milyen költségekkel jár, ha a személy hitelkeretét nem osztályozza.
Ha a modell magas hitelkockázat-kockázatot jelez, hogy valaki, aki valójában alacsony hitelkockázat, a modell téves besorolást kapott.

A fordított téves besorolás azonban ötször drágább a pénzügyi intézménynél: Ha a modell előre jelez egy alacsony hitelkockázat-kockázatot a gyakorlatban.

Tehát szeretné betanítani a modellt úgy, hogy az utóbbi típusú téves besorolások díja ötször nagyobb legyen, mint a másik módszer téves besorolása.

Ennek egyik egyszerű módja, ha a kísérlet során a modell betanítása során egy olyan bejegyzést duplikál (ötször), amely a magas hitelkockázati kockázattal rendelkező személyt jelöli. 

Ezt követően, ha a modell a nagy kockázatnak kitetten alacsony hitelkockázat-besorolást végez valakinek, a modell ugyanazt a téves osztályozást ötször, egyszer az egyes duplikált elemek esetében. Ezzel a művelettel növelheti a hiba költségeit a betanítási eredményekben.


### <a name="convert-the-dataset-format"></a>Az adatkészlet formátumának konvertálása

Az eredeti adatkészlet üres, elválasztott formátumot használ. A Machine Learning Studio (klasszikus) a vesszővel tagolt (CSV) fájlokkal jobban működik, ezért az adathalmazt vesszővel kell helyettesíteni.  

Az ilyen típusú adatkonvertálás számos módon lehetséges. Az egyik módszer a következő Windows PowerShell-parancs használata:   

```powershell
cat german.data | %{$_ -replace " ",","} | sc german.csv  
```

Egy másik módszer a UNIX sed parancs használatával:  

```console
sed 's/ /,/g' german.data > german.csv
```

Mindkét esetben létrehozta az adatok vesszővel tagolt verzióját egy **german.csv** nevű fájlban, amelyet használhat a kísérletben.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Az adatkészlet feltöltése Machine Learning Studio (klasszikus)

Miután az adatok CSV formátumra lettek konvertálva, fel kell töltenie Machine Learning Studioba (klasszikus). 

1. Nyissa meg a Machine Learning Studio (klasszikus) kezdőlapját ( [https://studio.azureml.net](https://studio.azureml.net) ). 

2. Kattintson a menüre ![ – Ez a menü ikon – három halmozott vonal.](./media/tutorial-part1-credit-risk/menu.png) az ablak bal felső sarkában kattintson a **Azure Machine learning** elemre, válassza a **Studio** lehetőséget, és jelentkezzen be.

3. Az ablak alján kattintson az **+ új** elemre.

4. Válassza az **adatkészlet** lehetőséget.

5. Válasszon **a helyi fájlból**.

    ![Adatkészlet hozzáadása helyi fájlból](./media/tutorial-part1-credit-risk/add-dataset.png)

6. Az **új adatkészlet feltöltése** párbeszédpanelen kattintson a Tallózás gombra, és keresse meg a létrehozott **german.csv** fájlt.

7. Adja meg az adatkészlet nevét. Ebben az oktatóanyagban hívja meg az "UCI German Credit Card-adatforrást".

8. Az adattípus mezőben válassza ki **az általános CSV-fájlt fejléc nélkül (.nh.csv)**.

9. Ha szeretné, adja meg a leírást.

10. Kattintson az **OK** pipa jelre.  

    ![Az adatkészlet feltöltése](./media/tutorial-part1-credit-risk/upload-dataset.png)

Ezzel feltölti az adatokat egy olyan adatkészlet-modulba, amelyet egy kísérletben használhat.

A Studio (klasszikus) ablak bal oldalán található **adatkészletek** lapra kattintva kezelheti a studióba feltöltött adatkészleteket.

![Adatkészletek kezelése](./media/tutorial-part1-credit-risk/dataset-list.png)

A más típusú adatok kísérletbe való importálásával kapcsolatos további információkért lásd [a betanítási adatok importálása Azure Machine learning Studioba (klasszikus)](import-data.md)című témakört.

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Az oktatóanyag következő lépése egy olyan Machine Learning Studio (klasszikus) kísérlet létrehozása, amely a feltöltött adatkészletet használja.  

1. A Studio (klasszikus) területen kattintson az **+ új** elemre az ablak alján.
1. Válassza a **kísérlet**, majd az "üres kísérlet" lehetőséget. 

    ![Új kísérlet létrehozása](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Válassza ki az alapértelmezett kísérlet nevét a vászon tetején, és nevezze át valami értelmesre.

    ![Kísérlet átnevezése](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > Célszerű kitölteni a kísérlet **összegzését** és **leírását** a **Tulajdonságok** ablaktáblán. Ezek a tulajdonságok lehetővé teszik, hogy dokumentálja a kísérletet, hogy a későbbiekben bárki megértse a célokat és a módszertant.
   > 
   > ![Kísérlet tulajdonságai](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. A kísérleti vászon bal oldalán található modul palettán bontsa ki a **mentett adatkészletek** elemet.
1. Keresse meg a **saját adatkészletek** alatt létrehozott adatkészletet, és húzza a vászonra. Az adatkészletet úgy is megtalálhatja, hogy a paletta fölötti **keresőmezőbe** írja be a nevet.  

    ![Adatkészlet hozzáadása a kísérlethez](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Az adatok előkészítése

Megtekintheti az adatok első 100 sorát és a teljes adathalmazra vonatkozó statisztikai adatokat: kattintson az adatkészlet kimeneti portjára (alul lévő kis kör), majd válassza a **Megjelenítés** lehetőséget.  

Mivel az adatfájl nem tartalmaz oszlopfejléceket, a Studio (klasszikus) általános fejléceket (Col1, Col2 *stb.*) adott meg. A megfelelő fejlécek nem elengedhetetlenek a modell létrehozásához, de megkönnyítik a kísérlet során felhasználható adatfeldolgozást. Emellett, amikor végül közzéteszi ezt a modellt egy webszolgáltatásban, a fejlécek segítenek azonosítani az oszlopokat a szolgáltatás felhasználójának.  

Oszlopfejlécek hozzáadásához használhatja a [metaadatok szerkesztése][edit-metadata] modult.

A [metaadatok szerkesztése][edit-metadata] modul használatával módosíthatja az adatkészlethez társított metaadatokat. Ebben az esetben a használatával több felhasználóbarát nevet adhat meg az oszlopfejlécek számára. 

A [metaadatok szerkesztésének][edit-metadata]használatához először meg kell adnia a módosítandó oszlopokat (ebben az esetben mindegyiket). Ezután meg kell adnia az ezen oszlopokon végrehajtandó műveletet (ebben az esetben az oszlopfejlécek módosítását).

1. A modul palettáján írja be a "metadata" **kifejezést a keresőmezőbe** . A [metaadatok szerkesztése][edit-metadata] a modulok listájában jelenik meg.

1. Kattintson és húzza a [metaadatok szerkesztése][edit-metadata] modult a vászonra, és dobja el a korábban hozzáadott adatkészlet alá.

1. Az adatkészlet csatlakoztatása a [metaadatok szerkesztése][edit-metadata]: kattintson az adatkészlet kimeneti portjára (az adatkészlet alján található kis kör), húzza a [metaadatok szerkesztése][edit-metadata] (a modul tetején lévő kis kör) bemeneti portjára, majd szabadítsa fel az egérgombot. Az adatkészlet és a modul akkor is csatlakoztatva marad, ha a vászon körül mozog.
 
    A kísérletnek ekkor az alábbihoz hasonlóan kell kinéznie:  

    ![Szerkesztési metaadatok hozzáadása](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    A piros felkiáltójel azt jelzi, hogy még nem állította be a modul tulajdonságait. Ezt a következő lépésben teheti meg.

    > [!TIP]
    > A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. Ebben az esetben kattintson duplán a [metaadatok szerkesztése][edit-metadata] modulra, és írja be az "oszlopfejlécek hozzáadása" megjegyzést. A szövegmező bezárásához kattintson a vászonon bárhová máshol. A Megjegyzés megjelenítéséhez kattintson a modulban a lefelé mutató nyílra.
    > 
    > ![Metaadat-modul szerkesztése megjegyzéssel hozzáadva](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Válassza a [metaadatok szerkesztése][edit-metadata]lehetőséget, és a vászontól jobbra található **Tulajdonságok** ablaktáblán kattintson az **oszlop választójának indítása** lehetőségre.

1. Az **Oszlopok kiválasztása** párbeszédpanelen válassza ki a **rendelkezésre álló oszlopok** összes sorát, és kattintson a > elemre a **kijelölt oszlopokra** való áthelyezéshez.
   A párbeszédpanelnek így kell kinéznie:

   ![Oszlop-választó az összes kijelölt oszloppal](./media/tutorial-part1-credit-risk/select-columns.png)


1. Kattintson az **OK** pipa jelre.

1. A **Tulajdonságok** ablaktáblában keresse meg az **új oszlopnevek** paramétert. Ebben a mezőben adja meg az adatkészlet 21 oszlopának neveit, vesszővel elválasztva és oszlopos sorrendben. Az oszlopok nevét az UCI webhelyén található adatkészlet dokumentációjában szerezheti be, vagy az alábbi lista másolásához és beillesztéséhez használhatja az adatokat:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   A Tulajdonságok ablaktábla így néz ki:

   ![A metaadatok szerkesztésének tulajdonságai](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Ha ellenőrizni szeretné az oszlopfejlécek fejléceit, futtassa a kísérletet (kattintson a kísérlet vászon alatt **lévő Futtatás** elemre). Ha a futása befejeződött (egy zöld pipa jelenik meg a [metaadatok szerkesztése][edit-metadata]elemnél), kattintson a [metaadatok szerkesztése][edit-metadata] modul kimeneti portjára, majd válassza a **Megjelenítés** lehetőséget. Bármelyik modul kimenetét ugyanúgy megtekintheti, hogy megtekintse az adatokat a kísérlet során.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Képzési és tesztelési adatkészletek létrehozása

A modell betanításához és a teszteléshez néhány adattal kell rendelkeznie.
A kísérlet következő lépéseként az adatkészletet két külön adatkészletre osztja szét: egyet a modell betanításához, egyet pedig a teszteléshez.

Ehhez az [Adatfelosztási][split] modult kell használnia.  

1. Keresse meg az [adatok felosztása][split] modult, húzza a vászonra, és kapcsolódjon a [metaadatok szerkesztése][edit-metadata] modulhoz.

1. Alapértelmezés szerint a felosztott arány 0,5, a **véletlenszerű felosztási** paraméter pedig be van állítva. Ez azt jelenti, hogy az adatmennyiség véletlenszerű felének kimenete a [felosztott][split] adatmodul egy portjára, a másik felé pedig a másikra mutat. Ezeket a paramétereket, valamint a **véletlenszerű mag** paramétert is módosíthatja a képzés és a tesztelési adatok közötti felosztás módosításához. Ebben a példában a következőt hagyja:.
   
   > [!TIP]
   > Az **első kimeneti adatkészletben szereplő sorok tulajdonságának töredéke** határozza meg, hogy a *bal oldali* kimeneti porton milyen mennyiségű adat kimenete látható. Ha például a 0,7 értékre állítja az arányt, akkor az adatmennyiség 70%-a a bal oldali porton, a jobb oldali porton pedig 30%-os kimeneten keresztül történik.  
   > 
   > 

1. Kattintson duplán az [Adatfelosztási][split] modulra, és írja be a következő megjegyzést: "képzés/tesztelési célú adatbontás 50%". 

Használhatja azonban a [felosztott][split] adatmodul kimeneteit, de úgy is dönthet, hogy a bal oldali kimenetet betanítási adatként, a jobb kimenetet pedig tesztelési adatként használja.  

Ahogy azt az [előző lépésben](tutorial-part1-credit-risk.md#upload)is említettük, az alacsony értékű magas hitelkockázat-besorolási díj ötszöröse magasabb, mint az alacsony hitelkockázat-kockázat magas szintű besorolása. Ennek érdekében létrehoz egy új adatkészletet, amely tükrözi ezt a Cost függvényt. Az új adatkészletben az egyes magas kockázatú példák öt alkalommal replikálódnak, míg az egyes alacsony kockázatú példák nem replikálódnak.   

Ezt a replikálást az R-kóddal végezheti el:  

1. Keresse meg és húzza az [R szkript végrehajtása][execute-r-script] modult a kísérleti vászonra. 

1. Az [R-szkript végrehajtása][execute-r-script] modul első bemeneti portjához ("DataSet1 elemet") kapcsolja össze az [adatfelosztási][split] modul bal oldali kimeneti portját.

1. Kattintson duplán az [R-parancsfájl végrehajtása][execute-r-script] modulra, és írja be a következő megjegyzést: "Cost-beállítás beállítása".

1. A **Tulajdonságok** ablaktáblán törölje az **R-parancsfájl** paraméter alapértelmezett szövegét, és adja meg a következő parancsfájlt:
   
    ```r
    dataset1 <- maml.mapInputPort(1)
    data.set<-dataset1[dataset1[,21]==1,]
    pos<-dataset1[dataset1[,21]==2,]
    for (i in 1:5) data.set<-rbind(data.set,pos)
    maml.mapOutputPort("data.set")
    ```

    ![R-szkript az R-szkript végrehajtása modulban](./media/tutorial-part1-credit-risk/execute-r-script.png)

Ugyanezt a replikálási műveletet kell végrehajtania az [Adatfelosztó][split] modul minden egyes kimenetéhez, hogy a betanítási és tesztelési adatokat ugyanazzal a költséghatékonysággal lehessen elvégezni. Ezt a legegyszerűbben úgy teheti meg, ha duplikálja az imént létrehozott [R-szkript][execute-r-script] modult, és csatlakoztatja a [felosztott][split] adatmodul másik kimeneti portjához.

1. Kattintson a jobb gombbal az [R-parancsfájl végrehajtása][execute-r-script] elemre, majd válassza a **Másolás** lehetőséget.

1. Kattintson a jobb gombbal a kísérlet vászonra, majd válassza a **Beillesztés** lehetőséget.

1. Húzza az új modult a pozícióba, majd az új [végrehajtási R script][execute-r-script] modul első bemeneti portjához kapcsolja össze az [adatbontó][split] modul jobb kimeneti portját. 

1. A vászon alján kattintson a **Futtatás** elemre. 

> [!TIP]
> Az R-szkript végrehajtása modul másolata ugyanazt a parancsfájlt tartalmazza, mint az eredeti modul. Amikor egy modult másol és illeszt be a vásznon, a másolat megőrzi az eredeti összes tulajdonságát.  
> 
>

A kísérlet most a következőhöz hasonlóan néz ki:

![Felosztott modul és R-szkriptek hozzáadása](./media/tutorial-part1-credit-risk/experiment.png)

További információ az R-szkriptek kísérletekben való használatáról: [a kísérlet kiterjesztése az r](index.yml)használatával.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következő lépéseket végezte el: 
 
> [!div class="checklist"]
> * Machine Learning Studio (klasszikus) munkaterület létrehozása
> * Meglévő adatok feltöltése a munkaterületre
> * Kísérlet létrehozása

Most már készen áll az adatmodellek betanítására és kiértékelésére.

> [!div class="nextstepaction"]
> [2. oktatóanyag – modellek betanítása és kiértékelése](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data