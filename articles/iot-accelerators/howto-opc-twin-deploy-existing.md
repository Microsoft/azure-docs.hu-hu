---
title: OPC Twin-modul üzembe helyezése meglévő Azure-projekthez | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan helyezhető üzembe az OPC Twin egy meglévő projektben. Azt is megtudhatja, hogyan lehet elhárítani az üzembe helyezési hibákat.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0a4866db463dcf4ff17b2f7b25806806a700e8fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92071642"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Az OPC Twin üzembe helyezése egy meglévő projektben

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Az OPC Twin modul IoT Edge fut, és több peremhálózati szolgáltatást biztosít az OPC Twin és a Registry szolgáltatásokhoz.

Az OPC Twin-szolgáltatás megkönnyíti a gyári operátorok és az OPC UA-kiszolgáló eszközök közötti kommunikációt a gyári emeleten egy OPC Twin IoT Edge modul használatával. A webszolgáltatás az OPC UA-szolgáltatásokat (Tallózás, olvasás, írás és végrehajtás) teszi elérhetővé a REST APIján keresztül. 

Az OPC UA-eszköz beállításjegyzék-szolgáltatása hozzáférést biztosít a regisztrált OPC UA-alkalmazásokhoz és a hozzájuk tartozó végpontokhoz. A kezelők és a rendszergazdák regisztrálhatják és módosíthatják az új OPC UA-alkalmazásokat, és böngészhetik a meglévőket, beleértve a végpontokat is. Az alkalmazások és a végpontok felügyelete mellett a beállításjegyzék szolgáltatás a regisztrált OPC Twin IoT Edge modulokat is katalogizálja. A Service API lehetővé teszi az Edge-modul funkcióinak vezérlését, például a kiszolgáló felderítésének (keresési szolgáltatások) indítását vagy leállítását, illetve az új végponti ikrek aktiválását, amelyek az OPC Twin Service használatával érhetők el.

A modul magja a felügyelő identitása. A felügyelő felügyeli a végpontot, amely a megfelelő OPC UA-beállításjegyzék API-val aktivált OPC UA-kiszolgálói végpontoknak felel meg. Ez a végponti ikrek lefordítja az OPC UA JSON-t, amely a felhőben futó OPC Twin-szolgáltatásból érkezett OPC UA bináris üzenetekbe, amelyeket egy állapot-nyilvántartó biztonságos csatornán küldenek a felügyelt végpontnak. A felügyelő olyan felderítési szolgáltatásokat is biztosít, amelyek eszköz-felderítési eseményeket küldenek az OPC UA-eszköz előkészítési szolgáltatásának feldolgozásra, ahol ezek az események frissítéseket eredményeznek az OPC UA beállításjegyzékben.  Ez a cikk bemutatja, hogyan helyezheti üzembe az OPC Twin modult egy meglévő projektben.

