---
title: Áttelepítés azure-beli szerepköralapú hozzáférés-vezérlési | Microsoft Docs
description: Útmutató tároló-hozzáférési szabályzatok Azure-szerepkörökbe való áttelepítéshez.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: a369ed26ca91dbf951b28b99250c6307608c5eb3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749077"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Áttelepítés tároló-hozzáférési szabályzatból Azure-beli szerepköralapú hozzáférés-vezérlési engedélymodellbe

A tároló-hozzáférési szabályzat modellje egy meglévő engedélyezési rendszer, amely Key Vault, hogy hozzáférést biztosítson a kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz. A hozzáférést úgy szabályozhatja, ha egyéni engedélyeket rendel a rendszerbiztonsági taghoz (felhasználó, csoport, szolgáltatásnév, felügyelt identitás) Key Vault hatókörben. 

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) [](../../azure-resource-manager/management/overview.md) egy olyan engedélyezési rendszer, amely Azure Resource Manager az Azure-erőforrások részletes hozzáférés-kezelését biztosítja. Az Azure RBAC használatával szerepkör-hozzárendelések létrehozásával szabályozhatja az erőforrásokhoz való hozzáférést, amely három elemből áll: rendszerbiztonsági tag, szerepkör-definíció (előre definiált engedélyek) és hatókör (erőforráscsoport vagy egyéni erőforrás). További információ: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC).](../../role-based-access-control/overview.md)

Az Azure RBAC-be való mirating előtt fontos megérteni annak előnyeit és korlátait.

Az Azure RBAC-kulcs előnyei a tároló-hozzáférési szabályzatokkal kapcsolatban:
- Egységes hozzáférés-vezérlési modellt biztosít az Azure-erőforrásokhoz – ugyanaz az API minden Azure-szolgáltatáshoz
- Központosított hozzáférés-kezelés rendszergazdák számára – az összes Azure-erőforrás kezelése egyetlen nézetben
- Integrálva [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) időalapú hozzáférés-vezérléshez
- Megtagadás-hozzárendelések – lehetőség a rendszerbiztonsági tag kizárására egy adott hatókörben. További információ: [Az Azure megtagadási hozzárendelései](../../role-based-access-control/deny-assignments.md)

Az Azure RBAC hátrányai:
- A szerepkör-hozzárendelések késése – a szerepkör-hozzárendelések alkalmazása több percig is eltarthat. A tároló-hozzáférési szabályzatok hozzárendelése azonnal meghozva.
- Korlátozott számú szerepkör-hozzárendelés – előfizetésenként 2000 szerepkör-hozzárendelés és 1024 hozzáférési szabályzat Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Hozzáférési szabályzatok az Azure-szerepkörök leképezéséhez

Az Azure RBAC számos beépített Azure-szerepkört kínál, amelyek felhasználókhoz, csoportokhoz, szolgáltatásnévhez és felügyelt identitásokhoz rendelhetők hozzá. Ha a beépített szerepkörök nem felelnie meg a szervezet igényeinek, létrehozhatja saját [egyéni Azure-szerepköreit.](../../role-based-access-control/custom-roles.md)

Key Vault kulcsok, tanúsítványok és titkos kulcsok hozzáférés-kezelésének beépített szerepkörei:
- Key Vault rendszergazda
- Key Vault Olvasó
- Key Vault tanúsítvány-felelős
- Key Vault kriptográfiai igazgató
- Key Vault kriptográfiai felhasználó
- Key Vault titkosítási szolgáltatás titkosítási felhasználója
- Key Vault titkos kulcsokért felelős igazgató
- Key Vault titkos kulcsok felhasználója

További információ a meglévő beépített szerepkörökről: [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md)

A tároló-hozzáférési szabályzatok egyedileg kiválasztott engedélyekkel vagy előre meghatározott engedélysablonokkal rendelhetők hozzá.

