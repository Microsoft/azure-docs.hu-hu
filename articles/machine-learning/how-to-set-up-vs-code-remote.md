---
title: Kapcsolódás a számítási példányhoz a Visual Studio Code-ban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan csatlakozhat egy Azure Machine Learning számítási példányhoz a Visual Studio Code-ban az interaktív Jupyter Notebook és a távoli fejlesztési feladatok futtatásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 11/16/2020
ms.openlocfilehash: ccd56afc8c4ea7e236946fc6afa54e471203fe31
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065980"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Kapcsolódás Azure Machine Learning számítási példányhoz a Visual Studio Code-ban (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan csatlakozhat egy Azure Machine Learning számítási példányhoz a Visual Studio Code használatával.

Az [Azure Machine learning számítási példány](concept-compute-instance.md) egy teljes körűen felügyelt felhőalapú munkaállomás az adatszakértők számára, és felügyeleti és vállalati készültségi képességeket biztosít a rendszergazdák számára.

A Visual Studio Code-ból kétféleképpen lehet csatlakozni a számítási példányokhoz:

* Távoli Jupyter Notebook-kiszolgáló. Ez a beállítás lehetővé teszi a számítási példányok távoli Jupyter Notebook kiszolgálóként való beállítását.
* A [Visual Studio Code távoli fejlesztése](https://code.visualstudio.com/docs/remote/remote-overview). A Visual Studio Code Remote Development lehetővé teszi, hogy egy tárolót, egy távoli gépet vagy a Linux (WSL) Windows alrendszerét teljes funkcionalitású fejlesztői környezetként használja.

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Számítási példány konfigurálása távoli jegyzetfüzet-kiszolgálóként

A számítási példányok távoli Jupyter Notebook kiszolgálóként való konfigurálásához néhány előfeltételre van szükség:

* Azure Machine Learning Visual Studio Code-bővítményt. További információ: [Azure Machine learning Visual Studio Code Extension telepítési útmutatója](tutorial-setup-vscode-extension.md).
* Azure Machine Learning munkaterület. [Hozzon létre egy új munkaterületet a Visual Studio Code Azure Machine learning használatával,](how-to-manage-resources-vscode.md#create-a-workspace) ha még nem rendelkezik ilyennel.

Kapcsolódás számítási példányhoz:

1. Nyisson meg egy Jupyter Notebook a Visual Studio Code-ban.
1. Ha az integrált jegyzetfüzet-élmény betöltődik, válassza a **Jupyter-kiszolgáló** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning távoli Jupyter Notebook kiszolgáló legördülő menüjének elindítása](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Azt is megteheti, hogy a parancs-palettát is használja:

    1. Nyissa meg a parancssort úgy, hogy kijelöli a menüsávban látható **> a parancs-palettát** .
    1. Adja meg a szövegmezőt `Azure ML: Connect to Compute instance Jupyter server` .

1. Válassza ki `Azure ML Compute Instances` a Jupyter-kiszolgáló beállításai közül a listából.
1. Válassza ki az előfizetését az előfizetések listájából. Ha korábban már konfigurálta az alapértelmezett Azure Machine Learning munkaterületet, ez a lépés kimarad.
1. Válassza ki a munkaterületet.
1. Válassza ki a számítási példányt a listából. Ha még nem rendelkezik ilyennel, válassza az **új Azure ml Compute-példány létrehozása** lehetőséget, és kövesse az utasításokat, és hozzon létre egyet.
1. A módosítások életbe léptetéséhez újra kell töltenie a Visual Studio Code-ot.
1. Nyisson meg egy Jupyter Notebook, és futtasson egy cellát.

> [!IMPORTANT]
> A kapcsolat létrehozásához egy cellát **kell** futtatnia.

Ezen a ponton továbbra is futtathat cellákat a Jupyter Notebook.

> [!TIP]
> A Python-szkripteket (. Jupyter) is használhatja. További információt a [Visual Studio Code Python interaktív dokumentációjában](https://code.visualstudio.com/docs/python/jupyter-support-py)talál.

## <a name="configure-compute-instance-remote-development"></a>Számítási példányok távoli fejlesztésének konfigurálása

A teljes funkcionalitású távoli fejlesztési élményhez néhány előfeltétel szükséges:

* [Visual Studio Code Remote SSH bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).
* SSH-kompatibilis számítási példány. További információ [: számítási példány létrehozása útmutató](how-to-create-manage-compute-instance.md).

> [!NOTE]
> Windows-platformokon [telepítenie kell egy OpenSSH-kompatibilis SSH-ügyfelet](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , ha még nem létezik ilyen. A PuTTY nem támogatott a Windowsban, mivel az SSH-parancsnak az elérési úton kell lennie.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>A számítási példány IP-címének és SSH-portjának beolvasása

1. Nyissa meg a Azure Machine Learning studiót a következő címen: https://ml.azure.com/ .
2. Válassza ki a [munkaterületet](concept-workspace.md).
1. Kattintson a **számítási példányok** fülre.
1. Az **alkalmazás URI** -oszlopában kattintson a távoli számításként használni kívánt számítási példány **SSH** -hivatkozására. 
1. A párbeszédpanelen jegyezze fel az IP-címet és az SSH-portot. 
1. Mentse a titkos kulcsot a ~/.ssh/könyvtárba a helyi számítógépen; például nyisson meg egy szerkesztőt egy új fájlhoz, és illessze be a kulcsot a következőbe: 

   **Linux**:

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   A titkos kulcs valahogy így fog kinézni:

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. Módosítsa a fájl engedélyeit, és győződjön meg arról, hogy csak a fájlt tudja olvasni.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>Példány hozzáadása gazdagépként

Nyissa meg a fájlt `~/.ssh/config` (Linux) vagy `C:\Users<username>.ssh\config` (Windows) egy szerkesztőben, és adjon hozzá egy, az alábbi tartalomhoz hasonló új bejegyzést:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

További részletek a mezőkről:

|Mező|Leírás|
|----|---------|
|Gazdagép|Bármilyen gyorsírást használhat a számítási példányhoz |
|HostName|Ez a számítási példány IP-címe. |
|Port|Ez a port a fenti SSH-párbeszédablakban látható. |
|User|Ennek a következőnek kell lennie `azureuser` |
|IdentityFile|Arra a fájlra mutasson, ahová a titkos kulcsot mentette |

Most a fentiekben használt gyorsírással SSH-t kell tudnia használni a számítási példányhoz `ssh azmlci1` .

### <a name="connect-vs-code-to-the-instance"></a>A VS Code összekötése a példánnyal

1. Az SSH-konfigurációk megjelenítéséhez kattintson a távoli SSH ikonra a Visual Studio Code tevékenység sávján.

1. Kattintson a jobb gombbal az imént létrehozott SSH-gazda konfigurációra.

1. Válassza a **Kapcsolódás a gazdagéphez lehetőséget az aktuális ablakban**. 

Innentől kezdve teljes mértékben dolgozik a számítási példányon, és mostantól szerkesztheti, hibakeresést végezhet, használhatja a git-t, a bővítmények használatát stb. – akárcsak a helyi Visual Studio Code-ban.

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a Visual Studio Code Remote-t, a Visual Studio Code-ból távoli számításként használhat számítási példányt a [kód interaktív hibakereséséhez](how-to-debug-visual-studio-code.md).

[Oktatóanyag: az első ml-modell betanítása](tutorial-1st-experiment-sdk-train.md) azt mutatja be, hogyan használható a számítási példány egy integrált jegyzetfüzettel.
