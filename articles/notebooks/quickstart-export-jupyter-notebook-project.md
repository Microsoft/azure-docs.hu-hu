---
title: Jupyter Notebook használata Microsoft-ajánlatokkal
description: Ismerje meg, hogyan használhatók a Jupyter-jegyzetfüzetek a Microsoft-ajánlatokkal.
ms.topic: quickstart
ms.date: 02/01/2021
ms.openlocfilehash: 5679c28d9cc8a4f1893ffad72002b66ad59861e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507377"
---
# <a name="use-a-jupyter-notebook-with-microsoft-offerings"></a>Jupyter Notebook használata Microsoft-ajánlatokkal

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan importálhat egy Jupyter Notebookt a válogatott Microsoft-ajánlatokban való használatra. 

Ha meglévő Jupyter-jegyzetfüzetekkel rendelkezik, vagy új projektet szeretne elindítani, számos Microsoft-ajánlattal is felhasználhatja őket. Az alábbi szakaszokban ismertetett néhány lehetőség az alábbiakat tartalmazza: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [GitHub-Codespaces](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Környezet létrehozása jegyzetfüzetekhez

Ha olyan környezetet szeretne létrehozni, amely megfelel a kivont Azure Notebooks előzetes verziójának, használhatja a GitHubban megadott parancsfájlt.

1. Navigáljon a Azure Notebooks [GitHub-adattárhoz](https://github.com/microsoft/AzureNotebooks) , vagy [közvetlenül hozzáférhessen a környezeti mappához](https://aka.ms/aznbrequirementstxt).
1. A parancssorban navigáljon a projektekhez használni kívánt címtárhoz.
1. Töltse le a környezeti mappa tartalmát, és kövesse a README utasításait a Azure Notebooks csomag függőségeinek telepítéséhez.


## <a name="use-notebooks-in-visual-studio-code"></a>Jegyzetfüzetek használata a Visual Studio Code-ban

A [vs Code](https://code.visualstudio.com/) egy ingyenes Kódszerkesztő, amelyet helyileg használhat, vagy távoli számítási feladatokhoz kapcsolódhat. A Python bővítménnyel együtt teljes körű környezetet kínál a Python-fejlesztéshez, beleértve a Jupyter notebookok használatának gazdag natív élményét. 

![VS Code Jupyter Notebook támogatás](media/vs-code-jupyter-notebook.png)

Ha van meglévő projektfájl, vagy új jegyzetfüzetet szeretne létrehozni, használhatja a VS Code-ot! A VS Code Jupyter notebookokkal való használatáról a Visual Studio Code-ban a Visual Studio Code-ban és az [adatelemzésben](https://code.visualstudio.com/docs/python/data-science-tutorial) a [Jupyter notebookok](https://code.visualstudio.com/docs/python/jupyter-support) használata című témakörben talál útmutatást.

A Visual Studio Code-ban a [Azure Notebooks környezeti parancsfájllal](#create-an-environment-for-notebooks) is létrehozható egy olyan környezet, amely megfelel a Azure Notebooks előzetes verziójának.

## <a name="use-notebooks-in-github-codespaces"></a>Jegyzetfüzetek használata a GitHub-Codespaces

A GitHub Codespaces olyan felhőalapú környezeteket biztosít, ahol a jegyzetfüzeteket a Visual Studio Code vagy a webböngésző használatával szerkesztheti. Ugyanazzal a nagyszerű Jupyter-élménysel rendelkezik, mint a VS Code, de nem kell semmit telepítenie az eszközre. Ha nem szeretné beállítani a helyi környezetet, és inkább egy felhőalapú megoldást szeretne használni, akkor a codespace létrehozása nagyszerű lehetőség. Első lépések:
1. Választható Gyűjtse össze a GitHub-Codespaces használni kívánt projektfájlok összes fájlját.
1. [Hozzon létre egy GitHub-tárházat](https://help.github.com/github/getting-started-with-github/create-a-repo) a jegyzetfüzetek tárolásához.   
1. [Adja hozzá a fájljait](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) a tárházhoz.
1. [Hozzáférés kérése a GitHub Codespaces előzetes verziójához](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Jegyzetfüzetek használata Azure Machine Learning

A Azure Machine Learning teljes gépi tanulási platformot biztosít, amely lehetővé teszi a felhasználók számára, hogy gyorsabban építsenek és helyezzen üzembe modelleket az Azure-ban. Az Azure ML lehetővé teszi, hogy Jupyter jegyzetfüzeteket futtasson egy virtuális gépen vagy egy megosztott fürtözött számítástechnikai környezetben. Ha felhőalapú megoldásra van szüksége az ML-munkaterheléshez a kísérlet követésével, az adatkészletek kezelésével és egyebekkel kapcsolatban, javasoljuk, hogy Azure Machine Learning. Ismerkedés az Azure ML-vel:

1. Választható Gyűjtse össze az Azure ML-vel használni kívánt projektfájlok összes fájlját.
1. [Hozzon létre egy munkaterületet](../machine-learning/how-to-manage-workspace.md) a Azure Portalban.

   ![Munkaterület létrehozása](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Nyissa meg az [Azure studiót (előzetes verzió)](https://ml.azure.com/).
1. A bal oldali navigációs sáv használatával válassza a **jegyzetfüzetek** lehetőséget.
1. Kattintson a **fájlok feltöltése** gombra, és töltse fel a projektfájlok fájljait.

Az Azure ML-vel és a Jupyter-jegyzetfüzetek futtatásával kapcsolatos további információkért tekintse át a [dokumentációt](../machine-learning/how-to-run-jupyter-notebooks.md) , vagy próbálja ki [Machine Learning](/learn/modules/intro-to-azure-machine-learning-service/) modult Microsoft Learn.


## <a name="use-azure-lab-services"></a>Azure Lab Services használata

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) lehetővé teszi a pedagógusok számára, hogy az előre konfigurált virtuális gépeket egy teljes osztályterem számára könnyedén beállítsák és igény szerint férhessenek hozzá. Ha a Jupyter-jegyzetfüzetekkel és a Felhőbeli számításokkal együtt szeretné használni a testreszabott tantermi környezetben, a labor Services nagyszerű megoldás.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

Ha van meglévő projektfájl, vagy új jegyzetfüzetet szeretne létrehozni, használhatja a Azure Lab Services. A laborok beállításával kapcsolatos útmutatásért lásd: [tesztkörnyezet beállítása az adatelemzés megtanításához a Python és a Jupyter notebook használatával](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>A GitHub használata

A GitHub lehetővé teszi a jegyzetfüzetek (és más fájlok) tárolását, a notebookok megosztását és másokkal való együttműködését. Ha a projektek megosztását és másokkal való együttműködését keresi, a GitHub nagyszerű megoldás, és a [GitHub-Codespaces](#use-notebooks-in-github-codespaces) kombinálható a nagyszerű fejlesztési élmény érdekében. Ismerkedés a GitHubtal

1. Választható Gyűjtse össze a GitHubhoz használni kívánt projektfájlok összes fájlját.
1. [Hozzon létre egy GitHub-tárházat](https://help.github.com/github/getting-started-with-github/create-a-repo) a jegyzetfüzetek tárolásához. 
1. [Adja hozzá a fájljait](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) a tárházhoz.

## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Pythonról a Visual Studio Code-ban](https://code.visualstudio.com/docs/python/python-tutorial)
- [Tudnivalók a Azure Machine Learning-és Jupyter-jegyzetfüzetekről](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Tudnivalók a GitHub-Codespaces](https://github.com/features/codespaces)
- [Tudnivalók a Azure Lab Services](https://azure.microsoft.com/services/lab-services/)
- [Tudnivalók a GitHubról](https://help.github.com/github/getting-started-with-github/)
