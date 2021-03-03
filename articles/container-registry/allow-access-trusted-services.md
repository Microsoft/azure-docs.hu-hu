---
title: Hozzáférés a hálózatra korlátozott beállításjegyzékhez a megbízható Azure szolgáltatás használatával
description: Megbízható Azure-szolgáltatási példány engedélyezése a hálózatra korlátozott tárolók beállításjegyzékének biztonságos eléréséhez a képek lekéréséhez vagy leküldéséhez
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 3cc32630ea689891e7ba75163c33bc499a38becd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716482"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>A megbízható szolgáltatások biztonságos elérésének engedélyezése a korlátozott hálózati tárolók beállításjegyzékében (előzetes verzió)

Azure Container Registry engedélyezheti a megbízható Azure-szolgáltatások kiválasztását a hálózati hozzáférési szabályokkal konfigurált beállításjegyzék eléréséhez. Ha a megbízható szolgáltatások engedélyezve vannak, a megbízható szolgáltatási példányok biztonságosan megkerülhetik a beállításjegyzék hálózati szabályait, és műveleteket végezhetnek, például lekéréses vagy leküldéses képeket is. A szolgáltatási példány felügyelt identitása a hozzáféréshez van használatban, és hozzá kell rendelnie egy Azure-szerepkört, és hitelesítenie kell magát a beállításjegyzékben.

Használja az Azure CLI Azure Cloud Shell vagy helyi telepítését a jelen cikkben szereplő példák futtatásához. Ha helyileg szeretné használni, a 2,18-es vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

A megbízható Azure-szolgáltatások a beállításjegyzékhez való hozzáférésének engedélyezése **előzetes** verziójú funkció.

## <a name="limitations"></a>Korlátozások

