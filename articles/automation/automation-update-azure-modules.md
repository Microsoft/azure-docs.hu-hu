---
title: A Azure Automation Azure PowerShell moduljainak frissítése
description: Ez a cikk azt ismerteti, hogyan lehet a Azure Automationban alapértelmezés szerint megadott általános Azure PowerShell-modulokat frissíteni.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 413293cdefd39285c57b58d4555d3d703492ea96
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894847"
---
# <a name="update-azure-powershell-modules"></a>Azure PowerShell-modulok frissítése

A leggyakoribb PowerShell-modulok alapértelmezés szerint az egyes Automation-fiókokban vannak megadva. Lásd: [alapértelmezett modulok](shared-resources/modules.md#default-modules). Mivel az Azure-csapat rendszeresen frissíti az Azure-modulokat, a befoglalt parancsmagokkal végzett módosítások is előfordulhatnak. Ezek a változások, például a paraméterek átnevezése vagy a parancsmagok teljesen elavulttá váltása negatív hatással lehet a runbookok. 

> [!NOTE]
> Nem törölheti a globális modulokat, amelyek az Automation által biztosított modulok.

## <a name="set-up-an-automation-account"></a>Automation-fiók beállítása

Ha el szeretné kerülni a runbookok és az általuk automatizálható folyamatok hatását, győződjön meg róla, hogy tesztelni és érvényesíteni szeretné a frissítéseket. Ha nem rendelkezik erre a célra szánt dedikált Automation-fiókkal, érdemes lehet létrehozni egy másikat, hogy tesztelje a runbookok fejlesztésének számos különböző forgatókönyvét. Ennek a tesztelésnek tartalmaznia kell az ismétlődő módosításokat, például a PowerShell-modulok frissítését.

Győződjön meg arról, hogy az Automation-fiókja rendelkezik az Azure-beli [futtató fiók hitelesítő adatainak](manage-runas-account.md) létrehozásával.

Ha helyileg fejleszti a parancsfájlokat, akkor azt javasoljuk, hogy az Automation-fiókban lévő, a tesztelés során azonos verziójú modulokat is ugyanazzal az eredménnyel kapja meg. Az eredmények ellenőrzése és a szükséges módosítások alkalmazása után áthelyezheti a módosításokat az éles környezetbe.

> [!NOTE]
> Előfordulhat, hogy egy új Automation-fiók nem tartalmazza a legújabb modulokat.

## <a name="obtain-a-runbook-to-use-for-updates"></a>A frissítésekhez használandó runbook beszerzése

Az Automation-fiókban lévő Azure-modulok frissítéséhez az [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook kell használnia, amely nyílt forráskódúként érhető el. A runbook az Azure-modulok frissítéséhez való használatának megkezdéséhez töltse le a GitHub-tárházból. Ezután importálhatja az Automation-fiókjába, vagy parancsfájlként futtathatja azt. A runbook az Automation-fiókban való importálásával kapcsolatos további információkért lásd: [Runbook importálása](manage-runbooks.md#import-a-runbook).

Az **Update-AutomationAzureModulesForAccount** runbook alapértelmezés szerint támogatja az Azure, a AzureRM és az az modulok frissítését. Tekintse át az [Azure-modulok frissítése runbook](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) további információt az az. Automation-modulok ezzel a runbook való frissítéséről. Az az Automation-fiókjában található modulok használatakor figyelembe kell vennie további fontos tényezőket. További információért lásd: [modulok kezelése Azure Automationban](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Frissítési runbook-kód használata normál PowerShell-parancsfájlként

A runbook kódot normál PowerShell-parancsfájlként is használhatja runbook helyett. Ehhez először jelentkezzen be az Azure-ba a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal, majd továbbítsa `-Login $false` a parancsfájlnak.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>A szuverén felhők frissítési runbook használata

Ha a runbook a szuverén felhőkön szeretné használni, a `AzEnvironment` paraméter használatával adja át a megfelelő környezetet a runbook. Elfogadható értékek: AzureCloud (Azure Public Cloud), AzureChinaCloud, AzureGermanCloud és AzureUSGovernment. Ezek az értékek a használatával kérhetők le `Get-AzEnvironment | select Name` . Ha nem adott meg értéket ehhez a parancsmaghoz, a runbook alapértelmezés szerint a AzureCloud értéket adja meg.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Egy adott modul verziójának frissítéséhez használja a runbook frissítését

Ha a PowerShell-galéria elérhető legújabb modul helyett egy adott Azure PowerShell modul verzióját szeretné használni, adja át ezeket a verziókat az `ModuleVersionOverrides` **Update-AutomationAzureModulesForAccount** runbook választható paraméterének. Példákért tekintse meg a  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) runbook. Azure PowerShell a paraméterben nem említett modulok a `ModuleVersionOverrides` PowerShell-Galéria legújabb moduljaival frissülnek. Ha nem adja meg a `ModuleVersionOverrides` paramétert, a rendszer az összes modult frissíti a PowerShell-Galéria legújabb moduljaival. Ez a viselkedés ugyanaz, mint az **Azure-modulok frissítése** gomb a Azure Portalban.

## <a name="next-steps"></a>Következő lépések

* A modulok használatával kapcsolatos részletekért lásd: [modulok kezelése Azure Automationban](shared-resources/modules.md).
* További információ a frissítési runbook: az [Azure-modulok Runbook frissítése](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
