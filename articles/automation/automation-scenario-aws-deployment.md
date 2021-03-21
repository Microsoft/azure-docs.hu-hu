---
title: Amazon Web Services virtuális gép üzembe helyezése Azure Automation runbook
description: Ez a cikk azt ismerteti, hogyan lehet automatizálni egy Amazon Web Services virtuális gép létrehozását.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 03f1f1659d120a2aa1b827063cb2bc84138f3655
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896070"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Amazon Web Services virtuális gép üzembe helyezése runbook

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Automationt egy virtuális gép kiépítéséhez az Amazon Web Service-(AWS-) előfizetésben, és adjon egy adott nevet a virtuális gépnek, amely az AWS a virtuális gép "címkézési" kifejezésére utal.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy Azure Automation fiókkal és egy Amazon Web Services (AWS) előfizetéssel. Azure Automation-fiók beállításával és az AWS-előfizetés hitelesítő adataival való konfigurálásával kapcsolatos további információkért tekintse át a következő témakört: a [hitelesítés konfigurálása a Amazon Web Services](automation-config-aws-account.md)használatával. Ennek a fióknak a továbblépés előtt létre kell hoznia vagy frissítenie kell az AWS-előfizetés hitelesítő adataival, ahogy az alábbi részekben hivatkozik erre a fiókra.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell-modul üzembe helyezése

A virtuális gép üzembe helyezési runbook az AWS PowerShell-modult használja a munkához. A következő lépésekkel adhatja hozzá a modult az AWS-előfizetés hitelesítő adataival konfigurált Automation-fiókhoz.  

