---
title: E-mail-értesítések automatikus finomhangolása útmutató
description: E-mail-értesítések engedélyezése Azure SQL Database automatikus lekérdezés-hangoláshoz.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/03/2019
ms.openlocfilehash: a373a28a180b2a6c72f6a291b9d1437a2e88d9ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500954"
---
# <a name="email-notifications-for-automatic-tuning"></a>Automatikus hangolással kapcsolatos e-mail-értesítések
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


A Azure SQL Database hangolási javaslatok Azure SQL Database [automatikus hangolással](automatic-tuning-overview.md)jönnek létre. Ez a megoldás folyamatosan figyeli és elemzi az adatbázisok munkaterheléseit, amelyek testreszabott hangolási javaslatokat biztosítanak minden egyes adatbázishoz, amely az indexek létrehozásával, az indexelés törlésével és a lekérdezés-végrehajtási tervek optimalizálásával kapcsolatos.

Azure SQL Database Automatikus hangolási javaslatok megtekinthetők a [Azure Portalban](database-advisor-find-recommendations-portal.md), [REST API](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) hívásokkal, illetve a [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) és a [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) -parancsok használatával. Ez a cikk az Automatikus hangolási javaslatok beolvasására szolgáló PowerShell-parancsfájl használatával készült.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>E-mail-értesítések automatizálása Automatikus hangolási javaslatok esetén

