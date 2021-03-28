---
title: Az OPC Vault Certificate Management szolgáltatás üzembe helyezése – Azure | Microsoft Docs
description: Az OPC-tár tanúsítványkezelő szolgáltatásának üzembe helyezése a semmiből.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 98dc9345d2c8b392fd094458b612857d6d454739
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646166"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Az OPC Vault Certificate Management szolgáltatás létrehozása és üzembe helyezése

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Ez a cikk bemutatja, hogyan helyezheti üzembe az OPC Vault tanúsítványkezelő szolgáltatást az Azure-ban.

> [!NOTE]
> További információ: GitHub [OPC Vault adattár](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Előfeltételek

### <a name="install-required-software"></a>A szükséges szoftverek telepítése

Jelenleg a létrehozási és üzembe helyezési művelet a Windowsra korlátozódik.
A minták mind a C# .NET szabványhoz készültek, amelyhez létre kell hoznia a szolgáltatást, és mintákat kell vennie az üzembe helyezéshez.
A .NET szabványhoz szükséges összes eszköz a .NET Core-eszközökkel együtt érhető el. Lásd: [a .net Core első lépései](/dotnet/articles/core/getting-started).

1. [Telepítse a .net Core 2.1 +][dotnet-install]-ot.
2. A [Docker telepítése][docker-url] (nem kötelező, csak akkor, ha a helyi Docker-Build szükséges).
4. Telepítse a [powershellhez készült Azure parancssori eszközöket][powershell-install].
5. Regisztráljon egy Azure- [előfizetésre][azure-free].

### <a name="clone-the-repository"></a>A tárház klónozása

Ha még nem tette meg, akkor a GitHub-tárház klónozásával. Nyisson meg egy parancssort vagy terminált, és futtassa a következőt:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Azt is megteheti, hogy közvetlenül a Visual Studio 2017-ben klónozást végez a tárházban.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Az Azure-szolgáltatás létrehozása és üzembe helyezése Windows rendszeren

A PowerShell-parancsfájlok egyszerű módszert biztosítanak az OPC-tároló-szolgáltatás és az alkalmazás üzembe helyezésére.

1. Nyisson meg egy PowerShell-ablakot a tárház gyökerében. 
3. Nyissa meg a központi telepítés mappát `cd deploy` .
3. Válasszon egy nevet, `myResourceGroup` amely nem valószínű, hogy ütközik más központilag telepített weblapokkal. A cikk későbbi részében tekintse meg a "webhely neve már használatban" című szakaszt.
5. Indítsa el a központi telepítést `.\deploy.ps1` interaktív telepítéssel, vagy adjon meg egy teljes parancssort:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Ha ezt az üzembe helyezést tervezi, a Hozzáadás `-development 1` gombra kattintva engedélyezheti a hencegő felületet, és üzembe helyezheti a hibakeresési buildeket.
6. A parancsfájl utasításait követve jelentkezzen be az előfizetésbe, és adjon meg további információkat.
9. Sikeres felépítési és üzembe helyezési művelet után a következő üzenetnek kell megjelennie:
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
   > Problémák esetén tekintse meg a cikk későbbi, "központi telepítési hibák elhárítása" című szakaszát.

8. Nyissa meg a kedvenc böngészőjét, és nyissa meg az alkalmazás lapot: `https://myResourceGroup.azurewebsites.net`
8. Az üzembe helyezést követően néhány perc alatt bemelegítheti a webalkalmazást és az OPC-tárolót. Előfordulhat, hogy a Webkezdőlap nem válaszol az első használatnál, akár egy percig, amíg meg nem kapja az első válaszokat.
11. A hencegő API megkereséséhez nyissa meg a következőt: `https://myResourceGroup-service.azurewebsites.net`
13. Ha egy helyi GDS-kiszolgálót a DotNet használatával szeretne elindítani, indítsa el a következőt: `.\myResourceGroup-gds.cmd` . A Docker-ben indítsa el a t `.\myResourceGroup-dockergds.cmd` .

A buildek újbóli üzembe helyezése pontosan ugyanazokkal a beállításokkal lehetséges. Vegye figyelembe, hogy egy ilyen művelet megújítja az összes alkalmazás titkos kulcsát, és egyes beállítások alaphelyzetbe állíthatók a Azure Active Directory (Azure AD) alkalmazás regisztrációjában.

Az is lehetséges, hogy csak a webalkalmazás bináris fájljait telepíti újra. A paraméterrel a `-onlyBuild 1` szolgáltatás új zip-csomagjait és az alkalmazást telepíti a webalkalmazások számára.

A sikeres telepítés után elkezdheti a szolgáltatások használatát. Lásd: [az OPC-tár tanúsítványkezelő szolgáltatásának kezelése](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Szolgáltatások törlése az előfizetésből

Ezt a következőképpen teheti meg:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg azt az erőforráscsoportot, amelyben a szolgáltatást telepítették.
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.
4. Rövid idő elteltével a rendszer az összes üzembe helyezett szolgáltatás-összetevőt törli.
5. Nyissa meg **Azure Active Directory**  >  **Alkalmazásregisztrációk**.
6. Minden telepített erőforráscsoport esetében három regisztrációnak kell szerepelnie. A regisztrációk a következő nevekkel rendelkeznek: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Törölje az egyes regisztrációkat külön.

Mostantól az összes telepített összetevő el lesz távolítva.

## <a name="troubleshooting-deployment-failures"></a>Telepítési hibák elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Használjon rövid és egyszerű erőforráscsoport-nevet. A név az erőforrások és a szolgáltatás URL-címének előtagját is használja. Ennek megfelelően meg kell felelnie az erőforrás-elnevezési követelményeknek.  

### <a name="website-name-already-in-use"></a>A webhely neve már használatban van

Lehetséges, hogy a webhely neve már használatban van. Más erőforráscsoport-nevet kell használnia. Az üzembe helyezési parancsfájl által használt állomásnevek a következők: https: \/ /resourcegroupname.azurewebsites.net és https: \/ /resourgroupname-Service.azurewebsites.net.
A szolgáltatások egyéb neveit a rövid névvel ellátott kivonatok kombinációjával építjük össze, és nem valószínű, hogy ütköznek más szolgáltatásokkal.

### <a name="azure-ad-registration"></a>Azure AD-regisztráció 

Az üzembe helyezési parancsfájl három Azure AD-alkalmazás regisztrálását kísérli meg az Azure AD-ben. A kiválasztott Azure AD-bérlő engedélyeitől függően ez a művelet sikertelen lehet. Két lehetőség érhető el:

- Ha az Azure AD-bérlőt választotta a bérlők listájáról, indítsa újra a szkriptet, és válasszon egy másikat a listából.
- Alternatív megoldásként helyezzen üzembe egy privát Azure AD-bérlőt egy másik előfizetésben. Indítsa újra a szkriptet, és válassza ki a használatát.

## <a name="deployment-script-options"></a>Üzembe helyezési parancsfájl beállításai

A parancsfájl a következő paramétereket veszi figyelembe:


```
-resourceGroupName
```

Ez lehet egy meglévő vagy egy új erőforráscsoport neve.

```
-subscriptionId
```


Ez az előfizetés-azonosító, amelyben az erőforrások telepítve lesznek. Ez nem kötelező.

```
-subscriptionName
```


Másik lehetőségként használhatja az előfizetés nevét is.

```
-resourceGroupLocation
```


Ez egy erőforráscsoport-hely. Ha meg van adva, ez a paraméter megpróbál létrehozni egy új erőforráscsoportot ezen a helyen. Ez a paraméter szintén nem kötelező.


```
-tenantId
```


Ezt az Azure AD-bérlőt kell használnia. 

```
-development 0|1
```

Ez a fejlesztéshez való üzembe helyezés. Használja a hibakeresési buildet, és állítsa be a ASP.NET környezetet a fejlesztéshez. Hozza létre az `.publishsettings` importáláshoz a Visual Studio 2017-ben, hogy lehetővé tegye az alkalmazás és a szolgáltatás közvetlen üzembe helyezését. Ez a paraméter szintén nem kötelező.

```
-onlyBuild 0|1
```

Ez az Újraépítés és a csak a webalkalmazások újbóli üzembe helyezése, valamint a Docker-tárolók újraépítése. Ez a paraméter szintén nem kötelező.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan helyezheti üzembe az OPC-tárolót a semmiből, a következőket teheti:

> [!div class="nextstepaction"]
> [OPC-tároló kezelése](howto-opc-vault-manage.md)