---
title: Azure Automation runbookok hozzáadása Site Recovery helyreállítási tervekhez
description: Megtudhatja, hogyan bővítheti a helyreállítási terveket a Azure Automation for vész-helyreállításra Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: ramamill
ms.openlocfilehash: a141280338632fdad7053cbbe76c8bdf2797443d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89424871"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Azure Automation-runbookok hozzáadása helyreállítási tervekhez

Ez a cikk azt ismerteti, hogyan integrálható Azure Automation runbookok, hogy kiterjessze [Azure site Recovery](site-recovery-overview.md) helyreállítási terveket. Bemutatjuk, hogy miként automatizálható az olyan alapszintű feladatok automatizálása, amelyek egyébként manuális beavatkozást igényelnek, és hogyan lehet egy többlépéses helyreállítást egyetlen kattintással végrehajtható műveletre konvertálni.

## <a name="recovery-plans"></a>Helyreállítási tervek 

Helyreállítási terveket akkor használhat, ha feladatátvételt végez a helyszíni gépeken vagy az Azure-beli virtuális gépeken. A helyreállítási tervek segítségével egy rendszeres helyreállítási folyamatot határozhat meg, amely meghatározza a gépek feladatátvételét, valamint azt, hogy a feladatátvételt követően hogyan indulnak és állíthatók vissza. 

A helyreállítási nagyméretű alkalmazások összetettek lehetnek. A helyreállítási tervek segítenek megszabni a sorrendet, így a helyreállítás konzisztens, ismételhető és automatizált. A helyreállítási tervekben a parancsfájlok segítségével automatizálhatja a feladatokat, valamint Azure Automation runbookok is. A tipikus példák egy Azure-beli virtuális gépen a feladatátvételt követően, vagy a virtuális gépen futó alkalmazás újrakonfigurálását is konfigurálják.

- [További](recovery-plan-overview.md) információ a helyreállítási tervekről.
- [További](../automation/automation-runbook-types.md) információ a Azure Automation runbookok.



## <a name="runbooks-in-recovery-plans"></a>Runbookok a helyreállítási tervekben

Azure Automation fiókot és runbookok kell hozzáadnia egy helyreállítási tervhez. A rendszer meghívja a runbook a helyreállítási terv futtatásakor.

- Az Automation-fiók bármely Azure-régióban lehet, és a Site Recovery-tárolóval megegyező előfizetésben kell lennie. 
- A runbook egy helyreállítási tervben is futtathatók a feladatátvétel során egy elsődleges helyről a másodlagosra, vagy a másodlagos helyről az elsődlegesre történő feladat-visszavétel során.
- A helyreállítási tervekben a runbookok sorosan, egymás után, a set sorrendben futnak.
- Ha a helyreállítási tervben a virtuális gépek különböző csoportokban való runbookok vannak konfigurálva, a helyreállítási terv csak akkor fog folytatódni, ha az Azure minden virtuális gépet futtat.
- A helyreállítási tervek továbbra is futnak, még akkor is, ha egy parancsfájl meghibásodik.

### <a name="recovery-plan-context"></a>Helyreállítási terv környezete

Parancsfájl futtatásakor a helyreállítási terv környezetét befecskendezi a runbook. A környezet tartalmazza a táblázatban összefoglalt változókat.

| **Változó neve** | **Leírás** |
| --- | --- |
| RecoveryPlanName |Helyreállítási terv neve. A nevén alapuló műveletekben használatos. |
| FailoverType |Meghatározza, hogy teszt vagy éles feladatátvétel van-e. 
| FailoverDirection | Meghatározza, hogy a helyreállítás elsődleges vagy másodlagos helyre történik-e. |
| GroupID |A csomag futásakor a helyreállítási tervben szereplő csoport számát azonosítja. |
| VmMap |A csoportban található összes virtuális gép tömbje. |
| VMMap kulcs |Egyedi kulcs (GUID) az egyes virtuális gépekhez. |
| SubscriptionId |Az Azure-előfizetés azonosítója, amelyben a virtuális gép létrejött. |
| ResourceGroupName | Azon erőforráscsoport neve, amelyben a virtuális gép található.
| Felhőszolgáltatásneve |Az Azure Cloud Service neve, amely alatt a virtuális gép létrejött. |
| RoleName |Az Azure-beli virtuális gép neve. |
| RecoveryPointId|A virtuális gép helyreállításának időbélyege. |

