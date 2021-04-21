---
title: Hozzáférés a hálózati beállításjegyzékhez megbízható Azure-szolgáltatás használatával
description: Egy megbízható Azure-szolgáltatáspéldány biztonságos hozzáférésének engedélyezése egy hálózat által korlátozott tároló-beállításjegyzékhez a rendszerképek leküldése vagy leküldése érdekében
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 4b0d7feb223bcfcec4e8b2c786b211f4e3c3c3eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785869"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>A megbízható szolgáltatások biztonságos hozzáférésének engedélyezése a hálózatra korlátozott tároló-beállításjegyzékhez (előzetes verzió)

Azure Container Registry engedélyezheti, hogy a megbízható Azure-szolgáltatások hozzáférjenek egy hálózati hozzáférési szabályokkal konfigurált beállításjegyzékhez. Ha a megbízható szolgáltatások engedélyezettek, a megbízható szolgáltatáspéldányok biztonságosan megkerülhetik a beállításjegyzék hálózati szabályait, és olyan műveleteket hajthatnak végre, mint a leküldéses vagy leküldéses rendszerképek. A szolgáltatáspéldány felügyelt identitását a rendszer a hozzáféréshez használja, és Azure-szerepkört kell hozzárendelnie, és hitelesítenie kell magát a beállításjegyzékben.

Futtassa Azure Cloud Shell cikkben látható példaparancsokat az Azure CLI Azure Cloud Shell vagy helyi telepítésével. Ha helyileg szeretné használni, a 2.18-as vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

A megbízható Azure-szolgáltatások számára a beállításjegyzékhez való hozzáférés előzetes **verziójú** funkció.

## <a name="limitations"></a>Korlátozások

