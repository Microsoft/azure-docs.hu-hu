---
title: 'ExpressRoute: egyéni riasztások konfigurálása hirdetett útvonalakhoz'
description: Ez a cikk bemutatja, hogyan használható a Azure Automation és a Logic Apps a ExpressRoute-átjáróról a helyszíni hálózatokra hirdetett útvonalak számának figyelésére, hogy megakadályozza a 200 útvonalak korlátozását.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: fed7663e2342a708aee70b9a54e6e0a6b6f97e8c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504401"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Egyéni riasztások konfigurálása a meghirdetett elérési utak figyeléséhez

Ebből a cikkből megtudhatja, Azure Automation és Logic Apps használatával folyamatosan figyelheti a ExpressRoute-átjáróról a helyszíni hálózatokra hirdetett útvonalak számát. A figyelés segíthet megakadályozni az [200 útvonalak korlátját](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering).

**Azure Automation** lehetővé teszi a *Runbook* tárolt egyéni PowerShell-parancsfájlok végrehajtásának automatizálását. A jelen cikkben található konfiguráció használatakor a runbook egy PowerShell-parancsfájlt tartalmaz, amely egy vagy több ExpressRoute-átjárót kérdez le. Egy adatkészletet gyűjt, amely tartalmazza az erőforráscsoportot, a ExpressRoute és a helyszínen meghirdetett hálózati előtagok számát.

**Azure Logic apps** egy egyéni munkafolyamatot ütemezhet, amely meghívja a Azure Automation runbook. A runbook végrehajtása feladatok használatával történik. Az adatgyűjtés futtatása után a Azure Logic Apps munkafolyamat osztályozza az adatokat, és az előre definiált küszöbértéket meghaladó vagy alacsonyabb hálózati előtagok számán alapuló egyeztetési feltételek alapján továbbítja az adatokat a célhely e-mail-címére.

### <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Az egyéni riasztások beállítása három fő lépésen alapul:

1. Hozzon létre egy Automation-fiókot "futtató" fiókkal és engedélyekkel.

2. Runbookok létrehozása és konfigurálása.

3. Hozzon létre egy logikai alkalmazást, amely az Automation-fiókot fogja használni, és küldjön riasztási e-mailt, ha a szám nagyobb, mint a küszöbérték (például 160).

## <a name="before-you-begin"></a><a name="before"></a>Kezdés előtt

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Legalább egy ExpressRoute-átjáróval rendelkezik az üzemelő példányban.

* A Azure Automation-beli [futtató fiókok](../automation/manage-runas-account.md) alapvető ismerete.

* Már ismeri a [Azure Logic apps](../logic-apps/logic-apps-overview.md).

* Ismeri a Azure PowerShell használatát. A hálózati előtagok ExpressRoute-átjárón való összegyűjtéséhez Azure PowerShell szükséges. Az általános Azure PowerShellről a [Azure PowerShell dokumentációjában](/powershell/azure/)talál további információt.

### <a name="notes-and-limitations"></a><a name="limitations"></a>Megjegyzések és korlátozások

* Az ebben a cikkben tárgyalt egyéni riasztás a jobb működés és irányítás érdekében egy bővítmény. Nem helyettesíti a natív riasztásokat a ExpressRoute.
* A háttérben a ExpressRoute-átjárók adatgyűjtése fut. A futtatókörnyezet a vártnál hosszabb lehet. A feladatok várólistára helyezésének elkerülése érdekében a munkafolyamat ismétlődését megfelelően kell beállítani.
* Parancsfájlok vagy ARM-sablonok által üzemelő példányok gyorsabban megtörténhetnek az egyéni riasztási triggernél. Ez a ExpressRoute-átjáróban a 200-es útvonalakon túlmutató hálózati előtagok számának növelését eredményezheti.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Fiókok létrehozása és konfigurálása