Az alábbi megoldás automatizálja az Automatikus hangolási javaslatokat tartalmazó e-mail-értesítések küldését. A leírt megoldás egy PowerShell-parancsfájl végrehajtásának automatizálása a [Azure Automation](../../automation/automation-intro.md)használatával történő hangolási javaslatok beolvasásához és az e-mailek kézbesítési feladatainak [Microsoft flow](https://flow.microsoft.com)használatával történő automatizálásához.

## <a name="create-azure-automation-account"></a>Azure Automation fiók létrehozása

A Azure Automation használatához az első lépés egy Automation-fiók létrehozása és konfigurálása az Azure-erőforrásokkal a PowerShell-parancsfájl végrehajtásához. Ha többet szeretne megtudni a Azure Automation és képességeiről, tekintse meg a [Bevezetés az Azure Automation](../../automation/index.yml)használatába című témakört.

Az alábbi lépéseket követve létrehozhat egy Azure Automation fiókot az Azure Marketplace-en elérhető Automation-alkalmazások kiválasztásának és konfigurálásának módszerével:

1. Jelentkezzen be az Azure Portalra.
1. Kattintson a bal felső sarokban található "**+ erőforrás létrehozása**" elemre.
1. Keressen rá az "**Automation**" kifejezésre (nyomja le az ENTER billentyűt).
1. Kattintson az Automation alkalmazásra a keresési eredmények között.

    ![Azure Automation hozzáadása](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. Az "Automation-fiók létrehozása" panelen kattintson a "**Létrehozás**" gombra.
1. Töltse fel a szükséges információkat: adja meg az Automation-fiók nevét, válassza ki a PowerShell-parancsfájl végrehajtásához használni kívánt Azure-előfizetési azonosítót és Azure-erőforrásokat.
1. Az "Azure-beli **futtató fiók létrehozása**" beállításnál válassza az **Igen** lehetőséget a fiók típusának konfigurálásához, amely alatt a PowerShell-szkript fut Azure Automation segítségével. További információ a fiókok típusairól: [futtató fiók](../../automation/manage-runas-account.md).
1. A **Létrehozás** gombra kattintva megköti az Automation-fiók létrehozását.

> [!TIP]
> Rögzítse a Azure Automation fiók nevét, az előfizetés AZONOSÍTÓját és az erőforrásokat (például másolás-beillesztés egy Jegyzettömbbe) pontosan az Automation-alkalmazás létrehozásakor megadott módon. Ezt az információt később kell megadnia.

Ha több Azure-előfizetéssel is rendelkezik, amelyek esetében ugyanazt az automatizálást szeretné felépíteni, akkor a többi előfizetéshez meg kell ismételnie ezt a folyamatot.

## <a name="update-azure-automation-modules"></a>Azure Automation modulok frissítése

Az Automatikus hangolási javaslat beolvasására szolgáló PowerShell-szkript a [Get-AzResource](/powershell/module/az.Resources/Get-azResource) és a [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) parancsokat használja, amelyekhez szükség van az Azure-modul 4-es vagy újabb verziójára.

- Ha az Azure-modulok frissítése szükséges, tekintse meg az [az modul-támogatás Azure Automationban](../../automation/shared-resources/modules.md)című témakört.

## <a name="create-azure-automation-runbook"></a>Azure Automation runbook létrehozása

A következő lépés egy olyan Runbook létrehozása Azure Automation belül, amelyben a hangolási javaslatok lekérésére szolgáló PowerShell-szkript található.

Új Azure Automation runbook létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg az előző lépésben létrehozott Azure Automation fiókot.
1. Az Automation-fiók panelen kattintson a bal oldalon található "**runbookok**" menüelemre, és hozzon létre egy új Azure Automation Runbook a PowerShell-parancsfájllal. Az Automation-runbookok létrehozásával kapcsolatos további tudnivalókért tekintse meg [az új Runbook létrehozása](../../automation/manage-runbooks.md#create-a-runbook)című témakört.
1. Új runbook hozzáadásához kattintson a "**+ Runbook hozzáadása**" menüpontra, majd kattintson a "**gyors létrehozás – új runbook létrehozása**" lehetőségre.
1. A Runbook panelen írja be a Runbook nevét (a jelen példában a "**AutomaticTuningEmailAutomation**" kifejezést használja), válassza ki a Runbook típusát **powershellként** , és írja le a Runbook leírását, hogy leírja a célját.
1. Kattintson a **Létrehozás** gombra egy új runbook létrehozásának befejezéséhez.

    ![Azure Automation-runbook hozzáadása](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

Kövesse az alábbi lépéseket egy PowerShell-szkript betöltéséhez a létrehozott runbook:

1. A "**PowerShell-Runbook szerkesztése**" ablaktáblán a menüsávon válassza a "**runbookok**" elemet, és bontsa ki a nézetet, amíg meg nem jelenik a Runbook neve (ebben a példában "**AutomaticTuningEmailAutomation**"). Válassza ki ezt a runbook.
1. A "PowerShell-Runbook szerkesztése" első sorában (az 1. számmal kezdődően) másolja be a következő PowerShell-szkript kódját. Ezt a PowerShell-szkriptet a következő módon biztosítjuk: a kezdéshez. Módosítsa a szkriptet az igényeinek megfelelően.

A megadott PowerShell-parancsfájl fejlécében le kell cserélnie az `<SUBSCRIPTION_ID_WITH_DATABASES>` Azure-előfizetés azonosítóját. Az Azure-előfizetés AZONOSÍTÓjának [beszerzésével](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal)kapcsolatos további információkért lásd: Azure-előfizetés GUID-azonosítójának beolvasása.

Több előfizetés esetén a parancsfájl fejlécében a "$subscriptions" tulajdonságot vesszővel elválasztva adhatja hozzá.

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Kattintson a jobb felső sarokban található **Save (Mentés**) gombra a parancsfájl mentéséhez. Ha elégedett a parancsfájllal, kattintson a "**Közzététel**" gombra a runbook közzétételéhez.

A fő runbook panelen a "**Start**" gombra kattintva **ellenőrizheti a** parancsfájlt. A "**kimenet**" elemre kattintva megtekintheti a végrehajtott parancsfájl eredményeit. Ez a kimenet lesz az e-mail tartalma. A szkript mintájának kimenete a következő képernyőképen látható.

![Automatikus hangolási javaslatok megtekintése a Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

Győződjön meg arról, hogy a PowerShell-szkript igényeinek megfelelően testreszabja a tartalmat.

A fenti lépésekkel az Automatikus hangolási javaslatok beolvasására szolgáló PowerShell-szkript betöltődik Azure Automationba. A következő lépés az e-mail kézbesítési feladatok automatizálása és ütemezve.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Az e-mail-feladatok automatizálása Microsoft Flow

A megoldás befejezéséhez az utolsó lépésként hozzon létre egy automatizálási folyamatot Microsoft Flow a három műveletből (feladatok) álló folyamatból:

- "**Azure Automation-Create Job**" – a PowerShell-szkript végrehajtásához használható a Azure Automation runbook lévő Automatikus hangolási javaslatok lekéréséhez.
- "**Azure Automation – a feladatok kimenetének** beolvasása" – a végrehajtott PowerShell-parancsfájl kimenetének lekérésére szolgál.
- "**Office 365 Outlook – E-mail küldése**" – e-mailek küldésére szolgál. Az e-maileket a folyamat létrehozásához használt egyéni munkahelyi vagy iskolai fiókkal küldi el a rendszer.

Microsoft Flow képességekkel kapcsolatos további tudnivalókért tekintse meg a [Microsoft flow első lépéseivel foglalkozó](/flow/getting-started)témakört.

Ennek a lépésnek az előfeltétele, hogy regisztráljon egy [Microsoft flow](https://flow.microsoft.com) fiókra, és jelentkezzen be. A megoldáson belül a következő lépésekkel állíthatja be az **új folyamatot**:

1. Hozzáférés **a "saját folyamatok**" menüponthoz.
1. A saját folyamatok részen válassza az oldal tetején található "**+ Létrehozás az üresből**" hivatkozást.
1. Kattintson a lap alján található "**több száz összekötő és triggerek keresése**" hivatkozásra.
1. A keresőmezőbe írja be az "**Ismétlődés**" kifejezést, majd a keresési eredmények közül válassza az "**ütemterv-ismétlődés**" lehetőséget az e-mail kézbesítési feladatok futtatásának ütemezése érdekében.
1. A gyakoriság mező ismétlődés ablaktábláján válassza ki a folyamat végrehajtásának ütemezési gyakoriságát, például az automatikus e-mailek küldését percenként, óránként, naponta, hetente stb.

A következő lépés a három feladat hozzáadása (létrehozás, kimenet beolvasása és e-mailek küldése) az újonnan létrehozott ismétlődő folyamathoz. A szükséges feladatok a folyamathoz való hozzáadásához kövesse az alábbi lépéseket:

1. Művelet létrehozása a hangolási javaslatok beolvasására szolgáló PowerShell-parancsfájl végrehajtásához

   - Válassza az "**+ új lépés**" lehetőséget, majd a "**művelet hozzáadása**" elemet az ismétlődési folyamat ablaktáblán belül.
   - A keresőmezőbe írja be az "**Automation**" kifejezést, majd a keresési eredmények közül válassza a "**Azure Automation – létrehozási feladatok**" lehetőséget.
   - A feladatok létrehozása panelen konfigurálja a feladatok tulajdonságait. Ehhez a konfigurációhoz a korábban az **Automation-fiók panelen** **rögzített** Azure-előfizetési azonosító, erőforráscsoport és Automation-fiók adataira lesz szüksége. Ha többet szeretne megtudni az ebben a szakaszban elérhető beállításokról, olvassa el a [Azure Automation-feladatok létrehozása](/connectors/azureautomation/#create-job)című témakört.
   - Fejezze be a művelet létrehozását a "**folyamat mentése**" gombra kattintva.

2. Művelet létrehozása a végrehajtott PowerShell-parancsfájl kimenetének lekéréséhez

   - Válassza az "**+ új lépés**" elemet, majd a "**művelet hozzáadása**" lehetőséget az ismétlődési folyamat ablaktáblán.
   - A keresőmezőbe írja be az "**Automation**" kifejezést, majd válassza a "**Azure Automation – feladatok kimenetének beolvasása**" lehetőséget a keresési eredmények közül. További információ az ebben a szakaszban elérhető beállításokról: [Azure Automation – feladatok kimenetének beolvasása](/connectors/azureautomation/#get-job-output).
   - Töltse ki a szükséges mezőket (az előző feladathoz hasonlóan) – töltse ki az Azure-előfizetés AZONOSÍTÓját, az erőforráscsoportot és az Automation-fiókot (az Automation-fiók panelen megadott módon).
   - Kattintson a "**feladatok azonosítója**" mezőre a "**dinamikus tartalom**" menü megjelenítéséhez. A menüben válassza a "**Job ID**" lehetőséget.
   - Fejezze be a művelet létrehozását a "**folyamat mentése**" gombra kattintva.

3. Művelet létrehozása e-mailek küldéséhez az Office 365-integráció használatával

   - Válassza az "**+ új lépés**" lehetőséget, majd a "**művelet hozzáadása**" elemet az ismétlődési folyamat ablaktáblán belül.
   - A keresőmezőbe írja be az "**e-mail küldése**" kifejezést, és válassza az "**Office 365 Outlook – e-mail küldése**" lehetőséget a keresési eredmények közül.
   - A "**to**" mezőbe írja be az e-mail-címet, amelyre el kell küldenie az értesítő e-mailt.
   - A "**Tárgy**" mezőbe írja be az e-mail-cím tárgyát, például: "Automatikus hangolási javaslatok e-mail értesítés".
   - Kattintson a "**törzs**" mezőre a "**dinamikus tartalom**" menü megjelenítéséhez. A menüben a "**feladatok kimenetének lekérése**" területen válassza a "**tartalom**" lehetőséget.
   - Fejezze be a művelet létrehozását a "**folyamat mentése**" gombra kattintva.

> [!TIP]
> Ha automatizált e-maileket szeretne küldeni a különböző címzetteknek, hozzon létre külön folyamatokat. Ezekben a további folyamatokban módosítsa a címzett e-mail-címét a "címzett" mezőbe, az e-mail tárgyát pedig a "subject" (tárgy) mezőbe. A testreszabott PowerShell-parancsfájlokkal (például az Azure-előfizetés AZONOSÍTÓjának módosításával) rendelkező Azure Automation új runbookok létrehozása lehetővé teszi az automatizált forgatókönyvek további testreszabását, például például a különálló előfizetésekre vonatkozó automatikus finomhangolási javaslatok elküldése külön címzettek számára.

A fenti lépések az e-mail kézbesítési feladatok munkafolyamatának konfigurálásához szükségesek. A három létrehozott műveletből álló teljes folyamat az alábbi képen látható.

![E-mail-értesítések automatikus finomhangolásának megtekintése](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

A folyamat teszteléséhez kattintson a jobb felső sarokban található "**Futtatás most**" elemre a folyamat ablaktáblán.

Az automatizált feladatok futtatásának statisztikája, amely az e-mail-értesítések sikeres elküldését mutatja be, a flow Analytics panelről is megtekinthető.

![Az e-mailek automatikus hangolására szolgáló folyamat futtatása](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

A flow Analytics panel hasznos a feladatok végrehajtásának sikerességének figyeléséhez, és ha szükséges a hibaelhárításhoz.  Hibaelhárítás esetén érdemes megvizsgálni a PowerShell-parancsfájl futtatási naplóját is a Azure Automation alkalmazáson keresztül.

Az automatizált e-mailek végső kimenete a megoldás kiépítése és futtatása után a következő e-mailekhez hasonlóan néz ki:

![Minta e-mail-kimenet Automatikus hangolási e-mail-értesítésekből](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

A PowerShell-szkript módosításával beállíthatja az automatikus e-mailek kimenetét és formázását az igényeinek megfelelően.

Előfordulhat, hogy a megoldás egy adott hangolási eseményen, illetve több címzettnél, több előfizetéshez vagy adatbázishoz való kiépítéséhez az egyéni forgatókönyvek függvényében tovább testreszabja a megoldást.

## <a name="next-steps"></a>Következő lépések

- További információ arról, hogy az automatikus hangolás hogyan segít az adatbázisok teljesítményének javításában: [Azure SQL Database automatikus finomhangolása](automatic-tuning-overview.md).
- A számítási feladatok kezeléséhez Azure SQL Database automatikus hangolásának engedélyezéséhez tekintse meg az [automatikus hangolás engedélyezése](automatic-tuning-enable.md)című témakört.
- Az Automatikus hangolási javaslatok manuális áttekintéséhez és alkalmazásához tekintse [meg a teljesítményre vonatkozó javaslatok keresése és alkalmazása](database-advisor-find-recommendations-portal.md)című témakört.