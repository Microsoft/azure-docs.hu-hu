---
title: Az Azure Industrial IoT platform üzembe helyezése
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe a IIoT platformot.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787745"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Oktatóanyag: az Azure Industrial IoT platform üzembe helyezése

Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Tudnivalók a IIoT platform főbb összetevőiről
> * Tudnivalók a különböző telepítési típusokról
> * Az ipari IoT platform üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

- Létre kell hozni egy Azure-előfizetést
- A [git](https://git-scm.com/downloads) letöltése
- A hitelesítéshez használt Azure Active Directory-(HRE-) alkalmazás-regisztrációhoz globális rendszergazda, alkalmazás-rendszergazda vagy Felhőbeli alkalmazás-rendszergazdai jogosultság szükséges a teljes bérlői rendszergazdai engedély biztosításához (további beállításokért lásd alább)
- A központi telepítéshez támogatott operációs rendszerek a következők: Windows, Linux és Mac
- IoT Edge támogatja a Windows 10 IoT Enterprise LTSC és Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>Fő összetevők

- Minimális függőségek: IoT Hub, Cosmos DB, Service Bus, Event hub, Key Vault, Storage
- Standard függőségek: minimum + jelző szolgáltatás, HRE-alkalmazás regisztrációja, eszközök kiépítési szolgáltatása, Time Series Insights, munkafüzet, Log Analytics, Application Insights
- Szolgáltatások: App Service csomag, App Service
- Felhasználói felület (webalkalmazás): App Service csomag (megosztva a-szolgáltatásokkal), App Service
- Szimuláció: virtuális gép, virtuális hálózat, IoT Edge
- Azure Kubernetes Service

## <a name="installation-types"></a>Telepítési típusok

- Minimum: minimális függőségek
- Helyi: minimum és a standard függőségek
- Szolgáltatások: helyi és a Service-szolgáltatások
- Szimuláció: minimális függőségek és a szimulációs összetevők
- Alkalmazás: szolgáltatások és felhasználói felület
- Összes (alapértelmezett): alkalmazás és a szimuláció

## <a name="deployment"></a>Üzembe helyezés

1. A IIoT platform üzembe helyezésének megkezdéséhez a tárházat a parancssorból vagy a terminálból kell klónozott.

    git Clone https://github.com/Azure/Industrial-IoT  cd Industrial-IoT

2. Indítsa el az irányított telepítést, a parancsfájl összegyűjti a szükséges információkat, például az Azure-fiókot, az előfizetést, a cél erőforrást és a csoport és az alkalmazás nevét.

Windows rendszeren:
    ```
    .\deploy
    ```

Linux vagy Mac rendszeren:
    ```
    ./deploy.sh
    ```

3. A szolgáltatások és a felhasználói felület olyan webalkalmazások, amelyek hitelesítést igényelnek, ez három alkalmazás regisztrációját igényli a HRE. Ha a szükséges jogosultságok hiányoznak, két lehetséges megoldás létezik:

- Kérje meg a HRE-rendszergazdát, hogy az alkalmazáshoz teljes körű rendszergazdai jóváhagyást adjon
- A HRE-rendszergazda létrehozhat egy HRE-alkalmazást. Az üzembe helyezés/parancsfájlok mappa tartalmazza a HRE-register.ps1 parancsfájlt, amely a HRE-regisztrációt a központi telepítéstől függetlenül hajtja végre. A parancsfájl kimenete egy olyan fájl, amely tartalmazza az üzembe helyezés részeként használandó releváns adatokat, és a deploy.ps1 parancsfájlnak a-aadConfig argumentum használatával kell átadnia a-szkriptnek.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

Az előkészítést, a visszaállítást, a skálázást és a rugalmasságot igénylő éles környezetekben a platform üzembe helyezhető az [Azure Kubernetes szolgáltatásban (ak)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)

Hivatkozik
- [Az Azure Industrial IoT platform üzembe helyezése](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [Az all-in-One üzembe helyezése](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [Platform üzembe helyezése az AK-ban](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Következő lépések
Most, hogy telepítette a IIoT platformot, megtudhatja, hogyan szabhatja testre az összetevők konfigurációját:

> [!div class="nextstepaction"]
> [Az összetevők konfigurációjának testreszabása](tutorial-configure-industrial-iot-components.md)
