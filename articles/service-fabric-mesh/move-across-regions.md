---
title: Új Service Fabric Mesh áthelyezése másik régióba
description: Az erőforrásokat Service Fabric Mesh helyezheti át, ha üzembe helyez egy másolatot az aktuális sablonról egy új Azure-régióban.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 9f3fcdc56b4e8d7873872212cb62f57a7669b459
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726627"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Új Service Fabric Mesh áthelyezése egy másik Azure-régióba

> [!IMPORTANT]
> A Azure Service Fabric Mesh előzetes verziója ki lettvezetve. Az új üzembe helyezések a továbbiakban nem lesznek engedélyezettek a Service Fabric Mesh API-n keresztül. A meglévő üzemelő példányok támogatása 2021. április 28-ig folytatódik.
> 
> További részletekért lásd: Azure Service Fabric Mesh preview retirement ( előzetes [verzió kieső)](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez a cikk azt ismerteti, hogyan helyezze át a Service Fabric Mesh alkalmazást és annak erőforrásait egy másik Azure-régióba. Több okból is áthelyezhet erőforrásokat egy másik régióba. Például kimaradás esetén, ha csak bizonyos régiókban elérhető funkciókat vagy szolgáltatásokat szerez be a belső szabályzat- és cégirányítási követelményeknek való megfelelés, vagy a kapacitástervezési követelményekre reagálva.

 [Service Fabric Mesh nem támogatja az erőforrások](../azure-resource-manager/management/move-support-resources.md#microsoftservicefabricmesh) Azure-régiók közötti közvetlen áthelyezésének képességét. Az erőforrásokat azonban közvetve áthelyezheti úgy, hogy üzembe helyez egy másolatot az aktuális Azure Resource Manager-sablonról az új célterületen, majd átirányítja a bejövő forgalmat és a függőségeket az újonnan létrehozott Service Fabric Mesh alkalmazásba.

## <a name="prerequisites"></a>Előfeltételek

* Bejövőforgalom-vezérlő (például Application Gateway [),](../application-gateway/index.yml)amely közvetítőként szolgál az ügyfelek és a Service Fabric Mesh közötti forgalom útválasztásához
* Service Fabric Mesh (előzetes verzió) rendelkezésre állása a cél Azure-régióban ( `westus` , `eastus` vagy `westeurope` )

## <a name="prepare"></a>Előkészítés

1. A legújabb üzemelő példányból exportálja a Azure Resource Manager sablont és paramétereket, és "pillanatképet" készít a Service Fabric Mesh aktuális állapotáról. Ehhez kövesse a Sablon exportálása az üzembe helyezés [után az](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) Azure Portal. Az Azure [CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)a [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)vagy a [REST API.](/rest/api/resources/resourcegroups/exporttemplate)

2. Ha lehetséges, [exportálja az ugyanabban](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) az erőforráscsoportban található egyéb erőforrásokat a célterületen való ismételt üzembe álláshoz.

3. Tekintse át (és szükség esetén szerkessze) az exportált sablont, és győződjön meg arról, hogy a meglévő tulajdonságértékeket a célterületen használni szeretné. Az új (Azure-régió) paramétert az újratelepítés során `location` fogja megírni.

## <a name="move"></a>Áthelyezés

1. Hozzon létre egy új erőforráscsoportot (vagy használjon egy meglévőt) a cél régióban.

2. Az exportált sablonnal kövesse az Erőforrások üzembe helyezése egyéni sablonból [az](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) Azure Portal. Az Azure [CLI,](../azure-resource-manager/templates/deploy-cli.md)a [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)vagy a [REST API.](../azure-resource-manager/templates/deploy-rest.md)

3. A kapcsolódó erőforrások, például az [Azure Storage-fiókok](../storage/common/storage-account-move.md)áthelyezésével kapcsolatos útmutatásért tekintse meg az Azure-erőforrások régiók közötti áthelyezésével kapcsolatos témakörben felsorolt egyes [szolgáltatásokra vonatkozó útmutatást.](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)

## <a name="verify"></a>Ellenőrzés

1. Az üzembe helyezés befejezése után tesztelje az alkalmazásvégpont(ak)t az alkalmazás funkcióinak ellenőrzéséhez.

2. Az alkalmazás állapotát az alkalmazás állapotának ellenőrzéséhez[(az mesh app show)](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show)és az alkalmazásnaplók és ([az mesh code-package-log](/cli/azure/ext/mesh/mesh/code-package-log)) parancsok áttekintéséhez is használhatja a Azure Service Fabric Mesh CLI [használatával.](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli)

## <a name="commit"></a>Véglegesítés

Miután megerősítette a Service Fabric Mesh-alkalmazás egyenértékű funkcióit a célterületen, konfigurálja a [bejövőforgalom-vezérlőt](../application-gateway/redirect-overview.md)(például Application Gateway ), hogy átirányítsa a forgalmat az új alkalmazásba.

## <a name="clean-up-source-resources"></a>Forráserőforrások tisztítása

A Service Fabric Mesh alkalmazás áthelyezésének befejezéséhez törölje a [forrásalkalmazást és/vagy a szülő erőforráscsoportot.](../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Következő lépések

* [Azure-erőforrások áthelyezése másik régióba](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)
* [Azure-erőforrások régiók közötti áthelyezésének támogatása](../azure-resource-manager/management/move-support-resources.md)
* [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Erőforrás-áthelyezési műveletek támogatása](../azure-resource-manager/management/move-support-resources.md
)
