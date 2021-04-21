---
title: PremiumV3 szintű csomag konfigurálása
description: Az új PremiumV3 tarifacsomagra való méretezéssel megtudhatja, hogyan teljesítményének növelése Azure App Service webes, mobil- és API-alkalmazásai számára.
keywords: app service, azure app service, méret, méretezhető, app service-csomag, app service ára
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fbd841626e2a074bc0a35cd1b4ac094e267b34a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833345"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>PremiumV3 csomag konfigurálása Azure App Service

Az új **PremiumV3** tarifacsomag gyorsabb processzorokat, SSD-tárolót és a meglévő tarifacsomagok memória–mag arányának négyszeresét biztosítja (a **PremiumV2** csomag duplája). A teljesítménybeli előnnyel pénzt takaríthat meg, ha alkalmazásait kevesebb példányon futtatja. Ebből a cikkből megtudhatja, hogyan hozhat létre egy alkalmazást a **PremiumV3** szinten, vagy hogyan skálázható fel egy alkalmazás **a PremiumV3 szintre.**

## <a name="prerequisites"></a>Előfeltételek

Egy alkalmazás **PremiumV3-ra** való horizontális felskálálozáshoz olyan Azure App Service-alkalmazásra van szükség, amely a PremiumV3-asnál alacsonyabb tarifacsomagban fut, és az alkalmazásnak egy, a **PremiumV3-ast** támogató App Service-környezetben kell futnia.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3 rendelkezésre állás

A **PremiumV3** szint natív és tárolóalkalmazások számára is elérhető, beleértve a Windows-tárolókat és a Linux-tárolókat is.

> [!NOTE]
> Az előzetes verzió időszakában a **Prémium** szintű tárolók szinten futó Windows-tárolók továbbra is az adott módon működnek, de a **Prémium** szintű tárolók továbbra is előzetes verzióban maradnak. A **PremiumV3** csomag a Prémium szintű tárolószint hivatalos **pótlása.** 

**A PremiumV3** egyes Azure-régiókban érhető el, és a rendelkezésre állás további régiókban folyamatosan bővül. Ha meg kell tudni, hogy elérhető-e az Ön régiójában, futtassa a következő Azure [CLI-parancsot](../cloud-shell/overview.md)a Azure Cloud Shell:

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Alkalmazás létrehozása a PremiumV3 szinten

A App Service alkalmazás tarifacsomagja abban a [](overview-hosting-plans.md) App Service tarifacsomagban van meghatározva, amelyn fut. Létrehozhat egy App Service csomag önmagában vagy az alkalmazás létrehozásának részeként.

A csomag App Service konfigurálásakor válassza <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **Tarifacsomag lehetőséget.** 

Válassza **az Éles** lehetőséget, majd a **P1V3,** **P2V3** vagy **P3V3** lehetőséget, majd kattintson az **Alkalmaz gombra.**