1. Nyissa meg a webböngészőt, és navigáljon a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AWSPowerShell/) , és kattintson a **telepítés a Azure Automation gombra**.<br><br> ![AWS PS-modul importálása](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. A hitelesítés után a rendszer átAzure Portal irányítja az Azure-beli bejelentkezési oldalra, majd a következő oldalon jelenik meg:<br><br> ![Modul importálása lap](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Válassza ki a használni kívánt Automation-fiókot, majd kattintson **az OK** gombra az üzembe helyezés megkezdéséhez.

   > [!NOTE]
   > Amikor Azure Automation egy PowerShell-modult importál, kibontja a parancsmagokat. A tevékenységek addig nem jelennek meg, amíg az Automation teljesen befejezte a modul importálását és a parancsmagok kicsomagolását. Ez a folyamat eltarthat néhány percig.  
   > <br>

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Kattintson az **eszközök** csempére, és az eszközök ablaktáblán válassza a **modulok** elemet.
3. A modulok oldalon láthatja a **AWSPowerShell** modult a listában.

## <a name="create-aws-deploy-vm-runbook"></a>AWS üzembe helyezése VM-runbook létrehozása

Miután telepítette az AWS PowerShell-modult, létrehozhat egy runbook, amely egy PowerShell-parancsfájl használatával automatizálja a virtuális gépeket az AWS-ben. Az alábbi lépések bemutatják, hogyan használható a natív PowerShell-parancsfájl a Azure Automationban.  

> [!NOTE]
> A parancsfájlra vonatkozó további beállítások és információk a [PowerShell-galériaban](https://www.powershellgallery.com/packages/New-AwsVM/)érhetők el.
> 

1. Töltse le a PowerShell-parancsfájlt New-AwsVM a PowerShell-galéria egy PowerShell-munkamenet megnyitásával, és írja be a következő parancsot:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. A Azure Portal nyissa meg Automation-fiókját, és válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.  
3. A Runbookok lapon válassza a **Runbook hozzáadása** elemet.
4. A runbook hozzáadása panelen válassza a **gyors létrehozás** lehetőséget egy új runbook létrehozásához.
5. A Runbook tulajdonságok ablaktábláján írja be a Runbook nevét.
6. A **Runbook típusa** legördülő listából válassza ki a **PowerShell** elemet, majd kattintson a **Létrehozás** gombra.<br><br> ![Runbook létrehozása panel](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. Amikor megjelenik a PowerShell-Runbook szerkesztése lap, másolja és illessze be a PowerShell-szkriptet a Runbook authoring vászonba.<br><br> ![Runbook PowerShell-parancsfájl](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    A példaként szolgáló PowerShell-parancsfájl használatakor vegye figyelembe a következőket:

    * A runbook számos alapértelmezett paraméter értéket tartalmaz. Szükség esetén értékelje ki az összes alapértelmezett értéket és frissítést.
    * Ha az AWS hitelesítő adatait egy másként megadott hitelesítőadat-eszközként tárolta `AWScred` , frissítenie kell a parancsfájlt az 57-es sorban, hogy az megfeleljen ennek megfelelően.  
    * Ha az AWS CLI-parancsokkal dolgozik a PowerShellben, különösen ebben a példában runbook, meg kell adnia az AWS régiót. Ellenkező esetben a parancsmagok meghiúsulnak. Az AWS megtekintése témakörben az AWS- [régiót](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) az AWS-eszközök PowerShell-dokumentumában találhatja meg további részletekért.  

8. Az AWS-előfizetésből származó rendszerkép-nevek listájának lekéréséhez indítsa el a PowerShell ISE-t, és importálja az AWS PowerShell-modult. A hitelesítése az AWS `Get-AutomationPSCredential` -ben az ISE-környezetben való kiváltásával történik `AWScred = Get-Credential` . Ez az utasítás kéri a hitelesítő adatait, és megadhatja a felhasználónévhez és a jelszóhoz tartozó titkos hozzáférési kulcshoz tartozó hozzáférési kulcs AZONOSÍTÓját. 

      ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
      ```
        
    A rendszer a következő kimenetet adja vissza:<br><br>
   ![AWS-lemezképek beolvasása](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
9. Másolja és illessze be az egyik rendszerkép nevét egy Automation-változóba úgy, ahogy az a runbook hivatkozik `$InstanceType` . Mivel ebben a példában az ingyenes AWS rétegű előfizetést használja, a **T2. microt** használja a runbook példához.  
10. Mentse a runbook, majd kattintson a **Közzététel** gombra a runbook közzétételéhez, majd az **Igen** gombra, amikor a rendszer kéri.

### <a name="test-the-aws-vm-runbook"></a>Az AWS virtuális gép runbook tesztelése

1. Győződjön meg arról, hogy a runbook létrehoz egy nevű adategységet `AWScred` az AWS-ben való hitelesítéshez, vagy frissítse a parancsfájlt a hitelesítő eszköz nevének megadásához.    
2. Ellenőrizze az új runbook, és győződjön meg arról, hogy az összes paraméter értékének frissítése szükséges.
Győződjön meg arról, hogy az AWS PowerShell-modul a Azure Automationba lett importálva.  
3. A Azure Automation-ben állítsa be a részletes **naplózási** **rekordokat** , és ha szükséges, a runbook művelet **naplózása és** **a** nyomkövetés a következőre lehetőséget.<br><br> ![Runbook naplózás és nyomkövetés ](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png) .  
4. Kattintson a **Start** gombra a runbook elindításához, majd kattintson az **OK** gombra a Start runbook panel megnyitásakor.
5. A Runbook indítása panelen adja meg a virtuális gép nevét. Fogadja el a parancsfájlban előre konfigurált egyéb paraméterek alapértelmezett értékeit. A runbook-feladatok elindításához kattintson **az OK** gombra.<br><br> ![New-AwsVM runbook elindítása](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. A panel bezárásához.
7. Megtekintheti a feladatok előrehaladását, és megtekintheti a kimeneti streameket a runbook-feladatok ablaktábla **összes naplójának** kiválasztásával.<br><br> ![Stream kimenete](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. Ha ellenőrizni szeretné, hogy a virtuális gép üzembe helyezése folyamatban van-e, jelentkezzen be az AWS felügyeleti konzolba, ha jelenleg nincs bejelentkezve.<br><br> ![AWS-konzol üzembe helyezett virtuális gép](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Következő lépések
 
* Ha szeretné megtudni, hogy milyen runbookok támogatottak, tekintse meg a[Azure Automation runbook-típusok](automation-runbook-types.md)című témakört.
* A runbookok használatával kapcsolatban lásd: [Runbookok kezelése a Azure Automationban](manage-runbooks.md).
* A PowerShell részleteiért lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).
* A parancsfájl-támogatással kapcsolatban lásd: [natív PowerShell-parancsfájl-támogatás a Azure Automationban](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