>[!Note]
>A "FailoverDirection" változó értéke "PrimaryToSecondary" lesz a feladatátvétel és a "SecondaryToPrimary" esetén a feladat-visszavétel esetén.

Az alábbi példa egy környezeti változót mutat be:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Ha a VMMap lévő összes virtuális gépet egy hurokban szeretné elérni, a következő kódot használhatja:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Aman Sharma blogja a [betakarítási felhőben](http://harvestingclouds.com) a [helyreállítási terv környezeti parancsfájljának](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)hasznos példája.



## <a name="before-you-start"></a>Előkészületek

- Ha még nem ismeri a Azure Automation, [regisztrálhat és](https://azure.microsoft.com/services/automation/) [letöltheti a minta parancsfájlokat](https://azure.microsoft.com/documentation/scripts/).
- Győződjön meg arról, hogy az Automation-fiók a következő modulokkal rendelkezik:
    - AzureRM. profil
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Minden modulnak kompatibilis verziónak kell lennie. A legegyszerűbb módszer az, hogy mindig az összes modul legújabb verzióját használja.



## <a name="customize-the-recovery-plan"></a>A helyreállítási terv testreszabása

1. A tárolóban válassza a **helyreállítási tervek (site Recovery) lehetőséget.**
2. Helyreállítási terv létrehozásához kattintson a **+ helyreállítási terv** elemre. [További információk](site-recovery-create-recovery-plans.md). Ha már rendelkezik helyreállítási tervvel, válassza ki a megnyitásához.
3. A helyreállítási terv lapon kattintson a **Testreszabás** elemre.

    ![Kattintson a Testreszabás gombra](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Kattintson a három pontra (...) az **1. csoport:** a  >  **post művelet hozzáadása** lehetőségre.
3. A **beszúrási művelet** területen ellenőrizze, hogy a **parancsfájl** ki van-e választva, majd adja meg a parancsfájl nevét (**Hello World**).
4. Adjon meg egy Automation-fiókot, és válasszon ki egy runbook. A parancsfájl mentéséhez kattintson **az OK** gombra. A szkript hozzá lett adva az **1. csoporthoz: utólagos lépések**.


## <a name="reuse-a-runbook-script"></a>Runbook-szkript újrafelhasználása

A külső változók használatával egyetlen runbook parancsfájlt is használhat több helyreállítási tervben. 

- [Azure Automation változók](../automation/shared-resources/variables.md) használatával tárolhatja a helyreállítási terv futtatásához szükséges paramétereket.
- Ha a helyreállítási terv nevét hozzáadja a változóhoz, az egyes helyreállítási tervekhez egyedi változókat hozhat létre. Ezután használja a változókat paraméterekként.
- A paramétereket módosíthatja a parancsfájl módosítása nélkül, de továbbra is megváltoztathatja a szkript működésének módját.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Egyszerű karakterlánc-változó használata runbook-parancsfájlban

Ebben a példában egy parancsfájl egy hálózati biztonsági csoport (NSG) bemenetét veszi át, és azt a helyreállítási tervben szereplő virtuális gépekre alkalmazza. 

1. Annak érdekében, hogy a parancsfájl képes legyen felderíteni, hogy melyik helyreállítási terv fut, használja a helyreállítási terv környezetét:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Jegyezze fel a NSG nevét és az erőforráscsoportot. Ezeket a változókat bemenetként használja a helyreállítási terv parancsfájljaihoz. 
1. Az Automation-fiók eszközeiben. hozzon létre egy változót, amely a NSG nevét tárolja. Adjon hozzá egy előtagot a változó nevéhez a helyreállítási terv nevével.

    ![NSG-név változó létrehozása](media/site-recovery-runbook-automation-new/var1.png)

2. Hozzon létre egy változót, amely a NSG erőforrás erőforráscsoport-nevét tárolja. Adjon hozzá egy előtagot a változó nevéhez a helyreállítási terv nevével.

    ![NSG-erőforráscsoport nevének létrehozása](media/site-recovery-runbook-automation-new/var2.png)


3.  A szkriptben használja ezt a hivatkozási kódot a változók értékeinek lekéréséhez:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  A runbook szereplő változók használatával alkalmazza a NSG a feladatátvételt használó virtuális gép hálózati adapterére:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Mindegyik helyreállítási tervhez hozzon létre független változókat, hogy újra fel tudja használni a parancsfájlt. Adjon hozzá egy előtagot a helyreállítási terv neve alapján. 

Ennek a forgatókönyvnek a teljes körű, végpontok közötti parancsfájlt ebben a [parancsfájlban](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)tekintheti át.


### <a name="use-a-complex-variable-to-store-more-information"></a>Összetett változó használata további információk tárolásához

Bizonyos esetekben előfordulhat, hogy nem tud külön változókat létrehozni az egyes helyreállítási tervekhez. Vegyünk egy olyan forgatókönyvet, amelyben egyetlen parancsfájlt szeretne hozzárendelni egy nyilvános IP-címet az adott virtuális gépeken. Egy másik esetben előfordulhat, hogy különböző NSG szeretne alkalmazni különböző virtuális gépeken (nem minden virtuális gépen). Vegye figyelembe:

- Létrehozhat egy olyan parancsfájlt, amely bármely helyreállítási tervhez újrahasznosítható.
- Minden helyreállítási terv változó számú virtuális gépet tartalmazhat.
- A SharePoint-helyreállítás például két előtéri végponttal rendelkezik. Egy alapszintű üzletági (LOB) alkalmazás csak egy előtérrel rendelkezik.
- Ebben a forgatókönyvben nem hozhat létre külön változókat az egyes helyreállítási tervekhez.

Az alábbi példában egy [összetett változót](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable) hozunk létre a Azure Automation fiókban.

Ehhez több értéket kell megadnia Azure PowerShell használatával.

1. A PowerShellben jelentkezzen be az Azure-előfizetésbe:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. A paraméterek tárolásához hozza létre a komplex változót a helyreállítási terv nevével:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. Ebben az összetett változóban a **VMDetails** a védett virtuális gép VIRTUÁLISGÉP-azonosítója. A virtuális gép AZONOSÍTÓjának lekéréséhez a Azure Portalban tekintse meg a virtuális gép tulajdonságait. Az alábbi képernyőfelvételen egy olyan változó látható, amely két virtuális gép részleteit tárolja:

    ![A virtuális gép AZONOSÍTÓjának használata GUID-ként](media/site-recovery-runbook-automation-new/vmguid.png)

4. Használja ezt a változót a runbook. Ha a megadott VM GUID a helyreállítási terv kontextusában található, alkalmazza a NSG a virtuális gépen:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. A runbook a helyreállítási terv környezetének virtuális gépeken keresztül hurkot kell készítenie. Győződjön meg arról, hogy a virtuális gép létezik a **$VMDetailsObjban**. Ha létezik, a NSG alkalmazásához nyissa meg a változó tulajdonságait:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

A különböző helyreállítási tervekhez ugyanazt a parancsfájlt használhatja. Adjon meg különböző paramétereket úgy, hogy a különböző változókban lévő helyreállítási tervnek megfelelő értéket tárolja.

## <a name="sample-scripts"></a>Mintaparancsfájlok

Ha a parancsfájlokat az Automation-fiókban szeretné üzembe helyezni, kattintson az **üzembe helyezés az Azure** -ban gombra.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Ez a videó egy másik példát is tartalmaz. Bemutatja, hogyan lehet helyreállítani egy kétrétegű WordPress-alkalmazást az Azure-ban:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Következő lépések

- [Azure Automation futtató fiók](../automation/manage-runas-account.md) megismerése
- Tekintse át [Azure Automation minta parancsfájlokat](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [További](site-recovery-failover.md) információ a feladatátvételek futtatásáról.
