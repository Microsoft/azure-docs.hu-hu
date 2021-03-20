---
title: BLOB elérési útjának módosítása az alapértelmezett értékről
description: Megtudhatja, hogyan állíthat be egy Azure-függvényt egy alapértelmezett blob-fájl elérési útjának átnevezéséhez, és hogyan helyezheti át a blobokat egy másik helyre.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6af095c7abdb9aa61e57d543ff2ab2f9192dadc8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011438"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Blob alapértelmezett elérési útjának megváltoztatása egy másik elérési útra

Ha a StorSimple Data Manager szolgáltatás átalakítja az adatmennyiséget, alapértelmezés szerint az átalakított blobokat a tároló létrehozása során megadott tárolási tárolóban helyezi el. Mivel a Blobok ezen a helyen érkeznek, érdemes lehet áthelyezni ezeket a blobokat egy másik helyre. Ez a cikk azt ismerteti, hogyan állíthat be egy Azure-függvényt az alapértelmezett blob-fájl elérési útjának átnevezéséhez, és így a blobokat másik helyre helyezheti át.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a StorSimple Data Manager szolgáltatásban megfelelően konfigurált feladatdefiníció van.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Azure-függvény létrehozásához hajtsa végre a következő lépéseket:

1. Nyissa meg az [Azure Portal](https://portal.azure.com/).

2. Kattintson az **+ Erőforrás létrehozása** gombra. A keresőmezőbe írja be a **Függvényalkalmazás** **kifejezést** , majd nyomja le az **ENTER** billentyűt. A megjelenített alkalmazások listájában válassza ki a **Function app** (alkalmazás) elemet, majd kattintson rá.

    ![Írja be a "függvényalkalmazás" kifejezést a keresőmezőbe](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Kattintson a **Létrehozás** lehetőségre.

    ![A függvényalkalmazás ablak "létrehozás" gombja](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. A **függvényalkalmazás** konfiguráció paneljén hajtsa végre a következő lépéseket:

    1. Adja meg az **alkalmazás egyedi nevét**.
    2. A legördülő listából válassza ki az **előfizetést**. Ennek az előfizetésnek meg kell egyeznie a StorSimple Data Manager szolgáltatáshoz társítotttal.
    3. Válassza az **Új erőforráscsoport létrehozása** lehetőséget.
    4. A **üzemeltetési terv** legördülő listában válassza a használati **terv** elemet.
    5. Adja meg azt a helyet, ahol a függvény fut. Ugyanazt a régiót szeretné használni, ahol a StorSimple Data Manager szolgáltatás és a feladattípushoz társított Storage-fiók található.
    6. Válasszon meglévő Storage-fiókot, vagy hozzon létre egy új Storage-fiókot. A függvényhez belső használatú Storage-fiók szükséges.

        ![Adja meg az új függvényalkalmazás konfigurációs adatkészletet](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Kattintson a **Létrehozás** lehetőségre. Létrejön a Function alkalmazás.
     
        ![függvényalkalmazás létrehozva](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Válassza a **függvények** lehetőséget, majd kattintson az **+ új függvény** gombra.

    ![Kattintson az + új függvény lehetőségre](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Válassza a **C#** lehetőséget a nyelvhez. A sablon csempék tömbben válassza a **C#** elemet a **QueueTrigger-csharp** csempén.

7. A **várólista-triggerben**:

    1. Adja meg a függvény **nevét** .
    2. Az **üzenetsor neve** mezőbe írja be az Adatátalakítási feladatdefiníció nevét.
    3. A **Storage-fiók kapcsolatai** területen kattintson az **új** elemre. A Storage-fiókok listájából válassza ki a feladatdefiníció hozzárendelt fiókját. Jegyezze fel a kapcsolatok nevét (kiemelve). A nevet később kell megadni az Azure-függvényben.

        ![Új C#-függvény létrehozása](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Kattintson a **Létrehozás** lehetőségre. A **függvény** létrejött.

     
10. A függvény ablakban futtassa a _. CSX_ fájlt.

    ![Új C#-függvény létrehozása 2](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Hajtsa végre a következő lépéseket.

    1. Illessze be a következő kódot:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Cserélje le a 11. sorban lévő **STORAGE_CONNECTIONNAME** a Storage-fiókja kapcsolataira (lásd a 7c lépést).

        ![A Storage-kapcsolatok nevének másolása](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Mentse** a függvényt.

        ![Függvény mentése](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. A függvény végrehajtásához vegyen fel még egy fájlt a következő lépések végrehajtásával:

    1. Kattintson a **fájlok megtekintése** elemre.

       ![A "fájlok megtekintése" hivatkozás](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Kattintson a **+ Hozzáadás** gombra.
        
        ![Új függvény hozzáadása fájl hozzáadása](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Írja be **aproject.js** be, majd nyomja le az **ENTER** billentyűt. Illessze be a következő kódot a fájl **project.jsba** :

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Kattintson a **Mentés** gombra.

        ![Új Function Project JSON](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Létrehozott egy Azure-függvényt. Ez a függvény minden alkalommal aktiválódik, amikor az Adatátalakítási feladat létrehoz egy új blobot.

## <a name="next-steps"></a>Következő lépések

[Az adatátalakítás StorSimple Data Manager felhasználói felület használatával](storsimple-data-manager-ui.md)