* Egy [megbízható szolgáltatásban](#trusted-services) engedélyezett, rendszerhez rendelt felügyelt identitást kell használnia a hálózatra korlátozott tárolók beállításjegyzékének eléréséhez. A felhasználó által hozzárendelt felügyelt identitások jelenleg nem támogatottak.
* A megbízható szolgáltatások engedélyezése nem vonatkozik a [szolgáltatás-végponttal](container-registry-vnet.md)konfigurált tároló-beállításjegyzékre. A szolgáltatás csak olyan beállításjegyzékeket érint, amelyek egy [privát végponttal](container-registry-private-link.md) vannak korlátozva, vagy amelyeken a [nyilvános IP-hozzáférési szabályok](container-registry-access-selected-networks.md) érvényesek. 

## <a name="about-trusted-services"></a>A megbízható szolgáltatások ismertetése

A Azure Container Registry többrétegű biztonsági modellel rendelkezik, amely támogatja a beállításjegyzékhez való hozzáférést korlátozó több hálózati konfigurációt, beleértve a következőket:

* [Privát végpont Azure Private-hivatkozással](container-registry-private-link.md). Ha be van állítva, a beállításjegyzék saját végpontja csak a virtuális hálózaton belüli erőforrásokhoz érhető el, magánhálózati IP-címek használatával.  
* A [beállításjegyzék tűzfalszabályok](container-registry-access-selected-networks.md), amelyek lehetővé teszik a beállításjegyzék nyilvános végponthoz való hozzáférését csak bizonyos nyilvános IP-címekről vagy címtartományból. A tűzfalat úgy is beállíthatja, hogy a magánhálózati végpontok használatakor blokkolja a nyilvános végponthoz való hozzáférést.

Virtuális hálózatban vagy tűzfalszabályok konfigurálásakor a beállításjegyzék alapértelmezés szerint megtagadja a hozzáférést a forrásokon kívüli felhasználókhoz vagy szolgáltatásokhoz. 

Számos több-bérlős Azure-szolgáltatás olyan hálózatokból működik, amelyek nem vehetők fel a beállításjegyzék hálózati beállításaiba, így megakadályozva, hogy a rendszerképeket a beállításjegyzékbe húzza vagy nyomja le. Bizonyos szolgáltatási példányok "megbízhatóként" való kiválasztásával a beállításjegyzék tulajdonosa engedélyezheti az Azure-erőforrások kijelölését a beállításjegyzék hálózati beállításainak biztonságos megkerüléséhez a lemezképek lekéréséhez vagy leküldéséhez. 

### <a name="trusted-services"></a>Megbízható szolgáltatások

A következő szolgáltatások példányai hozzáférhetnek a hálózatra korlátozott tárolók beállításjegyzékéhez, ha a beállításjegyzékben engedélyezve van a **megbízható szolgáltatások engedélyezése** beállítás (az alapértelmezett érték). Több szolgáltatás lesz hozzáadva az idő múlásával.

|Megbízható szolgáltatás  |Támogatott használati forgatókönyvek  |
|---------|---------|
|ACR-feladatok     | [Hozzáférés egy másik beállításjegyzékhez egy ACR-feladatból](container-registry-tasks-cross-registry-authentication.md)       |
|Azure Container Registry | [Lemezképek importálása egy másik Azure Container registryből](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> A megbízható szolgáltatások engedélyezése beállítás engedélyezése nem teszi lehetővé más felügyelt Azure-szolgáltatások (például a App Service, a Azure Container Instances és a Azure Security Center) példányai számára a hálózatra korlátozott curently elérését.

## <a name="allow-trusted-services---cli"></a>Megbízható szolgáltatások engedélyezése – parancssori felület

Alapértelmezés szerint a megbízható szolgáltatások engedélyezése beállítás engedélyezve van egy új Azure Container registryben. Tiltsa le vagy engedélyezze a beállítást az az [ACR Update](/cli/azure/acr#az-acr-update) parancs futtatásával.

A letiltáshoz:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

A beállítás engedélyezése egy meglévő beállításjegyzékben vagy egy olyan beállításjegyzékben, amely már le van tiltva:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Megbízható szolgáltatások engedélyezése – portál

Alapértelmezés szerint a megbízható szolgáltatások engedélyezése beállítás engedélyezve van egy új Azure Container registryben. 

A beállítás letiltása vagy újbóli engedélyezése a portálon:

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **Beállítások** területen válassza a **hálózatkezelés** lehetőséget. 
1. A **nyilvános hálózati hozzáférés engedélyezése** lapon válassza a **kiválasztott hálózatok** vagy a **Letiltva** lehetőséget.
1. Tegye a következők egyikét:
    * A megbízható szolgáltatások hozzáférésének letiltásához a **tűzfal kivétele** alatt törölje a **jelet a megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a tároló-beállításjegyzékhez** jelölőnégyzetből. 
    * A megbízható szolgáltatások engedélyezéséhez a **tűzfal kivétele** alatt tekintse meg a **megbízható Microsoft-szolgáltatások számára a tároló-beállításjegyzék elérésének engedélyezése lehetőséget**.
1. Kattintson a **Mentés** gombra.

## <a name="trusted-services-workflow"></a>Megbízható szolgáltatások munkafolyamata

Íme egy tipikus munkafolyamat, amely lehetővé teszi, hogy egy megbízható szolgáltatás egy példánya hozzáférhessen a hálózatra korlátozott tárolók beállításjegyzékéhez.

1. Az Azure-erőforrások rendszerhez rendelt [felügyelt identitásának](../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a Azure Container Registry egyik [megbízható szolgáltatásának](#trusted-services) egy példányán.
1. Rendelje hozzá az identitást egy [Azure-szerepkörhöz](container-registry-roles.md) a beállításjegyzékhez. Rendelje hozzá például a ACRPull szerepkört a tárolók lemezképének lekéréséhez.
1. A hálózatra korlátozott beállításjegyzékben konfigurálja a beállítást, hogy engedélyezze a megbízható szolgáltatások hozzáférését.
1. Az identitás hitelesítő adataival hitelesítheti magát a hálózatra korlátozott beállításjegyzék használatával. 
1. Lekéri a lemezképeket a beállításjegyzékből, vagy más, a szerepkör által engedélyezett műveleteket hajthat végre.

### <a name="example-acr-tasks"></a>Példa: ACR-feladatok

Az alábbi példa az ACR-feladatok megbízható szolgáltatásként való használatát mutatja be. Lásd: a [beállításjegyzék közötti hitelesítés egy ACR-feladatban egy Azure által felügyelt identitás használatával](container-registry-tasks-cross-registry-authentication.md) a feladat részleteihez.

1. Hozzon létre vagy frissítsen egy Azure Container registryt, és küldjön le [egy minta alapképet](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) a beállításjegyzékbe. Ez a beállításjegyzék a forgatókönyv *alapszintű beállításjegyzéke* .
1. Egy második Azure Container-beállításjegyzékben [definiáljon](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) és [hozzon létre](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) egy ACR-feladatot egy rendszerkép lekéréséhez az alap beállításjegyzékből. A feladat létrehozásakor engedélyezze a rendszerhez rendelt felügyelt identitást.
1. Rendelje hozzá a feladat identitását [egy Azure-szerepkörhöz az alapszintű beállításjegyzék eléréséhez](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Rendelje hozzá például a AcrPull szerepkört, amely jogosult a képek lekérésére.
1. [Felügyelt identitás hitelesítő adatainak hozzáadása](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) a feladathoz.
1. Annak megerősítéséhez, hogy a feladat megkerüli a hálózati korlátozásokat, [Tiltsa le a nyilvános hozzáférést](container-registry-access-selected-networks.md#disable-public-network-access) az alapszintű beállításjegyzékben.
1. Futtassa a feladatot. Ha az alapszintű beállításjegyzék és a feladat megfelelően van konfigurálva, a feladat sikeresen lefut, mert az alapszintű beállításjegyzék engedélyezi a hozzáférést.

A megbízható szolgáltatások általi hozzáférés letiltásának tesztelése:

1. Az alapszintű beállításjegyzékben tiltsa le a megbízható szolgáltatások hozzáférésének engedélyezésére vonatkozó beállítást.
1. Futtassa újra a feladatot. Ebben az esetben a feladat futtatása sikertelen lesz, mert az alapszintű beállításjegyzék már nem teszi lehetővé a feladat elérését.

## <a name="next-steps"></a>Következő lépések

* Ha egy virtuális hálózat privát végpontján keresztül szeretné korlátozni a beállításjegyzékhez való hozzáférést, tekintse meg az [Azure Private-hivatkozás konfigurálása Azure Container registryhez](container-registry-private-link.md)című témakört.
* A beállításjegyzék tűzfalszabályok beállításával kapcsolatban lásd: [nyilvános IP-hálózati szabályok konfigurálása](container-registry-access-selected-networks.md).
