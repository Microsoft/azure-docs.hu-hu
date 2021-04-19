---
title: Az Azure Security Center a PowerShell-sel
description: Ez a dokumentum végigvezeti a PowerShell-parancsmagok Azure Security Center való engedélyezésének folyamatán.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: f9ab258f59279112d0b90c5d460e6761ac911a2d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713353"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>A powershell-Azure Security Center automatizálása

Az Azure-beli számítási feladatokat programozott módon biztosíthatja az Azure Security Center PowerShell-modullal.
A PowerShell használatával automatizálhatja a feladatokat, és elkerülheti a manuális feladatokban rejlő emberi hibákat. Ez különösen hasznos nagy méretű üzemelő példányok esetén, amelyek több tucat, több száz és több ezer erőforrást is magában foglaló előfizetést foglalnak magukban – és mindegyiket a kezdetektől biztonságossá kellni.

Az Azure Security Center PowerShell használatával történő felvételével programozott módon automatizálhatja az Azure-erőforrások felvételét és kezelését, és hozzáadhatja a szükséges biztonsági vezérlőket.

Ez a cikk egy PowerShell-példaszkprogramot tartalmaz, amely módosítható és használható a környezetben a Security Center előfizetések közötti bevezetéshez. 

Ebben a példában engedélyezni fogjuk az Security Center-t egy d07c0080-170c-4c24-861d-9c817742786c azonosítójú előfizetésen, és alkalmazunk egy magas szintű védelmet lehetővé tevő ajánlott beállításokat a Azure Defender engedélyezésével, amely fejlett fenyegetésvédelmet és észlelési képességeket biztosít:

1. Engedélyezze a [Azure Defender.](azure-defender.md) 
 
2. Állítsa be azt a Log Analytics-munkaterületet, amelyre a Log Analytics-ügynök elküldi az előfizetéshez társított virtuális gépekről gyűjtött adatokat – ebben a példában egy meglévő, felhasználó által definiált munkaterületet (myWorkspace).

3. Aktiválja Security Center ügynök automatikus üzembe helyezését, amely telepíti a [Log Analytics-ügynököt.](security-center-enable-data-collection.md#auto-provision-mma)

5. Állítsa be a szervezet CISO-ját a riasztások és a Security Center események [biztonsági kapcsolattartójaként.](security-center-provide-security-contact-details.md)

6. Rendelje Security Center alapértelmezett [biztonsági szabályzatát.](tutorial-security-policy.md)

## <a name="prerequisites"></a>Előfeltételek

Ezeket a lépéseket a következő parancsmagok futtatása előtt kell Security Center végrehajtani:

1. Futtassa a PowerShellt rendszergazdaként.

1. Futtassa a következő parancsokat a PowerShellben:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Az Security Center a PowerShell használatával

1. Regisztrálja előfizetését a Security Center erőforrás-szolgáltatónál:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Nem kötelező: Beállíthatja az előfizetések lefedettségi szintjét (Azure Defender be- és kikapcsolt). Ha nincs meghatározva, a Defender ki van kapcsolva:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Konfigurálja a Log Analytics-munkaterületet, amelyre az ügynökök jelentést fognak jelenteni. Olyan Log Analytics-munkaterülettel kell lennie, amelyről az előfizetés virtuális gépei jelentést készítnek. Több előfizetést is meghatározhat, hogy egy munkaterületnek jelentsen. Ha nincs meghatározva, a rendszer az alapértelmezett munkaterületet használja.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. A Log Analytics-ügynök automatikus telepítése az Azure-beli virtuális gépeken:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Javasoljuk, hogy engedélyezze az automatikus kiépítést, hogy az Azure-beli virtuális gépeket automatikusan védjék a Azure Security Center.
    >

1. Választható: Erősen ajánlott megadni [](security-center-provide-security-contact-details.md) az előkészített előfizetések biztonsági kapcsolattartási adatait, amelyeket a rendszer a következő által létrehozott riasztások és értesítések címzettjeként Security Center:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert```

1. Rendelje hozzá az alapértelmezett Security Center szabályzat kezdeményezéshez:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

Sikeresen Azure Security Center PowerShell használatával.

Ezeket a PowerShell-parancsmagokat automatizálási szkriptekkel használva programozottan iterálhat az előfizetések és erőforrások között. Ezzel időt takaríthat meg, és csökkentheti az emberi hibák valószínűségét. Ezt a [példaszkprogramot használhatja](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) referenciaként.




## <a name="see-also"></a>Lásd még
Ha többet szeretne megtudni arról, hogyan automatizálhatja az Security Center a PowerShell használatával, tekintse meg a következő cikket:

* [Az.Security](/powershell/module/az.security)

További információt a Security Center a következő cikkben talál:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése](security-center-managing-and-responding-alerts.md) és válaszadás a Azure Security Center – Útmutató a biztonsági riasztások kezeléséhez és a riasztásra való válaszadáshoz.