![Képernyőkép az alkalmazáshoz ajánlott tarifacsomagról.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Ha nem látja a **P1V3,** **P2V3,** és **P3V3** lehetőségeket, vagy ha a beállítások szürkével érhetők el, akkor a **PremiumV3** valószínűleg nem érhető el a mögöttes App Service-környezetben, amely az App Service-tervet tartalmazza. További [részletekért lásd: Felskálálás](#unsupported) nem támogatott erőforráscsoportból és régió kombinációból.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Meglévő alkalmazás felskál méretezése a PremiumV3 szintre

Mielőtt egy meglévő alkalmazást a **PremiumV3** szintre skáláz, ellenőrizze, hogy elérhető-e **a PremiumV3.** További információ: [PremiumV3 rendelkezésre állás.](#availability) Ha nem érhető el, tekintse meg a felskálálás nem támogatott erőforráscsoportból és [régiókombinációból való felskálálást.](#unsupported)

Az üzemeltetési környezettől függően a felméretezéshez további lépésekre lehet szükség. 

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>nyissa meg a App Service alkalmazás oldalát.

Az alkalmazás bal oldali navigációs sávján válassza App Service **felskálás (App Service csomag) lehetőséget.**

![Képernyőkép az App Service-csomag felskál való felskál való méretezéséről.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Válassza **az Éles** lehetőséget, majd a **P1V3,** **P2V3** vagy **P3V3** lehetőséget, majd kattintson az **Alkalmaz gombra.**

![Képernyőkép az alkalmazáshoz ajánlott tarifacsomagról.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Ha a művelet sikeresen befejeződik, az alkalmazás áttekintő oldala azt mutatja, hogy most már **PrémiumV3 szinten** van.

![Képernyőkép a PremiumV3 tarifacsomagról az alkalmazás áttekintő oldalán.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Ha hibaüzenetet kap

Egyes App Service csomag nem skálázhat fel a PremiumV3 szintre, ha a mögöttes App Service nem támogatja a PremiumV3 szintet. További [részletekért lásd: Felskálálás](#unsupported) nem támogatott erőforráscsoportból és régió kombinációból.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Felskálálás nem támogatott erőforráscsoportból és régiókombinációból

Ha az alkalmazás olyan App Service-környezetben fut, ahol a **PremiumV3** nem érhető el, vagy ha az alkalmazás olyan régióban fut, amely jelenleg nem támogatja a **PremiumV3-as** virtuális gépeket, újra üzembe kell helyeznie az alkalmazást a **PremiumV3** előnyeinek kihasznál érdekében.  Erre két lehetősége van:

- Hozzon létre egy alkalmazást egy új erőforráscsoportban és egy új App Service tervvel. A csomag App Service válasszon ki egy **PremiumV3** szintet. Ez a lépés biztosítja, hogy a App Service terv egy, a PremiumV3-as verzióval támogatott üzembe **helyezési egységben üzemel.** Ezután újra üzembe kell vennie az alkalmazáskódot az újonnan létrehozott alkalmazásban. Még ha a App Service is alacsonyabb szintre skáláz a költségek csökkentése érdekében, mindig felskálinthat **a PremiumV3** szintre, mivel az üzembe helyezési egység támogatja azt.
- Ha az alkalmazás már fut egy meglévő **Prémium** szinten, akkor az összes alkalmazásbeállítással, kapcsolati sztringekkel és üzembe helyezési konfigurációval klónozhatja az alkalmazást egy új, **PremiumV3-t** használó App Service-csomag új erőforráscsoportjában.

    ![Az alkalmazás klónozását bemutató képernyőkép.](media/app-service-configure-premium-tier/clone-app.png)

    Az Alkalmazás **klónozása** lapon létrehozhat egy App Service-tervet a **PremiumV3** használatával a kívánt régióban, és megadhatja a klónozni kívánt alkalmazásbeállításokat és konfigurációt.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Áthelyezés Prémium szintű tárolóról Prémium V3 termékváltozatra

Ha olyan alkalmazása van, amely az előzetes verziójú Premium Container SKU-t használja, és át szeretne lépni az új Prémium V3 termékváltozatra, akkor újra üzembe kell helyeznie az alkalmazást a **PremiumV3 előnyeinek kihasználhoz.** Ehhez tekintse meg a felskálálás nem támogatott erőforráscsoportból és régió kombinációból való felskálálás [első beállítását](#scale-up-from-an-unsupported-resource-group-and-region-combination)

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

A **PremiumV3** szinten szkriptekkel automatizálhatja az alkalmazások létrehozását az [Azure CLI](/cli/azure/install-azure-cli) vagy [a Azure PowerShell.](/powershell/azure/)

### <a name="azure-cli"></a>Azure CLI

Az alábbi parancs létrehoz egy App Service _P1V3-ban._ A következőben futtathatja Cloud Shell. A lehetőségei `--sku` a következőek: P1V3, _P2V3_ és _P3V3._

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancs létrehoz egy App Service _P1V3-ban._ A `-WorkerSize` beállításai: _Kicsi,_ _Közepes_ és _Nagy._

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>További erőforrások

[Alkalmazás horizontális felskálskál használata az Azure-ban](manage-scale-up.md) 
 [Példányszám manuális vagy automatikus skálázható](../azure-monitor/autoscale/autoscale-get-started.md)
