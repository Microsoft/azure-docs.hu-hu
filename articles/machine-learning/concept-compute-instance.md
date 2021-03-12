---
title: Mi az az Azure Machine Learning számítási példány?
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Azure Machine Learning számítási példányával, amely egy teljes körűen felügyelt felhőalapú munkaállomás.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: ac90827fec65187088787971623483a96da77d7c
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016236"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Mi az az Azure Machine Learning számítási példány?

Az Azure Machine Learning számítási példány egy felügyelt felhőalapú munkaállomás adatszakértők számára.

A számítási példányok megkönnyítik a Azure Machine Learning fejlesztésének megkezdését, valamint a rendszergazdák számára a felügyeleti és a vállalati készültségi képességek biztosítását.  

Számítási példányt használhat a felhőben a gépi tanuláshoz teljes mértékben konfigurált és felügyelt fejlesztési környezetként. Emellett számítási célokként is használhatók a fejlesztési és tesztelési célú képzéshez és következtetésekhez.  

Az üzemi szintű modell betanításához használjon [Azure Machine learning számítási fürtöt](how-to-create-attach-compute-cluster.md) több csomópontos skálázási képességekkel. Az üzemi szintű modell üzembe helyezéséhez használja az [Azure Kubernetes Service-fürtöt](how-to-deploy-azure-kubernetes-service.md).

A számítási példányok Jupyter működéséhez győződjön meg arról, hogy a webes szoftvercsatorna-kommunikáció nincs letiltva. Győződjön meg arról, hogy a hálózat engedélyezi a WebSocket-kapcsolatokat a *. instances.azureml.net és a *. instances.azureml.ms.

## <a name="why-use-a-compute-instance"></a>Miért érdemes számítási példányt használni?

A számítási példány egy teljes körűen felügyelt felhőalapú munkaállomás, amely a gépi tanulási fejlesztési környezethez van optimalizálva. A következő előnyöket biztosítja:

