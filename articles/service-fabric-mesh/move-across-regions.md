---
title: Új Service Fabric Mesh áthelyezése egy másik régióba
description: Az erőforrásokat Service Fabric Mesh helyezheti át, ha üzembe helyez egy másolatot az aktuális sablonról egy új Azure-régióban.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: bce61a00ae1b6b451927b43dbcf19ddb615f79a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861174"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Új Service Fabric Mesh áthelyezése egy másik Azure-régióba

> [!IMPORTANT]
> A Azure Service Fabric Mesh előzetes verziója ki lettvezetve. Az új üzemelő példányok a továbbiakban nem lesznek engedélyezettek a Service Fabric Mesh API-n keresztül. A meglévő üzemelő példányok támogatása 2021. április 28-ig folytatódik.
> 
> Részletekért lásd a [Azure Service Fabric Mesh előzetes verzió kieső kiadását.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Ez a cikk azt ismerteti, hogyan helyezze át Service Fabric Mesh alkalmazását és erőforrásait egy másik Azure-régióba. Több okból is áthelyezhet erőforrásokat egy másik régióba. Például kimaradás esetén, ha csak bizonyos régiókban elérhető funkciókat vagy szolgáltatásokat szerez be a belső szabályzat- és cégirányítási követelményeknek való megfeleléshez, vagy a kapacitástervezési követelményekre reagálva.

 [Service Fabric Mesh nem támogatja az erőforrások](../azure-resource-manager/management/move-support-resources.md#microsoftservicefabricmesh) Azure-régiók közötti közvetlen áthelyezését. Az erőforrásokat azonban közvetve áthelyezheti úgy, hogy üzembe helyez egy másolatot az aktuális Azure Resource Manager-sablonról az új célterületen, majd átirányítja a bejövő forgalmat és a függőségeket az újonnan létrehozott Service Fabric Mesh alkalmazásba.

## <a name="prerequisites"></a>Előfeltételek

* Bejövőforgalom-vezérlő [(például](../application-gateway/index.yml)Application Gateway ), amely közvetítőként szolgál az ügyfelek és a Service Fabric Mesh közötti forgalom útválasztásához
* Service Fabric Mesh (előzetes verzió) rendelkezésre állása a cél Azure-régióban ( `westus` , `eastus` vagy `westeurope` )

## <a name="prepare"></a>Előkészítés

1. A legújabb üzembe helyezésből exportálja a Azure Resource Manager sablont és paramétereket, és "pillanatképet" készít a Service Fabric Mesh aktuális állapotáról. Ehhez kövesse a Sablon exportálása az üzembe helyezés [után](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) a következővel: Azure Portal. Az Azure [CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)a [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)vagy a [REST API.](/rest/api/resources/resourcegroups/exporttemplate)

2. Ha lehetséges, [exportálja az ugyanabban](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) az erőforráscsoportban található egyéb erőforrásokat a célterületen való ismételt üzembe álláshoz.

3. Tekintse át (és szükség esetén szerkessze) az exportált sablont, és győződjön meg arról, hogy a meglévő tulajdonságértékeket szeretné használni a célterületen. Az új (Azure-régió) egy paraméter, amely az újratelepítés során `location` lesz megadva.

## <a name="move"></a>Áthelyezés

1. Hozzon létre egy új erőforráscsoportot (vagy használjon egy meglévőt) a célterületen.

2. Az exportált sablonnal kövesse az [](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) Erőforrások üzembe helyezése egyéni sablonból az Azure Portal. Az Azure [CLI,](../azure-resource-manager/templates/deploy-cli.md)a [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)vagy a [REST API.](../azure-resource-manager/templates/deploy-rest.md)

3. A kapcsolódó erőforrások, például az [Azure Storage-fiókok](../storage/common/storage-account-move.md)áthelyezésével kapcsolatos útmutatásért tekintse meg az Azure-erőforrások régiók közötti áthelyezésével kapcsolatos témakörben felsorolt egyes szolgáltatások [útmutatóját.](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)

## <a name="verify"></a>Ellenőrzés

1. Az üzembe helyezés befejezése után tesztelje az alkalmazásvégpont(ak)t az alkalmazás funkcióinak ellenőrzéséhez.

2. Az alkalmazás állapotát az alkalmazás állapotának ellenőrzéséhez[(az mesh app show)](/cli/azure/mesh/app#az_mesh_app_show)és az alkalmazásnaplók és ([az mesh code-package-log](/cli/azure/mesh/code-package-log)) parancsok áttekintéséhez is használhatja az Azure Service Fabric Mesh CLI [használatával.](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli)

## <a name="commit"></a>Véglegesítés

Miután megerősítette a Service Fabric Mesh-alkalmazás megfelelő funkcióit a célterületen, konfigurálja a bejövőforgalom-vezérlőt (például Application Gateway [),](../application-gateway/redirect-overview.md)hogy átirányítsa a forgalmat az új alkalmazásba.

## <a name="clean-up-source-resources"></a>Forráserőforrások megtisztítása

Az alkalmazás áthelyezésének befejezéséhez Service Fabric Mesh [a forrásalkalmazást és/vagy](../azure-resource-manager/management/delete-resource-group.md)a szülő erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

* [Azure-erőforrások áthelyezése másik régióba](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)
* [Azure-erőforrások régiók közötti áthelyezésének támogatása](../azure-resource-manager/management/move-support-resources.md)
* [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Erőforrás-áthelyezési műveletek támogatása](../azure-resource-manager/management/move-support-resources.md
)
