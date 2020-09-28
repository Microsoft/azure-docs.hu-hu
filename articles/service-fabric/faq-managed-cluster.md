---
title: Service Fabric felügyelt fürtökkel kapcsolatos gyakori kérdések
description: Service Fabric felügyelt fürtökkel kapcsolatos gyakori kérdések, beleértve a képességeket, a használati eseteket és a gyakori forgatókönyveket.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 73815e793357dd38244dd429f9056c4c6a0bfff1
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410331"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Service Fabric felügyelt fürtök gyakran ismételt kérdései

A Service Fabric felügyelt fürtökkel (előzetes verzió) és azok használatának módjával kapcsolatban számos gyakori kérdés szerepel. Ez a dokumentum ismerteti a gyakori kérdéseket és a rájuk adott válaszokat.

## <a name="general"></a>Általános kérdések

### <a name="what-are-service-fabric-managed-clusters"></a>Mik azok a Service Fabric felügyelt fürtök?

Service Fabric felügyelt fürtök a Service Fabric fürterőforrás-modell fejlődése, amely megkönnyíti a fürtök üzembe helyezését és kezelését. A Service Fabric felügyelt fürt a Azure Resource Manager beágyazási modellt használja, így a felhasználónak csak egyetlen fürterőforrás-erőforrást kell meghatároznia és üzembe helyeznie a ma üzembe helyezni kívánt számos független erőforráshoz képest (virtuálisgép-méretezési csoport, Load Balancer, IP stb.).

### <a name="what-regions-are-supported-in-the-preview"></a>Mely régiók támogatottak az előzetes verzióban?

A Service Fabric felügyelt fürtök előzetes verziójának támogatott régiói a következők:,,,, `centraluseuap` `eastus2euap` `eastasia` `northeurope` `westcentralus` és `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Használhatom a meglévő Service Fabric-fürtem helyi áttelepítését egy felügyelt fürterőforrás-erőforrásra?

Ekkor létre kell hoznia egy új Service Fabric fürterőforrás-erőforrást az új Service Fabric felügyelt fürterőforrás-típus használatához.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Van-e további díj a felügyelt fürtök Service Fabric?

Nem, a Service Fabric felügyelt fürthöz nem tartozik további díj a fürthöz szükséges mögöttes számítási, tárolási és hálózati erőforrások után.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Van olyan új SLA, amelyet a Service Fabric felügyelt fürterőforrás vezet be?

Az SLA nem változik az aktuális Service Fabric erőforrás-modellből.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Mi a különbség az alapszintű és a standard SKU-fürt között?

Egy alapszintű SKU-fürt azt jelenti, hogy a konfigurációk többségét a Service Fabric erőforrás-szolgáltató biztosítja. Az alapszintű SKU-fürtök tesztelésre és üzem előtti környezetekre való használatra készültek. A standard SKU-fürtök lehetővé teszik a felhasználók számára, hogy az igényeiknek megfelelő módon konfigurálja a fürtöt. További információ: [Service Fabric felügyelt fürt SKU](overview-managed-cluster.md#service-fabric-managed-cluster skus.md) -ban további részleteket.

## <a name="cluster-deployment-and-management"></a>Fürt üzembe helyezése és kezelése

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Egyéni szkript-bővítményeket futtatok a virtuálisgép-méretezési csoporton, továbbra is használhatom a felügyelt Service Fabric erőforrásokkal?

Igen, a csomópont típusú virtuálisgép-bővítmények is megadhatók. További információért lásd a csomópont típusú bővítmény mintáját.

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Szeretném, hogy csak belső terheléselosztó legyen elérhető?

Jelenleg nem lehet csak belső terheléselosztó. A hálózati biztonsági csoport szabályainak zárolását javasoljuk a nemkívánatos bejövő/kimenő forgalom blokkolására.

### <a name="can-i-autoscale-my-cluster"></a>Használhatom-e a fürtem méretezését? 
Az automatikus skálázás jelenleg nem érhető el az előzetes verzióban.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Telepíthetem a fürtöt a rendelkezésre állási zónák között? 
A több rendelkezésre állási zóna fürtök jelenleg nem érhetők el az előzetes verzióban.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Választhatok a fürt futtatókörnyezetének automatikus és manuális frissítései között? 
Az előzetes verzióban az összes futtatókörnyezet frissítése automatikusan megtörténik.

## <a name="applications"></a>Alkalmazások

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Létezik-e helyi fejlesztési élmény Service Fabric felügyelt fürtökhöz?

A helyi fejlesztési élmény változatlan marad a meglévő Service Fabric fürtökön. További információ: .NET- [alkalmazás létrehozása](https://docs.microsoft.com/azure/service-fabric/service-fabric-quickstart-dotnet) a helyi fejlesztési felülettel kapcsolatos további részletekért.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Telepíthetem az alkalmazásaikat Azure Resource Manager erőforrásként?

Az előzetes verzióban nem telepíthet alkalmazásokat Azure Resource Manager erőforrásként. Az alkalmazásokat úgy kell telepíteni, hogy a PowerShell vagy a CLI használatával közvetlenül a fürthöz csatlakoznak. Ezt a funkciót hozzá kell adni, mielőtt a rendszer felveszi a Service Fabric fürtök általános rendelkezésre állását.
