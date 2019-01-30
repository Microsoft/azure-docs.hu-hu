---
title: API-verzióprofilok használata a PowerShell-lel az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az API-verzióprofilok használata a PowerShell-lel az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: c6bee5c66661f59a6287d624bf5a55428ebbe56e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238117"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Az Azure Stack PowerShell API-verzióprofilok használata

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

API-verzióprofilok kezelése az Azure és az Azure Stack közötti terveztek. Egy API-verzióprofil egy adott API-verziók az AzureRM PowerShell-modulok. Minden felhőalapú platform a támogatott API-verzióprofilok készletével rendelkezik. Például Azure Stack is támogatja a felülírás Profilverzió például **2018-03-01-hibrid**, és az Azure támogatja a **legújabb** API-verzióprofil. Amikor telepít egy profilt, az AzureRM PowerShell-modulok, amelyek megfelelnek a megadott profil vannak telepítve.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Az API-verzióprofilok használatához szükséges PowerShell-modul telepítése

A **AzureRM.Bootstrapper** modul, amely a PowerShell-galériából érhető el az API-verzióprofilok működéséhez szükséges PowerShell-parancsmagokat kínál. A következő parancsmag segítségével telepítse a **AzureRM.Bootstrapper** modul:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>Profil és az Azure Stack verziójára

A következő táblázat felsorolja az API-profil szükséges verzió és a használt verziók az Azure Stack PowerShell rendszergazdai modul kézjegyet. Ez a cikk egy verzióval 1808 előtt használja, ha a helyes értéket frissítse a verzió-profil és a kézjegy.

| Verzió nem. | API-verzióprofil | PS felügyeleti modul kézjegyet |
| --- | --- | --- |
| 1811 vagy újabb | 2018-03-01-hybrid | 1.6.0 |
| 1808 vagy újabb | 2018-03-01-hybrid | 1.5.0 |
| 1804 vagy újabb | 2017-03-09-profile | 1.4.0 |
| Az 1804 előtti verziói | 2017-03-09-profile | 1.2.11 |

> [!NOTE]  
> Frissítse a 1.2.11 verzióját, lásd: a [áttelepítési útmutató](https://aka.ms/azpsh130migration).

## <a name="install-a-profile"></a>Telepítse a profilt

Használja a **Install-azurermprofile új** parancsmagot a **2018-03-01-hibrid** API-verzióprofil az Azure Stack által igényelt AzureRM-modulok telepítéséhez. Az Azure Stack operátori modulok nincsenek telepítve a profil API verziója. Akkor kell telepíteni külön meghatározott a 3. lépését a [Azure Stack PowerShell telepítése](../azure-stack-powershell-install.md) cikk.

```PowerShell
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>Telepítse, és a egy profil-modulok importálása

Használja a **használata-azurermprofile új** parancsmag telepítése és a egy API-verzióprofil társított modulok importálásához. Csak egy API-verzióprofil importálhatja a PowerShell-munkamenetben. Egy másik API-verzió profil importálása, egy új PowerShell-munkamenetet kell megnyitni. A **használata-azurermprofile új** parancsmag a következő feladatokat hajtja végre:

1. Ellenőrzi a PowerShell-modulok a megadott API-verzió profilhoz társított telepítve vannak-e az aktuális hatókörben.  
2. Letölti és telepíti a modulokat, ha azok nem települnek.
3. Importálja a modulokat az aktuális PowerShell-munkamenetbe.

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

Telepítse, és a kiválasztott AzureRM-modulok importálása egy API-verzióprofil, futtassa a **használata-azurermprofile új** parancsmagot a **modul** paraméter:

```PowerShell
# Installs and imports the compute, storage and network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>A telepített profilok beolvasása

Használja a **Get-azurermprofile új** parancsmag elérhető API-verzióprofilok listájának beolvasása:

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```

## <a name="update-profiles"></a>Profil frissítése

Használja a **Update-azurermprofile új** parancsmagot, hogy a modulok egy API-verzió profil frissítése a legújabb verzióra, amelyek a PSGallery érhetők el. Javasoljuk, hogy mindig futtatja a **Update-azurermprofile új** parancsmag egy új PowerShell-munkamenetben a modulok importálása során az ütközések elkerülése érdekében. A **Update-azurermprofile új** parancsmagot futtatja a következő feladatokat:

1. Ellenőrzi, hogy ha a megadott API verzió profilt az aktuális hatókör telepített modulok a legújabb verzióra.  
2. Kéri, hogy a telepítés, ha azok nem települnek.  
3. Telepíti, és importálja a frissített modulok az aktuális PowerShell-munkamenetbe.  

```PowerShell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` -->

Ez a parancsmag futtatása a következő feladatokat:  

1. Ellenőrzi, hogy ha a megadott API verzió profilt az aktuális hatókör telepített modulok a legújabb verzióra.  
2. Eltávolítja a modulok korábbi verziói, a jelenlegi API-verzióprofil, valamint a jelenlegi PowerShell-munkamenetben.  
3. Telepítse a legújabb verziót kéri.  
4. Telepíti, és importálja a frissített modulok az aktuális PowerShell-munkamenetbe.  

## <a name="uninstall-profiles"></a>Távolítsa el a profilok

Használja a **Uninstall-azurermprofile új** parancsmag segítségével távolítsa el a megadott API-verzió profilt.

```PowerShell
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
