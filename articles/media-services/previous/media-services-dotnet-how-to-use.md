---
title: Számítógép beállítása Media Services-fejlesztéshez a .NET-tel
description: Ismerkedjen meg a .NET-hez készült Media Services SDK-val való Media Services előfeltételeivel. Azt is megtudhatja, hogyan hozhat létre egy Visual Studio-alkalmazást.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a2a9ecbcafa89b4981fe403d116ac0288e60b6b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013890"
---
# <a name="media-services-development-with-net"></a>Media Services fejlesztés .NET-tel

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Ez a cikk azt ismerteti, hogyan kezdheti el Media Services alkalmazások fejlesztését a .NET használatával.

A **Azure Media Services .net SDK** -kódtár lehetővé teszi a Media Services a .NET használatával való program használatát. Ahhoz, hogy még egyszerűbb legyen a .NET-fejlesztés, a **Azure Media Services .net SDK-bővítmények** könyvtára is elérhető. Ez a könyvtár a .NET-kód egyszerűsítését szolgáló kiterjesztési módszerek és segítő függvények készletét tartalmazza. Mindkét könyvtár elérhető a **NuGet** és a **githubon** keresztül.

## <a name="prerequisites"></a>Előfeltételek
* Egy Media Services-fiók egy új vagy meglévő Azure-előfizetésben. Tekintse [meg a Media Services-fiók létrehozását](media-services-portal-create-account.md)ismertető cikket.
* Operációs rendszerek: Windows 10, Windows 7, Windows 2008 R2 vagy Windows 8.
* A .NET-keretrendszer 4,5-es vagy újabb verziója.
* A Visual Studióval.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása
Ebből a szakaszból megtudhatja, hogyan hozhat létre egy projektet a Visual Studióban, és hogyan állíthatja be Media Services fejlesztéshez.  Ebben az esetben a projekt egy C# nyelvű Windows Console-alkalmazás, de az itt látható telepítési lépések a Media Services-alkalmazásokhoz (például egy Windows Forms alkalmazáshoz vagy egy ASP.NET-webalkalmazáshoz) létrehozott más típusú projektekre is érvényesek.

Ez a szakasz bemutatja, hogyan adhat hozzá Media Services .NET SDK-bővítményeket és más függő kódtárakat a **NuGet** használatával.

Azt is megteheti, hogy letölti a legújabb Media Services .NET SDK-biteket a GitHubról ([GitHub.com/Azure/Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services) vagy [GitHub.com/Azure/Azure-SDK-for-Media-Services-Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), felépíti a megoldást, és hozzáadja a hivatkozásokat az ügyfél-projekthez. Az összes szükséges függőség letöltése és kinyerése automatikusan megtörténik.

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást. Adja meg a **nevet**, a **helyet** és a **megoldás nevét**, majd kattintson az OK gombra.
2. Hozza létre a megoldást.
3. A **NuGet** használatával telepítse és vegye fel **Azure Media Services .net SDK-bővítményeket** (**windowsazure. Mediaservices. Extensions**). Ennek a csomagnak a telepítése a **Media Services .NET SDK** csomagot és az összes további szükséges függőséget is feltelepíti
   
    Győződjön meg arról, hogy a NuGet legújabb verziója van telepítve. További információt és telepítési útmutatót a következő témakörben talál: [NuGet](https://nuget.codeplex.com/).

    1. A Megoldáskezelőban kattintson a jobb gombbal a projekt nevére, és válassza a **NuGet-csomagok kezelése** lehetőséget.

    2. Megjelenik a Manage NuGet Packages (NuGet-csomagok kezelése) párbeszédpanel.

    3. Az online katalógusban keressen rá az Azure MediaServices Extensions kifejezésre, válassza a **Azure Media Services .net SDK-bővítmények** (**windowsazure. MediaServices. Extensions**) elemet, majd kattintson a **telepítés** gombra.
   
    4. A projekt módosult, és a Media Services .NET SDK-bővítményekre, Media Services .NET SDK-ra és más függő szerelvényekre hivatkozik.
4. A tisztább fejlesztési környezet elősegítése érdekében érdemes lehet engedélyezni a NuGet-csomagok visszaállítását. További információ: NuGet- [csomag visszaállítása "](https://docs.nuget.org/consume/package-restore).
5. Adjon hozzá egy hivatkozást **System.Configszülő** szerelvényhez. Ez a szerelvény a System.Configszülő tartalmazza. A konfigurációs fájlok eléréséhez használt **ConfigurationManager** osztály (például App.config).
   
    1. Hivatkozások hozzáadásához a hivatkozások kezelése párbeszédpanelen kattintson a jobb gombbal a projekt nevére a Megoldáskezelő. Ezután kattintson a **Hozzáadás**, majd a **hivatkozás...** elemre.
   
    2. Megjelenik a hivatkozások kezelése párbeszédpanel.
    3. A .NET-keretrendszer szerelvények területen keresse meg és válassza ki a System.Configszülő szerelvényt, majd kattintson az **OK gombra**.
6. Nyissa meg a App.config fájlt, és adjon hozzá egy **appSettings** szakaszt a fájlhoz. Adja meg a Media Services API-hoz való kapcsolódáshoz szükséges értékeket. További információ: [hozzáférés a Azure Media Services API-hoz az Azure ad-hitelesítéssel](media-services-use-aad-auth-to-access-ams-api.md). 

    Állítsa be a kapcsolódáshoz szükséges értékeket az **egyszerű szolgáltatásnév** hitelesítési módszerének használatával.

    ```xml
    <configuration>
    ...
        <appSettings>
            <add key="AMSAADTenantDomain" value="tenant"/>
            <add key="AMSRESTAPIEndpoint" value="endpoint"/>
            <add key="AMSClientId" value="id"/>
            <add key="AMSClientSecret" value="secret"/>
        </appSettings>
    </configuration>
    ```

7. Adja hozzá az **System.Configszülő** -hivatkozást a projekthez.
8. Írja felül a meglévő **using** utasításokat a program. cs fájl elején a következő kóddal:

    ```csharp      
    using System;
    using System.Configuration;
    using System.IO;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Collections.Generic;
    using System.Linq;
    ```

    Ezen a ponton készen áll egy Media Services alkalmazás fejlesztésének megkezdésére.    

## <a name="example"></a>Példa

Íme egy kis példa, amely az AMS API-hoz csatlakozik, és felsorolja az összes elérhető adathordozó-processzort.

```csharp
class Program
{
    // Read values from the App.config file.

    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];
        
    private static CloudMediaContext _context = null;
    static void Main(string[] args)
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
        // List all available Media Processors
        foreach (var mp in _context.MediaProcessors)
        {
            Console.WriteLine(mp.Name);
        }
        
    }
 ```

## <a name="next-steps"></a>Következő lépések

Most már [csatlakozhat az AMS API-hoz](media-services-use-aad-auth-to-access-ams-api.md) , és megkezdheti a [fejlesztést](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
