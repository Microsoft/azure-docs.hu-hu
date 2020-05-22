---
title: A verziókövetés integrációjának használata Azure Automation
description: Ez a cikk azt ismerteti, hogyan lehet szinkronizálni Azure Automation a verziókövetés más adattárakkal.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 248cbd42d86371742ad4985b515d70d022722385
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744928"
---
# <a name="use-source-control-integration"></a>Verziókövetés-integrálás használata

 A verziókövetés integrációja Azure Automation támogatja az egyirányú szinkronizálást a verziókövetés adattárból. A verziókövetés lehetővé teszi, hogy a runbookok az Automation-fiókban naprakészek maradjanak a GitHub vagy az Azure Repos Source Control adattárában található parancsfájlok használatával. Ez a szolgáltatás megkönnyíti a fejlesztési környezetben tesztelt kód előállítását az üzemi Automation-fiókba.
 
 A verziókövetés integrációja lehetővé teszi, hogy könnyedén együttműködjön a csapatával, nyomon követheti a változásokat, és visszaállíthatja a runbookok korábbi verzióit. A verziókövetés lehetővé teszi például a verziókövetés különböző ágainak szinkronizálását a fejlesztési, tesztelési és üzemi Automation-fiókokkal. 

## <a name="source-control-types"></a>Verziókövetés típusai

A Azure Automation háromféle verziókövetés használatát támogatja:

