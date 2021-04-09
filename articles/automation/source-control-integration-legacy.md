---
title: A verziókövetés integrációjának használata Azure Automation örökölt
description: Ez a cikk azt ismerteti, hogyan használható a verziókövetés integrációja.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4dedbcf58e76b8c969f8607db6922e87a85f08e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591873"
---
# <a name="use-source-control-integration-in-azure-automation---legacy"></a>A verziókövetés integrációjának használata Azure Automation örökölt

> [!NOTE]
> A verziókövetés új felülettel rendelkezik. További információ az új felületről: [verziókövetés (előzetes verzió)](source-control-integration.md).

A verziókövetés integrációja lehetővé teszi, hogy az Automation-fiókban runbookok rendeljen egy GitHub-forrás vezérlőelem-tárházhoz. A verziókövetés segítségével könnyedén együttműködhet a csapatával, követheti a változásokat, és visszatérhet a runbookok korábbi verzióihoz. A verziókövetés lehetővé teszi például, hogy különböző ágakat szinkronizáljon a fejlesztési, tesztelési vagy éles üzemi automatizálási fiókjaiba, így könnyen előléptetheti a fejlesztői környezetben tesztelt kódokat a termelési Automation-fiókjába.

A verziókövetés lehetővé teszi a kód leküldését Azure Automationról a forrás vezérlőelemre, vagy lekéri a runbookok a forrás vezérlőelemről a Azure Automationra. Ez a cikk azt ismerteti, hogyan állítható be a verziókövetés a Azure Automation-környezetben. Kezdjük azzal, hogy Azure Automation konfiguráljuk a GitHub-tárház elérését, és különböző műveleteket hajtanak végre, amelyeket a verziókövetés integrálásával lehet elvégezni. 