Hozzáférési szabályzatok előre definiált engedélysablonjai:
- Kulcs, titkos kulcs, tanúsítványkezelés
- Key & Secret Management
- Titkos & tanúsítványkezelés
- Kulcskezelés
- Titkos tok kezelése
- Tanúsítványkezelés
- SQL Server Connector
- Azure Data Lake Storage or Azure Storage
- Azure Backup
- Exchange Online-ügyfélkulcs
- SharePoint Online-ügyfélkulcs
- Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Hozzáférési szabályzatsablonok az Azure-szerepkörök leképezéséhez
| Hozzáférési szabályzat sablonja | Üzemeltetés | Azure-szerepkör |
| --- | --- | --- |
| Kulcs, titkos kulcs, tanúsítványkezelés | Kulcsok: minden művelet <br>Tanúsítványok: minden művelet<br>Titkos kulcsok: minden művelet | Key Vault rendszergazda |
| Key & Secret Management | Kulcsok: minden művelet <br>Titkos kulcsok: minden művelet| Key Vault kriptográfiai igazgató <br> Key Vault titkos kulcsokért felelős igazgató |
| Titkos & tanúsítványkezelés | Tanúsítványok: minden művelet <br>Titkos kulcsok: minden művelet| Key Vault tanúsítvány-felelős <br> Key Vault titkos kulcsokért felelős igazgató|
| Kulcskezelés | Kulcsok: minden művelet| Key Vault kriptográfiai igazgató|
| Titkos tok kezelése | Titkos kulcsok: minden művelet| Key Vault titkos kulcsokért felelős igazgató|
| Tanúsítványkezelés | Tanúsítványok: minden művelet | Key Vault tanúsítvány-felelős|
| SQL Server Connector | Kulcsok: get, list, wrap key, unwrap key | Key Vault titkosítási szolgáltatás titkosítási felhasználója|
| Azure Data Lake Storage or Azure Storage | Kulcsok: lekért, listás, kicsomagott kulcs | N/A<br> Egyéni szerepkör szükséges|
| Azure Backup | Kulcsok: lekért, listás, biztonsági mentés<br> Tanúsítvány: get, list, backup | N/A<br> Egyéni szerepkör szükséges|
| Exchange Online-ügyfélkulcs | Kulcsok: get, list, wrap key, unwrap key | Key Vault titkosítási szolgáltatás titkosítási felhasználója|
| Exchange Online-ügyfélkulcs | Kulcsok: get, list, wrap key, unwrap key | Key Vault titkosítási szolgáltatás titkosítási felhasználója|
| Azure Information BYOK | Kulcsok: get, decrypt, sign | N/A<br>Egyéni szerepkör szükséges|


## <a name="assignment-scopes-mapping"></a>Hozzárendelési hatókörök leképezése  

Az Azure RBAC Key Vault lehetővé teszi a szerepkörök hozzárendelését a következő hatókörökben:
- Felügyeleti csoport
- Előfizetés
- Erőforráscsoport
- Key Vault erőforrás
- Egyéni kulcs, titkos kulcs és tanúsítvány

A tároló-hozzáférési szabályzat engedélymodellje csak a Key Vault erőforrásszinten rendelhető hozzá, amely 

Általában az ajánlott eljárás alkalmazásonként egy kulcstartó, és a hozzáférés kezelése a kulcstartó szintjén. Vannak olyan forgatókönyvek, amelyekben a hozzáférés más hatókörökben való kezelése leegyszerűsítheti a hozzáférés-kezelési folyamatokat.

- **Infrastruktúra, biztonsági rendszergazdák és operátorok: a kulcstartók csoportjának felügyeleti csoportban, előfizetésben vagy erőforráscsoportban való kezeléséhez a tároló-hozzáférési szabályzatok használatához minden kulcstartóhoz meg kell tartani a szabályzatokat. Az Azure RBAC lehetővé teszi egy szerepkör-hozzárendelés létrehozását a felügyeleti csoportban, az előfizetésben vagy az erőforráscsoportban. Ez a hozzárendelés minden új kulcstartóra érvényes lesz, amely ugyanabban a hatókörben jött létre. Ebben a forgatókönyvben javasoljuk, hogy a Privileged Identity Management a folyamatos hozzáférés biztosítása fölötti, megfelelő időben való hozzáféréssel.
 
