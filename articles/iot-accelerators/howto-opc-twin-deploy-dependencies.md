---
title: Az OPC Twin Cloud-függőségek üzembe helyezése az Azure-ban | Microsoft Docs
description: Ez a cikk bemutatja, hogyan helyezheti üzembe a helyi fejlesztéshez és hibakereséshez szükséges OPC Twin Azure-függőségeket.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73824099"
---
# <a name="deploying-dependencies-for-local-development"></a>Függőségek üzembe helyezése helyi fejlesztéshez

Ez a cikk azt ismerteti, hogyan helyezhető üzembe csak a helyi fejlesztéshez és hibakereséshez szükséges Azure platform-szolgáltatások.   A végén egy olyan erőforráscsoport lesz telepítve, amely a helyi fejlesztéshez és a hibakereséshez szükséges mindent tartalmaz.

## <a name="deploy-azure-platform-services"></a>Az Azure platform szolgáltatásainak üzembe helyezése

1. Győződjön meg arról, hogy telepítve van a PowerShell és a [AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) -bővítmények.  Nyisson meg egy parancssort vagy terminált, és futtassa a parancsot:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Az utasításokat követve rendeljen hozzá egy nevet az erőforráscsoporthoz az üzemelő példányhoz.  A parancsfájl csak az Azure-előfizetésében lévő ezen erőforráscsoport függőségeit telepíti, a Micro Services azonban nem.  A parancsfájl Azure Active Directoryban is regisztrál egy alkalmazást.  Erre azért van szükség, hogy támogassa a OAUTH-alapú hitelesítést.  Az üzembe helyezés több percet is igénybe vehet.

3. A parancsfájl befejeződése után kiválaszthatja a. env fájl mentését.  A. env környezeti fájl a fejlesztői gépen futtatni kívánt összes szolgáltatás és eszköz konfigurációs fájlja.  

## <a name="troubleshooting-deployment-failures"></a>Telepítési hibák elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Győződjön meg arról, hogy rövid és egyszerű erőforráscsoport-nevet használ.  A nevet az erőforrások elnevezésére is használni kell, mert meg kell felelnie az erőforrás-elnevezési követelményeknek.  

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (HRE) regisztráció

Az üzembe helyezési parancsfájl megpróbálja regisztrálni a HRE-alkalmazásokat a Azure Active Directoryban.  A kiválasztott HRE-bérlő jogaitól függően ez sikertelen lehet.   Három beállítás érhető el:

1. Ha a bérlők listájából választ egy HRE-bérlőt, indítsa újra a szkriptet, és válasszon egy másikat a listából.
2. Másik megoldásként helyezzen üzembe egy privát HRE-bérlőt, indítsa újra a parancsfájlt, és válassza ki a használatát.
3. Folytatás hitelesítés nélkül.  Mivel a Micro Services helyileg fut, ez elfogadható, de nem utánozza az éles környezeteket.  

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen telepítette az OPC Twin-szolgáltatásokat egy meglévő projektbe, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Tudnivalók az OPC Twin-modulok üzembe helyezéséről](howto-opc-twin-deploy-modules.md)
