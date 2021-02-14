---
title: 'ML Studio (klasszikus) oktatóanyag 3: kredit kockázati modellek üzembe helyezése – Azure'
description: Részletes oktatóanyag, amely bemutatja, hogyan hozhat létre a hitelkockázat értékelésére szolgáló prediktív elemzési megoldást Azure Machine Learning Studio (klasszikus). Ez az oktatóanyag egy háromrészes oktatóanyag-sorozat harmadik része. Bemutatja, hogyan helyezhet üzembe egy modellt webszolgáltatásként.
keywords: hitelkockázat, prediktív elemzési megoldás, kockázatértékelés, üzembe helyezés, webszolgáltatás
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 68a5f6773543b922d024336556e4de24dd96fd33
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517382"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>3. Oktatóanyag: kredit kockázati modell üzembe helyezése – Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus) ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Ez az oktatóanyag részletesen bemutatja a prediktív elemzési megoldások fejlesztésének folyamatát. Egy egyszerű modellt fejleszt Machine Learning Studio (klasszikus).  Ezután Azure Machine Learning webszolgáltatásként helyezheti üzembe a modellt.  Ez a központilag telepített modell új adatelemzést végezhet az előrejelzésekhez. Ez az oktatóanyag **egy háromrészes oktatóanyag-sorozat harmadik** része.

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

A hitelkockázat-értékelés összetett probléma, de ez az oktatóanyag egy kicsit egyszerűbbé válik. Példaként fogja használni, hogyan hozhat létre prediktív elemzési megoldást Microsoft Azure Machine Learning Studio (klasszikus) használatával. Ehhez a megoldáshoz Azure Machine Learning Studio (klasszikus) és egy Machine Learning webszolgáltatást fog használni. 

Ebben a három részből álló oktatóanyagban nyilvánosan elérhető hitelkockázat-adatforrásokkal kezdheti meg.  Ezután fejlesztheti és betaníthatja a prediktív modellt.  Végül üzembe helyezi a modellt webszolgáltatásként.

[Az oktatóanyag első részében](tutorial-part1-credit-risk.md)létrehozott egy Machine learning Studio (klasszikus) munkaterületet, feltöltött és létrehozott egy kísérletet.

[Az oktatóanyag második részében](tutorial-part2-credit-risk-train.md)betanított és kiértékelt modelleket.

Az oktatóanyag ezen részében:

> [!div class="checklist"]
> * Felkészülés az üzembe helyezésre
> * A webszolgáltatás üzembe helyezése
> * A webszolgáltatás tesztelése
> * Webszolgáltatás kezelése
> * Hozzáférés a webszolgáltatáshoz

## <a name="prerequisites"></a>Előfeltételek

