---
title: Azure Security Center a PowerShell-lel
description: Ez a dokumentum végigvezeti a Azure Security Center PowerShell-parancsmagokkal való engedélyezésének lépésein.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 25639509cfb689e025ea4388f302378648af8ba6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439493"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Azure Security Center bevezetésének automatizálása a PowerShell használatával

Az Azure számítási feladatait programozott módon, a Azure Security Center PowerShell-modul használatával biztosíthatja.
A PowerShell használatával automatizálhatja a feladatokat, és elkerülheti a manuális feladatokban rejlő emberi hibákat. Ez különösen hasznos a nagy léptékű központi telepítések esetében, amelyek több tucat előfizetést foglalnak magukban több száz vagy több ezer erőforrással – mindezt az elejétől védeni kell.

A PowerShell használatával történő bevezetési Azure Security Center lehetővé teszi az Azure-erőforrások bevezetését és felügyeletét, valamint a szükséges biztonsági vezérlők hozzáadását.

Ez a cikk egy minta PowerShell-parancsfájlt tartalmaz, amely módosítható és használható a környezetben az előfizetések Security Centerének kiszámításához. 

Ebben a példában a következő AZONOSÍTÓval rendelkező előfizetés esetében engedélyezzük a Security Centert: d07c0080-170c-4c24-861d-9c817742786c és a magas szintű védelmet biztosító ajánlott beállítások alkalmazása az Azure Defender engedélyezésével, amely fejlett veszélyforrások elleni védelmet és észlelési képességeket biztosít:

1. Az [Azure Defender](azure-defender.md)engedélyezése. 
 
2. Állítsa be azt a Log Analytics munkaterületet, amelyhez a Log Analytics ügynök az előfizetéshez társított virtuális gépeken gyűjtött adatokat küldi el – ebben a példában egy meglévő felhasználó által definiált munkaterületet (Sajátmunkaterület).

3. Aktiválja Security Center automatikus ügynökének [üzembe helyezését, amely telepíti az log Analytics ügynököt](security-center-enable-data-collection.md#auto-provision-mma).

5. A szervezet CISO adja meg [Security Center riasztások és jelentős események biztonsági kapcsolattartójának](security-center-provide-security-contact-details.md).

6. Security Center [alapértelmezett biztonsági házirendjeinek](tutorial-security-policy.md)kiosztása.

## <a name="prerequisites"></a>Előfeltételek

Ezeket a lépéseket az Security Center-parancsmagok futtatása előtt kell végrehajtani:

1. Futtassa a PowerShellt rendszergazdaként.

1. Futtassa a következő parancsokat a PowerShellben:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Security Center a PowerShell használatával

1. Regisztrálja előfizetéseit a Security Center erőforrás-szolgáltatónál:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Nem kötelező: állítsa be az előfizetések lefedettségi szintjét (Azure Defender be/ki). Ha nincs meghatározva, a Defender ki van kapcsolva:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Konfiguráljon egy Log Analytics munkaterületet, amelyre az ügynökök jelentést küldenek. Rendelkeznie kell egy már létrehozott Log Analytics-munkaterülettel, amelyet az előfizetés virtuális gépei fognak jelenteni. Több előfizetést is megadhat ugyanahhoz a munkaterülethez. Ha nincs meghatározva, a rendszer az alapértelmezett munkaterületet fogja használni.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. A Log Analytics-ügynök automatikus üzembe helyezése az Azure-beli virtuális gépeken:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Javasoljuk, hogy az automatikus kiépítés engedélyezésével győződjön meg arról, hogy az Azure-beli virtuális gépeket a Azure Security Center automatikusan védi.
    >

1. Nem kötelező: erősen ajánlott megadnia a beépített előfizetések biztonsági kapcsolattartási adatait, amelyeket a rendszer a Security Center által generált riasztások és értesítések címzettjeiként használ:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert```

1. Az alapértelmezett Security Center házirend-kezdeményezés kiosztása:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

Sikeresen előkészítette Azure Security Center a PowerShell-lel.

Ezeket a PowerShell-parancsmagokat az Automation-parancsfájlok segítségével programozott módon is megismételheti az előfizetések és az erőforrások között. Ezzel időt takaríthat meg, és csökkentheti az emberi hiba valószínűségét. A [minta parancsfájl](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) hivatkozásként használható.




## <a name="see-also"></a>Lásd még
Ha többet szeretne megtudni arról, hogy a PowerShell Hogyan automatizálható a Security Center bevezetésének automatizálásához, tekintse meg a következő cikket:

* [Az. Security](/powershell/module/az.security)

Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő cikket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Center-ben](security-center-managing-and-responding-alerts.md) – útmutató a biztonsági riasztások kezeléséhez és megválaszolásához.