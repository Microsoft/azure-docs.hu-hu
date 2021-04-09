---
title: Azure-beli virtuálisgép-méretezési csoportok vertikális skálázása
description: A virtuális gépek vertikális skálázása a riasztások figyelésére válaszul Azure Automation
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: b172f1f7137b53e98384d92c9c709694eaf0b7e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594500"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Vertikális automata méretezés a virtuálisgép-méretezési csoportokkal

Ez a cikk azt ismerteti, hogyan lehet vertikálisan méretezni az Azure [Virtual Machine Scale Setst](https://azure.microsoft.com/services/virtual-machine-scale-sets/) az Újraépítés nélkül vagy anélkül. 

A vertikális skálázás, más néven vertikális *fel* *-és leskálázás,* a virtuális gépek (VM) méretének növelését és csökkentését jelenti a számítási feladatokra válaszul. Hasonlítsa össze ezt a viselkedést [horizontális skálázással](virtual-machine-scale-sets-autoscale-overview.md), más néven *kibővíthető* és *méretezhető*, ahol a virtuális gépek száma a munkaterheléstől függően módosul.

Az Újraépítés azt jelenti, hogy egy meglévő virtuális gépet eltávolítanak, és egy újat cserélnek. A virtuálisgép-méretezési csoportokban lévő virtuális gépek méretének növelésével vagy csökkentésével bizonyos esetekben a meglévő virtuális gépeket szeretné átméretezni, és megőrizni az adatait, míg más esetekben új virtuális gépeket kell telepítenie az új méretre. Ez a dokumentum mindkét esetet magában foglalja.

A vertikális skálázás akkor lehet hasznos, ha:

* A virtuális gépekre épülő szolgáltatások kihasználtsága (például hétvégén) használatban van. A virtuális gép méretének csökkentése csökkentheti a havi költségeket.
* A virtuális gép méretének növelése a nagyobb igények kielégítése érdekében további virtuális gépek létrehozása nélkül.

Beállíthatja a vertikális skálázást úgy, hogy a virtuálisgép-méretezési csoport mérőszám-alapú riasztásai alapján induljon el. A riasztás aktiválása után egy webhookot indít el, amely elindítja a méretezési csoport felfelé vagy lefelé skálázására képes runbook. A vertikális skálázás a következő lépésekkel konfigurálható:

1. Hozzon létre egy Azure Automation fiókot futtató képességgel.
2. Importálja Azure Automation vertikális méretezési runbookok a virtuálisgép-méretezési csoportokhoz az előfizetésében.
3. Vegyen fel egy webhookot a runbook.
4. Hozzon létre egy riasztást a virtuálisgép-méretezési csoportba egy webhook-értesítés használatával.

> [!NOTE]
> Az első virtuális gép mérete miatt a méretezhető méretre korlátozható, mert a fürt aktuális virtuális gépe más méreteinek rendelkezésre állása is a-ben települ. A cikkben használt közzétett Automation-runbookok ezt az esetet vesszük figyelembe, és csak az alábbi virtuálisgép-méret párokon belül méretezhetők. Ez azt jelenti, hogy egy Standard_D1v2 virtuális gép nem lesz hirtelen felskálázásra Standard_G5 vagy méretezése Basic_A0re. A virtuálisgép-méretek vertikális fel-és leskálázása is korlátozott, és nem támogatott. A következő méretek közül választhat:
> 
> | VM-méretek méretezése pár tag | Tag |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Futtatási képességgel rendelkező Azure Automation fiók létrehozása
Először létre kell hoznia egy Azure Automation fiókot, amely a virtuálisgép-méretezési csoport példányainak méretezéséhez használt runbookok tárolja. A közelmúltban [Azure Automation](https://azure.microsoft.com/services/automation/) bemutatta a "futtató fiók" funkciót, amely lehetővé teszi az egyszerű szolgáltatás beállítását a runbookok automatikus futtatásához a felhasználó nevében. További információkért lásd:

* [Forgatókönyvek hitelesítése Azure-beli futtató fiókkal](../automation/manage-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Azure Automation vertikális skálázási runbookok importálása az előfizetésbe

A virtuálisgép-méretezési csoportok vertikális skálázásához szükséges runbookok már közzé van téve a Azure Automation Runbook-galériában. Az előfizetésbe való importáláshoz kövesse a cikkben ismertetett lépéseket:

* [Runbook- és modulkatalógusok az Azure Automationhöz](../automation/automation-runbook-gallery.md)

Válassza a tallózási katalógus lehetőséget a Runbookok menüben:

![Importálandó runbookok][runbooks]

Az importálni kívánt runbookok láthatók. Válassza ki a runbook annak alapján, hogy a vertikális skálázást szeretné-e Újraépítés nélkül:

![Runbookok-gyűjtemény][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Webhook hozzáadása a runbook

Miután importálta a runbookok, vegyen fel egy webhookot a runbook, hogy egy virtuálisgép-méretezési csoport riasztása aktiválható legyen. A Runbook webhook létrehozásának részleteit a cikk ismerteti:

* [Webhookok Azure Automation](../automation/automation-webhooks.md)

> [!NOTE]
> Győződjön meg arról, hogy a webhook URI-JÁT a webhook párbeszédpanel bezárása előtt másolja, mivel a következő szakaszban erre a címnek szüksége lesz.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Riasztás hozzáadása a virtuálisgép-méretezési csoporthoz

Az alábbiakban egy PowerShell-szkript látható, amely bemutatja, hogyan adhat hozzá riasztást a virtuálisgép-méretezési csoportokhoz. A következő cikkből megtudhatja, hogyan kérheti le a metrika nevét a riasztás bekapcsolásához: [Azure monitor általános mérőszámok automatikus skálázása](../azure-monitor/autoscale/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Javasoljuk, hogy a riasztáshoz egy ésszerű időablakot állítson be, hogy elkerülje a vertikális skálázást és a kapcsolódó szolgáltatások megszakítását. Vegyünk egy legalább 20-30 percet tartalmazó ablakot. Ha el szeretné kerülni a megszakítást, vegye figyelembe a horizontális skálázást.
> 
> 

A riasztások létrehozásával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [PowerShell-minták Azure Monitor](../azure-monitor/powershell-samples.md)
* [Platformfüggetlen CLI-minták Azure Monitor](../azure-monitor/cli-samples.md)

## <a name="summary"></a>Összefoglalás

Ez a cikk egyszerű vertikális skálázási példákat mutat be. Ilyen építőelemek – Automation-fiók, runbookok, webhookok, riasztások – számos különböző eseményt kapcsolhat össze testreszabott műveletekkel.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
