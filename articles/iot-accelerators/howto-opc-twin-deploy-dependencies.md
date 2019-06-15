---
title: Az OPC-Twin felhőalapú függőségek az Azure-beli üzembe helyezése |} A Microsoft Docs
description: Hogyan helyezheti üzembe az OPC-Twin Azure függőségek.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae9f2b05bfc6ea6315022d04c8d267d916cf282e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61451599"
---
# <a name="deploying-dependencies-for-local-development"></a>Helyi telepítéshez tartozó függőségek telepítése

Ez a cikk bemutatja, hogyan helyezhet üzembe, csak az Azure Platformszolgáltatásokkal kell helyi fejlesztés és hibakeresés.   Egy erőforráscsoport üzembe helyezve, minden szükséges helyi fejlesztés és hibakeresés tartalmazó kell a végén.

## <a name="deploy-azure-platform-services"></a>Az Azure adatplatform-szolgáltatásaival üzembe helyezése

1. Ellenőrizze, hogy a PowerShell és [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) bővítmények telepítve.  Nyisson meg egy parancssort vagy a terminált, és futtassa:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Kövesse az utasításokat követve rendeljen egy nevet az erőforráscsoporthoz az üzembe helyezéshez.  A parancsfájl telepíti ezt az erőforráscsoportot az Azure-előfizetése, de nem a mikroszolgáltatásokhoz csak a függőségeket.  A parancsfájl-alkalmazás is regisztrálja az Azure Active Directoryban.  Erre azért van szükség, OAUTH-alapú hitelesítés támogatásához.  Üzembe helyezés több percet is igénybe vehet.

3. Miután a parancsfájl lefutott, kiválaszthatja a .env fájl mentéséhez.  .Env környezet fájl összes szolgáltatását és a fejlesztői gépen futtatni kívánt eszközök konfigurációs fájlját.  

## <a name="troubleshooting-deployment-failures"></a>Üzembe helyezés hibáinak elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Győződjön meg arról, egy rövid és egyszerű erőforráscsoport-nevet használja.  A név hálózatnév-erőforrások, például meg kell felelnie az elnevezési követelményeknek erőforrás is szolgál.  

### <a name="azure-active-directory-aad-registration"></a>Az Azure Active Directory (AAD) regisztráció

Az üzembe helyezési parancsfájl megpróbálja regisztrálni az AAD-alkalmazást az Azure Active Directoryban.  A kiválasztott AAD-bérlő jogosultságok, attól függően ez meghiúsulhat.   3 lehetőség áll rendelkezésre:

1. Ha egy AAD-bérlő a bérlők listáját, indítsa újra a parancsfájlt, és válasszon egy másikat a listából.
2. Azt is megteheti telepíthet egy saját AAD-bérlő, indítsa újra a parancsfájlt, és válassza ki a használatára.
3. Lépjen tovább, hitelesítés nélkül.  Helyileg futtatja a mikroszolgáltatásokhoz, mivel ez elfogadható, de nem utánzására éles környezetben.  

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen telepítette az OPC-Twin-szolgáltatások egy meglévő projektjébe, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Ismerje meg az OPC-Twin-modulok telepítése](howto-opc-twin-deploy-modules.md)
