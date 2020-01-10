---
title: Batch-feladatok elindítása Azure Functions használatával
description: Oktatóanyag – OCR alkalmazása a beolvasott dokumentumokra a tárolási blobba való felvételük során
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: 99b9a8cac1342f30fcd70f8e938ba6d3dcb90aef
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770152"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Oktatóanyag: batch-feladatok elindítása Azure Functions használatával

Ebből az oktatóanyagból megtudhatja, hogyan indíthat el egy batch-feladatot a Azure Functions használatával. Megmutatjuk, hogy az Azure Storage blob-tárolóhoz hozzáadott dokumentumok milyen optikai karakterfelismeréssel (OCR) vannak alkalmazva a Azure Batchon keresztül. Az OCR-feldolgozás egyszerűsítése érdekében egy batch OCR-feladatot futtató Azure-függvényt állítunk be, amikor egy fájlt hozzáadnak a blob-tárolóhoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* Egy Azure Batch-fiók és egy társított Azure Storage-fiók. A fiókok létrehozásával és összekapcsolásával kapcsolatos további információkért tekintse meg [a Batch-fiók létrehozása](quick-create-portal.md#create-a-batch-account) című témakört.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Batch-készlet és batch-feladatok létrehozása a Batch Explorer használatával

Ebben a szakaszban a Batch Explorer használatával hozza létre a Batch-készletet és a Batch-feladatot, amely OCR-feladatokat fog futtatni. 

### <a name="create-a-pool"></a>Készlet létrehozása

1. Jelentkezzen be Batch Explorer Azure-beli hitelesítő adataival.
1. Hozzon létre egy készletet a bal oldali sávban található **készletek** kiválasztásával, majd a keresési űrlap fölé a **Hozzáadás** gombra kattintva. 
    1. Válassza ki az azonosítót és a megjelenítendő nevet. Ezt a példát `ocr-pool` fogjuk használni.
    1. Állítsa a skálázási típust **rögzített méretre**, és állítsa a dedikált csomópontok száma értéket 3 értékre.
    1. Válassza az **Ubuntu 18,04-LTS** operációs rendszert.
    1. A virtuális gép méreteként válassza a `Standard_f2s_v2` lehetőséget.
    1. Engedélyezze az indítási feladatot, és adja hozzá a `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`parancsot. Ügyeljen arra, hogy a felhasználói identitást a **feladat alapértelmezett felhasználója (rendszergazda)** értékre állítsa be, amely lehetővé teszi, hogy a feladatok elindításához a `sudo`parancsokat tartalmazzon.
    1. Kattintson az **OK** gombra.
### <a name="create-a-job"></a>Feladat létrehozása

1. Hozzon létre egy feladatot a készleten a bal oldali sávban a **feladatok** elem kiválasztásával, majd a keresési űrlap fölé a **Hozzáadás** gombra kattintva. 
    1. Válassza ki az azonosítót és a megjelenítendő nevet. Ezt a példát `ocr-job` fogjuk használni.
    1. Állítsa a készletet `ocr-pool`re, vagy bármilyen nevet, amelyet a készlethez választott.
    1. Kattintson az **OK** gombra.


## <a name="create-blob-containers"></a>BLOB-tárolók létrehozása

Itt olyan blob-tárolókat hoz létre, amelyek a bemeneti és kimeneti fájljait fogják tárolni az OCR batch-feladathoz.

1. Jelentkezzen be Storage Explorer Azure-beli hitelesítő adataival.
1. A Batch-fiókhoz csatolt Storage-fiók használatával hozzon létre két BLOB-tárolót (egyet a bemeneti fájlokhoz, egyet a kimeneti fájlokhoz) a [blob-tároló létrehozása](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container)című témakör lépéseit követve.

Ebben a példában a bemeneti tároló neve `input`, és az a hely, ahol az OCR nélküli összes dokumentumot fel kell tölteni a feldolgozásba. A kimeneti tároló neve `output`, és az a hely, ahol a Batch-feladatok a feldolgozott dokumentumokat az OCR-sel együtt írja.  
    * Ebben a példában meghívjuk a bemeneti tárolót `input`és a kimeneti tárolót `output`.  
    * A bemeneti tároló az OCR nélküli összes dokumentum feltöltése.  
    * A kimeneti tároló, ahol a Batch-feladatok a dokumentumokat OCR-sel írja.  

Hozzon létre egy közös hozzáférési aláírást a kimeneti tárolóhoz Storage Explorerban. Ehhez kattintson a jobb gombbal a kimeneti tárolóra, és válassza a **közös hozzáférésű aláírás beolvasása..** . lehetőséget. Az **engedélyek**alatt keresse meg az **írást**. Nincs szükség további engedélyekre.  

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Ebben a szakaszban létrehoz egy Azure-függvényt, amely elindítja az OCR batch-feladatot, valahányszor feltölt egy fájlt a bemeneti tárolóba.

1. A függvények létrehozásához kövesse az [Azure Blob Storage által aktivált függvény létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) című témakör lépéseit.
    1. Amikor a rendszer a Storage-fiókra kéri, használja ugyanazt a Storage-fiókot, amelyet a Batch-fiókjához társított.
    1. A **futásidejű verem**esetében válassza a .net elemet. A függvény beírásával kihasználhatja a Batch .NET SDK- C# t.
1. A blob által aktivált függvény létrehozása után a függvényben használja a GitHubon lévő [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) és [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) .
    * a `run.csx` akkor fut le, amikor új blobot adnak hozzá a bemeneti blob-tárolóhoz.
    * `function.proj` listázza a függvény kódjában található külső kódtárakat, például a Batch .NET SDK-t.
1. Módosítsa a változók helyőrző értékét a `run.csx` fájl `Run()` függvényében, hogy az tükrözze a Batch és a Storage hitelesítő adatait. A Batch-és Storage-fiók hitelesítő adatait a Batch-fiók **kulcsok** szakaszának Azure Portaljában találja.
    * Kérje le a Batch-és a Storage-fiók hitelesítő adatait a Batch-fiók **kulcsok** szakaszának Azure Portal. 

## <a name="trigger-the-function-and-retrieve-results"></a>A függvény elindítása és az eredmények beolvasása

Töltse fel a beolvasott fájlok bármelyikét vagy mindegyikét a GitHubon lévő [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) könyvtárából a bemeneti tárolóba. Figyelje a Batch Explorer annak ellenőrzéséhez, hogy a feladat minden fájlhoz hozzá lesz-e adva `ocr-pool`hoz. Néhány másodperc elteltével a rendszer hozzáadja az OCR-t alkalmazó fájlt a kimeneti tárolóhoz. A fájl ezután látható és beolvasható Storage Explorer.

Emellett megtekintheti a naplófájlokat a Azure Functions Webszerkesztő ablakának alján, ahol a bemeneti tárolóba feltöltött összes fájlhoz hasonló üzenet jelenik meg:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

A kimeneti fájlok Storage Explorerról a helyi gépre való letöltéséhez először jelölje ki a kívánt fájlokat, majd válassza ki a **letöltést** a felső menüszalagon. 

> [!TIP]
> A letöltött fájlok kereshetők, ha PDF-olvasóban vannak megnyitva.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Készletek és feladatok létrehozása Batch Explorer használatával
> * BLOB-tárolók és közös hozzáférésű aláírás (SAS) létrehozásához használja a Storage Explorer
> * BLOB által aktivált Azure-függvény létrehozása
> * Bemeneti fájlok feltöltése a Storage-ba
> * Tevékenységek végrehajtásának figyelése
> * Kimeneti fájlok lekérése

* Ha további példákat szeretne használni a .NET API-t a Batch-munkaterhelések ütemezhetik és dolgozzák fel, tekintse [meg a mintákat a githubon](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* A Batch-munkaterhelések futtatására használható Azure Functions-eseményindítók megjelenítéséhez tekintse meg [a Azure functions dokumentációját](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
