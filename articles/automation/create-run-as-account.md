---
title: Azure Automation futtató fiók létrehozása
description: Ez a cikk azt ismerteti, hogyan hozható létre egy futtató fiók a PowerShell-lel vagy a Azure Portal.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: ef6afff30da48b79b42e5fb4b3c72c3500f22dd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172303"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Azure Automation futtató fiók létrehozása

A Azure Automation futtató fiókok hitelesítést biztosítanak a Azure Resource Manager vagy a klasszikus Azure üzemi modell erőforrásainak automatizálási runbookok és egyéb Automation-funkciók használatával történő kezeléséhez. Ez a cikk azt ismerteti, hogyan hozhat létre futtató vagy klasszikus futtató fiókot a Azure Portal vagy a Azure PowerShell.

## <a name="create-account-in-azure-portal"></a>Fiók létrehozása Azure Portalban

A következő lépésekkel frissítheti Azure Automation-fiókját a Azure Portalban. A futtató és a klasszikus futtató fiókok külön jönnek létre. Ha nem kell klasszikus erőforrásokat felügyelnie, egyszerűen létrehozhatja csak a futtató fiókot.

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.

2. Keresse meg és válassza ki az **Automation-fiókokat**.

3. Az **Automation-fiókok** lapon válassza ki az Automation-fiókját a listából.

4. A bal oldali ablaktáblán válassza a **futtató fiókok** lehetőséget a **Fiókbeállítások** szakaszban.

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Válassza a futtató fiók lehetőséget.":::

5. Attól függően, hogy milyen fiókra van szüksége, használja a **+ Azure-beli futtató fiók** vagy a **klasszikus Azure** -beli futtató fiók panelt. Az áttekintő információk áttekintése után kattintson a **Létrehozás** gombra.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Válassza a futtató fiók létrehozása lehetőséget.":::

6. Amíg az Azure létrehozza a futtató fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát. Megjelenik egy szalagcím is, amely azt jelzi, hogy a fiók létrehozása folyamatban van. A folyamat eltarthat néhány percig.

## <a name="create-account-using-powershell"></a>Fiók létrehozása a PowerShell használatával

A következő lista a futtató fióknak a PowerShellben egy megadott parancsfájllal történő létrehozásához szükséges követelményeket ismerteti. Ezek a követelmények mindkét típusú futtató fiókra érvényesek.

* Windows 10 vagy Windows Server 2016, Azure Resource Manager modul 3.4.1-es és újabb verzióival. A PowerShell-parancsfájl nem támogatja a Windows korábbi verzióit.
* Azure PowerShell PowerShell-6.2.4 vagy újabb. További információ: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps).
* Egy Automation-fiók, amelyre a és a paraméterek értékeként hivatkozunk `AutomationAccountName` `ApplicationDisplayName` .
* A [futtató fiókok konfigurálásához szükséges engedélyekkel](automation-security-overview.md#permissions)egyenértékű engedélyek.

A következő lépések végrehajtásával kérheti le a, a és a értékeit a `AutomationAccountName` `SubscriptionId` PowerShell- `ResourceGroupName` parancsfájlhoz szükséges paraméterekkel.

1. Jelentkezzen be az Azure Portalra.

1. Keresse meg és válassza ki az **Automation-fiókokat**.

1. Az Automation-fiókok lapon válassza ki az Automation-fiókját a listából.

1. A bal oldali ablaktáblán válassza a **Tulajdonságok** lehetőséget.

1. Jegyezze fel a **Name**, az **előfizetés-azonosító** és az **erőforráscsoport** értékét a **Tulajdonságok** lapon.

   ![Automation-fiók tulajdonságai lap](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell-parancsfájl futtató fiók létrehozásához

A PowerShell-parancsfájl több konfigurációt is támogat.

* Futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Futtató fiók és/vagy klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Hozzon létre egy futtató fiókot és/vagy egy klasszikus futtató fiókot a vállalati hitelesítésszolgáltató (CA) által kiadott tanúsítvány használatával.
* Hozzon létre egy futtató fiókot és/vagy klasszikus futtató fiókot egy önaláírt tanúsítvány használatával a Azure Government-felhőben.

1. Töltse le és mentse a parancsfájlt egy helyi mappába a következő parancs használatával.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Indítsa el a PowerShellt emelt szintű felhasználói jogosultságokkal, és navigáljon a parancsfájlt tartalmazó mappához.

3. Futtassa a következő parancsok egyikét egy futtató és/vagy klasszikus futtató fiók létrehozásához a követelmények alapján.

    * Futtató fiók létrehozása önaláírt tanúsítvány használatával.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Futtató fiók és klasszikus futtató fiók létrehozása vállalati tanúsítvány használatával.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Ha a klasszikus futtató fiókot vállalati nyilvános tanúsítvánnyal (. cer fájllal) hozta létre, használja ezt a tanúsítványt. A parancsfájl létrehozza és menti a számítógép ideiglenes fájlok mappájába a `%USERPROFILE%\AppData\Local\Temp` PowerShell-munkamenet végrehajtásához használt felhasználói profil alatt. Lásd: [felügyeleti API-tanúsítvány feltöltése a Azure Portal](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával a Azure Government-felhőben

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. A parancsfájl végrehajtása után a rendszer kéri, hogy végezzen hitelesítést az Azure-ban. Jelentkezzen be egy olyan fiókkal, amely tagja az előfizetés-rendszergazdák szerepkörnek. Ha klasszikus futtató fiókot hoz létre, akkor a fióknak az előfizetés közös rendszergazdájának kell lennie.

## <a name="next-steps"></a>Következő lépések

* További információ a grafikus létrehozásról: [grafikus runbookok készítése Azure Automationban](automation-graphical-authoring-intro.md).
* A PowerShell-runbookok megkezdéséhez tekintse meg az [oktatóanyag: PowerShell-Runbook létrehozása](learn/automation-tutorial-runbook-textual-powershell.md)című témakört.
* A Python 3 runbook megkezdéséhez tekintse meg az [oktatóanyag: Python 3 Runbook létrehozása](learn/automation-tutorial-runbook-textual-python-3.md)című témakört.
