---
title: Forrásvezérlési integráció használata a Azure Automation
description: Ez a cikk bemutatja, hogyan szinkronizálhatja Azure Automation a forrásvezérlőt más tárházokkal.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d94da9792d40a389e3981163e565d85d82a9cdc9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831239"
---
# <a name="use-source-control-integration"></a>Verziókövetés-integrálás használata

 A forrásvezérlő integrációja Azure Automation támogatja az egy irányból való szinkronizálást a forrásvezérlő adattárból. A verzióvezérléssel naprakészen tarthatja az Automation-fiókjában található forgatókönyveket a GitHubon vagy az Azure Repos verzióvezérlő adattárában található szkriptekkel. Ez a funkció megkönnyíti a fejlesztési környezetben tesztelt kód megléptethetővé az éles Automation-fiókba.

 A verzióvezérlés integrációja lehetővé teszi, hogy könnyedén együttműködjön a csapatával, nyomon kövesse a módosításokat, és visszaveszi a runbookok korábbi verzióit. A forrásvezérléssel például szinkronizálhatja a különböző fiókokat a forrásvezérlésben a fejlesztési, tesztelési és éles Automation-fiókokkal.

## <a name="source-control-types"></a>Forrásvezérlési típusok

Azure Automation a forrásvezérlés három típusát támogatja:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Előfeltételek

