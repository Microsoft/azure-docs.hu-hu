---
title: PremiumV3 szintű csomag konfigurálása
description: Ismerje meg, hogyan javíthatja a teljesítményt a webes, mobil-és API-alkalmazásokban Azure App Service az új PremiumV3 díjszabási szintjére való skálázással.
keywords: app service, azure app service, méret, méretezhető, app service-csomag, app service ára
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: c187a1521762c0b69d42c81f464694729d3c02f7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594227"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Azure App Service PremiumV3-szintjeinek konfigurálása

Az új **PremiumV3** díjszabása gyorsabb processzorokkal, SSD-tárolóval és a meglévő árképzési szintek (Double the **PremiumV2** szint) a memória és a mag közötti arányát adja meg. A teljesítmény előnye, hogy pénzt takaríthat meg az alkalmazások kevesebb példányon való futtatásával. Ebből a cikkből megtudhatja, hogyan hozhat létre alkalmazást **PremiumV3** -szinten, vagy hogyan méretezheti fel az alkalmazást a **PremiumV3** szintjére.

## <a name="prerequisites"></a>Előfeltételek

Egy alkalmazás **PremiumV3** való felskálázásához rendelkeznie kell egy olyan Azure app Service alkalmazással, amely alacsonyabb, mint a **PremiumV3**, és az alkalmazásnak egy olyan app Service-telepítésben kell futnia, amely támogatja a PremiumV3-t.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3 rendelkezésre állása

A **PremiumV3** -csomag natív és Container-alkalmazásokhoz egyaránt elérhető, beleértve a Windows-tárolókat és a Linux-tárolókat is.

> [!NOTE]
> Az előzetes verzió időszakában a **prémium szintű tároló** szintjén futó Windows-tárolók továbbra is működni fognak, de a **prémium szintű tárolók** szintjének továbbra is előzetes verzióban marad. A **PremiumV3** szint a **prémium szintű tárolók** szintjének hivatalos helyettesítője. 