Amikor Automation-fiókot hoz létre a Azure Portalban, a rendszer automatikusan létrehoz egy [futtató](../automation/automation-security-overview.md#run-as-accounts) fiókot. Ez a fiók a következő műveleteket végzi el:

* Létrehoz egy Azure Active Directory (Azure AD) alkalmazást egy önaláírt tanúsítvánnyal. A futtató fióknak olyan tanúsítványa van, amelyet alapértelmezés szerint minden évben meg kell újítani.

* Létrehoz egy egyszerű szolgáltatásfiókot az alkalmazáshoz az Azure AD-ben.

* Maga a közreműködő szerepkört (Azure RBAC) rendeli hozzá a használatban lévő Azure-előfizetéshez. Ez a szerepkör a runbookok használatával felügyeli a Azure Resource Manager erőforrásokat.

Automation-fiók létrehozásához jogosultságok és engedélyek szükségesek. További információ: [Automation-fiók létrehozásához szükséges engedélyek](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account).

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. Automation-fiók létrehozása

Hozzon létre egy Automation-fiókot futtató engedéllyel. Útmutatásért lásd: [Azure Automation fiók létrehozása](../automation/automation-quickstart-create-account.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Automation-fiók hozzáadása" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. szerepkör társítása a futtató fiókhoz

Alapértelmezés szerint a **közreműködői** szerepkör hozzá van rendelve a **futtató** fiók által használt egyszerű szolgáltatáshoz. Megtarthatja az egyszerű szolgáltatáshoz rendelt alapértelmezett szerepkört, vagy korlátozhatja az engedélyeket egy [beépített szerepkör](../role-based-access-control/built-in-roles.md) (például olvasó) vagy egy [Egyéni szerepkör](../active-directory/roles/custom-create.md)hozzárendelésével.

 A következő lépések végrehajtásával határozhatja meg a futtató fiók által használt egyszerű szolgáltatáshoz hozzárendelt szerepkört:

1. Navigáljon az Automation-fiókjához. Navigáljon a **Fiókbeállítások** menüpontra, majd válassza a **futtató fiókok** lehetőséget.

2. Válassza ki a **szerepkörök** elemet a használatban lévő szerepkör-definíció megtekintéséhez.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Szerepkör hozzárendelése":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Runbookok létrehozása és konfigurálása

### <a name="1-install-modules"></a><a name="install-modules"></a>1. modulok telepítése

A PowerShell-parancsmagok Azure Automation runbookok való futtatásához telepítenie kell néhány további Azure PowerShell az az modulokat. A modulok telepítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg Azure Automation-fiókját, és navigáljon a **modulokhoz**.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="A modulok navigálása":::

2. Keresse meg a katalógust, és importálja a következő modulokat: **az. accounts**, **az. Network**, **az. Automation** és **az az. profil**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Modulok keresése és importálása" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. runbook létrehozása

1. A PowerShell-runbook létrehozásához navigáljon az Automation-fiókjához. A **folyamat automatizálása** területen válassza a **runbookok** csempét, majd válassza a **runbook létrehozása** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Hozzon létre runbook.":::

2. Válassza a **Létrehozás** lehetőséget a runbook létrehozásához.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Válassza a létrehozás lehetőséget.":::

3. Válassza ki az újonnan létrehozott runbook, majd válassza a **Szerkesztés** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Runbook szerkesztése":::

4. A **Szerkesztés** területen illessze be a PowerShell-szkriptet. A [példa parancsfájl](#script) módosítható és használható a ExpressRoute-átjárók figyelésére egy vagy több erőforráscsoport esetében.

   A példában szereplő parancsfájlban figyelje meg a következő beállításokat:

    * A tömb **$rgList** a ExpressRoute-átjárókkal rendelkező erőforráscsoportok listáját tartalmazza. A lista alapú ExpressRoute-átjárók testreszabhatók.
    * Az **$thresholdNumRoutes** változó határozza meg a ExpressRoute-átjáróról a helyszíni hálózatokra hirdetett hálózati előtagok számának küszöbértékét.

#### <a name="example-script"></a><a name="script"></a>Példaszkript

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. a runbook mentése és közzététele

1. Válassza a **Mentés** lehetőséget a runbook piszkozatának mentéséhez.
2. A **Közzététel** lehetőség kiválasztásával közzéteheti a runbook a runbook hivatalos verziójaként az Automation-fiókban.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Mentse és tegye közzé a runbook.":::

A PowerShell-parancsfájl futtatásakor a rendszer összegyűjti az értékek listáját:
 
* Erőforráscsoport

* ExpressRoute-átjáró neve

* Az első BGP-társ (peer1) IP-címe

* A második BGP-társ (peer2) IP-címe

* A ExpressRoute-átjáróról az első BGP-társra (peer1) hirdetett hálózati előtagok száma

* A ExpressRoute-átjáróról a második BGP-társra (peer2) hirdetett hálózati előtagok száma

* Időbélyeg

* Állapot, besorolás:

  * "OK", ha az útvonalak száma kisebb a küszöbértéknél.
  * "Riasztás", ha az útvonalak száma meghaladja a küszöbértéket
  * "Figyelmeztetés", ha a két BGP-társra hirdetett hálózati előtagok száma eltérő

* Riasztási üzenet az állapot részletes leírásához (OK, riasztás, figyelmeztetés)

A PowerShell-parancsfájl egy JSON-kimenetre konvertálja az összegyűjtött adatokat. A runbook a [Write-output PowerShell-](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  parancsmagot használja kimeneti adatfolyamként az információknak az ügyfél felé való továbbításához.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. a runbook ellenőrzése

A runbook létrehozása után érvényesíteni kell azt. Kattintson a **Start** gombra, és jelölje be a különböző feladatokhoz tartozó adatfolyamok kimenetét és hibáit.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="A runbook ellenőrzése" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Logikai alkalmazás létrehozása és konfigurálása

Azure Logic Apps a gyűjtemény és a műveletek összes folyamatának Orchestrator. A következő fejezetekben egy logikai alkalmazás használatával hoz létre munkafolyamatot.

### <a name="workflow"></a>Munkafolyamat

Ebben a munkafolyamatban egy olyan logikai alkalmazást fog létrehozni, amely rendszeresen figyeli a ExpressRoute-átjárókat. Ha új elemeket talál, a logikai alkalmazás mindegyikről e-mailt küld. Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Logic Apps munkafolyamat":::

### <a name="1-create-a-logic-app"></a>1. logikai alkalmazás létrehozása

A **Logic app Designerben** hozzon létre egy logikai alkalmazást az **üres logikai alkalmazás** sablon használatával. A lépéseket lásd: [Create Logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Üres sablon":::

### <a name="2-add-a-trigger"></a>2. trigger hozzáadása

Minden logikai alkalmazást egy trigger indít el. Egy eseményindító egy adott esemény bekövetkezésekor, vagy egy adott feltétel teljesülése esetén következik be. A Azure Logic Apps motor minden alkalommal elindít egy logikai alkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot.

Az előre definiált időütemezésen alapuló logikai alkalmazások rendszeres futtatásához adja hozzá a beépített **Ismétlődés: ütemezéset** a munkafolyamathoz. A keresőmezőbe írja be az **Schedule** kifejezést. Válassza az **Eseményindítók** lehetőséget. Az eseményindítók listából válassza az **Ismétlődés ütemezése** lehetőséget.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Ismétlődés: ütemezése":::

Az ismétlődés ütemezése Eseményindítóban megadhatja az időzónát és az ismétlődést a munkafolyamat megismétléséhez. Az intervallum és a gyakoriság együtt határozza meg a logikai alkalmazás eseményindítójának ütemezését. Az ésszerű minimális ismétlődési gyakoriság megállapításához vegye figyelembe a következő tényezőket:

* Az Automation-runbook található PowerShell-szkript végrehajtásához időt vesz igénybe. A futtatókörnyezet a figyelni kívánt ExpressRoute-átjárók számától függ. A túl rövid ismétlődési gyakoriság miatt A rendszer A feladatok várólistáját fogja eredményezni.

* A PowerShell-parancsfájl feladatokként fut a háttérben. Nem azonnal indul el; a változó késleltetése után fut.

* Túl rövid ismétlődési gyakoriság az Azure ExpressRoute-átjárók szükségtelen terhelését eredményezi.

A munkafolyamat-konfiguráció végén megtekintheti az ismétlődés gyakoriságának konzisztenciáját, ha a munkafolyamatot néhányszor futtatja, majd ellenőrzi az eredményt a **futtatások előzményeiben**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="A képernyőképen az ismétlődési időköz és a gyakoriság értékei láthatók." lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. feladatok létrehozása

A logikai alkalmazások más alkalmazásokhoz, szolgáltatásokhoz és platformokhoz is hozzáférnek, bár az összekötők. A munkafolyamat következő lépése egy összekötő kiválasztása a korábban definiált Azure Automation-fiók eléréséhez.

1. **Logic apps Designerben** az **Ismétlődés** alatt válassza az **új lépés** lehetőséget. A **válasszon egy műveletet** és a keresőmezőbe az **összes** kijelölése elemet.
2. A keresőmezőbe írja be a **Azure Automation** és a keresés kifejezést. Válassza a **létrehozási feladatot**. A **létrehozási feladatot** a rendszer a korábban létrehozott Automation-runbook használatára fogja használni.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Feladat létrehozása":::

3. Jelentkezzen be egy egyszerű szolgáltatásnév használatával. Használhat meglévő szolgáltatásnevet, vagy létrehozhat egy újat is. Új egyszerű szolgáltatásnév létrehozásához tekintse meg az [erőforrások elérésére képes Azure ad-szolgáltatásnév létrehozása a portál használatával](../active-directory/develop/howto-create-service-principal-portal.md)című témakört. Válassza **a kapcsolat egyszerű szolgáltatásnév** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Képernyőkép, amely a &quot;kapcsolat egyszerű szolgáltatással&quot; művelettel jelölt &quot;Ismétlődés&quot; szakaszt jeleníti meg.":::

4. Adja meg a **kapcsolatok nevét**, adja meg az **ügyfél-azonosítót** (alkalmazás-azonosító), az **ügyfél titkos kulcsát** és a **bérlői azonosítóját**. Ezután kattintson a **Létrehozás** elemre.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Az egyszerű szolgáltatással való kapcsolat":::

5. A **feladat létrehozása** lapon az egyszerű szolgáltatásnak rendelkeznie kell a "Reader" szerepkörrel az Automation-fiókot futtató **erőforráscsoporthoz** , és az Automation- **fiók**"Automation-feladat operátora". Továbbá győződjön meg arról, hogy a **Runbook neve** új paraméterként lett hozzáadva.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="A képernyőfelvételen a feladatok értékének ismétlődése látható, ahol ellenőrizheti a Runbook nevét." lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. a feladatok kimenetének beolvasása

1. Válassza az **Új lépés** lehetőséget. Keressen rá a "Azure Automation" kifejezésre. A **műveletek** listából válassza a **feladatok kimenetének beolvasása** elemet.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Feladat kimenetének lekérése":::

2. A **feladatok kimenetének beolvasása** lapon adja meg az Automation-fiókhoz való hozzáféréshez szükséges adatokat. Válassza ki a használni kívánt **előfizetést, erőforráscsoportot** és **Automation-fiókot** . Kattintson a **Job ID (feladattípus** ) mezőre. Amikor megjelenik a **dinamikus tartalom** lista, válassza a **feladatütemezés** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="AZONOSÍTÓJÚ feladatok" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. a JSON értelmezése

A "Azure Automation Create Job Action" (előző lépések) kimenetében található információk JSON-objektumot hoznak létre. Logic Apps a **JSON** elemzése egy beépített művelet, amellyel felhasználóbarát tokeneket hozhat létre a tulajdonságok és azok értékei JSON-tartalomban való létrehozásához. Ezeket a tulajdonságokat használhatja a munkafolyamatban.

1. Adjon hozzá egy műveletet. A **feladatok kimenetének beolvasása – >művelet** alatt válassza az **új lépés** lehetőséget.
2. Az ehhez a művelethez tartozó összekötők kereséséhez a **művelet kiválasztása** keresési mezőbe írja be a "JSON elemzése" kifejezést. A **műveletek** listában válassza ki a használni kívánt adatműveletek esetében a **JSON** elemzése műveletet.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="JSON elemzése":::

3. Kattintson a **Content (tartalom** ) mezőre. Amikor megjelenik a dinamikus tartalom lista, válassza a **tartalom** elemet.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Képernyőfelvétel: a JSON-elemzés párbeszédpanel, ahol a tartalom ki van választva." lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. A JSON-elemzéshez sémára van szükség. A séma az Automation-runbook kimenetének használatával hozható létre. Nyisson meg egy új webböngésző-munkamenetet, futtassa az Automation runbook, és ragadja meg a kimenetet. Térjen vissza az **Logic apps JSON-adatok** elemzése művelethez. A lap alján válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Minta hasznos adatok használata séma létrehozásához":::

5. A **JSON-adattartalom beviteléhez vagy beillesztéséhez** illessze be az Automation-runbook kimenetét, és válassza a **kész** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Minta adattartalom beillesztése" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. A rendszer automatikusan létrehoz egy sémát a JSON bemeneti adattartalom elemzésével.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Séma előállítása" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. változó definiálása és inicializálása

A munkafolyamat ezen lépésében létrehozunk egy feltételt, amely e-mailben küldi el a riasztást. Az e-mail szövegtörzsének rugalmas, egyéni formázása esetén a munkafolyamat egy kiegészítő változót is bevezet.

1. A **feladatok kimenetének beolvasása művelet** alatt válassza az **új lépés** lehetőséget. A keresőmezőbe keresse meg és válassza ki a **változókat**.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="A képernyőképen megjelenik a művelet kiválasztása párbeszédpanel, amely változóval rendelkezik a keresőmezőbe, és a kiválasztott változók szerepelnek a listában.":::

2. A **műveletek** listából válassza a **változó inicializálása** műveletet.

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Változók inicializálása":::

3. Adja meg a változó nevét. A **Típus mezőben** válassza a **karakterlánc** lehetőséget. A változó **értéke** később lesz hozzárendelve a munkafolyamatban.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="A képernyőképen az inicializálási változóhoz társított elemzési JSON látható, ahol megadhatja a nevet, a típust és az értéket." lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. hozzon létre egy "for each" műveletet

A JSON elemzését követően a JSON- **adatok** elemzése művelet a *törzs* kimenetében tárolja a tartalmat. A kimenet feldolgozásához létrehozhat egy "for each" hurkot egy vagy több műveletet a tömb minden elemén.

1. A **változó inicializálása** területen válassza **a művelet hozzáadása** lehetőséget. A keresőmezőbe írja be a "for each" kifejezést a szűrőként.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Képernyőfelvétel: a művelet kiválasztása párbeszédpanel, amely a keresési mezőben és a kijelölt vezérlőelemben is megjelenik.":::

2. A **műveletek** listából válassza ki az **egyes vezérlők** műveleteit.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="Mindegyik vezérlőhöz":::

3. Kattintson a **Kimenet kiválasztása az előző lépésekből** szövegmezőbe. Amikor megjelenik a **dinamikus tartalom** lista, válassza ki a **törzset**, amely az elemzett JSON kimenete.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Képernyőfelvétel: az egyesekhez társított inicializált változó, amely tartalmazza az előző lépések kimenetének kiválasztása szövegmezőt.":::

4. A JSON-törzs minden eleméhez meg kell határozni egy feltételt. A műveleti csoportban válassza a **vezérlés** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Vezérlés":::

5. A **műveletek** listában válassza a **feltétel – vezérlés** elemet. A Condition-Control egy vezérlő struktúra, amely összehasonlítja a munkafolyamatban lévő, meghatározott értékekkel vagy mezőkkel kapcsolatos adatait. Ezt követően különböző műveleteket is megadhat, amelyek attól függően futnak, hogy az adott állapot megfelel-e a feltételnek.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Feltétel-ellenőrzés":::

6. A **feltétel** gyökerében módosítsa a logikai műveletet a következőre: **vagy**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Vagy" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. A ExpressRoute-átjáró által a két BGP-társnak meghirdetett hálózati előtagok számának bejelölése. Az útvonalak száma a "numRoutePeer1" és a "numRoutePeer2" elemben érhető el **dinamikus tartalomban**. Az érték mezőbe írja be a **numRoutePeer1** értékét.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. Ha további sort szeretne felvenni a feltétellel, válassza a **Hozzáadás – > sor hozzáadása** elemet. A második mezőben a **dinamikus tartalom** területen válassza a **numRoutePeer2** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. A logikai feltétel akkor igaz, ha a két dinamikus változó, a numRoute1 vagy a numRoute2 egyike meghaladja a küszöbértéket. Ebben a példában a küszöbérték 160 (a 200-útvonalak maximális értékének 80%-ában) rögzített. A küszöbértéket úgy módosíthatja, hogy az megfeleljen az igényeinek. A konzisztencia esetében az értéknek meg kell egyeznie a runbook PowerShell-parancsfájlban használt értékkel.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Logikai feltétel":::

10. **Ha az igaz** értékre van állítva, formázza és hozza létre azokat a műveleteket, amelyek e-mailben küldik el a riasztást. A * * területen válasszon ki egy műveletet, és válassza a **változók** lehetőséget.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="Igaz érték esetén":::

11. A változók területen válassza a **művelet hozzáadása** lehetőséget. A **műveletek** listában válassza a **változó beállítása** lehetőséget.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Képernyőkép a &quot;változók&quot; szakaszról, a &quot;műveletek&quot; fülre, és a &quot;változó beállítása&quot; kijelölve.":::

12. A **név** mezőben válassza ki a korábban létrehozott **EmailBody** nevű változót. Az **Érték mezőben** illessze be a riasztási e-mail formátumához szükséges HTML-szkriptet. A **dinamikus tartalom** használatával adja meg a JSON-törzs értékeit. A beállítások megadása után az eredmény az, hogy a **emailBody** változó a riasztással kapcsolatos összes információt tartalmazza HTML formátumban.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Változó beállítása":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. adja hozzá az e-mail-összekötőt

Logic Apps számos e-mail-összekötőt biztosít. Ebben a példában egy Outlook-összekötőt adunk hozzá, amely e-mailben küldi el a riasztást. A **set változó** területen válassza **a művelet hozzáadása** lehetőséget. A **válasszon műveletet** mezőben írja be az "e-mail küldése" kifejezést a keresőmezőbe.

1. Válassza az **Office 365 Outlook** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="E-mail küldése":::

2. A **műveletek** listában válassza az **E-mail küldése (v2)** lehetőséget.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="E-mail küldése (v2)":::

3. Jelentkezzen be, és hozzon létre egy, az Office 365 Outlookhoz való kapcsolódást.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Bejelentkezés":::

4. A **törzs** mezőben kattintson a **dinamikus tartalom hozzáadása** lehetőségre. A dinamikus tartalom panelen adja hozzá a **emailBody** változót. Töltse ki a **tárgyat** és **a** mezőket.

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Törzs":::

5. Az **E-mail küldése (v2)** művelet befejezi a munkafolyamat telepítését.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="E-mail küldése v2" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. munkafolyamat-érvényesítés

Az utolsó lépés a munkafolyamat-ellenőrzés. Az **Logic apps áttekintése** lapon válassza az **trigger futtatása** lehetőséget. Válassza az **Ismétlődés** lehetőséget. A munkafolyamat nyomon követhető és ellenőrizhető a **futtatási előzményekben**.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Trigger futtatása":::

## <a name="next-steps"></a>Következő lépések

További információ a munkafolyamat testreszabásáról: [Azure Logic apps](../logic-apps/logic-apps-overview.md).