> [!NOTE]
> Az üzembe helyezés részleteiről és az utasításokról a GitHub- [tárházban](https://github.com/Azure/azure-iiot-opc-twin-module)talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy telepítve van a PowerShell és a [AzureRM PowerShell](/powershell/azure/azurerm/install-azurerm-ps) -bővítmények. Ha még nem tette meg, akkor a GitHub-tárház klónozásával. Futtassa a következő parancsokat a PowerShellben:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Ipari IoT-szolgáltatások üzembe helyezése az Azure-ban

1. A PowerShell-munkamenetben futtassa a következőket:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Az utasításokat követve rendeljen hozzá egy nevet a központi telepítés erőforráscsoporthoz, valamint egy nevet a webhelyhez.   A szkript üzembe helyezi az Azure-előfizetésében található erőforráscsoportot és az Azure platform-függőségeit.  A parancsfájl egy alkalmazást is regisztrál a Azure Active Directory (HRE) bérlőn a OAUTH-alapú hitelesítés támogatásához.  Az üzembe helyezés több percet is igénybe vehet.  Egy példa arra, hogy mit láthat a megoldás sikeres üzembe helyezése után:

   ![Ipari IoT OPC kettős üzembe helyezés meglévő projekthez](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   A kimenet tartalmazza a nyilvános végpont URL-címét. 

3. Miután a szkript sikeresen befejeződik, válassza ki, hogy szeretné-e menteni a `.env` fájlt.  A környezeti fájlra akkor van szükség, `.env` Ha olyan eszközökkel szeretne csatlakozni a Felhőbeli végponthoz, mint például a konzol, vagy a fejlesztéshez és a hibakereséshez telepítse a modulokat.

## <a name="troubleshooting-deployment-failures"></a>Telepítési hibák elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Győződjön meg arról, hogy rövid és egyszerű erőforráscsoport-nevet használ.  A nevet az erőforrások elnevezésére is használni kell, mert meg kell felelnie az erőforrás-elnevezési követelményeknek.  

### <a name="website-name-already-in-use"></a>A webhely neve már használatban van

Lehetséges, hogy a webhely neve már használatban van.  Ha ezt a hibát futtatja, másik nevet kell használnia.

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (HRE) regisztráció

Az üzembe helyezési parancsfájl két HRE-alkalmazást próbál regisztrálni Azure Active Directoryban.  A kiválasztott HRE-bérlő jogaitól függően előfordulhat, hogy a telepítés sikertelen lesz. Két lehetőség érhető el:

1. Ha a bérlők listájából választ egy HRE-bérlőt, indítsa újra a szkriptet, és válasszon egy másikat a listából.
2. Másik megoldásként helyezzen üzembe egy privát HRE-bérlőt egy másik előfizetésben, indítsa újra a szkriptet, és válassza ki a használatát.

> [!WARNING]
> Soha ne folytassa hitelesítés nélkül.  Ha úgy dönt, hogy bárki hozzáférhet az OPC kettős végpontokhoz az internetről, nem hitelesített.   A gumiabroncsok elindításához mindig válassza a ["helyi" központi telepítési lehetőséget](howto-opc-twin-deploy-dependencies.md) .

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Egy teljes körű ipari IoT Services-bemutató üzembe helyezése

A szolgáltatások és a függőségek helyett egy teljes körű bemutatót is üzembe helyezhet.  Az all in One bemutató három OPC UA-kiszolgálót, az OPC Twin modult, az összes szolgáltatást és egy minta webalkalmazást tartalmaz.  A szolgáltatás szemléltetési célokra szolgál.

1. Győződjön meg arról, hogy rendelkezik az adattár klónozásával (lásd fent). Nyisson meg egy PowerShell-parancssort a tárház gyökerében, és futtassa a következőket:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Az utasításokat követve rendeljen hozzá egy új nevet az erőforráscsoporthoz és egy nevet a webhelyhez.  A sikeres üzembe helyezés után a parancsfájl megjeleníti a webalkalmazás-végpont URL-címét.

## <a name="deployment-script-options"></a>Üzembe helyezési parancsfájl beállításai

A parancsfájl a következő paramétereket veszi figyelembe:

```powershell
-type
```

Az üzembe helyezés típusa (virtuális gép, helyi, bemutató)

```powershell
-resourceGroupName
```

Egy meglévő vagy egy új erőforráscsoport neve lehet.

```powershell
-subscriptionId
```

Nem kötelező megadni az előfizetés AZONOSÍTÓját, ahol az erőforrások telepítve lesznek.

```powershell
-subscriptionName
```

Vagy az előfizetés neve.

```powershell
-resourceGroupLocation
```

Az erőforráscsoport helye nem kötelező. Ha meg van adva, akkor megpróbál létrehozni egy új erőforráscsoportot ezen a helyen.

```powershell
-aadApplicationName
```

Annak a HRE-alkalmazásnak a neve, amelybe regisztrálni kell.

```powershell
-tenantId
```

A HRE-bérlőt használja.

```powershell
-credentials
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan telepíthet OPC Twin-et egy meglévő projektbe, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az OPC UA-ügyfél és az OPC UA PLC biztonságos kommunikációja](howto-opc-vault-secure.md)