Fejezze be [az oktatóanyag második részét](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre
Annak érdekében, hogy a jelen oktatóanyagban fejlesztett prediktív modellt az Azure-ban, webszolgáltatásként is üzembe helyezheti.

Ez a pont már kísérletezett a modell betanításával. Az üzembe helyezett szolgáltatás azonban már nem fog képzést végezni – az új előrejelzések létrehozásához a modellen alapuló felhasználó bemenetét kell megmutatnia. Ezért elvégezünk némi felkészülést, hogy a kísérletet a ***betanítási** _ kísérletből egy _ *_prediktív_** kísérletre konvertáljuk. 

Az üzembe helyezés előkészítése egy három lépésből álló folyamat:  

1. Az egyik modell eltávolítása
1. A létrehozott *betanítási kísérlet* átalakítása *prediktív kísérletbe*
1. A prediktív kísérlet webszolgáltatásként való üzembe helyezése

### <a name="remove-one-of-the-models"></a>Az egyik modell eltávolítása

Először el kell távolítania ezt a kísérletet egy kicsit. Jelenleg két különböző modellel rendelkezik a kísérletben, de csak egyetlen modellt szeretne használni, amikor webszolgáltatásként telepíti ezt.  

Tegyük fel, hogy úgy döntött, hogy a felerősített famodell jobban teljesített, mint a SVM modell. Ezért először távolítsa el a [kétosztályos támogatású vektoros gépi][two-class-support-vector-machine] modult és a képzéshez használt modulokat. Először is érdemes lehet másolatot készíteni a kísérletről a kísérlet vászon alján a **Mentés másként** lehetőségre kattintva.

törölnie kell a következő modulokat:  

* [Kétosztályos támogató vektorgép][two-class-support-vector-machine]
* A [betanítási modell][train-model] és a [pontszám modellhez][score-model] kapcsolódó modulok
* [Az Adatnormalizálás][normalize-data] (mindkettő)
* [Modell kiértékelése][evaluate-model] (mivel elkészült a modellek kiértékelésével)

Válassza ki az egyes modulokat, és nyomja le a DELETE billentyűt, vagy kattintson a jobb gombbal a modulra, és válassza a **Törlés** lehetőséget. 

![Kiemeli, hogy mely modulok törlődjenek a támogatási vektoros gép modell eltávolításához](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

A modellnek most ehhez hasonlóan kell kinéznie:

![Eredményül kapott kísérlet a támogatási vektoros gép modelljének törlésekor](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Most már készen áll a modell üzembe helyezésére a [kétosztályos felerősítő döntési fában][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A betanítási kísérlet átalakítása prediktív kísérletre

Ahhoz, hogy ez a modell készen álljon a telepítésre, át kell alakítania a betanítási kísérletet egy prediktív kísérletbe. Ez három lépést foglal magában:

1. Mentse a betanított modellt, majd cserélje ki a betanítási modulokat
1. Vágja le a kísérletet, és távolítsa el azokat a modulokat, amelyek csak a képzéshez szükségesek
1. Adja meg, hogy a webszolgáltatás hol fogadja el a bemenetet, és hogy hol hozza létre a kimenetet

Ezt manuálisan is megteheti, de szerencsére mind a három lépés elvégezhető a kísérlet vászon alján található **webszolgáltatás beállítása** elemre kattintva (és a **prediktív webszolgáltatás** lehetőség kiválasztásával).

> [!TIP]
> Ha többet szeretne megtudni arról, hogy mi történik, ha egy képzési kísérletet prediktív kísérletre alakít át, tekintse meg a [modell előkészítése az üzembe helyezéshez Azure Machine learning Studio (klasszikus)](deploy-a-machine-learning-web-service.md)című témakört.

Ha a **webszolgáltatás beállítása** gombra kattint, több dolog történik:

* A betanított modellt egyetlen **betanított modell** -modulra konvertáljuk, és a modul palettáján, a kísérlet vászontól balra tároljuk (a **betanított** modellekben található)
* A betanításhoz használt modulok el lesznek távolítva; kifejezetten
  * [Kétosztályos súlyozott döntési fa][two-class-boosted-decision-tree]
  * [Modell betanítása][train-model]
  * [Adatok felosztása][split]
  * az adatteszteléshez használt második [R-parancsfájl][execute-r-script] -modul
* A rendszer visszaadja a mentett betanított modellt a kísérlethez
* **Webszolgáltatások bemeneti** és **webszolgáltatás-kimeneti** moduljai (ezek azonosítják, hogy a felhasználó mely adatokat írja be a modellbe, és milyen adatokat ad vissza a rendszer a webszolgáltatás elérésekor)

> [!NOTE]
> Láthatja, hogy a kísérletet a rendszer a kísérlet vászon felső részén felvett lapok alatt két részre menti. Az eredeti betanítási kísérlet a lap **betanítási kísérlete** alatt áll, az újonnan létrehozott prediktív kísérlet pedig **prediktív kísérlet**. A prediktív kísérlet egy webszolgáltatásként üzembe helyezhető.

Ehhez az adott kísérlethez még egy lépést kell elvégeznie.
két [végrehajtási R parancsfájl][execute-r-script] -modult adott hozzá, amelyek súlyozási funkciót biztosítanak az adathoz. Ez csak egy, a képzéshez és a teszteléshez szükséges trükk volt, így az utolsó modellben is elvégezheti ezeket a modulokat.
Machine Learning Studio (klasszikus) eltávolított egy [Execute R script][execute-r-script] modult, amikor eltávolította a [felosztott][split] modult. Most már eltávolíthatja a másikat, és közvetlenül is összekapcsolhat [metaadat-szerkesztőt][metadata-editor] a [pontszám modellhez][score-model].    

A kísérletnek most így kell kinéznie:  

![A betanított modell pontozása](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Előfordulhat, hogy kíváncsi, hogy miért hagyta el az UCI német hitelkártya-adatkészletet a prediktív kísérlet során. A szolgáltatás a felhasználó adatait fogja kimutatni, nem az eredeti adatkészletet, ezért miért hagyja az eredeti adatkészletet a modellben?
> 
> Igaz, hogy a szolgáltatásnak nincs szüksége az eredeti bankkártyás adatforrásokra. Ehhez azonban szükség van az adatok sémára, amely olyan adatokat tartalmaz, mint például az oszlopok száma, és hogy mely oszlopok numerikusak. Ez a séma-információ a felhasználó adatainak értelmezéséhez szükséges. ezeket az összetevőket nem kell csatlakoztatni, hogy a pontozási modul a szolgáltatás futása során a DataSet sémával rendelkezik. Az adathalmaz nem használatos, csak a sémát.  
> 
>Fontos megjegyezni, hogy ha az eredeti adatkészlet szerepel a címkén, akkor a webes bemenet várt sémája is egy oszlopot fog várni a címkével. A következő módon távolíthatja el a címkét, valamint a betanítási adatkészletben található egyéb adatokat, de nem a webes bemeneteken, hanem a webes bemenet és a betanítási adatkészlet közös modulba való csatlakoztatása előtt. 
> 

A kísérlet utolsó futtatása (kattintson a **Futtatás** gombra.) Ha szeretné ellenőrizni, hogy a modell továbbra is működik-e, kattintson a [pontszám modell][score-model] modul kimenetére, és válassza az **eredmények megtekintése** lehetőséget. Láthatja, hogy a rendszer megjeleníti az eredeti értékeket, valamint a hitelkockázat értékét ("pontozásos címkék") és a pontozási valószínűségi értéket ("pontozásos valószínűség"). 

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
A kísérletet klasszikus webszolgáltatásként vagy Azure Resource Manageron alapuló új webszolgáltatásként is telepítheti.

### <a name="deploy-as-a-classic-web-service"></a>Üzembe helyezés klasszikus webszolgáltatásként
A kísérletből származtatott klasszikus webszolgáltatás üzembe helyezéséhez kattintson az **webszolgáltatás üzembe helyezése** a vászon alatt lehetőségre, majd válassza a **webszolgáltatás telepítése [klasszikus]** lehetőséget. Machine Learning Studio (klasszikus) a kísérletet webszolgáltatásként helyezi üzembe, és az adott webszolgáltatás irányítópultján viszi át. Ezen a lapon visszatérhet a kísérlethez (**Pillanatkép megtekintése** vagy **legutóbbi megtekintése**), és futtathatja a webszolgáltatás egyszerű tesztjét (lásd az alábbi **webszolgáltatások tesztelését** ). Itt tájékozódhat a webszolgáltatáshoz hozzáférő alkalmazások létrehozásáról is (erről bővebben az oktatóanyag következő lépésében talál).

![Webszolgáltatás irányítópultja](./media/tutorial-part3-credit-risk-deploy/publish6.png)


A szolgáltatás konfigurálásához kattintson a **konfiguráció** fülre. Itt módosíthatja a szolgáltatás nevét (alapértelmezés szerint a kísérlet nevét adja meg), és leírást adhat hozzá. Megadhatja a bemeneti és kimeneti adatokhoz a felhasználóbarát címkéket is.  

![Webszolgáltatás konfigurálása](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Üzembe helyezés új webszolgáltatásként

> [!NOTE] 
> Új webszolgáltatás telepítéséhez megfelelő engedélyekkel kell rendelkeznie ahhoz az előfizetéshez, amelyhez a webszolgáltatást telepíti. További információ: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md). 

A kísérletből származtatott új webszolgáltatás üzembe helyezése:

1. Kattintson a **webszolgáltatások üzembe helyezése** a vászon alatt lehetőségre, majd válassza a **webszolgáltatás üzembe helyezése [új]** lehetőséget. Machine Learning Studio (klasszikus) átviszi Önt a Azure Machine Learning webszolgáltatások **üzembe helyezési kísérlet** lapjára.

1. Adja meg a webszolgáltatás nevét. 

1. A díjszabáshoz kiválaszthat egy meglévő árképzési csomagot, vagy kiválaszthatja az "új létrehozása" **lehetőséget, és** megadhatja az új terv nevét, és kiválaszthatja a havi csomag beállítást. A csomag alapértelmezett értéke az alapértelmezett régió csomagjai, a webszolgáltatás pedig az adott régióban van üzembe helyezve.

1. Kattintson az **Üzembe helyezés** gombra.

Néhány perc elteltével megnyílik a webszolgáltatás rövid **útmutató lapja.**

A szolgáltatás konfigurálásához kattintson a configure ( **Konfigurálás** ) fülre. Itt módosíthatja a szolgáltatás címét, és leírást adhat hozzá. 

A webszolgáltatás teszteléséhez kattintson a **teszt** fülre (lásd az alábbi **webszolgáltatás tesztelését** ). A webszolgáltatáshoz hozzáférő alkalmazások létrehozásával kapcsolatos információkért kattintson a felhasználás **lapra (az oktatóanyag** következő lépése részletesebben jelenik meg).

> [!TIP]
> A webszolgáltatás telepítése után frissítheti a webszolgáltatást. Ha például módosítani szeretné a modellt, akkor szerkesztheti a betanítási kísérletet, megkeresheti a modell paramétereit, majd kattintson a webszolgáltatás **telepítése** lehetőségre, válassza a **webszolgáltatás telepítése [klasszikus]** vagy a **webszolgáltatás telepítése [új]** lehetőséget. Ha ismét telepíti a kísérletet, a a webszolgáltatás helyett a frissített modellt használja.  
> 
> 

## <a name="test-the-web-service"></a>A webszolgáltatás tesztelése

A webszolgáltatáshoz való hozzáféréskor a felhasználó **adatbevitele a webszolgáltatás bemeneti** modulján keresztül történik, ahol a rendszer átadja a [pontszám modell][score-model] modulnak, és lelőtte azt. A prediktív kísérlet beállításának módja, a modell az eredeti hitelkockázat-adatkészlettel megegyező formátumban várja az adatokat.
Az eredményeket a webszolgáltatás **kimeneti** modulján keresztül adja vissza a rendszer a webszolgáltatásnak.

> [!TIP]
> Ahogy a prediktív kísérlet konfigurálva van, a [pontszám modell][score-model] modul teljes eredményét adja vissza. Ebbe beletartozik az összes bemeneti adat, valamint a hitelkockázat és a pontozási valószínűség. De ha szeretné, visszaállíthat egy másikat, például csak a hitelkockázat-kockázati értéket adhat vissza. Ehhez helyezzen be egy [Select Columns][select-columns] modult a [score Model][score-model] és a **webszolgáltatás kimenete** között, hogy eltávolítsa azokat az oszlopokat, amelyeket nem kíván visszaadni a webszolgáltatásnak. 
> 
> 

A klasszikus webszolgáltatás **Machine learning Studio (klasszikus)** vagy a **Azure Machine learning webszolgáltatások** portálján is tesztelhető.
Egy új webszolgáltatást csak a **Machine learning webszolgáltatások** portálon lehet tesztelni.

> [!TIP]
> Ha a Azure Machine Learning webszolgáltatások portálon végez tesztelést, a portálon létrehozhat mintaadatok, amelyek segítségével tesztelheti a Request-Response szolgáltatást. A **configure (Konfigurálás** ) lapon válassza az "igen" lehetőséget a **mintaadatok engedélyezéséhez?**. Amikor megnyitja a Request-Response fület a **teszt** lapon, a portál kitölti az eredeti hitelkockázat-adatkészletből vett mintaadatok adatait.

### <a name="test-a-classic-web-service"></a>Klasszikus webszolgáltatás tesztelése

A klasszikus webszolgáltatások tesztelését Machine Learning Studio (klasszikus) vagy a Machine Learning webszolgáltatások portálján végezheti el. 

#### <a name="test-in-machine-learning-studio-classic"></a>Tesztelés Machine Learning Studio (klasszikus)

1. A webszolgáltatás **irányítópult** lapján kattintson a **teszt** gombra az **alapértelmezett végpont** alatt. Megjelenik egy párbeszédpanel, és megkérdezi a szolgáltatás bemeneti adatát. Ezek ugyanazok az oszlopok, amelyek szerepelnek az eredeti hitelkockázat-adatkészletben.  

1. Adjon meg egy adatkészletet, majd kattintson **az OK** gombra. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Tesztelés a Machine Learning Web Services portálon

1. A webszolgáltatáshoz tartozó **irányítópult** lapon kattintson az **alapértelmezett végpont** alatti **teszt előnézet** hivatkozásra. Megnyílik a webszolgáltatási végpont Azure Machine Learning webszolgáltatások portáljának teszt lapja, és megkérdezi a szolgáltatás bemeneti adatait. Ezek ugyanazok az oszlopok, amelyek szerepelnek az eredeti hitelkockázat-adatkészletben.

2. Kattintson a **kérelem tesztelése – válasz** elemre. 

### <a name="test-a-new-web-service"></a>Új webszolgáltatás tesztelése

Egy új webszolgáltatást csak a Machine Learning webszolgáltatások portálon lehet tesztelni.

1. A [Azure Machine learning webszolgáltatások](https://services.azureml.net/quickstart) portálján kattintson a **teszt** gombra az oldal tetején. Megnyílik a **teszt** lap, és beírhat adatokat a szolgáltatáshoz. A megjelenített beviteli mezők az eredeti hitelkockázat-adatkészletben megjelenő oszlopoknak felelnek meg. 

1. Adjon meg egy adatkészletet, majd kattintson a **kérelem tesztelése – válasz** elemre.

A teszt eredményei az oldal jobb oldalán jelennek meg a kimenet oszlopban. 


## <a name="manage-the-web-service"></a>Webszolgáltatás kezelése

Miután telepítette a webszolgáltatást, akár klasszikus, akár új, felügyelheti azt a [Microsoft Azure Machine learning webszolgáltatások](https://services.azureml.net/quickstart) portálján.

A webszolgáltatás teljesítményének figyelése:

1. Bejelentkezés a [Microsoft Azure Machine learning Web Services](https://services.azureml.net/quickstart) portálra
1. Kattintson a **webszolgáltatások** elemre.
1. Kattintson a webszolgáltatásra
1. Kattintson az **irányítópultra**

## <a name="access-the-web-service"></a>Hozzáférés a webszolgáltatáshoz

Az oktatóanyag előző lépésében üzembe helyezett egy webszolgáltatást, amely a hitelkockázat-előrejelzési modellt használja. A felhasználók most már képesek az adatküldésre és az eredmények fogadására. 

A webszolgáltatás egy Azure-webszolgáltatás, amely a REST API-k használatával képes fogadni és visszaadni az adatokhoz a következő két módszer egyikét:  

* **Kérés/válasz** – a felhasználó egy vagy több hitelkeretet küld a szolgáltatásnak egy HTTP protokoll használatával, és a szolgáltatás egy vagy több eredménnyel válaszol.
* **Kötegelt végrehajtás** – a felhasználó egy vagy több jóváírási adatokat tárol egy Azure-blobban, majd elküldi a blob helyét a szolgáltatásnak. A szolgáltatás a bemeneti blobban lévő összes sornyi adatot megjeleníti, az eredményeket egy másik blobban tárolja, és visszaadja a tároló URL-címét. 

> [!NOTE]
> A Studio (klasszikus) szolgáltatás oszlopainak neve **megkülönbözteti a kis-és nagybetűket**. Ügyeljen arra, hogy a webszolgáltatás meghívásához használt bemeneti adatok azonos oszlopnevek legyenek, mint a betanítási adatkészletben.

További információ a webszolgáltatás eléréséről és használatáról: [Azure Machine learning webszolgáltatások felhasználása webalkalmazás-sablonnal](./consume-web-services.md).



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következő lépéseket végezte el:

> [!div class="checklist"]
> * Felkészülés az üzembe helyezésre
> * A webszolgáltatás üzembe helyezése
> * A webszolgáltatás tesztelése
> * Webszolgáltatás kezelése
> * Hozzáférés a webszolgáltatáshoz

Létrehozhat egy egyéni alkalmazást is a webszolgáltatáshoz való hozzáféréshez az R-, C#-és Python-programozási nyelveken elérhető kezdő kódok használatával.

> [!div class="nextstepaction"]
> [Azure Machine Learning webszolgáltatás felhasználása](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[metadata-editor]: /azure/machine-learning/studio-module-reference/edit-metadata
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset