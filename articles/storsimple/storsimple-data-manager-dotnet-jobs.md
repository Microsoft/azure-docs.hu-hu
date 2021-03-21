---
title: A .NET SDK használata Microsoft Azure StorSimple Data Manager feladatokhoz
description: Megtudhatja, hogyan használhatja a .NET SDK-t a StorSimple Data Manager szolgáltatáson belül a StorSimple-eszközökre vonatkozó adatátalakításhoz.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b18627d2806662d6d966af95d51873d5623b2393
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015536"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>A .NET SDK használata adatátalakítás kezdeményezéséhez

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan használható a StorSimple Data Manager szolgáltatás Adatátalakítási funkciója az StorSimple átalakításához. Az átalakított adatmennyiséget a felhőben lévő más Azure-szolgáltatások is felhasználják.

Az Adatátalakítási feladatok kétféleképpen indíthatók el:

- A .NET SDK használata
- Azure Automation runbook használata
 
  Ez a cikk részletesen ismerteti, hogyan hozhat létre egy minta .NET-konzol alkalmazást egy Adatátalakítási feladatok elindításához, majd nyomon követheti a végrehajtást. Ha többet szeretne megtudni arról, hogyan indíthatja el az adatátalakítást az Automation segítségével, lépjen a [Azure Automation Runbook használatával az Adatátalakítási feladatok](storsimple-data-manager-job-using-automation.md)elindításához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik az alábbiakkal:
*   Egy rendszert futtató számítógép:

    - Visual Studio 2012, 2013, 2015 vagy 2017.

    - Azure PowerShell. [Töltse le az Azure PowerShellt](/powershell/azure/).
*   Egy adott erőforráscsoport StorSimple Data Manager megfelelően konfigurált feladatdefiníció.
*   Az összes szükséges DLL-fájl. Töltse le ezeket a DLL-eket a [GitHub-adattárból](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) szkript a GitHub-adattárból.

## <a name="step-by-step-procedure"></a>Lépésről lépésre haladó eljárás

Az alábbi lépések végrehajtásával indítson el egy adat-átalakítási feladatot a .NET használatával.

1. A konfigurációs paraméterek beolvasásához hajtsa végre a következő lépéseket:
    1. Töltse le az `Get-ConfigurationParams.ps1` elemet a GitHub-tárház parancsfájlból a `C:\DataTransformation` helyről.
    1. Futtassa a `Get-ConfigurationParams.ps1` szkriptet a GitHub-adattárból. Írja be a következő parancsot:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        A ActiveDirectoryKey és a AppName bármilyen értéket adhat át.

2. Ez a szkript a következő értékeket jeleníti meg:
    * Ügyfél-azonosító
    * Bérlőazonosító
    * Active Directory kulcs (ugyanaz, mint a fent megadott)
    * Előfizetés azonosítója

        ![Konfigurációs paraméterek parancsfájl kimenete](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. A Visual Studio 2012, 2013 vagy 2015 használatával hozzon létre egy C# .NET-konzol alkalmazást.

    1. Indítsa el a **Visual Studio 2012/2013/2015**-es kiadását.
    1. Válassza a **Fájl > Új projekt** lehetőséget.

        ![1. projekt létrehozása](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Válassza a **telepített > sablonok > Visual C# > Console Application** elemet.
    3. Adja  meg a DataTransformationApp **nevet**.
    4. A **helyhez** válassza a **C:\DataTransformation** lehetőséget.
    6. A projekt létrehozásához kattintson az **OK** gombra.

        ![2. projekt létrehozása](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Most adja hozzá a DLL-fájlok [mappában](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) található összes DLL-t a létrehozott projektben található **hivatkozásként** . A DLL-fájlok hozzáadásához hajtsa végre a következőket:

   1. A Visual Studióban lépjen a **> megoldáskezelő megtekintése** elemre.
   2. Kattintson az Adatátalakítási alkalmazás projekt bal oldalán található nyílra. Kattintson a **hivatkozások** elemre, majd kattintson a jobb gombbal a **hivatkozás hozzáadásához**.
    
       ![DLL-fájlok hozzáadása 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Keresse meg a csomagok mappa helyét, jelölje ki az összes dll-fájlt, és kattintson a **Hozzáadás**, majd **az OK** gombra.

       ![DLL-fájlok hozzáadása 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Adja hozzá az alábbi **using** utasításokat a projekt forrásfájljához (Program.cs).

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. A következő kód inicializálja az Adatátalakítási feladatsort. Adja hozzá ezt a **Main metódushoz**. Cserélje le a konfigurációs paraméterek értékét a korábban beszerzett értékekre. Dugja be az erőforráscsoport- **név** és a **resourcename** értékeit. A **ResourceGroupName** az a StorSimple Data Manager társítva, amelyhez a feladatdefiníció konfigurálva lett. A **resourcename** a StorSimple Data Manager szolgáltatás neve.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Adja meg azokat a paramétereket, amelyeknek a feladattípust futtatni kell

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    VAGY

    Ha módosítani szeretné a feladatdefiníció paramétereit a Futtatás ideje alatt, adja hozzá a következő kódot:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Az inicializálás után adja hozzá a következő kódot egy Adatátalakítási feladatok elindításához a feladatsoron. Csatlakoztassa a megfelelő **feladatdefiníció nevét**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    A kód beillesztése után hozza létre a megoldást. Itt látható az Adatátalakítási feladatsor inicializálásához szükséges kódrészlet képernyőképe.

   ![Kódrészlet az Adatátalakítási feladatok inicializálásához](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Ez a művelet átalakítja azokat az adatokkal, amelyek megfelelnek a gyökérkönyvtárnak és a StorSimple a köteten belül, és a megadott tárolóba/fájlmegosztásba helyezi azokat. Egy fájl átalakításakor a rendszer egy üzenetet ad hozzá a tárolási várólistához (ugyanabban a Storage-fiókban, mint a tároló/fájlmegosztás), amelynek a neve megegyezik a feladatdefiníció nevével. Ez az üzenet triggerként használható a fájl további feldolgozásának elindításához.

10. A feladatok elindítása után a következő kód használatával követheti nyomon a feladatot a befejezéshez. Ez a kód nem kötelező a feladatok futtatásához.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Itt látható a teljes, a .NET-tel kapcsolatos feladatok elindításához használt kód mintája.

    ![Teljes kódrészlet a .NET-feladatok elindításához](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Következő lépések

[Az adatátalakításhoz használja StorSimple Data Manager felhasználói felületét](storsimple-data-manager-ui.md).