> [!NOTE]
> A verziókövetés támogatja a PowerShell- [munkafolyamatok runbookok](automation-runbook-types.md#powershell-workflow-runbooks) , valamint a [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks). A [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) még nem támogatottak.

## <a name="configure-source-control"></a>Verziókövetés konfigurálása

Két egyszerű lépés szükséges az Automation-fiókhoz tartozó verziókövetés konfigurálásához, és csak akkor, ha már rendelkezik egy GitHub-fiókkal. 

### <a name="create-a-github-repository"></a>GitHub-adattár létrehozása

Ha már rendelkezik egy GitHub-fiókkal és egy Azure Automationhoz csatolni kívánt adattárral, jelentkezzen be a meglévő fiókjába, és kezdje az alábbi 2. lépéssel. Ellenkező esetben navigáljon a [githubra](https://github.com/), regisztráljon egy új fiókot, és [hozzon létre egy új tárházat](https://help.github.com/articles/create-a-repo/).

### <a name="set-up-source-control"></a>Verziókövetés beállítása

1. A Azure Portal Automation-fiók lapjának **Fiókbeállítások** területén kattintson a verziókövetés elemre **.**

2. Megnyílik a verziókövetés oldal, ahol beállíthatja a GitHub-fiók adatait. Alább látható a konfigurálandó paraméterek listája:  

   | **Paraméter** | **Leírás** |
   |:--- |:--- |
   | Forrás kiválasztása |Válassza ki a forrást. Jelenleg csak a **GitHub** támogatott. |
   | Engedélyezés |Kattintson az **Engedélyezés** gombra, és adja meg Azure Automation hozzáférést a GitHub-tárházhoz. Ha már bejelentkezett a GitHub-fiókjába egy másik ablakban, akkor a rendszer a fiók hitelesítő adatait használja. Az engedélyezés sikerességét követően az oldal megjeleníti a GitHub-felhasználónevet az **engedélyezési tulajdonság** alatt. |
   | Adattár kiválasztása |Válasszon egy GitHub-tárházat az elérhető Tárházak listájából. |
   | Ág kiválasztása |Válasszon ki egy ágat az elérhető ágak listájából. Ha nem hozott létre ágakat, csak a **fő** ág jelenik meg. |
   | Runbook mappa elérési útja |A runbook mappa elérési útja meghatározza a GitHub-tárház azon elérési útját, amelyről le kívánja küldeni vagy le szeretné kérni a kódot. Ezt a formátumot **/FOLDERNAME/subfoldername** formátumban kell megadni. Az Automation-fiókjába csak a runbook mappa elérési útjának runbookok lesznek szinkronizálva. A runbook mappa elérési útjának almappáiban lévő runbookok **nem** lesznek szinkronizálva. A paranccsal **/** szinkronizálhatja az adattár alatt lévő összes runbookok. |
3. Ha például rendelkezik egy **PowerShellScripts** nevű adattárral, amely egy **gyökérmappa** nevű mappát tartalmaz, amely egy almappa nevű mappát **tartalmaz.** Az egyes mappák szintjének szinkronizálásához a következő sztringeket használhatja:

   1. A runbookok az **adattárból** való szinkronizálásához a runbook mappa elérési útja a következő: **/** .
   2. A runbookok **gyökérmappa**-ből való szinkronizálásához a runbook mappa elérési útja **/RootFolder**.
   3. A runbookok az **almappából** való szinkronizálásához a runbook mappa elérési útja **/RootFolder/SubFolder**.
4. A paraméterek konfigurálása után ezek megjelennek a verziókövetés beállítása lapon.  

    ![A beállításokat megjelenítő forrásoldali vezérlő lap](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Miután rákattintott az **OK** gombra, a verziókövetés integrációja már konfigurálva van az Automation-fiókjához, és frissítenie kell a GitHub-információkkal. Erre a részre kattintva megtekintheti a verziókövetés szinkronizálási feladatok előzményeit.  

    ![A jelenlegi konfigurált verziókövetés konfigurációjának értékei](media/source-control-integration-legacy/automation-RepoValues.png)
6. A verziókövetés beállítása után két [változó eszköz](./shared-resources/variables.md) jön létre az Automation-fiókban. Emellett a rendszer hozzáad egy, a GitHub-fiókhoz egy erre a jogosultsággal rendelkező alkalmazást.

   * A **Microsoft. Azure. Automation. SourceControl. link** változó a következő ábrán látható módon tartalmazza a kapcsolatok karakterláncának értékeit.  

     | **Paraméter** | **Érték** |
     |:--- |:--- |
     | `Name`  |Microsoft. Azure. Automation. SourceControl.-kapcsolatok |
     | `Type`  |Sztring |
     | `Value` |{"Ág": \<*Your branch name*> , "RunbookFolderPath": \<*Runbook folder path*> , "ProviderType": \<*has a value 1 for GitHub*> , "adattár": \<*Name of your repository*> , "username": \<*Your GitHub user name*> } |

   * A **Microsoft. Azure. Automation. SourceControl. OAuthToken** változó a OAuthToken biztonságos titkosított értékét tartalmazza.  

     |**Paraméter**            |**Érték** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![A verziókövetés változóit megjelenítő ablak](media/source-control-integration-legacy/automation-Variables.png)  

   * Az **Automation-verziókövetés** a GitHub-fiókjának megfelelő alkalmazásként lesz hozzáadva. Az alkalmazás megtekintéséhez a GitHub kezdőlapján navigáljon a **Profilbeállítások**  >    >  **alkalmazások** menüpontra. Ez az alkalmazás lehetővé teszi, hogy Azure Automation szinkronizálja a GitHub-tárházat egy Automation-fiókkal.  

     ![Alkalmazásbeállítások a GitHubon](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="use-source-control-in-automation"></a>Verziókövetés használata az Automationben

A Runbook beadásával leküldheti az Azure Automationban végrehajtott módosításokat a Runbook a forrás vezérlő tárházba. A következő lépésekkel ellenőrizheti a runbook:

1. Az Automation-fiókból [hozzon létre egy új szöveges runbook](./learn/automation-tutorial-runbook-textual.md), vagy [szerkesszen egy meglévő, szöveges runbook](automation-edit-textual-runbook.md). Ez a runbook egy PowerShell-munkafolyamat vagy egy PowerShell-parancsfájl runbook lehet.  
2. A runbook szerkesztése után mentse a lapot, majd kattintson a **beadás** gombra a szerkesztés oldalon.  

    ![A GitHub beadásának gombját megjelenítő ablak](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > A beadás Azure Automation felülírja a verziókövetés jelenleg létező kódját. A git-ekvivalens parancssori útmutató a bejelentkezéshez **git Hozzáadás + git commit + git push**  

3. Ha a **Bejelentkezés** gombra kattint, a rendszer megerősítő üzenetet kér, és a folytatáshoz kattintson az **Igen** gombra.  

    ![Egy párbeszédpanel, amely megerősíti a beadást a verziókövetés számára](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. A beadás elindítja a verziókövetés runbook: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Ez a runbook csatlakozik a GitHubhoz, és leküldi a változásokat Azure Automationról a tárházba. A bejelentkezett feladatok előzményeinek megtekintéséhez lépjen vissza a **verziókövetés integrációja** lapra, és kattintson az adattár-szinkronizálás lap megnyitásához. Ezen az oldalon az összes verziókövetés-feladat látható. Válassza ki a megtekinteni kívánt feladatot, és kattintson ide a részletek megtekintéséhez.  

    ![Egy szinkronizálási feladatok eredményét megjelenítő ablak](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > A verziókövetés runbookok olyan speciális Automation-runbookok, amelyeket nem lehet megtekinteni vagy szerkeszteni. Habár a runbook listáján nem jelennek meg, a feladatok listájában megjelenik a szinkronizálási feladatok.

5. A módosított runbook neve bemeneti paraméterként lesz elküldve a bejelentkezett runbook. [A feladatok részleteit úgy tekintheti](automation-runbook-execution.md#job-statuses) meg, hogy kibővíti a Runbook az adattár szinkronizálása lapon.  

    ![A szinkronizálási feladatok bemenetét bemutató ablak](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Frissítse a GitHub-tárházat, miután a feladatok befejeződik a módosítások megtekintéséhez.  A tárházban véglegesíteni kell egy véglegesítő üzenettel: a **frissített *Runbook neve* Azure Automationban.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Runbookok szinkronizálása a forrás vezérlőelemről a Azure Automationba

Az adattár-szinkronizálás lapon található szinkronizálás gomb lehetővé teszi, hogy a tárház runbook mappájában lévő összes runbookok lekérje az Automation-fiókba. Ugyanaz a tárház több Automation-fiókkal is szinkronizálható. A runbook szinkronizálásának lépései a következők:

1. Az Automation-fiókban, ahol a verziókövetés beállítását választotta, nyissa meg a verziókövetés integráció/tárház szinkronizálása lapot, és kattintson a **szinkronizálás** elemre.  A rendszer megerősítő üzenetet kér, és a folytatáshoz kattintson az **Igen** gombra.  

    ![Szinkronizálás gomb üzenettel, amely megerősíti, hogy minden runbookok szinkronizálva lesz](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. A Sync elindítja a  **Sync-MicrosoftAzureAutomationAccountFromGitHubV1** runbook, amely csatlakozik a githubhoz, és lekéri a tárházban bekövetkező változásokat a Azure Automation. Ehhez a művelethez egy új feladatot kell megjelennie az adattár szinkronizációs lapján. A szinkronizálási feladattal kapcsolatos részletek megtekintéséhez kattintson a feladatok részletei lap megnyitásához.  

    ![Egy GitHub-adattár szinkronizálási feladatainak szinkronizálási eredményeit bemutató ablak](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > A verziókövetés szinkronizálása felülírja az Automation-fiókban jelenleg létező runbookok-Piszkozat verzióját az **összes** olyan runbookok esetében, amely jelenleg a verziókövetés részét képezi. A git-ekvivalens parancssori útmutató a szinkronizáláshoz **git pull**

![A felfüggesztett forrásoldali vezérlők szinkronizálási feladataiból származó összes naplót megjelenítő ablak](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnect-source-control"></a>Verziókövetés leválasztása

A GitHub-fiókból való leválasztáshoz nyissa meg az adattár szinkronizálása lapot, és kattintson a **Leválasztás** elemre. A verziókövetés leválasztása után a korábban szinkronizált runbookok továbbra is az Automation-fiókban maradnak, de az adattár szinkronizációs lapja nem lesz engedélyezve.  

  ![Egy ablak, amely a leválasztás gombot mutatja a verziókövetés leválasztásához](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Következő lépések

* A verziókövetés Azure Automationban való integrálásához tekintse meg a [Azure Automation: verziókövetés integrálása a Azure Automationban](https://azure.microsoft.com/blog/azure-automation-source-control-13/)című témakört.  
* A runbook a Visual Studio online-ban való integrálásához tekintse meg a következőt [: Azure Automation: a runbook-vezérlés integrálása a Visual Studio online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)-ban.  
