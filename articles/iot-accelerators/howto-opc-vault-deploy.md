---
title: Az OPC Vault tanúsítványkezelő szolgáltatás üzembe helyezése – Azure | Microsoft Docs
description: Az OPC-tároló tanúsítványkezelő szolgáltatásának üzembe helyezése az nulláról.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b20f6318c2e6be701446e29ab93598752e93d287
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870282"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Az OPC-tároló tanúsítványkezelő szolgáltatásának összeállítása és üzembe helyezése

> [!IMPORTANT]
> A cikk frissítése közben a legfrissebb tartalmakért tekintse meg az [Azure Industrial IoT-t.](https://azure.github.io/Industrial-IoT/)

Ez a cikk bemutatja, hogyan helyezheti üzembe az OPC-tároló tanúsítványkezelő szolgáltatását az Azure-ban.

> [!NOTE]
> További információkért lásd a GitHub [OPC-tároló adattárát.](https://github.com/Azure/azure-iiot-opc-vault-service)

## <a name="prerequisites"></a>Előfeltételek

### <a name="install-required-software"></a>A szükséges szoftverek telepítése

A build- és üzembe helyezési művelet jelenleg a Windowsra korlátozódik.
A minták mind a C# .NET Standardhoz vannak írva, amelyekre a szolgáltatás és az üzembe helyezéshez szükséges minták felépítéséhez van szükség.
A .NET Standardhoz szükséges összes eszköz a .NET Core-eszközökkel együtt rendelkezésre áll. Lásd: [A .NET Core első lépések.](/dotnet/articles/core/getting-started)

1. [Telepítse a .NET Core 2.1+ programot.][dotnet-install]
2. [Telepítse a Dockert][docker-url] (nem kötelező, csak akkor, ha a helyi Docker-build szükséges).
4. Telepítse a [PowerShellhez szükséges Azure parancssori eszközöket.][powershell-install]
5. Regisztráljon egy [Azure-előfizetésre.][azure-free]

### <a name="clone-the-repository"></a>A tárház klónozása

Ha még nem tette meg, klónozza ezt a GitHub-adattárat. Nyisson meg egy parancssort vagy terminált, és futtassa a következőt:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Másik lehetőségként közvetlenül a 2017-es Visual Studio is klónozhatja.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Az Azure-szolgáltatás összeállítása és üzembe helyezése Windows rendszeren

A PowerShell-szkriptek egyszerű lehetőséget kínálnak az OPC-tároló mikroszolgáltatás és az alkalmazás üzembe helyezésére.

1. Nyisson meg egy PowerShell-ablakot a repo gyökerében. 
3. Ugrás a deploy `cd deploy` mappára.
3. Válasszon olyan nevet, amely nem valószínű, hogy ütközést okoz `myResourceGroup` más üzembe helyezett weblapokkal. A cikk későbbi részében tekintse meg a "Webhely neve már használatban" című szakaszt.
5. Az interaktív telepítéshez indítsa el az üzembe helyezést a paranccsal, vagy `.\deploy.ps1` adjon meg egy teljes parancssort:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Ha ezzel az üzembe helyezéssel tervezi a fejlesztést, adja hozzá a et a Swagger felhasználói felület engedélyezéséhez és a `-development 1` hibakeresési buildek üzembe helyezéséhez.
6. A szkript utasításait követve jelentkezzen be az előfizetésbe, és adjon meg további információkat.
9. A sikeres összeállítási és üzembe helyezési művelet után a következő üzenet jelenik meg:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > Problémák esetén tekintse meg a cikk későbbi, "Üzembehelyezéssel kapcsolatos hibák elhárítása" című szakaszát.

8. Nyissa meg kedvenc böngészőjét, és nyissa meg az alkalmazás oldalát: `https://myResourceGroup.azurewebsites.net`
8. Adjon néhány percet a webalkalmazásnak és az OPC-tároló mikroszolgáltatásnak, hogy bemelegítsen az üzembe helyezés után. Előfordulhat, hogy a weblap nem válaszol az első használatra, legfeljebb egy percig, amíg meg nem kapják az első válaszokat.
11. A Swagger API megnyitásához nyissa meg a következőt: `https://myResourceGroup-service.azurewebsites.net`
13. Egy helyi GDS-kiszolgáló dotnettel való indításhoz indítsa el a(on) `.\myResourceGroup-gds.cmd` et. A Dockerben indítsa el `.\myResourceGroup-dockergds.cmd` a-t.

A buildek újratelepítése pontosan ugyanazokkal a beállításokkal lehetséges. Vegye figyelembe, hogy egy ilyen művelet megújítja az alkalmazás összes titkos jelszavát, és alaphelyzetbe állíthatja a Azure Active Directory (Azure AD) alkalmazásregisztrációk egyes beállításait.

Az is lehetséges, hogy csak a webalkalmazás bináris fájlokat használja újra. A paraméterrel a szolgáltatás és az alkalmazás új zip-csomagjai helyezhetők `-onlyBuild 1` üzembe a webalkalmazások számára.

A sikeres üzembe helyezés után elkezdheti használni a szolgáltatásokat. Lásd: [Az OPC-tároló tanúsítványkezelő szolgáltatásának kezelése.](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Szolgáltatások törlése az előfizetésből

Ezt a következőképpen teheti meg:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ugrás arra az erőforráscsoportra, amelyben a szolgáltatás üzembe lett stb.
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.
4. Rövid idő után az összes üzembe helyezett szolgáltatás-összetevő törlődik.
5. Ugrás a **Azure Active Directory**  >  **Alkalmazásregisztrációk.**
6. Minden üzembe helyezett erőforráscsoporthoz három regisztrációnak kell lennie. A regisztrációk neve a következő: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Minden regisztrációt külön töröljön.

Most már az összes telepített összetevő el lett távolítva.

## <a name="troubleshooting-deployment-failures"></a>Üzembehelyezéssel kapcsolatos hibák elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Használjon rövid és egyszerű erőforráscsoport-nevet. A név az erőforrások és a szolgáltatás URL-előtagja elnevezésére is használatos. Ezért meg kell felelnie az erőforrás-elnevezési követelményeknek.  

### <a name="website-name-already-in-use"></a>A webhely neve már használatban van

Lehetséges, hogy a webhely neve már használatban van. Másik erőforráscsoport-nevet kell használnia. Az üzembe helyezési szkript a következő állomásneveket használja: \/ https: /resourcegroupname.azurewebsites.net és https: \/ /resourgroupname-service.azurewebsites.net.
A szolgáltatások más nevei rövid név-hashekkel vannak összeépítve, és nem valószínű, hogy ütköznek más szolgáltatásokkal.

### <a name="azure-ad-registration"></a>Azure AD-regisztráció 

Az üzembe helyezési szkript három Azure AD-alkalmazást próbál regisztrálni az Azure AD-ban. A kiválasztott Azure AD-bérlőben megadott engedélyektől függően ez a művelet meghiúsulhat. Két lehetőség érhető el:

- Ha egy Azure AD-bérlőt választott a bérlők listájából, indítsa újra a szkriptet, és válasszon egy másikat a listából.
- Másik lehetőségként üzembe helyezhet egy privát Azure AD-bérlőt egy másik előfizetésben. Indítsa újra a szkriptet, és válassza ki a használatát.

## <a name="deployment-script-options"></a>Üzembe helyezési szkript beállításai

A szkript a következő paramétereket veszi fel:


```
-resourceGroupName
```

Ez lehet egy meglévő vagy egy új erőforráscsoport neve.

```
-subscriptionId
```


Ez az előfizetés azonosítója, ahol az erőforrások üzembe lesznek helyezni. Ez nem kötelező.

```
-subscriptionName
```


Másik lehetőségként használhatja az előfizetés nevét.

```
-resourceGroupLocation
```


Ez egy erőforráscsoport-hely. Ha meg van adva, ez a paraméter megpróbál létrehozni egy új erőforráscsoportot ezen a helyen. Ez a paraméter nem kötelező.


```
-tenantId
```


Ezt az Azure AD-bérlőt kell használnia. 

```
-development 0|1
```

Ezt a fejlesztéshez kell üzembe helyezni. Használja a hibakeresési buildet, és állítsa a ASP.NET környezetet Fejlesztésre. Hozzon létre 2017 Visual Studio importáláshoz, hogy közvetlenül telepítheti az alkalmazást és a `.publishsettings` szolgáltatást. Ez a paraméter nem kötelező.

```
-onlyBuild 0|1
```

Ennek az a módja, hogy újraépítse és csak a webalkalmazásokat, és újraépítse a Docker-tárolókat. Ez a paraméter nem kötelező.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://dotnet.microsoft.com/download

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan helyezheti üzembe az OPC-tárolót az nulláról, a következőt tudja:

> [!div class="nextstepaction"]
> [OPC-tároló kezelése](howto-opc-vault-manage.md)