A **PremiumV3** néhány Azure-régióban elérhető, és a további régiókban való rendelkezésre állás folyamatosan bővül. Ha szeretné megtekinteni, hogy elérhető-e az Ön régiójában, futtassa a következő Azure CLI-parancsot a [Azure Cloud Shellban](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Alkalmazás létrehozása a PremiumV3 szinten

Az App Service alkalmazás díjszabási szintje a [app Service](overview-hosting-plans.md) -csomagban van definiálva. Létrehozhat egy App Service tervet saját maga vagy az alkalmazás létrehozása részeként is.

Ha a App Service csomagot a <a href="https://portal.azure.com" target="_blank">Azure Portalban</a>konfigurálja, válassza az **árképzési szintet**. 

Válassza az **éles környezet**, majd az **P1V3**, a **P2V3** vagy a **P3V3** lehetőséget, majd kattintson az **alkalmaz** gombra.

![Képernyőkép az alkalmazás ajánlott díjszabási szintjeiről.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Ha nem látja a **P1V3**, a **P2V3** és a **P3V3** lehetőséget, vagy ha a beállítások szürkén jelennek meg, akkor a **PremiumV3** valószínűleg nem érhető el a app Service csomagot tartalmazó mögöttes app Service-telepítésben. További részletekért tekintse meg a nem [támogatott erőforráscsoport és régió kombinációjának skálázása](#unsupported) című témakört.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Meglévő alkalmazás vertikális felskálázása PremiumV3-szintűre

Mielőtt egy meglévő alkalmazást **PremiumV3** -szinten méretezni, győződjön meg arról, hogy a **PremiumV3** elérhető. További információ: [PremiumV3 rendelkezésre állása](#availability). Ha nem érhető el, tekintse meg [a vertikális felskálázás egy nem támogatott erőforráscsoport és régió kombinációjával](#unsupported)című témakört.

Az üzemeltetési környezettől függően a vertikális felskálázás további lépéseket is igényelhet. 

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>nyissa meg a app Service alkalmazás lapját.

A App Service alkalmazás lap bal oldali navigációs sávján válassza a vertikális **felskálázás (App Service terv)** lehetőséget.

![Az App Service-csomag vertikális felskálázását bemutató képernyőkép.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Válassza az **éles környezet**, majd az **P1V3**, a **P2V3** vagy a **P3V3** lehetőséget, majd kattintson az **alkalmaz** gombra.

![Képernyőkép az alkalmazás ajánlott díjszabási szintjeiről.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Ha a művelet sikeresen befejeződik, az alkalmazás áttekintő lapja azt mutatja, hogy most már **PremiumV3** szinten van.

![Képernyőfelvétel: az PremiumV3 díjszabási szintje az alkalmazás Áttekintés oldalán.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Ha hibaüzenetet kap

Egyes App Service csomagok nem méretezhetők a PremiumV3 szintjére, ha a mögöttes App Service üzemelő példány nem támogatja a PremiumV3. További részletekért tekintse meg a nem [támogatott erőforráscsoport és régió kombinációjának skálázása](#unsupported) című témakört.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Vertikális felskálázás egy nem támogatott erőforráscsoporthoz és régió-kombinációból

Ha az alkalmazás olyan App Service üzemelő példányban fut, ahol a **PremiumV3** nem érhető el, vagy ha az alkalmazás olyan régióban fut, amely jelenleg nem támogatja a **PremiumV3**-et, akkor a **PremiumV3** kihasználásához újra kell telepítenie az alkalmazást.  Erre két lehetősége van:

- Hozzon létre egy alkalmazást egy új erőforráscsoporthoz, és egy új App Service-csomaggal. A App Service terv létrehozásakor válasszon ki egy **PremiumV3** -szintet. Ez a lépés biztosítja, hogy a App Servicei terv a **PremiumV3** támogató központi telepítési egységbe legyen telepítve. Ezután telepítse újra az alkalmazás kódját az újonnan létrehozott alkalmazásba. Még akkor is, ha a App Service-csomagot alacsonyabb szinten méretezi a költségek megtakarítása érdekében, bármikor **PremiumV3** a biztonsági mentést, mert az üzembe helyezési egység támogatja azt.
- Ha az alkalmazás már egy meglévő **prémium** szinten fut, akkor az alkalmazást az alkalmazás összes beállításával, a kapcsolódási karakterláncokkal és az üzembe helyezési konfigurációval együtt egy új, a **PremiumV3**-t használó app Service-csomagból is elvégezheti.

    ![Az alkalmazás klónozását bemutató képernyőkép.](media/app-service-configure-premium-tier/clone-app.png)

    A **klónozási alkalmazás** oldalon létrehozhat egy app Service tervet a **PremiumV3** használatával a kívánt régióban, és megadhatja a klónozott alkalmazás beállításait és konfigurációját.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Áttérés prémium szintű tárolóról prémium v3 SKU-ra

Ha van olyan alkalmazás, amely a Premium Container SKU-t használja, és az új prémium v3 SKU-ra szeretne áttérni, újra kell telepítenie az alkalmazást, hogy kihasználhassa a **PremiumV3**. Ennek elvégzéséhez tekintse meg a vertikálisan nem [támogatott erőforráscsoport és a régió kombinációjának](#scale-up-from-an-unsupported-resource-group-and-region-combination) első lehetőségét.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az **PremiumV3** szinten automatizálhatja az alkalmazások létrehozását az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell](/powershell/azure/)használatával.

### <a name="azure-cli"></a>Azure CLI

A következő parancs egy App Service tervet hoz létre a _P1V3_-ben. A Cloud Shell futtathatja. A következő lehetőségei: `--sku` P1V3, _P2V3_ és _P3V3_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancs egy App Service tervet hoz létre a _P1V3_-ben. A `-WorkerSize` _kis_, _közepes_ és _nagyméretű_ beállítások.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>További erőforrások

[Alkalmazások vertikális Felskálázása az Azure](manage-scale-up.md) 
 -ban [Példányszám manuális vagy automatikus méretezése](../azure-monitor/autoscale/autoscale-get-started.md)
