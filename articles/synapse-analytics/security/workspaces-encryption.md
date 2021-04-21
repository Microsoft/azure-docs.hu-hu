---
title: Azure Synapse Analytics titkosítás
description: A titkosítást a Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 71249534c6a088088213659b5a45e042229721c7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813181"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Titkosítás Azure Synapse Analytics-munkaterületekhez

Ez a cikk a következőt ismerteti:
* Az adatok titkosítása az Synapse Analytics munkaterületeken.
* Synapse-munkaterületek konfigurálása az ügyfél által felügyelt kulccsal való titkosítás engedélyezéséhez.
* A munkaterületeken lévő adatok titkosításához használt kulcsok kezelése.

## <a name="encryption-of-data-at-rest"></a>Az adatok titkosítása

Egy teljes körű titkosítási megoldás biztosítja, hogy az adatok soha ne maradnak meg titkosítatlan formában. Az adattitkosítással az adatok kettős titkosítása két, különálló titkosítási réteggel csökkenti a fenyegetéseket, így védelmet nyújt bármely réteg biztonsági rései ellen. Azure Synapse Analytics egy második titkosítási réteget kínál a munkaterületen lévő adatokhoz egy ügyfél által felügyelt kulccsal. Ennek a kulcsnak a védelme a [Azure Key Vault,](../../key-vault/general/overview.md)amely lehetővé teszi a kulcskezelés és a rotáció saját tulajdonba való irányítását.

Az Azure-szolgáltatások titkosításának első rétege platform által kezelt kulcsokkal van engedélyezve. Alapértelmezés szerint az Azure-lemezek és az Azure Storage-fiókokban lévő adatok titkosítása automatikusan megtörténik. A titkosítás használatával kapcsolatos további információkért Microsoft Azure [Az Azure Encryption áttekintése témakörben talál.](../../security/fundamentals/encryption-overview.md)

## <a name="azure-synapse-encryption"></a>Azure Synapse titkosítás

Ez a szakasz segít jobban megérteni, hogyan engedélyezhető és kényszeríthető az ügyfél által felügyelt kulcstitkosítás a Synapse-munkaterületeken. Ez a titkosítás meglévő vagy a Azure Key Vault. A munkaterület összes adata egyetlen kulccsal van titkosítva. A Synapse-munkaterületek támogatják a 2048-as és 3072-es bájtméretű RSA-kulcsokat.

> [!NOTE]
> A Synapse-munkaterületek nem támogatják a titkosításhoz Elliptic-Curve ECC-kulcsok használatát.

Az alábbi Synapse-összetevőkben lévő adatok a munkaterület szintjén konfigurált, ügyfél által felügyelt kulccsal vannak titkosítva:
* SQL-készletek
 * Dedikált SQL-készletek
 * Kiszolgáló nélküli SQL-készletek
* Apache Spark-készletek
* Azure Data Factory, folyamatok és adatkészletek létrehozása.

## <a name="workspace-encryption-configuration"></a>Munkaterület titkosítási konfigurációja

A munkaterületek konfigurálhatóak úgy, hogy az ügyfél által felügyelt kulccsal engedélyezték a dupla titkosítást a munkaterület létrehozásakor. Új munkaterület létrehozásakor válassza a "Dupla titkosítás engedélyezése ügyfél által felügyelt kulccsal" lehetőséget a "Biztonság" lapon. Megadhatja a kulcsazonosító URI-ját, vagy választhat a  munkaterülettel egy régióban található kulcstartók listájából. Magának Key Vault rendszernek engedélyeznie kell a **végleges kiürítés elleni védelmet.**

> [!IMPORTANT]
> A dupla titkosítás konfigurációs beállítása a munkaterület létrehozása után nem módosítható.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Ezen az ábrán az a beállítás látható, amelynél ki kell választani azt a beállítást, amely lehetővé teszi a munkaterület számára a kettős titkosítást egy ügyfél által felügyelt kulccsal.":::

### <a name="key-access-and-workspace-activation"></a>Kulcs elérése és a munkaterület aktiválása

