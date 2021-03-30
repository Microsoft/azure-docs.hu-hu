---
title: A.Net Core-alkalmazás létrehozása és közzététele egy távoli Linux-fürtön
description: Távoli Linux-fürtöt célzó .net Core-alkalmazások létrehozása és közzététele a Visual studióból
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614349"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>A Visual Studio használatával létrehozhat és közzétehet egy távoli linuxos Service Fabric-fürtöt célzó .net Core-alkalmazásokat
A Visual Studio-eszközökkel olyan Service Fabric .net Core-alkalmazásokat fejleszthet és tehet közzé, amelyek egy linuxos Service Fabric-fürtöt céloznak meg. Az SDK-verziónak 3,4-es vagy újabb verziójúnak kell lennie ahhoz, hogy üzembe helyezzen egy .net Core-alkalmazást a Visual studióból származó linuxos Service Fabric

> [!Note]
> A Visual Studio nem támogatja a Linux rendszerű alkalmazások hibakeresését Service Fabric.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Service Fabric-alkalmazás létrehozása .net Core-hoz
1. Indítsa el a Visual studiót **rendszergazdaként**.
2. Hozzon létre egy projektet **>New->Project fájllal**.
3. Az **új projekt** párbeszédpanelen válassza a **Cloud-> Service Fabric alkalmazás** lehetőséget.
![alkalmazás létrehozása]
4. Nevezze el az alkalmazást, és kattintson **az OK** gombra.
5. Az **új Service Fabric szolgáltatás** lapon válassza ki a **.net Core szakaszban** létrehozni kívánt szolgáltatás típusát.
![szolgáltatás létrehozása]

## <a name="deploy-to-a-remote-linux-cluster"></a>Üzembe helyezés távoli Linux-fürtön
1. A megoldás Explorerben kattintson a jobb gombbal az alkalmazásra, majd válassza a **Létrehozás** lehetőséget.
![Build – alkalmazás]
2. Miután befejezte az alkalmazás létrehozási folyamatát, kattintson a jobb gombbal a szolgáltatásra, és válassza a **csproj fájl** szerkesztése lehetőséget.
![Szerkesztés – csproj]
3. Szerkessze a UpdateServiceFabricManifestEnabled tulajdonságot True értékről **hamis** értékre, ha a szolgáltatás egy **szereplő típusú projekt**. Ha az alkalmazás nem rendelkezik színészi szolgáltatással, ugorjon a 4. lépésre.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> A UpdateServiceFabricManifestEnabled hamis értékre állításával letilthatja a ServiceManifest.xml frissítését a Build során. A rendszer nem jeleníti meg azokat a módosításokat, mint például a szolgáltatás hozzáadása, eltávolítása vagy átnevezése a ServiceManifest.xml. Ha bármilyen módosítást hajt végre, manuálisan kell frissítenie a ServiceManifest, vagy átmenetileg be kell állítania a UpdateServiceFabricManifestEnabled, és létre kell hoznia azt a szolgáltatást, amely frissíti a ServiceManifest.xml, majd visszaállítja a hamis értéket.
>

4. Frissítse a RuntimeIndetifier a Win7-x64-ből a cél platformra a szolgáltatási projektben.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. A ServiceManifest frissítse a BelépésiPont programot a. exe fájl eltávolításához. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. A Megoldáskezelőban kattintson a jobb gombbal az alkalmazásra, és válassza a **Közzététel** lehetőséget. Megjelenik a **Publish** (Közzététel) párbeszédpanel.
7. A **kapcsolódási végpont** területen válassza ki a célként használni kívánt távoli Service Fabric Linux-fürt végpontját.
![közzététel – alkalmazás]

<!--Image references-->
[alkalmazás létrehozása]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[szolgáltatás létrehozása]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[Build – alkalmazás]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[Szerkesztés – csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[közzététel – alkalmazás]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Következő lépések
* Ismerkedjen meg a [Service Fabric és a .net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/) használatába