* Verzióvezérlési adattár (GitHub vagy Azure-adattárak)
* Egy [run as account](automation-security-overview.md#run-as-accounts)
* A [ `AzureRM.Profile` modult](/powershell/module/azurerm.profile/) importálni kell az Automation-fiókjába. Vegye figyelembe, hogy az egyenértékű Az modul ( `Az.Accounts` ) nem fog működni az Automation forrásvezérlővel.

> [!NOTE]
> A forrásvezérlő szinkronizálási feladatok a felhasználó Automation-fiókjában futnak, és a számlázás ugyanolyan sebességgel történik, mint a többi Automation-feladat.

## <a name="configure-source-control"></a>A forrásvezérlés konfigurálása

Ez a szakasz azt mutatja be, hogyan konfigurálhatja a forrásvezérlést az Automation-fiókjához. Használhatja a Azure Portal PowerShellt.

### <a name="configure-source-control-in-azure-portal"></a>A forrásvezérlés konfigurálása a Azure Portal

Ezzel az eljárással konfigurálhatja a forrásvezérlőt a Azure Portal.

1. Az Automation-fiókban válassza a **Forrásvezérlés lehetőséget, majd** kattintson a **Hozzáadás gombra.**

    ![Forrásvezérlő kiválasztása](./media/source-control-integration/select-source-control.png)

2. Válassza **a Forrásvezérlés típusát,** majd kattintson a **Hitelesítés elemre.**

3. Megnyílik egy böngészőablak, amely felkéri a bejelentkezésre. Kövesse az utasításokat a hitelesítés befejezéséhez.

4. A Forrásvezérlő összefoglalása lapon a mezők használatával töltse ki az alább definiált forrásvezérlő-tulajdonságokat. Ha **elkészült, kattintson** a Mentés gombra.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Forrásvezérlő neve     | A forrásvezérlő rövid neve. A név csak betűket és számokat tartalmazhat.        |
    |Forrásvezérlő típusa     | A forrásvezérlési mechanizmus típusa. Az elérhető lehetőségek:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Adattár     | Az adattár vagy projekt neve. Lekéri az első 200 adattárat. Adattár kereséshez írja be a nevet a mezőbe, majd kattintson a Keresés a **GitHubon elemre.**|
    |Elágaztatás     | A forrásfájlok lekért ága. Az ágak célcsoport-megcélzása nem érhető el a TFVC forrásvezérlő-típushoz.          |
    |Mappa elérési útja     | A szinkronizálni kívánt runbookokat tartalmazó mappa, például **/Runbooks.** A rendszer csak a megadott mappában lévő runbookokat szinkronizálja. A rekurzió nem támogatott.        |
    |Automatikus szinkronizálás<sup>1</sup>     | Beállítás, amely be- vagy kikapcsolja az automatikus szinkronizálást, amikor véglegesítés történik a forrásvezérlő adattárban.        |
    |Runbook közzététele     | Ha a runbookokat a rendszer automatikusan közzéteszi a forrásvezérlőből való szinkronizálás után, a Be beállítást, egyébként pedig a Ki beállítást.           |
    |Description     | A forrásvezérlővel kapcsolatos további részleteket tartalmazó szöveg.        |

    <sup>1</sup> Ha engedélyezni szeretné az automatikus szinkronizálást a forrásvezérlés Azure-adattárokkal való integrációjának konfigurálásakor, projekt-rendszergazdának kell lennie.

   ![A forrásvezérlő összegzése](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Előfordulhat, hogy a forrásvezérlő adattár bejelentkezési adatai eltérnek a fiókhoz Azure Portal. A forrásvezérlő konfigurálásakor győződjön meg arról, hogy a megfelelő fiókkal jelentkezett be a forrásvezérlő adattárhoz. Ha kétségei vannak, nyisson meg egy új lapot a böngészőben, jelentkezzen ki a **dev.azure.com,** **visualstudio.com**, **github.com**, és próbáljon meg újra csatlakozni a forrásvezérlőhöz.

### <a name="configure-source-control-in-powershell"></a>A forrásvezérlés konfigurálása a PowerShellben

A PowerShell használatával is konfigurálhatja a forrásvezérlést a Azure Automation. Ha a PowerShell-parancsmagokat ehhez a művelethez használnia kell egy személyes hozzáférési jogkivonatra (PAT). Használja a [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol) parancsmagot a forrásvezérlő kapcsolat létrehozásához. Ehhez a parancsmaghoz biztonságos sztringre van szükség a PAT-hez. A biztonságos sztringek létrehozásáról lásd: [ConvertTo-SecureString.](/powershell/module/microsoft.powershell.security/convertto-securestring)

Az alábbi alszakaszok bemutatják a Verzióvezérlési kapcsolat PowerShell-alapú létrehozását a GitHub, az Azure Repos (Git) és az Azure Repos (TFVC) számára.

#### <a name="create-source-control-connection-for-github"></a>Verzióvezérlési kapcsolat létrehozása a GitHubhoz

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Verzióvezérlési kapcsolat létrehozása azure-adattárakhoz (Git)

> [!NOTE]
> Az Azure Repos (Git) olyan  URL-címet használ, dev.azure.com a korábbi formátumokban használt visualstudio.com helyett a -hez fér hozzá.  A régebbi `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` URL-formátum elavult, de továbbra is támogatott. Az új formátumot részesíti előnyben.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Forrásvezérlési kapcsolat létrehozása az Azure Reposhoz (TFVC)

> [!NOTE]
> Az Azure Repos (TFVC) olyan  URL-címet használ, amely a dev.azure.com a korábbi formátumokban használt visualstudio.com helyett a -hez fér hozzá.  A régebbi `https://<accountname>.visualstudio.com/<projectname>/_versionControl` URL-formátum elavult, de továbbra is támogatott. Az új formátumot részesíti előnyben.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Személyes hozzáférési jogkivonat (PAT) engedélyei

A forrásvezérléshez minimális engedélyekre van szükség a PAT-hez. Az alábbi alszakaszok tartalmazzák a GitHubhoz és az Azure Reposhoz szükséges minimális engedélyeket.

##### <a name="minimum-pat-permissions-for-github"></a>Minimális PAT-engedélyek a GitHubhoz

Az alábbi táblázat a GitHubhoz szükséges minimális PAT-engedélyeket határozza meg. A PAT GitHubon való létrehozásával kapcsolatos további információkért lásd: Személyes hozzáférési [jogkivonat létrehozása a parancssorhoz.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)

|Hatókör  |Leírás  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Hozzáférés véglegesítési állapota         |
|`repo_deployment`      | Hozzáférés az üzembe helyezés állapotához         |
|`public_repo`     | Nyilvános adattárak elérése         |
|`repo:invite` | Adattár-meghívók elérése |
|`security_events` | Biztonsági események olvasása és írása |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Adattár hookok írása         |
|`read:repo_hook`|Adattári hookok olvasása|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimális PAT-engedélyek az Azure-adattárakhoz

Az alábbi lista az Azure-adattárakhoz szükséges minimális PAT-engedélyeket határozza meg. A PAT Azure-adattárakban való létrehozásával kapcsolatos további információkért lásd: [Hozzáférés hitelesítése személyes hozzáférési jogkivonatokkal.](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)

| Hatókör  |  Hozzáférés típusa  |
|---------| ----------|
| `Code`      | Olvasás  |
| `Project and team` | Olvasás |
| `Identity` | Olvasás     |
| `User profile` | Olvasás     |
| `Work items` | Olvasás    |
| `Service connections` | Olvasás, lekérdezés,<sup>kezelés 1</sup>    |

<sup>1</sup> Az `Service connections` engedélyre csak akkor van szükség, ha engedélyezte az automatikus szinkronizálást.

## <a name="synchronize-with-source-control"></a>Szinkronizálás a forrásvezérlővel

Kövesse az alábbi lépéseket a forrásvezérlővel való szinkronizáláshoz.

1. Válassza ki a forrást a Forrásvezérlő lapon található táblázatból.

2. Kattintson **a Szinkronizálás kezdése** gombra a szinkronizálási folyamat elkezdéshez.

3. A Szinkronizálási feladatok lapfülre kattintva megtekintheti az aktuális vagy korábbi **szinkronizálási feladatok** állapotát.

4. A **Forrásvezérlés legördülő** menüben válasszon ki egy forrásvezérlési mechanizmust.

    ![Szinkronizálás állapota](./media/source-control-integration/sync-status.png)

5. Ha egy feladatra kattint, megtekintheti a feladat kimenetét. Az alábbi példa egy forrásvezérlő szinkronizálási feladatának kimenete.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. További naplózási lehetőségeket a **Forrásvezérlő** szinkronizálási feladat összegzése oldalon található Minden napló lehetőség kiválasztásával lehet elérhetővé tenni. Ezek a további naplóbejegyzések segíthetnek a forrásvezérlés használata során felmerülő problémák elhárításában.

## <a name="disconnect-source-control"></a>A forrásvezérlő leválasztása

A forrásvezérlő adattár leválasztása:

1. Nyissa **meg a Forrásvezérlőt** **az** Automation-fiók Fiókbeállítások területén.

2. Válassza ki az eltávolítani kívánt forrásvezérlési mechanizmust.

3. A Source Control Summary (Forrásvezérlő összegzése) lapon kattintson a **Delete (Törlés) parancsra.**

## <a name="handle-encoding-issues"></a>Kódolási problémák megoldása

Ha többen is különböző szerkesztők használatával szerkesztik a runbookokat a forrásvezérlő adattárban, kódolási problémák léphetnek fel. További információ erről a helyzetről: [Kódolási problémák gyakori okai.](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="update-the-pat"></a>A PAT frissítése

Jelenleg nem használhatja a biztonsági Azure Portal a PAT frissítésére a verzióvezérlőben. Ha a PAT lejárt vagy visszavonták, a következő módokon frissítheti a verzióvezérlőt egy új hozzáférési jogkivonattal:

* Használja a [REST API.](/rest/api/automation/sourcecontrol/update)
* Használja az [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) parancsmagot.

## <a name="next-steps"></a>Következő lépések

* A forrásvezérlés integrációja a Azure Automation: [Azure Automation: Source Control Integration in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* A Runbook forrásvezérlésének integrációja a Visual Studio Codespaces: [Azure Automation: A Runbook](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)forrásvezérlésének integrálása a Visual Studio Codespaces.
