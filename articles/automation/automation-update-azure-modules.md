---
title: A Azure PowerShell moduljainak Azure Automation
description: Ez a cikk azt mutatja be, hogyan frissítheti Azure PowerShell által alapértelmezés szerint a modulban megadott gyakori Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c1632da35864fc6822b385adac06d7f124aea061
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830411"
---
# <a name="update-azure-powershell-modules"></a>Azure PowerShell-modulok frissítése

A leggyakoribb PowerShell-modulok alapértelmezés szerint minden Automation-fiókban rendelkezésre állnak. Lásd: [Alapértelmezett modulok.](shared-resources/modules.md#default-modules) Ahogy az Azure csapata rendszeresen frissíti az Azure-modulokat, a benne foglalt parancsmagokkal is előfordulhatnak változások. Ezek a módosítások, például egy paraméternanatása vagy egy parancsmag teljes kiajánlása, negatív hatással lehetnek a runbookra. 

> [!NOTE]
> Nem törölhet globális modulokat, amelyek az Automation által már meglévő modulok.

## <a name="set-up-an-automation-account"></a>Automation-fiók beállítása

Annak érdekében, hogy ne legyen hatással a runbookokra és az automatizálható folyamatokra, mindenképpen tesztelje és ellenőrizze a frissítéseket. Ha nincs erre a célra szánt dedikált Automation-fiókja, érdemes létrehoznia egyet, hogy számos különböző forgatókönyvet tesztelhet a runbookok fejlesztése során. A tesztelésnek tartalmaznia kell iteratív módosításokat, például a PowerShell-modulok frissítését.

Győződjön meg arról, hogy az Automation-fiókhoz létre lett [hozva egy Azure-beli futtatás fiókja.](automation-security-overview.md#run-as-accounts)

Ha helyileg fejleszt szkripteket, javasoljuk, hogy az Automation-fiókban helyileg is ugyanazokkal a modulverzióval dolgozzon, mint az Automation-fiókban, hogy a teszt során is ugyanazt az eredményt kapja. Az eredmények ellenőrzése és a szükséges módosítások alkalmazása után áthelyezheti a módosításokat az éles környezetbe.

> [!NOTE]
> Előfordulhat, hogy egy új Automation-fiók nem tartalmazza a legújabb modulokat.

## <a name="obtain-a-runbook-to-use-for-updates"></a>A frissítésekhez használható runbook beszerzése

Az Automation-fiókban található Azure-modulok frissítéséhez az [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbookot kell használnia, amely nyílt forráskódúként érhető el. Az Azure-modulok frissítéséhez a runbookot a GitHub-adattárból töltheti le. Ezután importálhatja az Automation-fiókjába, vagy futtathatja szkriptként. Ha többet szeretne megtudni arról, hogyan importálhat runbookot az Automation-fiókjába, tekintse meg [a Runbook importálása cikkeket.](manage-runbooks.md#import-a-runbook)

Az **Update-AutomationAzureModulesForAccount runbook** alapértelmezés szerint támogatja az Azure-, AzureRM- és Az-modulok frissítését. Az Az.Automation-modulok a runbook használatával való frissítésével kapcsolatos további információkért tekintse át az Azure-modulok [runbook readME](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) frissítését ismertető részt. Az Automation-fiókBan az Az modulok használatakor további fontos tényezőket is figyelembe kell vennie. További információ: [Modulok kezelése a Azure Automation.](shared-resources/modules.md)

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Runbook-kód frissítése normál PowerShell-szkriptként

A runbook kódját normál PowerShell-szkriptként használhatja runbook helyett. Ehhez először jelentkezzen be az [Azure-ba a Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal, majd adja át a `-Login $false` parancsprogramnak.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>A frissítési runbook használata szuverén felhőkben

Ha szuverén felhőkben használja ezt a runbookot, a paraméterrel adja át a megfelelő környezetet a `AzEnvironment` runbooknak. Elfogadható értékek: AzureCloud (Nyilvános Azure-felhő), AzureChinaCloud, AzureGermanCloud és AzureUSGovernment. Ezek az értékek a használatával olvashatók `Get-AzEnvironment | select Name` be. Ha nem ad meg értéket a parancsmagnak, a runbook alapértelmezés szerint az AzureCloudba lesz beállítva.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Egy adott modulverzió frissítése a frissítési runbook használatával

Ha a PowerShell-galéria-on elérhető legújabb modul helyett egy adott Azure PowerShell-modulverziót szeretne használni, ezeket a verziókat adja át az `ModuleVersionOverrides` **Update-AutomationAzureModulesForAccount** runbook választható paraméterének. Példákért tekintse meg a  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) forgatókönyvet. Azure PowerShell paraméterben nem említett modulokat a rendszer a modul legújabb verzióival frissíti a `ModuleVersionOverrides` PowerShell-galéria. Ha semmit sem ad át a paraméternek, az összes modul frissül a modul legújabb verzióival a `ModuleVersionOverrides` PowerShell-galéria. Ez a viselkedés megegyezik az Azure-modulok frissítése gombbal a Azure Portal. 

## <a name="next-steps"></a>Következő lépések

* A modulok használatával kapcsolatos részletekért lásd: [Modulok kezelése a Azure Automation.](shared-resources/modules.md)
* További információ a frissítési runbookról: [Azure-modulok runbook frissítése.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)