|Főbb előnyök|Leírás|
|----|----|
|Termelékenység|A modelleket integrált jegyzetfüzetekkel és a Azure Machine Learning Studio következő eszközeivel hozhatja létre és helyezheti üzembe:<br/>– Jupyter<br/>- JupyterLab<br/>– RStudio (előzetes verzió)<br/>A számítási példány teljes mértékben integrálva van Azure Machine Learning munkaterülettel és Studióval. A jegyzetfüzeteket és az egyéb adatszakértőket a munkaterületen is megoszthatja.<br/> A [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) -ot számítási példányokkal is használhatja.
|Felügyelt & biztonságos|Csökkentse a biztonsági lábnyomot, és adja hozzá a vállalati biztonsági követelményeknek való megfelelést. A számítási példányok robusztus felügyeleti házirendeket és biztonságos hálózati konfigurációkat biztosítanak, például:<br/><br/>– Kiépítés Resource Manager-sablonokból vagy Azure Machine learning SDK-ból<br/>- [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Virtuális hálózatok támogatása](./how-to-secure-training-vnet.md#compute-instance)<br/>– SSH-szabályzat az SSH-hozzáférés engedélyezéséhez vagy letiltásához<br/>TLS 1,2 engedélyezve |
|Előre konfigurálva &nbsp; ( &nbsp; ml)|Időt takaríthat meg a telepítési feladatokban előre konfigurált és naprakész ML-csomagokkal, mély tanulási keretrendszerekkel, GPU-illesztővel.|
|Teljes mértékben testreszabható|Az Azure-beli virtuálisgép-típusok széles körű támogatása, beleértve a GPU-ket és a megőrzött alacsony szintű testreszabásokat, például a csomagok és illesztőprogramok telepítésének speciális forgatókönyveit. |

[Létrehozhat egy számítási példányt](how-to-create-manage-compute-instance.md?tabs=python#create) saját maga, vagy egy rendszergazda létrehozhat [egy számítási példányt](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview)is.

## <a name="tools-and-environments"></a><a name="contents"></a>Eszközök és környezetek

> [!IMPORTANT]
> Az ebben a cikkben megjelölt elemek (előzetes verzió) jelenleg nyilvános előzetes verzióban érhetők el.
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Azure Machine Learning számítási példány lehetővé teszi, hogy a munkaterületen teljes mértékben integrált jegyzetfüzet-felülettel készítse el a modelleket, betanítsa és üzembe helyezzen.

A [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) -ban a Jupyter-jegyzetfüzeteket a számítási példányok használatával futtathatja, ha a távoli kiszolgáló SSH-val nem szükséges. A VS Code-integrációt a [távoli SSH-bővítmény](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/)használatával is engedélyezheti.

[Telepítheti a csomagokat](how-to-access-terminal.md#install-packages) , és [hozzáadhat kerneleket](how-to-access-terminal.md#add-new-kernels) a számítási példányhoz.  

A következő eszközök és környezetek már telepítve vannak a számítási példányon: 

|Általános eszközök & környezetekben|Részletek|
|----|:----:|
|Illesztőprogramok|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-könyvtár||
|Azure CLI ||
|Azure Machine Learning minták ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protopuf|| 

|**R** -eszközök & környezetek|Részletek|
|----|:----:|
|RStudio Server nyílt forráskódú kiadás (előzetes verzió)||
|R kernel||
|Az R-hez készült Azure Machine Learning SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-minták|

|**PYTHON** -eszközök & környezetekben|Részletek|
|----|----|
|Anaconda Python||
|Jupyter és-bővítmények||
|Jupyterlab és-bővítmények||
[Pythonhoz készült Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)</br>PyPI|A legtöbb azureml extra csomagot tartalmazza.  A teljes lista megtekintéséhez nyisson meg [egy terminál ablakot a számítási példányon](how-to-access-terminal.md) , és futtassa a következőt: <br/> `conda list -n azureml_py36 azureml*` |
|Egyéb PyPI-csomagok|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-csomagok|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Deep learning-csomagok|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-csomagok|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & R SDK-minták||

A Python-csomagok mind a **python 3,6-AzureML** környezetben települnek.  

## <a name="accessing-files"></a>Fájlok elérése

A jegyzetfüzeteket és R-parancsfájlokat az Azure-fájlmegosztás munkaterületének alapértelmezett Storage-fiókjában tárolja a rendszer.  Ezek a fájlok a "felhasználói fájlok" könyvtár alatt találhatók. Ez a tároló megkönnyíti a jegyzetfüzetek megosztását a számítási példányok között. A Storage-fiók azt is megőrzi, hogy a jegyzetfüzetek biztonságosan megmaradnak a számítási példány leállításakor vagy törlésekor.

A munkaterület Azure-fájlmegosztás fiókja a számítási példány meghajtójának megfelelően van csatlakoztatva. Ez a meghajtó a Jupyter, a Jupyter Labs és a RStudio alapértelmezett munkakönyvtára. Ez azt jelenti, hogy a Jupyter, JupyterLab vagy RStudio-ben létrehozott jegyzetfüzetek és egyéb fájlok automatikusan tárolódnak a fájlmegosztás során, és más számítási példányokban is használhatók.

A fájlmegosztás fájljai a munkaterületen lévő összes számítási példányból elérhetők. A rendszer a számítási példányon a fájlok minden módosítását megbízhatóan megőrzi a fájlmegosztás számára.

A legújabb Azure Machine Learning mintákat a munkaterület fájlmegosztás felhasználói fájlok könyvtára alatt is klónozott mappába helyezheti.

A kisméretű fájlok írása a hálózati meghajtókon lassabb lehet, mint a számítási példány helyi lemezének írásakor.  Ha sok kis fájlt ír, próbálkozzon közvetlenül a számítási példányon, például a `/tmp` címtárban. Vegye figyelembe, hogy ezek a fájlok nem lesznek elérhetők más számítási példányokból. 

Az `/tmp` ideiglenes adatokhoz használhatja a számítási példány könyvtárát is.  A számítási példány operációsrendszer-lemezén azonban ne írjon nagy mennyiségű adatfájlt.  Használjon [](concept-azure-machine-learning-architecture.md#datasets-and-datastores) helyette adattárolókat. Ha telepítette a JupyterLab git-bővítményt, akkor a számítási példányok teljesítményének lelassulását is eredményezheti.

## <a name="managing-a-compute-instance"></a>Számítási példány kezelése

Azure Machine Learning Studio munkaterületén válassza a **számítás**, majd a felül található **számítási példány** elemet.

![Számítási példány kezelése](./media/concept-compute-instance/manage-compute-instance.png)

A következő műveleteket hajthatja végre:

* [Hozzon létre egy számítási példányt](#create). 
* Frissítse a számítási példányok lapot.
* Számítási példány elindítása, leállítása és újraindítása.  A példányért kell fizetnie, amikor fut. Állítsa le a számítási példányt, ha nem használja azt a Cost csökkentése érdekében. Egy számítási példány leállítása felszabadítja azt. Ezután indítsa el újra, amikor szüksége van rá. Vegye figyelembe, hogy a számítási példány leállítása leállítja a számítási órák számlázását, de a lemez, a nyilvános IP-cím és a standard Load Balancer esetében továbbra is számlázásra kerül.
* Számítási példány törlése.
* A számítási példányok listájának szűrése csak a létrehozott adatok megjelenítéséhez.

A munkaterületen minden olyan számítási példány esetében, amelyet használhat, a következőket teheti:

* Hozzáférés Jupyter, JupyterLab, RStudio a számítási példányon
* Az SSH-t a számítási példányba. Az SSH-hozzáférés alapértelmezés szerint le van tiltva, de a számítási példány létrehozási idején is engedélyezhető. Az SSH-hozzáférés a nyilvános/titkos kulcs mechanizmusán keresztül történik. A lapon megadhatja az SSH-kapcsolat adatait, például az IP-címet, a felhasználónevet és a portszámot.
* Egy adott számítási példány, például az IP-cím és a régió részletes adatainak beolvasása.

Az [Azure RBAC](../role-based-access-control/overview.md) lehetővé teszi annak szabályozását, hogy a munkaterület mely felhasználói hozhatnak létre, törölhetnek, indíthatnak le, állíthatnak le, indíthatnak újra egy számítási példányt. A munkaterület közreműködői és tulajdonosi szerepkörben lévő összes felhasználó létrehozhatja, törölheti, elindíthatja, leállíthatja és újraindíthatja a számítási példányokat a munkaterületen. Azonban csak egy adott számítási példány létrehozója, vagy a felhasználó nevében létrejött, a Jupyter, a JupyterLab és a RStudio hozzáférése engedélyezett a számítási példányon. A számítási példányok egyetlen, rendszergazdai hozzáféréssel rendelkező felhasználóhoz vannak hozzárendelve, és a Jupyter/JupyterLab/RStudio-en keresztül is csatlakozhatnak. A számítási példánynak egyfelhasználós bejelentkezéssel kell rendelkeznie, és minden művelet a felhasználó identitását fogja használni az Azure RBAC és a kísérlet futtatásához. Az SSH-hozzáférés a nyilvános/titkos kulcs mechanizmusán keresztül vezérelhető.

Ezeket a műveleteket az Azure RBAC is szabályozhatja:
* *Microsoft. MachineLearningServices/munkaterületek/számítások/olvasás*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/törlés*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/indítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/leállítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/újraindítás/művelet*

Számítási példány létrehozásához a következő műveletekhez szükséges engedélyekkel kell rendelkeznie:
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


### <a name="create-a-compute-instance"></a><a name="create"></a>Új számítási példány létrehozása

A Azure Machine Learning Studio munkaterületén [hozzon létre egy új számítási példányt](how-to-create-attach-compute-studio.md#compute-instance) a **számítási** szakaszból vagy a **jegyzetfüzetek** szakaszban, amikor készen áll az egyik jegyzetfüzet futtatására. 

Létrehozhat egy példányt is
* Közvetlenül az [integrált jegyzetfüzetek felületéről](tutorial-1st-experiment-sdk-setup.md#azure)
* Azure Portal
* Azure Resource Manager sablonból. Példa a sablonra: [Azure Machine learning számítási példány sablonjának létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* [Azure Machine learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md) -val
* A [CLI-bővítményből Azure Machine learning](reference-azure-machine-learning-cli.md#computeinstance)

A számítási példányok létrehozásakor a dedikált magok régiónként, a virtuálisgép-család kvótája és a teljes regionális kvóta alapján, valamint az Azure Machine Learning betanítása számítási fürt kvótáját egyesítjük és megosztva. A számítási példány leállítása nem mentesíti a kvótát, hogy biztosan újra tudja indítani a számítási példányt.


### <a name="create-on-behalf-of-preview"></a>Létrehozás a következő nevében (előzetes verzió)

Rendszergazdaként létrehozhat egy számítási példányt egy adattudós nevében, és hozzárendelheti a példányt a következőhöz:
* [Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  A sablonban szükséges TenantID és ObjectID megkeresésével kapcsolatos részletekért lásd a [hitelesítési konfiguráció azonosító objektum-azonosítóinak megkeresése](../healthcare-apis/fhir/find-identity-object-ids.md)című témakört.  Ezeket az értékeket a Azure Active Directory portálon is megtalálhatja.
* REST API

A számítási példányt a következő Azure RBAC-engedélyekkel hozza létre: 
* *Microsoft. MachineLearningServices/munkaterületek/számítások/indítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/leállítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/újraindítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/applicationaccess/művelet*

Az adattudós elindíthatja, leállíthatja és újraindíthatja a számítási példányt. A számítási példány a következőhöz használható:
* Jupyter
* JupyterLab
* RStudio
* Integrált jegyzetfüzetek

## <a name="compute-target"></a>Számítási cél

A számítási példányok a Azure Machine Learning számítási képzési fürtökhöz hasonló [képzési számítási célként](concept-compute-target.md#train) használhatók. 

Egy számítási példány:
* Rendelkezik a feladatok várólistával.
* A feladatokat a virtuális hálózati környezetben biztonságosan futtatja anélkül, hogy a vállalatoknak SSH-portot kellene megnyitnia. A feladatot egy tároló környezetben hajtja végre a rendszer, és a modell függőségeit egy Docker-tárolóban csomagolja.
* Több kisebb feladatot is futtathat párhuzamosan (előzetes verzió).  Az alapszintű két feladat párhuzamosan futtatható, míg a többi feladat várólistára kerül.
* Az egycsomópontos multi-GPU elosztott betanítási feladatok támogatása

A számítási példányokat a tesztelési és hibakeresési forgatókönyvekhez helyi előállítási célként is használhatja.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Mi történt a notebook virtuális géppel?

A számítási példányok a notebook virtuális gépet cserélik le.  

A munkaterület-fájlmegosztás és a munkaterület-adattárakban tárolt adatok egy számítási példányból lesznek elérhetők. Azonban a notebook virtuális gépen korábban telepített egyéni csomagokat újra kell telepíteni a számítási példányon. A kvóta korlátozásai, amelyek a számítási fürtök létrehozására vonatkoznak, a számítási példányok létrehozására is érvényesek lesznek.

Nem hozhatók létre új virtuális gépek a notebookon. Azonban továbbra is elérheti és használhatja a létrehozott notebook-alapú virtuális gépeket a teljes funkcionalitással. A számítási példányok a meglévő notebook virtuális gépekkel megegyező munkaterületen hozhatók létre.


## <a name="next-steps"></a>Következő lépések

* [Számítási példány létrehozása és kezelése](how-to-create-manage-compute-instance.md)
* [Oktatóanyag: az első ml-modell betanítása](tutorial-1st-experiment-sdk-train.md) azt mutatja be, hogyan használható a számítási példány egy integrált jegyzetfüzettel.
