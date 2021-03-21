---
title: Virtuális gépek migrálása a Resource Managerbe az Azure CLI használatával
description: Ez a cikk végigvezeti az erőforrások platformon támogatott áttelepítésének klasszikusról Azure Resource Managerre az Azure CLI használatával.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 671b27f927c91397d2aacd98cb7b500d8197d1c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669345"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>IaaS-erőforrások migrálása a klasszikusból Resource Manager-alapú környezetbe az Azure CLI használatával

> [!IMPORTANT]
> Napjainkban a IaaS virtuális gépek 90%-a [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)használ. 2020. február 28-án a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen megszűnnek. [További]( https://aka.ms/classicvmretirement) információ erről az elavult szolgáltatásról, valamint arról, [hogy Ön hogyan befolyásolja Önt](classic-vm-deprecation.md#how-does-this-affect-me).

Ezek a lépések bemutatják, hogyan használhatja az Azure parancssori felület (CLI) parancsait az infrastruktúra szolgáltatásként (IaaS) való áttelepítésére a klasszikus üzemi modellből a Azure Resource Manager telepítési modellbe. A cikkhez a [klasszikus Azure parancssori](/cli/azure/install-classic-cli)felület szükséges. Mivel az Azure CLI csak Azure Resource Manager erőforrásokra alkalmazható, nem használható ehhez az áttelepítéshez.

> [!NOTE]
> Az itt leírt összes művelet idempotens. Ha a nem támogatott funkció vagy a konfigurációs hiba nem megfelelő, javasoljuk, hogy próbálkozzon újra az előkészítés, a megszakítás vagy a végrehajtás művelettel. A platform ezután újra próbálkozik a művelettel.
> 
> 

<br>
Az alábbi folyamatábra azt határozza meg, hogy milyen sorrendben kell végrehajtani a lépéseket egy áttelepítési folyamat során.

![Képernyőkép a migrálási lépésekről](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>1. lépés: Felkészülés az áttelepítésre
Íme néhány ajánlott eljárás, amelyet ajánlunk a IaaS-erőforrások klasszikusról Resource Managerbe való áttelepítésének kiértékelése során:

* Olvassa el a nem [támogatott konfigurációk vagy szolgáltatások listáját](migration-classic-resource-manager-overview.md). Ha olyan virtuális gépekkel rendelkezik, amelyek nem támogatott konfigurációkat vagy szolgáltatásokat használnak, javasoljuk, hogy várjon, amíg bejelenti a szolgáltatás/konfiguráció támogatását. Azt is megteheti, hogy eltávolítja ezt a szolgáltatást, vagy kilép az adott konfigurációból, hogy engedélyezze az áttelepítést, ha az megfelel az igényeinek.
* Ha olyan automatizált parancsfájlokkal rendelkezik, amelyek jelenleg telepítik az infrastruktúrát és az alkalmazásokat, próbáljon meg hasonló tesztet létrehozni a parancsfájlok áttelepítéshez való használatával. Azt is megteheti, hogy a Azure Portal használatával is beállíthatja a mintavételezési környezeteket.

> [!IMPORTANT]
> Az Application Gateway-átjárók jelenleg nem támogatottak a Klasszikusból a Resource Managerbe való áttelepítéshez. Egy klasszikus virtuális hálózat Application Gateway-átjáróval való áttelepítéséhez távolítsa el az átjárót, mielőtt egy előkészítési művelet futtatása után áthelyezi a hálózatot. Az áttelepítés befejezése után csatlakoztassuk újra az átjárót Azure Resource Manager. 
>
>A ExpressRoute-áramkörökhöz egy másik előfizetésben csatlakozó ExpressRoute-átjárók nem telepíthetők át automatikusan. Ilyen esetekben távolítsa el a ExpressRoute-átjárót, telepítse át a virtuális hálózatot, és hozza létre újra az átjárót. További információkért lásd: [ExpressRoute-áramkörök és társított virtuális hálózatok áttelepítése a Klasszikusból a Resource Manager](../expressroute/expressroute-migration-classic-resource-manager.md) -alapú üzemi modellbe.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>2. lépés: az előfizetés beállítása és a szolgáltató regisztrálása
Áttelepítési forgatókönyvek esetén a környezetet a klasszikus és a Resource Managerhez is be kell állítania. [Telepítse az Azure CLI](/cli/azure/install-classic-cli) -t, és [válassza ki az előfizetését](/cli/azure/authenticate-azure-cli).

Jelentkezzen be a fiókjába.

```azurecli
azure login
```

Az alábbi parancs használatával válassza ki az Azure-előfizetést.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> A regisztráció egy egyszeri lépés, de az áttelepítés megkísérlése előtt egyszer kell elvégezni. Regisztráció nélkül az alábbi hibaüzenet jelenik meg 
> 
> *BadRequest: az előfizetés nincs regisztrálva áttelepítésre.* 
> 
> 

Regisztrálja az áttelepítési erőforrás-szolgáltatót az alábbi parancs használatával. Vegye figyelembe, hogy bizonyos esetekben a parancs időtúllépést eredményez. A regisztráció azonban sikeres lesz.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

Várjon öt percet, amíg a regisztráció befejeződik. A jóváhagyás állapotát a következő parancs használatával tekintheti meg. A folytatás előtt győződjön meg arról, hogy a RegistrationState `Registered` .

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

Most váltson a CLI-re a `asm` módba.

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>3. lépés: Győződjön meg arról, hogy rendelkezik-e elegendő Azure Resource Manager virtuálisgép-vCPU a jelenlegi üzembe helyezési vagy VNET Azure-régiójában
Ehhez a lépéshez át kell váltania `arm` módba. Ezt a következő paranccsal teheti meg.

```azurecli
azure config mode arm
```

A következő CLI-paranccsal ellenőrizhető, hogy az aktuálisan hány vCPU Azure Resource Manager. További információ a vCPU-kvótákkal kapcsolatban: [korlátok és a Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Ha elkészült a lépés ellenőrzésével, visszaválthat `asm` módba.

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>4. lépés: 1. lehetőség – virtuális gépek migrálása egy felhőalapú szolgáltatásban
A következő parancs használatával szerezze be a Cloud Services listáját, majd válassza ki az áttelepíteni kívánt felhőalapú szolgáltatást. Vegye figyelembe, hogy ha a felhőalapú szolgáltatásban lévő virtuális gépek virtuális hálózaton vannak, vagy webes/feldolgozói szerepkörökkel rendelkeznek, hibaüzenet jelenik meg.

```azurecli
azure service list
```

Futtassa a következő parancsot a felhőalapú szolgáltatás központi telepítési nevének a részletes kimenetből való lekéréséhez. A legtöbb esetben a központi telepítés neve megegyezik a felhőalapú szolgáltatás nevével.

```azurecli
azure service show <serviceName> -vv
```

Először is ellenőrizze, hogy a következő parancsok segítségével áttelepítheti-e a Cloud Service-t:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Készítse elő a virtuális gépeket a Cloud Service-ben áttelepítésre. Két lehetőség közül választhat.

Ha a virtuális gépeket egy platform által létrehozott virtuális hálózatra szeretné áttelepíteni, használja a következő parancsot.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Ha a Resource Manager-alapú üzemi modellben meglévő virtuális hálózatra kíván áttelepítést végezni, használja a következő parancsot.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

Az előkészítési művelet sikeres végrehajtása után megtekintheti a részletes kimenetet, hogy beolvassa a virtuális gépek áttelepítési állapotát, és győződjön meg arról, hogy azok `Prepared` állapotban vannak.

```azurecli
azure vm show <vmName> -vv
```

A CLI vagy a Azure Portal használatával vizsgálja meg az előkészített erőforrások konfigurációját. Ha nem áll készen az áttelepítésre, és vissza szeretné állítani a régi állapotot, használja a következő parancsot.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

Ha az előkészített konfiguráció jól néz ki, az alábbi parancs használatával továbbíthatja és véglegesítheti az erőforrásokat.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>4. lépés: 2. lehetőség – virtuális gépek migrálása virtuális hálózatban
Válassza ki az áttelepíteni kívánt virtuális hálózatot. Vegye figyelembe, hogy ha a virtuális hálózat olyan webes/feldolgozói szerepköröket vagy virtuális gépeket tartalmaz, amelyek nem támogatott konfigurációval rendelkeznek, egy érvényesítési hibaüzenet jelenik meg.

Az alábbi parancs használatával szerezze be az előfizetésben található összes virtuális hálózatot.

```azurecli
azure network vnet list
```

A kimenet a következőhöz hasonlóan fog kinézni:

![Képernyőkép a parancssorról a Kiemelt teljes virtuális hálózat nevével.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

A fenti példában a **virtualNetworkName** a **"csoport classicubuntu16 classicubuntu16"** teljes neve.

Először is ellenőrizze, hogy a következő paranccsal telepítheti-e át a virtuális hálózatot:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Készítse elő az áttelepítéshez választott virtuális hálózatot a következő parancs használatával.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

A CLI vagy a Azure Portal használatával vizsgálja meg az előkészített virtuális gépek konfigurációját. Ha nem áll készen az áttelepítésre, és vissza szeretné állítani a régi állapotot, használja a következő parancsot.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

Ha az előkészített konfiguráció jól néz ki, az alábbi parancs használatával továbbíthatja és véglegesítheti az erőforrásokat.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>5. lépés: Storage-fiók átmigrálása
Ha elkészült a virtuális gépek áttelepítésével, javasoljuk, hogy telepítse át a Storage-fiókot.

Készítse elő a Storage-fiókot az áttelepítéshez a következő parancs használatával

```azurecli
azure storage account prepare-migration <storageAccountName>
```

A CLI vagy a Azure Portal használatával vizsgálja meg az előkészített Storage-fiók konfigurációját. Ha nem áll készen az áttelepítésre, és vissza szeretné állítani a régi állapotot, használja a következő parancsot.

```azurecli
azure storage account abort-migration <storageAccountName>
```

Ha az előkészített konfiguráció jól néz ki, az alábbi parancs használatával továbbíthatja és véglegesítheti az erőforrásokat.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>Következő lépések

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](migration-classic-resource-manager-community-tools.md)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md)