* Egy megbízható szolgáltatásban engedélyezett, rendszer által hozzárendelt felügyelt identitással [kell](#trusted-services) hozzáférnie egy hálózatra korlátozott tárolójegyzékhez. A felhasználó által hozzárendelt felügyelt identitások jelenleg nem támogatottak.
* A megbízható szolgáltatások engedélyezésének nem kell vonatkoznia a szolgáltatásvégponttal konfigurált [tároló-beállításjegyzékre.](container-registry-vnet.md) A funkció csak olyan regisztrációs adatbázisokat [](container-registry-private-link.md) érint, amelyek privát végponttal vannak korlátozva, vagy amelyekre nyilvános [IP-hozzáférési szabályok vannak alkalmazva.](container-registry-access-selected-networks.md) 

## <a name="about-trusted-services"></a>A megbízható szolgáltatásokról

Azure Container Registry réteges biztonsági modellel rendelkezik, amely több hálózati konfigurációt támogat, amelyek korlátozzák a beállításjegyzékhez való hozzáférést, beleértve a következőket:

* [Privát végpont a Azure Private Link.](container-registry-private-link.md) Konfigurálás esetén a beállításjegyzék privát végpontja csak a virtuális hálózaton belüli erőforrások számára érhető el magánhálózati IP-címekkel.  
* [A beállításjegyzék tűzfalszabályai,](container-registry-access-selected-networks.md)amelyek csak meghatározott nyilvános IP-címekről vagy címtartományból engedélyezik a hozzáférést a beállításjegyzék nyilvános végpontjaihoz. A tűzfalat konfigurálhatja úgy is, hogy privát végpontok használata esetén letiltsa a nyilvános végponthoz való hozzáférést.

Virtuális hálózatban üzembe állítva vagy tűzfalszabályokkal konfigurálva a beállításjegyzék alapértelmezés szerint megtagadja a hozzáférést az ezen forrásokon kívüli felhasználók és szolgáltatások számára. 

Számos több-bérlős Azure-szolgáltatás olyan hálózatokból működik, amelyek nem szerepelnek ezekben a beállításjegyzékbeli hálózati beállításokban, így nem tudnak lekért vagy lekért rendszerképeket lekedni a beállításjegyzékbe. Bizonyos szolgáltatáspéldányok "megbízhatóként" való megtervezésével a beállításjegyzék tulajdonosa engedélyezheti, hogy egyes Azure-erőforrások biztonságosan megkerüljék a regisztrációs adatbázis hálózati beállításait a rendszerképek leküldéséhez vagy leküldéséhez. 

### <a name="trusted-services"></a>Megbízható szolgáltatások

Az alábbi szolgáltatások példányai hozzáférhetnek egy hálózatra korlátozott  tárolójegyzékhez, ha a beállításjegyzék megbízható szolgáltatások engedélyezése beállítás engedélyezve van (ez az alapértelmezett beállítás). Idővel további szolgáltatások lesznek hozzáadva.

|Megbízható szolgáltatás  |Támogatott használati forgatókönyvek  |
|---------|---------|
|ACR-feladatok     | [Hozzáférés egy másik beállításjegyzékhez egy ACR-feladatból](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | [Modell](../machine-learning/how-to-deploy-custom-docker-image.md) üzembe [helyezése vagy](../machine-learning/how-to-train-with-custom-image.md) betanítása egy Machine Learning munkaterületen egyéni Docker-tároló rendszerképének használatával |
|Azure Container Registry | [Rendszerképek importálása egy másik Azure Container Registryből](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Az allow trusted services (megbízható szolgáltatások engedélyezése) beállítás engedélyezése nem teszi lehetővé más felügyelt Azure-szolgáltatások (például App Service, Azure Container Instances és Azure Security Center) példányai számára, hogy hozzáférjenek egy hálózatra korlátozott tároló-beállításjegyzékhez.

## <a name="allow-trusted-services---cli"></a>Megbízható szolgáltatások engedélyezése – CLI

Alapértelmezés szerint a megbízható szolgáltatások engedélyezése beállítás engedélyezve van egy új Azure-beli tároló-beállításjegyzékben. Tiltsa le vagy engedélyezze a beállítást az [az acr update parancs futtatásával.](/cli/azure/acr#az_acr_update)

A letiltásához:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

A beállítás meglévő beállításjegyzékben vagy olyan beállításjegyzékben való engedélyezéséhez, ahol az már le van tiltva:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Megbízható szolgáltatások engedélyezése – portál

Alapértelmezés szerint a megbízható szolgáltatások engedélyezése beállítás engedélyezve van egy új Azure-beli tároló-beállításjegyzékben. 

A beállítás letiltása vagy újra engedélyezése a portálon:

1. A portálon lépjen a tároló-beállításjegyzékhez.
1. A **Beállítások alatt** válassza a Hálózat **lehetőséget.** 
1. A **Nyilvános hálózati hozzáférés engedélyezése beállításnál válassza** a Kijelölt hálózatok **vagy** a **Letiltva lehetőséget.**
1. Tegye a következők egyikét:
    * Ha le szeretné tiltani a megbízható szolgáltatások hozzáférését, a **Tűzfal** kivétele alatt törölje A megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a **tároló-beállításjegyzékhez jelölőnégyzetet.** 
    * Ha engedélyeznie kell a megbízható szolgáltatásokat, a **Tűzfal** kivétele alatt jelölje be A megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a **tároló-beállításjegyzékhez jelölőnégyzetet.**
1. Kattintson a **Mentés** gombra.

## <a name="trusted-services-workflow"></a>Megbízható szolgáltatások munkafolyamata

Az alábbi általános munkafolyamat lehetővé teszi, hogy egy megbízható szolgáltatás egy példánya hozzáférjen egy hálózatra korlátozott tárolójegyzékhez.

1. Engedélyezzen egy rendszer által hozzárendelt [felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md) [](#trusted-services) az Azure-erőforrásokhoz a szolgáltatás megbízható szolgáltatásainak Azure Container Registry.
1. Rendeljen egy [Azure-szerepkört az identitáshoz](container-registry-roles.md) a regisztrációs adatbázishoz. Például rendelje hozzá az ACRPull szerepkört a tároló rendszerképének lekért szerephez.
1. A hálózati beállításjegyzékben konfigurálja úgy a beállítást, hogy engedélyezze a megbízható szolgáltatások hozzáférését.
1. Az identitás hitelesítő adataival hitelesítse magát a hálózati beállításjegyzékben. 
1. Rendszerképek lekérte a regisztrációs adatbázisból, vagy a szerepkör által engedélyezett egyéb műveleteket hajthat végre.

### <a name="example-acr-tasks"></a>Például: ACR-feladatok

Az alábbi példa a ACR-feladatok szolgáltatásként való használatát mutatja be. A [feladatok részleteiért lásd: Adatbázisközi hitelesítés egy ACR-feladatban Azure által felügyelt](container-registry-tasks-cross-registry-authentication.md) identitás használatával.

1. Hozzon létre vagy frissítsen egy Azure Container Registryt, és [egy minta](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) alapként szolgáló rendszerképet a regisztrációs adatbázisba. Ez a beállításjegyzék a *forgatókönyv alap* regisztrációs adatbázisa.
1. Egy második Azure Container Registryben [definiálhat](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) és létrehozhat egy ACR-feladatot, amely lekért egy rendszerképet az alapjegyzékből. [](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) Engedélyezzen egy rendszer által hozzárendelt felügyelt identitást a feladat létrehozásakor.
1. Rendeljen hozzá egy [Azure-szerepkört a feladatidentitáshoz az alap beállításjegyzék eléréséhez.](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources) Hozzárendelheti például az AcrPull szerepkört, amely rendelkezik a rendszerképek lekért engedélyével.
1. [Felügyelt identitás hitelesítő adatainak hozzáadása a](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) feladathoz.
1. Annak megerősítéséhez, hogy a feladat megkerüli a hálózati korlátozásokat, tiltsa le a [nyilvános](container-registry-access-selected-networks.md#disable-public-network-access) hozzáférést az alap beállításjegyzékben.
1. Futtassa a feladatot. Ha az alap beállításjegyzék és a feladat megfelelően van konfigurálva, a feladat sikeresen lefut, mert az alap beállításjegyzék engedélyezi a hozzáférést.

A megbízható szolgáltatások hozzáférésének letiltásának tesztelése:

1. Az alap beállításjegyzékben tiltsa le azt a beállítást, amely engedélyezi a megbízható szolgáltatások hozzáférését.
1. Futtassa újra a feladatot. Ebben az esetben a feladat futtatása meghiúsul, mert az alap beállításjegyzék már nem engedélyezi a hozzáférést a feladat számára.

## <a name="next-steps"></a>Következő lépések

* A regisztrációs adatbázishoz való hozzáférés virtuális hálózaton lévő privát végpont használatával való korlátozásához lásd: Configure Azure Private Link for an Azure Container Registry (Regisztrációs adatbázis konfigurálása [Azure-beli tároló-beállításjegyzékhez).](container-registry-private-link.md)
* A beállításjegyzék tűzfalszabályainak beállításához lásd: [Nyilvános IP-hálózati szabályok konfigurálása.](container-registry-access-selected-networks.md)