- **Alkalmazások: vannak olyan forgatókönyvek, amikor az alkalmazásnak titkos okat kellene megosztania más alkalmazásokkal. A tároló-hozzáférésiházirendek használatával külön kulcstartót kellett létrehozni, hogy ne legyen hozzáférés minden titkos kulcshoz. Az Azure RBAC lehetővé teszi, hogy egyetlen kulcstartó használata helyett egyéni titkos kulcsok hatókörével rendeljen hozzá szerepköröket.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Tároló-hozzáférési szabályzat az Azure RBAC-be való migrálás lépései
Az Azure RBAC és a tároló-hozzáférési szabályzat engedélymodellje között számos különbség van. A migrálás során a kimaradások elkerülése érdekében az alábbi lépéseket javasoljuk.
 
1. **Szerepkörök azonosítása és hozzárendelése:** a beépített szerepkörök azonosítása a fenti leképezési táblázat alapján, és szükség esetén egyéni szerepkörök létrehozása. Szerepkörök hozzárendelése hatókörökhöz a hatókörleképezési útmutató alapján. További információ a szerepkörök kulcstartóhoz való hozzárendelésről: Hozzáférés Key Vault azure-beli szerepköralapú [hozzáférés-vezérléssel.](rbac-guide.md)
1. **Szerepkörök hozzárendelésének ellenőrzése:** Az Azure RBAC szerepkör-hozzárendelésének propagálása több percig is eltarthat. A szerepkör-hozzárendelések ellenőrzésének útmutatóját lásd: [Szerepkörök hozzárendelésének felsorolása hatókörben](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Monitorozás és riasztások konfigurálása a Key Vaulton:** Fontos engedélyezni a naplózást és beállítani a hozzáférés-megtagadásos kivételekre vonatkozó riasztásokat. További információ: Figyelés [és riasztások a Azure Key Vault](./alert.md)
1. **Azure szerepköralapú hozzáférés-vezérlési** engedélymodell beállítása a Key Vault: az Azure RBAC-engedélymodell engedélyezése érvényteleníti az összes meglévő hozzáférési szabályzatot. Hiba esetén az engedélymodell visszakapcsolható úgy, hogy az összes meglévő hozzáférési szabályzat változatlan marad.

> [!NOTE]
> Az engedélymodell módosításához "Microsoft.Authorization/roleAssignments/write" engedélyre van szükség, amely a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) és a Felhasználói hozzáférés rendszergazdája szerepkör [részét](../../role-based-access-control/built-in-roles.md#user-access-administrator) képezi. A klasszikus előfizetés-rendszergazdai szerepkörök, például a szolgáltatás-rendszergazda és a társadi rendszergazda nem támogatottak.

> [!NOTE]
> Ha az Azure RBAC-engedélymodell engedélyezve van, a hozzáférési szabályzatok frissítésére tett összes szkript sikertelen lesz. Fontos, hogy frissítse ezeket a szkripteket az Azure RBAC használatára.

## <a name="troubleshooting"></a>Hibaelhárítás
-  A szerepkör-hozzárendelés néhány perc után nem működik – vannak olyan helyzetek, amikor a szerepkör-hozzárendelések hosszabb időt is igénybe venek. Fontos, hogy ezeket az eseteket lefedő újrapróbálkozási logikát írjon a kódba.
- A szerepkör-hozzárendelések a Key Vault (helyreállítható törlés) és helyreállítása után eltűntek – ez jelenleg az összes Azure-szolgáltatás helyreállítható törlési funkcióját korlátozza. A helyreállítás után újra létre kell hoznia az összes szerepkör-hozzárendelést.    

## <a name="learn-more"></a>Tudjon meg többet

- [Az Azure RBAC áttekintése](../../role-based-access-control/overview.md)
- [Egyéni szerepkörök – oktatóanyag](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)