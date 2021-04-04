---
title: Node.js Express-alkalmazás létrehozása és üzembe helyezése az Azure Cloud Services (klasszikus)
description: Ezzel az Oktatóanyaggal létrehozhat egy új alkalmazást az expressz modul használatával, amely MVC keretrendszert biztosít Node.js webalkalmazások létrehozásához.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: e15af589b3a3c496738c97c0c2c6429ba708ba7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743338"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services-classic"></a>Node.js Webalkalmazás létrehozása és üzembe helyezése az Express használatával egy Azure Cloud Services (klasszikus)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A Node.js az alapszintű futtatókörnyezetben minimálisan szükséges funkciókat tartalmaz.
A fejlesztők gyakran használják a harmadik féltől származó modulokat a Node.js alkalmazások fejlesztéséhez szükséges további funkciók biztosításához. Ebben az oktatóanyagban egy új alkalmazást fog létrehozni az [expressz](https://github.com/expressjs/express) modul használatával, amely MVC keretrendszert biztosít Node.js webalkalmazások létrehozásához.

A befejezett alkalmazás képernyőképe a következő:

![Egy webböngésző, amely az Azure Expresst jeleníti meg](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Cloud Service-projekt létrehozása
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

A következő lépések végrehajtásával hozzon létre egy új, "expressapp" nevű Cloud Service-projektet:

1. A **Start menüből** vagy a **kezdőképernyőn** keressen rá a **Windows PowerShell** kifejezésre. Végül kattintson a jobb gombbal a **Windows PowerShell** elemre, és válassza **a Futtatás rendszergazdaként** lehetőséget.

    ![Azure PowerShell ikon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Módosítsa a könyvtárakat a **c: \\ Node** könyvtárba, majd írja be a következő parancsokat egy **expressapp** nevű új megoldás létrehozásához, valamint egy **webrole1 webes** nevű webes szerepkört:

   ```powershell
   PS C:\node> New-AzureServiceProject expressapp
   PS C:\Node\expressapp> Add-AzureNodeWebRole
   PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   ```

   > [!NOTE]
   > Alapértelmezés szerint a **Add-AzureNodeWebRole** a Node.js egy régebbi verzióját használja. A fenti **set-AzureServiceProjectRole** utasítás arra utasítja az Azure-t, hogy használja a csomópont v 0.10.21.  Megjegyzés: a paraméterek megkülönböztetik a kis-és nagybetűket.  A Node.js helyes verziójának kiválasztásához ellenőrizze a **motorok** tulajdonságot a **WebRole1\package.json**.
>
>

## <a name="install-express"></a>Az Express telepítése
1. Telepítse az Express generátort a következő parancs kiadásával:

    ```powershell
    PS C:\node\expressapp> npm install express-generator -g
    ```

    A NPM parancs kimenetének az alábbi eredményekhez hasonlóan kell kinéznie.

    ![A Windows PowerShell a NPM install Express parancs kimenetét jeleníti meg.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Módosítsa a címtárakat a **webrole1 webes** könyvtárba, és az Express parancs használatával állítson elő új alkalmazást:

    ```powershell
    PS C:\node\expressapp\WebRole1> express
    ```

    A rendszer kérni fogja a korábbi alkalmazás felülírását. A folytatáshoz adja meg az **y** vagy az **Igen értéket** . Az Express létrehozza a app.js fájlt és az alkalmazás létrehozásához szükséges mappastruktúrát.

    ![Az expressz parancs kimenete](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. A package.jsfájlban definiált további függőségek telepítéséhez írja be a következő parancsot:

    ```powershell
    PS C:\node\expressapp\WebRole1> npm install
    ```

   ![A NPM telepítési parancsának kimenete](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. A következő parancs használatával másolja a **bin/www** -fájlt **server.jsba**. Így a Cloud Service megtalálja az alkalmazás belépési pontját.

    ```powershell
    PS C:\node\expressapp\WebRole1> copy bin/www server.js
    ```

   A parancs végrehajtása után **server.js** -fájllal kell rendelkeznie a webrole1 webes könyvtárban.
5. Módosítsa az **server.js** a "." karakterek egyikének a következő sorból való eltávolításához.

    ```js
    var app = require('../app');
    ```

   A módosítást követően a sornak a következőképpen kell megjelennie.

    ```js
    var app = require('./app');
    ```

   Erre a módosításra azért van szükség, mert a (korábban **bin/www**) fájlt áthelyezte ugyanarra a könyvtárba, amelyben az alkalmazás fájlja szükséges. A módosítás elvégzése után mentse a **server.js** fájlt.
6. Használja az alábbi parancsot az alkalmazás futtatásához az Azure emulatorban:

    ```powershell
    PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    ```

    ![Az Expresszre mutató üdvözlést tartalmazó weblap.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>A nézet módosítása
Most módosítsa a nézetet, hogy megjelenjen az "Üdvözöljük az expressz az Azure-ban" üzenet.

1. Az index. Jade fájl megnyitásához írja be a következő parancsot:

    ```powershell
    PS C:\node\expressapp\WebRole1> notepad views/index.jade
    ```

   ![Az index. Jade fájl tartalma](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

   A Jade az Express-alkalmazások által használt alapértelmezett nézet motorja. A Jade View motorról további információt a következő témakörben talál: [http://jade-lang.com][http://jade-lang.com] .
2. Módosítsa a szöveg utolsó sorát az **Azure-ban** való hozzáfűzéssel.

   ![Az index. Jade fájl, az utolsó sor olvasás: p Üdvözöljük a \# (z) {title} az Azure-ban](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Mentse a fájlt, és zárja be a jegyzettömböt.
4. Frissítse a böngészőt, és látni fogja a módosításokat.

   ![A böngészőablakban az oldal üdvözli az Azure Expresst](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Az alkalmazás tesztelése után a **stop-AzureEmulator** parancsmag használatával állítsa le az emulátort.

## <a name="publishing-the-application-to-azure"></a>Az alkalmazás közzététele az Azure-ban
Az Azure PowerShell ablakban a **publish-AzureServiceProject** parancsmag használatával helyezheti üzembe az alkalmazást egy felhőalapú szolgáltatásban

```powershell
PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch
```

Miután a telepítési művelet befejeződik, megnyílik a böngésző, és megjeleníti a weblapot.

![Egy webböngésző, amely az expressz lapot jeleníti meg. Az URL-cím azt jelzi, hogy az már az Azure-ban üzemel.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Következő lépések
További információk: [Node.js fejlesztői központ](/azure/developer/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com