* GitHub
* Azure-repók (git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Előfeltételek

* Egy forrásoldali vezérlő adattár (GitHub vagy Azure Repos)
* [Futtató fiók](manage-runas-account.md)
* A [legújabb Azure-modulok](automation-update-azure-modules.md) az Automation-fiókban, beleértve a `Az.Accounts` modult (az a modul megfelelője `AzureRM.Profile` )

> [!NOTE]
> A verziókövetés szinkronizációs feladatai a felhasználó Automation-fiókjában futnak, és a többi Automation-feladattal megegyező sebességgel lesznek számlázva.

## <a name="configure-source-control"></a>Verziókövetés konfigurálása

Ez a szakasz azt ismerteti, hogyan konfigurálható a verziókövetés az Automation-fiókhoz. Használhatja a Azure Portal vagy a PowerShellt is.

### <a name="configure-source-control-in-azure-portal"></a>A verziókövetés konfigurálása a Azure Portalban

Ezzel az eljárással konfigurálhatja a verziókövetés használatát a Azure Portal használatával.

1. Az Automation-fiókban válassza a **verziókövetés** lehetőséget, majd kattintson a **Hozzáadás**gombra.

    ![Forrás vezérlőelem kiválasztása](./media/source-control-integration/select-source-control.png)

2. Válassza a **forrás vezérlőelem típusát**, majd kattintson a **hitelesítés**elemre. 

3. Megnyílik egy böngészőablak, amely felszólítja a bejelentkezésre. A hitelesítés befejezéséhez kövesse az utasításokat.

4. A verziókövetés összegzése lapon használja a mezőket az alább megadott verziókövetés tulajdonságainak kitöltéséhez. Ha elkészült, kattintson a **Mentés** gombra. 

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Forrás vezérlőelem neve     | A verziókövetés rövid neve. A név csak betűket és számokat tartalmazhat.        |
    |Forrás vezérlőelem típusa     | A forrás-ellenőrzési mechanizmus típusa. Az elérhető lehetőségek:</br> * GitHub</br>* Azure-repók (git)</br> * Azure-repók (TFVC)        |
    |Adattár     | A tárház vagy a projekt neve. A rendszer beolvassa az első 200 adattárat. Egy adattár kereséséhez írja be a nevet a mezőbe, majd kattintson a **Keresés a githubon**lehetőségre.|
    |Elágaztatás     | A forrásfájlokat lekérő ág. A TFVC nem érhető el a fiókra vonatkozó célzás.          |
    |Mappa elérési útja     | A szinkronizálni kívánt runbookok tartalmazó mappa, például **/Runbooks**. A rendszer csak a megadott mappában lévő runbookok szinkronizálja. A rekurzió nem támogatott.        |
    |<sup>1</sup> . automatikus szinkronizálás     | Az automatikus szinkronizálás bekapcsolásának vagy kikapcsolásának beállítása a verziókövetés adattárában.        |
    |Runbook közzététele     | Annak beállítása, hogy a runbookok automatikusan közzé legyenek-e téve a forrás-vezérlőelemről való szinkronizálás után, vagy más módon.           |
    |Leírás     | A verziókövetés további részleteit megadó szöveg        |

    <sup>1</sup> ha engedélyezni szeretné az automatikus szinkronizálást a verziókövetés Azure Repos-integrációjának konfigurálásakor, a projekt rendszergazdájának kell lennie.

   ![Verziókövetés összegzése](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Előfordulhat, hogy a forrásként szolgáló adattárházhoz tartozó bejelentkezési azonosító eltér a Azure Portalhoz tartozó bejelentkezési adatoktól. Győződjön meg arról, hogy a verziókövetés konfigurálásakor a forrás vezérlő adattárának megfelelő fiókkal van bejelentkezve. Ha kétségei vannak, nyisson meg egy új fület a böngészőben, jelentkezzen ki a **dev.Azure.com**, a **visualstudio.com**vagy a **GitHub.com**-ből, és próbálkozzon újra a verziókövetés használatával.

### <a name="configure-source-control-in-powershell"></a>A verziókövetés konfigurálása a PowerShellben

A PowerShell használatával is konfigurálhatja a verziókövetés Azure Automationban való konfigurálását. Ehhez a művelethez a PowerShell-parancsmagok használatához személyes hozzáférési jogkivonat (PAT) szükséges. A [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) parancsmaggal hozza létre a verziókövetés-kapcsolatokat. Ehhez a parancsmaghoz biztonságos karakterlánc szükséges a PAT számára. A biztonságos sztringek létrehozásáról további információt a következő témakörben talál: [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Az alábbi alszakaszok a GitHub, az Azure Repos (git) és az Azure Repos (TFVC) forrás-ellenőrzési kapcsolatainak létrehozását szemléltetik. 

#### <a name="create-source-control-connection-for-github"></a>Verziókövetés-kapcsolatok létrehozása a GitHubhoz

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Adatforrás-vezérlési kapcsolatok létrehozása az Azure Repos (git) szolgáltatáshoz

> [!NOTE]
> Az Azure Repos (git) olyan URL-címet használ, amely a korábbi formátumokban használt **VisualStudio.com**helyett a **dev.Azure.com** -hez fér hozzá. A régebbi URL-formátum `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` elavult, de továbbra is támogatott. Az új formátum előnyben részesített.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Adatforrás-vezérlési kapcsolatok létrehozása az Azure Repos (TFVC) számára

> [!NOTE]
> Az Azure Repos (TFVC) olyan URL-címet használ, amely a korábbi formátumokban használt **VisualStudio.com**helyett a **dev.Azure.com** -hoz fér hozzá. A régebbi URL-formátum `https://<accountname>.visualstudio.com/<projectname>/_versionControl` elavult, de továbbra is támogatott. Az új formátum előnyben részesített.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Személyes hozzáférési jogkivonat (PAT) engedélyei

A verziókövetés bizonyos minimális engedélyeket igényel a PATs számára. A következő alszakaszok tartalmazzák a GitHub és az Azure Repos minimálisan szükséges engedélyeit.

##### <a name="minimum-pat-permissions-for-github"></a>A GitHub minimálisan szükséges engedélyei

A következő táblázat a GitHubhoz szükséges minimális PAT-engedélyeket határozza meg. A PAT a GitHubon való létrehozásával kapcsolatos további információkért lásd: [személyes hozzáférési jogkivonat létrehozása a parancssorhoz](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Hatókör  |Leírás  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Hozzáférési véglegesítés állapota         |
|`repo_deployment`      | Hozzáférés központi telepítési állapota         |
|`public_repo`     | Nyilvános adattárak elérése         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Adattár-hookok írása         |
|`read:repo_hook`|Adattár-hookok olvasása|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Az Azure-adattárakhoz minimálisan szükséges PAT-engedélyek

Az alábbi lista meghatározza az Azure Repos minimálisan szükséges PAT-engedélyeit. További információ a PAT Azure Repos-beli létrehozásáról: [hozzáférés hitelesítése személyes hozzáférési jogkivonatokkal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Hatókör  |  Hozzáférés típusa  |
|---------| ----------|
| `Code`      | Olvasás  |
| `Project and team` | Olvasás |
| `Identity` | Olvasás     |
| `User profile` | Olvasás     |
| `Work items` | Olvasás    |
| `Service connections` | Olvasás, lekérdezés, kezelés<sup>1</sup>    |

<sup>1</sup> az `Service connections` engedély csak akkor szükséges, ha engedélyezve van az AutoSync.

## <a name="synchronize-with-source-control"></a>Szinkronizálás a forrás vezérlőelemmel

Kövesse az alábbi lépéseket a verziókövetés szinkronizálásához. 

1. Válassza ki a forrást a forrás vezérlőelem oldalon található táblázatból. 

2. A szinkronizálási folyamat elindításához kattintson a **szinkronizálás indítása** elemre. 

3. A **szinkronizálási feladatok** lapra kattintva megtekintheti az aktuális szinkronizálási feladat vagy az előzőek állapotát. 

4. **A verziókövetés legördülő menüben** válassza ki a forrás vezérlő mechanizmust.

    ![Szinkronizálás állapota](./media/source-control-integration/sync-status.png)

5. A feladatokra kattintva megtekintheti a feladatok kimenetét. A következő példa egy verziókövetés-szinkronizálási feladatokból származó kimenet.

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

6. A további naplózást a verziókövetés szinkronizálása feladatok összegzése lapon található **összes napló** lehetőség kiválasztásával érheti el. Ezek a további naplóbejegyzések segíthetnek a verziókövetés használata során felmerülő problémák megoldásában.

## <a name="disconnect-source-control"></a>Verziókövetés leválasztása

A forrás-felügyeleti adattárból való leválasztáshoz:

1. Az **Automation-fiók Fiókbeállítások területén** nyissa meg a **verziókövetés** csomópontot.

2. Válassza ki az eltávolítandó verziókövetés-mechanizmust. 

3. A verziókövetés összegzése lapon kattintson a **Törlés**elemre.

## <a name="handle-encoding-issues"></a>Kódolási problémák kezelése

Ha több ember szerkeszti a runbookok a különböző szerkesztők használatával, a kódolási problémák léphetnek fel. Ha többet szeretne megtudni erről a helyzetről, tekintse meg a [kódolási problémák gyakori okait](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="update-the-pat"></a>A PAT frissítése

Jelenleg nem használhatja a Azure Portal a PAT in Source vezérlőben való frissítésére. Ha a PAT lejárt vagy visszavont, a verziókövetés egy új hozzáférési jogkivonattal is frissíthető az alábbi módokon:

* Használja a [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Használja az [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) parancsmagot.

## <a name="next-steps"></a>További lépések

* [Azure Automation: verziókövetés integrációja Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Azure Automation: a Runbook-verziókövetés integrálása az Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  