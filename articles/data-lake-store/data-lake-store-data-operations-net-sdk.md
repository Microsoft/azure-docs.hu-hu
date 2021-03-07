---
title: .NET SDK – fájlrendszerbeli műveletek Data Lake Storage Gen1-Azure-ban
description: Használja a Azure Data Lake Storage Gen1 .NET SDK-t a fájlrendszerbeli műveletekhez Data Lake Storage Gen1 például mappák létrehozása stb.).
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.custom: devx-track-dotnet
ms.openlocfilehash: faa539119bbb8d73e66788b8ba472cf0db26a2e4
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431737"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Fájlrendszer-műveletek Data Lake Storage Gen1 a .NET SDK használatával

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Ebből a cikkből megtudhatja, hogyan hajthat végre fájlrendszer-műveleteket Data Lake Storage Gen1 a .NET SDK használatával. A fájlrendszeri műveletek közé tartozik a mappák létrehozása Data Lake Storage Gen1 fiókban, fájlok feltöltése, fájlok letöltése stb.

A Data Lake Storage Gen1 a .NET SDK használatával történő Fiókkezelés-kezelési műveletekkel kapcsolatos útmutatásért lásd: [Fiókkezelés a Data Lake Storage Gen1 a .net SDK használatával](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

* A **Visual Studio 2013-es vagy újabb** verzióját. A cikkben szereplő utasítások a Visual Studio 2019-et használják.

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1 fiók**. A fiókok létrehozásával kapcsolatos útmutatásért tekintse meg a [Azure Data Lake Storage Gen1 első lépéseivel](data-lake-store-get-started-portal.md)foglalkozó témakört.

## <a name="create-a-net-application"></a>.NET-alkalmazás létrehozása

A [GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) elérhető kódminta végigvezeti a fájlok tárolóban való létrehozásának, a fájlok összetűzésének, a fájlok letöltésének és az egyes fájlok tárolóból való törlésének folyamatán. A cikk ezen szakasza a kód fő részeit mutatja be.

1. A Visual Studióban kattintson a **fájl** menüre, majd az **új**, majd a **projekt** elemre.
1. Válassza a **konzol alkalmazás (.NET-keretrendszer)** elemet, majd kattintson a **tovább** gombra.
1. A **Project Name (projekt neve**) mezőben adja meg a nevet `CreateADLApplication` , majd válassza a **Létrehozás** lehetőséget.
1. Adja hozzá a NuGet-csomagokat a projekthez.

   1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
   1. Győződjön meg arról, hogy a **NuGet csomagkezelő** lapon a **csomag forrása** **nuget.org** értékre van állítva. Győződjön meg arról is, hogy az **előzetes verzió belefoglalása** jelölőnégyzet be van jelölve.
   1. Keresse meg és telepítse az alábbi NuGet-csomagokat:

      * `Microsoft.Azure.DataLake.Store` – Ez a cikk a v 1.0.0-t használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez a cikk a v 2.3.1-et használja.

      Zárja be a **NuGet-csomagkezelőt**.

1. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Deklarálja a változókat az alább látható módon, majd adja meg a helyőrzők értékét. Arról is győződjön meg, hogy az itt megadott helyi elérési út és fájlnév létezik a számítógépen.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

A cikk hátralévő részeiben láthatja, hogyan használhatók az elérhető .NET-metódusok olyan műveletek elvégzésére, mint a hitelesítés, a fájlok feltöltése stb.

## <a name="authentication"></a>Hitelesítés

* Az alkalmazás végfelhasználói hitelesítéséhez lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md).
* Az alkalmazás szolgáltatások közötti hitelesítéséhez lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Ügyfélobjektum létrehozása

A következő kódrészlet létrehozza a Data Lake Storage Gen1 filesystem Client objektumot, amely a szolgáltatásra vonatkozó kérelmek kibocsátására szolgál.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Fájl és könyvtár létrehozása

Adja hozzá a következő kódrészletet az alkalmazásához. Ez a kódrészlet egy nem létező fájlt és bármely szülő könyvtárat felhelyez.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Hozzáfűzés fájlhoz

Az alábbi kódrészlet egy meglévő, Data Lake Storage Gen1-fiókban lévő fájlhoz fűzi az adathalmazt.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Fájl beolvasása

A következő kódrészlet beolvassa egy fájl tartalmát a Data Lake Storage Gen1ban.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Fájltulajdonságok lekérdezése

Az alábbi kódrészlet egy fájlhoz vagy könyvtárhoz társított tulajdonságokat adja vissza.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

A metódus definíciója `PrintDirectoryEntry` a [githubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)található minta részeként érhető el.

## <a name="rename-a-file"></a>Fájl átnevezése

A következő kódrészlet átnevez egy meglévő fájlt egy Data Lake Storage Gen1 fiókban.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Könyvtárak enumerálása

Az alábbi kódrészlet egy Data Lake Storage Gen1-fiókhoz tartozó címtárakat sorolja fel.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

A metódus definíciója `PrintDirectoryEntry` a [githubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)található minta részeként érhető el.

## <a name="delete-directories-recursively"></a>Könyvtárak rekurzív törlése

A következő kódrészlet rekurzív módon töröl egy könyvtárat és annak alkönyvtárait.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Példák

Íme néhány példa, amely bemutatja, hogyan használhatja a Data Lake Storage Gen1 filesystem SDK-t.

* [Alapszintű minta a GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Speciális minta a GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Lásd még

* [Fiókkezelés Data Lake Storage Gen1 a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK-dokumentáció](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Következő lépések

* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)