Az Azure Synapse ügyfél által kezelt kulcsokkal való titkosítási modell magában foglalja, hogy a munkaterület hozzáfér a Azure Key Vault kulcsához, hogy szükség szerint titkosítsa és visszafejtsen. A kulcsok egy hozzáférési szabályzaton vagy [RBAC-hozzáférésen keresztül](../../key-vault/general/rbac-guide.md)érhetők el Azure Key Vault munkaterület számára. Amikor egy Azure Key Vault hozzáférési szabályzaton keresztül ad engedélyeket, válassza a ["Csak alkalmazás"](../../key-vault/general/security-features.md#key-vault-authentication-options) lehetőséget a szabályzat létrehozása során (válassza ki a munkaterület felügyelt identitását, és ne adja hozzá hitelesített alkalmazásként).

 A munkaterület felügyelt identitásának rendelkeznie kell a szükséges engedélyekkel a kulcstartón a munkaterület aktiválása előtt. A munkaterület aktiválásának ezen szakaszos megközelítése biztosítja, hogy a munkaterületen lévő adatok titkosítása az ügyfél által felügyelt kulccsal történik. Vegye figyelembe, hogy a titkosítás engedélyezhető vagy letiltható dedikált SQL-készletek esetén – alapértelmezés szerint az egyes készletek titkosítása nincs engedélyezve.

#### <a name="permissions"></a>Engedélyek

Az adatok titkosításához vagy visszafejtéséhez a munkaterület által felügyelt identitásnak a következő engedélyekkel kell rendelkeznie:
* WrapKey (kulcs beszúrása a Key Vault új kulcs létrehozásakor).
* UnwrapKey (a visszafejtési kulcs lekért kulcsához).
* Get (kulcs nyilvános részének olvasása)

#### <a name="workspace-activation"></a>Munkaterület aktiválása

Miután a munkaterület (engedélyezve van a dupla titkosítás) létrejött, az "Függőben" állapotban marad, amíg az aktiválás sikeresen be nem zárul. A munkaterületet aktiválni kell, mielőtt az összes funkciót teljes mértékben ki tudja használni. Például csak akkor hozhat létre új dedikált SQL-készletet, ha az aktiválás sikeres volt. Adjon hozzáférést a munkaterület felügyelt identitásának a kulcstartóhoz, és kattintson a munkaterületen az aktiválási hivatkozásra Azure Portal szalagcímen. Ha az aktiválás sikeresen befejeződött, a munkaterület készen áll a használatra, és biztosítjuk, hogy a benne lévő összes adat védve van az ügyfél által felügyelt kulccsal. Ahogy azt korábban már említettük, a kulcstartón engedélyezni kell a véglegesen kiürítés elleni védelmet az aktiválás sikeres védelmének érdekében.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Ezen az ábrán a szalagcím látható a munkaterület aktiválási hivatkozásával.":::


### <a name="manage-the-workspace-customer-managed-key"></a>A munkaterület ügyfél által felügyelt kulcsának kezelése 

Az adatok titkosításához használt, ügyfél által  kezelt kulcsot a titkosítási folyamat Titkosítás lapján Azure Portal. Itt is választhat új kulcsot egy kulcsazonosítóval, vagy választhat olyan Kulcstartók közül, amelyekhez a munkaterülettel azonos régióban van hozzáférése. Ha a korábban használt kulcstartótól eltérő kulcstartóban választ kulcsot, adjon a munkaterület által felügyelt identitásnak "Get", "Wrap" és "Unwrap" engedélyeket az új kulcstartóhoz. A munkaterület ellenőrzi az új kulcstartóhoz való hozzáférését, és a munkaterületen lévő összes adatot újratitkosítani fogja az új kulccsal.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Az ábrán a munkaterület Titkosítás szakasza látható a Azure Portal.":::

>[!IMPORTANT]
>Egy munkaterület titkosítási kulcsának módosításakor őrizze meg a kulcsot, amíg a munkaterületen nem cseréli le egy új kulcsra. Ez lehetővé teszi az adatok visszafejtése a régi kulccsal, mielőtt az új kulccsal újratitkosítani lenne.

Az Azure Key Vault szabályzatai a kulcsok automatikus, rendszeres rotációja vagy a kulcsokon való műveletek esetén új kulcsverziók létrehozását eredményezhetik. Dönthet úgy, hogy a munkaterület összes adatát újratitkosítsa az aktív kulcs legújabb verziójával. A titkosítás újratitkosításához módosítsa a Azure Portal kulcsát egy ideiglenes kulcsra, majd váltson vissza a titkosításhoz használni kívánt kulcsra. Ha például az adattitkosítást a Key1 aktív kulcs legújabb verziójával frissíti, módosítsa a munkaterület ügyfél által felügyelt kulcsát a Key2 ideiglenes kulcsra. Várjon, amíg a 2. kulccsal végzett titkosítás befejeződik. Ezután váltsa vissza a munkaterület ügyfél által felügyelt kulcsát a munkaterület key1-data kulcsára, és a rendszer a Key1 legújabb verziójával újratitkosítani fogja.

> [!NOTE]
> Azure Synapse Analytics azon dolgozik, hogy új kulcsverziók létrehozásakor olyan funkciókat ad hozzá, amelyek automatikusan újratitkosják az adatokat. Amíg ez a funkció elérhetővé nem válik, a munkaterület konzisztenciájának biztosításához kényszeríteni kell az adatok újratitkosítását a fent leírt folyamattal.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>SQL transzparens adattitkosítás szolgáltatás által felügyelt kulcsokkal

Az SQL transzparens adattitkosítás (TDE) elérhető a dedikált SQL-készletekhez olyan munkaterületeken, *amelyeken* nincs engedélyezve a dupla titkosítás. Az ilyen típusú munkaterületeken egy szolgáltatás által felügyelt kulcs biztosítja a dedikált SQL-készletekben lévő adatok dupla titkosítását. A szolgáltatás által felügyelt kulccsal rendelkező TDE engedélyezhető vagy letiltható az egyes dedikált SQL-készletekhez.

## <a name="next-steps"></a>Következő lépések

[A Synapse-munkaterületek titkosítási védelmének megvalósítása beépített Azure-szabályzatokkal](../policy-reference.md)

