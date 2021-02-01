---
title: Gyakori kérdések az Azure Resource mozgató szolgáltatásról?
description: Válaszok az Azure-erőforrás-mozgatóval kapcsolatos gyakori kérdésekre
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: bb8cfa3e1aa874f807ce46b254a22f3003c2f600
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222815"
---
# <a name="common-questions"></a>Gyakori kérdések

Ez a cikk az Azure- [erőforrás-mozgatóval](overview.md)kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános

### <a name="is-resource-mover-generally-available"></a>Általánosan elérhető az erőforrás-mozgató?

Az erőforrás-mozgató szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. A termelési munkaterhelések támogatottak.



## <a name="moving-across-regions"></a>Áthelyezés régiók között

### <a name="can-i-move-resources-across-any-regions"></a>Helyezhetek át erőforrásokat bármely régióban?

Jelenleg az [adott régióban elérhető erőforrástípusok](https://azure.microsoft.com/global-infrastructure/services/)függvényében bármely forrás nyilvános régióból áthelyezheti az erőforrásokat a cél nyilvános régióba. A Azure Government-régiók erőforrásainak áthelyezése jelenleg nem támogatott.

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Milyen erőforrásokat helyezhetek át a régiók között az erőforrás-mozgató használatával?

Az erőforrás-mozgató használatával jelenleg a következő erőforrásokat helyezheti át a régiók között:

- Azure-beli virtuális gépek és kapcsolódó lemezek
- Hálózati adapterek (NIC-k)
- Rendelkezésre állási csoportok 
- Azure-beli virtuális hálózatok 
- Nyilvános IP-címek
- Hálózati biztonsági csoportok (NSG-k)
- Belső és nyilvános terheléselosztó 
- Azure SQL Database-adatbázisok és rugalmas készletek


### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>Át lehet helyezni az erőforrásokat az előfizetések között, amikor áthelyezem őket a régiók között?

Az erőforrásoknak a célhelyre való áthelyezése után módosíthatja az előfizetést. [További](../azure-resource-manager/management/move-resource-group-and-subscription.md) információ az erőforrások másik előfizetésre való áthelyezéséről. 

### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>Hol találhatók a metaadatok a tárolt régiók között?

Ez egy [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -adatbázisban, az [Azure Blob Storage](../storage/common/storage-service-encryption.md)-ban, egy Microsoft-előfizetésben tárolódik. Jelenleg a metaadatok tárolása az USA 2. keleti régiójában és Észak-Európában történik. Ezt a lefedettséget más régiókban is kiterjesztjük. Ez nem korlátozza az erőforrások áthelyezését a nyilvános régiók között.

### <a name="is-the-collected-metadata-encrypted"></a>Titkosították-e az összegyűjtött metaadatokat?

Igen, mind a tranzit, mind a nyugalmi állapotban.
- Az átvitel során a metaadatokat a rendszer biztonságosan továbbítja az erőforrás-mozgató szolgáltatásnak az interneten keresztül HTTPS használatával.
- A tárolóban a metaadatok titkosítva vannak.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Hogyan használják a felügyelt identitást az erőforrás-mozgató?

A [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) (korábbi nevén Managed Service Identity (MSI)) Azure-szolgáltatásokat biztosít az Azure ad-ben automatikusan felügyelt identitással.
- Az erőforrás-mozgató felügyelt identitást használ, hogy az Azure-előfizetések hozzáférjenek az erőforrások régiók közötti áthelyezéséhez.
- Az áthelyezési gyűjteménynek szüksége van egy rendszerhez rendelt identitásra, amely hozzáféréssel rendelkezik az áthelyezni kívánt erőforrásokat tartalmazó előfizetéshez.

- Ha erőforrásokat helyez át a portál régiói között, a folyamat automatikusan megtörténik.
- Ha az erőforrásokat a PowerShell használatával helyezi át, a parancsmagok futtatásával rendeljen hozzá egy rendszerhez rendelt identitást a gyűjteményhez, majd rendeljen hozzá egy szerepkört a megfelelő előfizetési engedélyekkel az identitás rendszerbiztonsági tagjához. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Milyen felügyelt identitási engedélyek szükségesek az erőforrás-mozgató számára?

Az Azure Resource Mover felügyelt identitásának eléréséhez legalább a következő engedélyekre van szükség: 

- Engedély a felhasználói előfizetésben lévő erőforrások írásához/létrehozásához, amely a *közreműködő* szerepkörrel érhető el. 
- Engedély szerepkör-hozzárendelések létrehozásához. Általában elérhető a *tulajdonos* vagy a *felhasználó hozzáférés-felügyeleti* szerepköreivel, illetve olyan egyéni szerepkörrel, amely rendelkezik a *Microsoft. Authorization/szerepkör-hozzárendelés/írási engedély* hozzárendelésével. Erre az engedélyre nincs szükség, ha az adatmegosztási erőforrás felügyelt identitása már hozzáféréssel rendelkezik az Azure-adattárhoz. 
 
Amikor erőforrásokat ad hozzá a Resource Mover hubhoz a portálon, az engedélyek kezelése automatikus, ha a felhasználó rendelkezik a fent ismertetett engedélyekkel. Ha erőforrásokat ad hozzá a PowerShell-lel, manuálisan rendelje hozzá az engedélyeket.

> [!IMPORTANT]
> Kifejezetten azt javasoljuk, hogy ne módosítsa vagy távolítsa el az identitás szerepkör-hozzárendeléseit. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>Mi a teendő, ha nincs engedélye a szerepkör-identitás hozzárendelésére?

**Lehetséges ok** | **Ajánlás**
--- | ---
Ha első alkalommal ad hozzá egy erőforrást, Ön nem *közreműködő* és *felhasználói hozzáférésű rendszergazda* (vagy *tulajdonos*). | Az előfizetéshez tartozó *közreműködői* és *felhasználói hozzáférési rendszergazdai* (vagy *tulajdonosi*) engedélyekkel rendelkező fiókot használjon.
Az erőforrás-mozgató felügyelt identitás nem rendelkezik a szükséges szerepkörrel. | Adja hozzá a közreműködő és a felhasználói hozzáférés rendszergazdai szerepkört.
Az erőforrás-mozgató felügyelt identitás *none* értékre lett visszaállítva. | Egy rendszerhez rendelt identitás újraengedélyezése a gyűjtemény áthelyezése > **identitásban**. Azt is megteheti, hogy újra hozzáadja az erőforrást a **Hozzáadás erőforrásokhoz**, ami ugyanezt teszi.  
Az előfizetés átkerült egy másik bérlőre. | Tiltsa le, majd engedélyezze a felügyelt identitást az áthelyezési gyűjtemény számára.

### <a name="how-can-i-do-multiple-moves-together"></a>Hogyan végezhetek együtt több lépést?

Szükség szerint módosítsa a forrás-és a cél kombinációt a portálon a módosítás lehetőség használatával.

## <a name="next-steps"></a>Következő lépések

[További](about-move-process.md) információ az erőforrás-mozgató összetevőkről és az áthelyezési folyamatról.
