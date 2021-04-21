---
title: Az Azure Managed HSM hozzáférés-vezérlése
description: Az Azure Managed HSM és a kulcsok hozzáférési engedélyeinek kezelése. Bemutatja a Managed HSM hitelesítési és engedélyezési modelljét, valamint a HSM-ek biztonságát.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: bea1ccf0777c6325bc86c15e0f88304c465d89c9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750283"
---
# <a name="managed-hsm-access-control"></a>A Managed HSM hozzáférés-vezérlése

> [!NOTE]
> Key Vault erőforrás-szolgáltató két erőforrástípust **támogat:** tárolókat és felügyelt **HSM-eket.** A jelen cikkben ismertetett hozzáférés-vezérlés csak a felügyelt **HSM-ekre vonatkozik.** További információ a felügyelt HSM hozzáférés-vezérlésről: Hozzáférés Key Vault kulcsokhoz, tanúsítványokhoz és titkos kulcsokhoz egy [Azure szerepköralapú hozzáférés-vezérléssel.](../general/rbac-guide.md)

Azure Key Vault Managed HSM egy felhőszolgáltatás, amely védi a titkosítási kulcsokat. Mivel ezek az adatok bizalmasak és üzleti szempontból kritikus fontosságúak, biztonságossá kell téve a felügyelt HSM-hez való hozzáférést, mivel csak a jogosult alkalmazások és felhasználók férhetnek hozzá. Ez a cikk áttekintést nyújt a Managed HSM hozzáférés-vezérlési modelljéről. Ismerteti a hitelesítést és az engedélyezést, és leírja, hogyan biztosíthatja a felügyelt HSM-hez való hozzáférést.

## <a name="access-control-model"></a>Hozzáférés-vezérlési modell

A felügyelt HSM-hez való hozzáférés  vezérlése két felületen, a felügyeleti síkon és az **adatsíkon keresztül történik.** A felügyeleti síkon kezelheti magát a HSM-et. A síkon a műveletek közé tartozik a felügyelt HSM-gépek létrehozása és törlése, valamint a felügyelt HSM-tulajdonságok leolvasása. Az adatsík az a hely, ahol a HSM által védett titkosítási kulcsok egy felügyelt HSM-ban tárolt adatokkal dolgozhat. Kulcsokat adhat hozzá, törölhet, módosíthat és használhat kriptográfiai műveletek végrehajtásához, kezelheti a szerepkör-hozzárendeléseket a kulcsokhoz való hozzáférés szabályozásához, teljes HSM biztonsági mentést hozhat létre, visszaállíthatja a teljes biztonsági mentést, és kezelheti a biztonsági tartományt az adatsík felületén.

