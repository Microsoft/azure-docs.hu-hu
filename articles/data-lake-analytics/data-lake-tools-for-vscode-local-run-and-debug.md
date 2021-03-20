---
title: U-SQL-feladatok hibakeresése – Azure Data Lake eszközök a Visual Studio Code-hoz
description: Ismerje meg, hogyan használhatja a Azure Data Lake Tools for Visual Studio Code-ot az U-SQL-feladatok helyi futtatásához és hibakereséséhez.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 07/14/2017
ms.openlocfilehash: 194fe3494915be9f6784e1596e647885634817ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97969010"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>A U-SQL és a hibakeresés helyi futtatása a Visual Studio Code-ban
Ez a cikk azt ismerteti, hogyan futtathatók a U-SQL-feladatok egy helyi fejlesztési gépen a korai kódolási fázisok felgyorsításához vagy a kód helyi hibakereséséhez a Visual Studio Code-ban. A Visual Studio Code Azure Data Lake eszközével kapcsolatos utasításokért lásd: a [Azure Data Lake Tools használata a Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)-hoz.

A Azure Data Lake Tools for Visual Studio csak Windows-telepítések támogatják a U-SQL helyi futtatásához és a U-SQL helyi hibakereséséhez szükséges műveletet. A macOS-és Linux-alapú operációs rendszerek telepítései nem támogatják ezt a funkciót.

## <a name="set-up-the-u-sql-local-run-environment"></a>A U-SQL helyi futtatási környezetének beállítása

1. Válassza a CTRL + SHIFT + P billentyűkombinációt a parancssor megnyitásához, majd írja be az **ADL: helyi futtatási csomag letöltése** lehetőséget a csomagok letöltéséhez.  

   ![Az ADL LocalRun-függőségi csomagok letöltése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Keresse meg a függőségi csomagokat a **kimenet** ablaktáblán látható elérési útról, majd telepítse a BuildTools és a Win10SDK 10240. Példa az alábbi elérési útra:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![A függőségi csomagok megkeresése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2,1 a **BuildTools** telepítéséhez kattintson a visualcppbuildtools_full.exe elemre a LocalRunDependency mappában, majd kövesse a varázsló utasításait.   

    ![A BuildTools telepítése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2,2 a **Win10SDK 10240** telepítéséhez kattintson a LocalRunDependency/Win10SDK_10.0.10240 _2 mappában található sdksetup.exe elemre, majd kövesse a varázsló utasításait.  

    ![A Win10SDK 10240 telepítése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Állítsa be a környezeti változót. Állítsa a **SCOPE_CPP_SDK** környezeti változót a következőre:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Indítsa el a helyi futtatási szolgáltatást, és küldje el a U-SQL-feladatot egy helyi fiókba 
Az első alkalommal használja az **ADL: helyi futtatási csomag letöltése** lehetőséget a helyi futtatási csomagok letöltéséhez, ha nem [állított be U-SQL helyi futtatási környezetet](#set-up-the-u-sql-local-run-environment).

1. A Command paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra, majd írja be az **ADL: Start local Run Service** parancsot.   
2. Válassza az **elfogadás** lehetőséget a Microsoft szoftverlicenc-szerződés első elfogadásához. 

   ![Microsoft szoftverlicenc-szerződés elfogadása](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Megnyílik a cmd-konzol. Első alkalommal a felhasználóknak **3** értéket kell megadnia, majd meg kell keresnie a helyi mappa elérési útját az adatok bemenetéhez és kimenetéhez. Ha az elérési út fordított perjelekkel nem sikerül, próbálkozzon a perjelek továbbításával. A többi lehetőség esetében használhatja az alapértelmezett értékeket.

   ![Data Lake eszközök a Visual Studio Code helyi futtatásához cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. A CTRL + SHIFT + P billentyűkombinációval nyissa meg a parancssort, írja be az **ADL: submit feladatot**, majd válassza a **Local (helyi** ) lehetőséget a feladatok helyi fiókba való elküldéséhez.

   ![Data Lake eszközök a Visual Studio Code-hoz válassza a helyi lehetőséget](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Miután elküldte a feladatot, megtekintheti a Küldés részleteit. A Küldés részleteinek megtekintéséhez válassza a **jobUrl** lehetőséget a **kimenet** ablakban. A beküldési állapotot a cmd konzolon is megtekintheti. Ha többet szeretne megtudni a feladatok részleteiről, írja be a **7-es** értéket a cmd konzolon.

   ![Data Lake eszközök Visual Studio Code helyi Futtatás kimeneti ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
    ![ Data Lake eszközök a Visual Studio Code helyi Futtatás cmd-állapotához](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Helyi hibakeresés elindítása a U-SQL-feladatokhoz  
A felhasználó első alkalommal:

1. Az **ADL használata: helyi** futtatási csomag letöltése helyi futtatási csomagok letöltéséhez, ha nem [állította be a U-SQL helyi futtatási környezetét](#set-up-the-u-sql-local-run-environment).
2. Ha nincs telepítve, telepítse a .NET Core SDK 2,0-as üzenetet az üzenet mezőbe.
 
  ![emlékeztető a DotNet telepítéséhez](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Telepítse a C#-t a Visual Studio Code-hoz, ahogy azt a nem telepítette az üzenetablakban. A folytatáshoz kattintson a **telepítés** gombra, majd indítsa újra a VSCode.

![Emlékeztető a C telepítéséhez #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

A helyi hibakeresés végrehajtásához kövesse az alábbi lépéseket:
  
1. A Command paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra, majd írja be az **ADL: Start local Run Service** parancsot. Megnyílik a cmd-konzol. Győződjön meg arról, hogy a **DataRoot** be van állítva.
2. Hozzon létre egy töréspontot a C#-kódban.
3. Vissza a parancsfájl-szerkesztőhöz, kattintson a jobb gombbal, és válassza az **ADL: helyi hibakeresés** lehetőséget.
    
   ![A Visual Studio Code helyi hibakeresési eredményének Data Lake eszközei](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Következő lépések
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)
* [A U-SQL fejlesztése a Python, az R és a CSharp Azure Data Lake Analytics a VSCode-ben](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Ismerkedés a Data Lake Analytics a PowerShell használatával](data-lake-analytics-get-started-powershell.md)
* [A Data Lake Analytics használatának első lépései a Azure Portal](data-lake-analytics-get-started-portal.md)
* [A Visual studióhoz készült Data Lake Tools használata az U-SQL-alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics (U-SQL) katalógus használata](./data-lake-analytics-u-sql-get-started.md)