A felügyelt HSM bármelyik síkon való eléréséhez minden hívónak megfelelő hitelesítéssel és engedélyezéssel kell rendelkezik. A hitelesítés megállapítja a hívó identitását. Az engedélyezés határozza meg, hogy a hívó mely műveleteket hajthatja végre. A hívók bármelyike lehet [](../../role-based-access-control/overview.md#security-principal) a Azure Active Directory, csoport, szolgáltatásnév vagy felügyelt identitás által meghatározott bármely rendszerbiztonsági tag.

Mindkét sík a Azure Active Directory használja a hitelesítéshez. Az engedélyezéshez különböző rendszereket használnak az alábbiak szerint
- A felügyeleti sík azure-beli szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ, amely az Azure-on Azure Resource Manager 
- Az adatsík egy felügyelt HSM-szintű RBAC-t (Managed HSM helyi RBAC) használ – egy engedélyezési rendszert, amely a felügyelt HSM szintjén van megvalósítva és kényszerítve.

Felügyelt HSM létrehozásakor a kérelmező az adatsík-rendszergazdák listáját is tartalmazza (minden rendszerbiztonsági tag [támogatott).](../../role-based-access-control/overview.md#security-principal) Csak ezek a rendszergazdák férhetnek hozzá a felügyelt HSM-adatsíkhoz a kulcsműveletek végrehajtásához és az adatsík szerepkör-hozzárendelésének kezeléséhez (felügyelt HSM helyi RBAC).

A két sík engedélymodellje ugyanazt a szintaxist használja, de különböző szinteken vannak kényszerítve, és a szerepkör-hozzárendelések különböző hatókörökhöz tartoznak. Az Azure RBAC felügyeleti síkját a Azure Resource Manager, míg az adatsík felügyelt HSM helyi RBAC-ját maga a felügyelt HSM kényszeríti ki.

> [!IMPORTANT]
> Ha egy rendszerbiztonsági tag felügyeleti síkjának hozzáférést ad egy felügyelt HSM-hez, nem biztosít számukra hozzáférést az adatsíkhoz a kulcsok vagy adatsík szerepkör-hozzárendelések eléréséhez Managed HSM helyi RBAC-hez). Ez az elkülönítés a terv szerint megakadályozza a jogosultságok véletlen bővítését, ami hatással van a Managed HSM-ban tárolt kulcsokhoz való hozzáférésre.

Például egy előfizetés-rendszergazda (mivel "Közreműködő" engedéllyel rendelkezik az előfizetésben található összes erőforráshoz) törölheti az előfizetésében található felügyelt HSM-et, de ha nem rendelkezik kifejezetten a Managed HSM helyi RBAC-n keresztül biztosított adatsík-hozzáféréssel, nem férhet hozzá a kulcsokhoz vagy kezelheti a szerepkör-hozzárendelést a felügyelt HSM-ről, hogy saját magának vagy másoknak hozzáférést biztosítson az adatsíkhoz.

## <a name="azure-active-directory-authentication"></a>Hitelesítés Azure Active Directory-fiókkal

Amikor létrehoz egy felügyelt HSM-et egy Azure-előfizetésben, az automatikusan társítva lesz az Azure Active Directory bérlőhöz. Mindkét síkon minden hívót regisztrálni kell ebben a bérlőben, és hitelesíteni kell magát a felügyelt HSM eléréséhez.

Az alkalmazás a hitelesítést a Azure Active Directory a sík hívása előtt. Az alkalmazás bármilyen támogatott [hitelesítési módszert használhat](../../active-directory/develop/authentication-vs-authorization.md) az alkalmazás típusa alapján. Az alkalmazás jogkivonatot szerez be egy erőforráshoz a síkon a hozzáféréshez. Az erőforrás az Azure-környezeten alapuló végpont a felügyeleti vagy adatsíkon. Az alkalmazás a jogkivonatot használja, és REST API a Managed HSM-végpontra. További tudnivalókért tekintse át a teljes [hitelesítési folyamatot.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Az egyetlen hitelesítési mechanizmus mindkét síkon való használata számos előnyt biztosít:

- A szervezetek központilag vezérelheti a hozzáférést a szervezetük összes felügyelt HSM-éhez.
- Ha egy felhasználó távozik, azonnal elveszíti a hozzáférést a szervezet összes felügyelt HSM-éhez.
- A szervezetek testre szabhatják a hitelesítést az Azure Active Directory beállításaival, például a többtényezős hitelesítés engedélyezésével a további biztonság érdekében.

## <a name="resource-endpoints"></a>Erőforrás-végpontok

A rendszerbiztonsági tag végpontok használatával fér hozzá a síkhoz. A két sík hozzáférés-vezérlése egymástól függetlenül működik. Ha hozzáférést ad egy alkalmazásnak a kulcsok felügyelt HSM-beli használatára, adatsík-hozzáférést biztosít a Managed HSM helyi RBAC használatával. Ha hozzáférést szeretne ad egy felhasználónak a Managed HSM-erőforráshoz a felügyelt HSM-ek létrehozásához, olvasáshoz, törléséhez, áthelyezéséhez, valamint az Azure RBAC-t használó egyéb tulajdonságok és címkék szerkesztéséhez.

Az alábbi táblázat a felügyeleti és adatsíkok végpontját mutatja be.

| Hozzáférési &nbsp; sík | Hozzáférés végpontjai | Üzemeltetés | Hozzáférés-vezérlési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík | **Globális:**<br> management.azure.com:443<br> | Felügyelt HSM-ek létrehozása, olvasása, frissítése, törlése és áthelyezése<br>Felügyelt HSM-címkék beállítása | Azure RBAC-vel |
| Adatsík | **Globális:**<br> &lt;hsm-name &gt; .managedhsm.azure.net:443<br> | **Kulcsok:** visszafejtés, titkosítás,<br> unwrap, wrap, verify, sign, get, list, update, create, import, delete, backup, restore, purge<br/><br/> **Adatsík szerepkör-kezelése (Managed HSM helyi RBAC):** szerepkör-definíciók listása, szerepkörök hozzárendelése, szerepkör-hozzárendelések _törlése, <br/> <br/>_ egyéni szerepkörök definiálása *Biztonsági mentés/visszaállítás: biztonsági mentés, visszaállítás, állapot biztonsági **<br/> <br/> mentési/visszaállítási** műveleteinek ellenőrzése Biztonsági tartomány**: biztonsági tartomány letöltése és feltöltése | Felügyelt HSM helyi RBAC |
|||||

## <a name="management-plane-and-azure-rbac"></a>Felügyeleti sík és Azure RBAC

A felügyeleti síkon az Azure RBAC használatával engedélyezheti a hívó által végrehajtható műveleteket. Az Azure RBAC-modellben minden Azure-előfizetés rendelkezik egy Azure Active Directory. Ebből a címtárból adhat hozzáférést a felhasználóknak, csoportoknak és alkalmazásoknak. A hozzáférés az Azure-előfizetésben az üzembe helyezési modellt Azure Resource Manager kezeléséhez. A hozzáférés megadásához használja a [Azure Portal,](https://portal.azure.com/)az [Azure CLI,](/cli/azure/install-classic-cli) [Azure PowerShell](/powershell/azureps-cmdlets-docs)vagy a Azure Resource Manager [REST API-kat.](/rest/api/authorization/roleassignments)

Egy kulcstartót egy erőforráscsoportban hozhat létre, és a hozzáférést a Azure Active Directory. Lehetővé teszi a felhasználók vagy csoportok számára az erőforráscsoportok kulcstartóinak kezelését. A hozzáférést egy adott hatókörszinten adhatja meg a megfelelő Azure-szerepkörök hozzárendelése által. Ahhoz, hogy hozzáférést biztosítsunk egy felhasználónak a kulcstartók kezeléséhez, előre meghatározott szerepkört kell hozzárendelni `key vault Contributor` a felhasználóhoz egy adott hatókörben. Az Azure-beli szerepkörhöz a következő hatókörszintek rendelhetők hozzá:

- **Felügyeleti csoport:** Az előfizetés szintjén hozzárendelt Azure-szerepkör az adott felügyeleti csoportban található összes előfizetésre vonatkozik.
- **Előfizetés:** Az előfizetés szintjén hozzárendelt Azure-szerepkör az előfizetésben található összes erőforráscsoportra és erőforrásra vonatkozik.
- **Erőforráscsoport:** Az erőforráscsoport szintjén hozzárendelt Azure-szerepkör az adott erőforráscsoportban található összes erőforrásra vonatkozik.
- **Adott erőforrás:** Egy adott erőforráshoz hozzárendelt Azure-szerepkör az adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Több előre definiált szerepkör is íme. Ha egy előre meghatározott szerepkör nem illik az igényeihez, saját szerepkört is definiálhat. További információ: [Azure RBAC: Beépített szerepkörök.](../../role-based-access-control/built-in-roles.md)

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Adatsík és felügyelt HSM helyi RBAC

A rendszerbiztonsági tagnak hozzáférést adhat adott kulcsműveletek végrehajtásához egy szerepkör hozzárendelése által. Minden szerepkör-hozzárendeléshez meg kell adnia egy szerepkört és hatókört, amelyre a hozzárendelés vonatkozik. A Managed HSM helyi RBAC-hez két hatókör érhető el.

- **"/" vagy "/keys"**: HSM-szintű hatókör. Az ebben a hatókörben szerepkörhöz rendelt rendszerbiztonsági tagokkal elvégezheti a szerepkörben meghatározott műveleteket a felügyelt HSM összes objektumához (kulcsához).
- **"/keys/ &lt; key-name &gt; "**: Kulcsszintű hatókör. A szerepkörhöz ebben a hatókörben hozzárendelt rendszerbiztonsági tag csak a megadott kulcs összes verziójára végezheti el az ebben a szerepkörben meghatározott műveleteket.

## <a name="next-steps"></a>Következő lépések

- A rendszergazdáknak való első lépésekről a Mi az a Managed HSM? (Mi az a [Managed HSM?](overview.md)
- A szerepkör-kezelési oktatóanyagért lásd: [Managed HSM local RBAC (Felügyelt HSM helyi RBAC)](role-management.md)
- A Managed HSM-naplózás használatának naplózását lásd: Managed HSM logging (Felügyelt [HSM-naplózás)](logging.md)