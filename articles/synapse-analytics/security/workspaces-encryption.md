---
title: Azure szinapszis Analytics-titkosítás
description: Egy cikk, amely az Azure szinapszis Analyticsben való titkosítást ismerteti
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d4bc59a9bd5299698bff9949aaaa881fbdf385ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100526272"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Titkosítás Azure Synapse Analytics-munkaterületekhez

Ez a cikk a következőket ismerteti:
* Inaktív adatok titkosítása a szinapszis Analytics-munkaterületeken.
* A szinapszis-munkaterületek konfigurációja, amely lehetővé teszi a titkosítást az ügyfél által felügyelt kulccsal.
* A munkaterületeken tárolt adattitkosításhoz használt kulcsok kezelése.

## <a name="encryption-of-data-at-rest"></a>Inaktív adatok titkosítása

A teljes titkosítást biztosító megoldás biztosítja, hogy az adatok nem maradnak meg titkosítatlan formában. A REST-adatok kettős titkosítása csökkenti a fenyegetéseket két, különálló titkosítási réteggel, hogy védelmet nyújtson egyetlen réteg sérülése ellen. Az Azure szinapszis Analytics egy második titkosítási réteget biztosít a munkaterületen lévő, ügyfél által felügyelt kulccsal rendelkező adataihoz. Ezt a kulcsot a [Azure Key Vault](../../key-vault/general/overview.md)biztosítjuk, amely lehetővé teszi a kulcskezelő és a rotációs szolgáltatás tulajdonjogának átvételét.

Az Azure-szolgáltatások titkosításának első rétege engedélyezve van a platform által felügyelt kulcsokkal. Alapértelmezés szerint a rendszer automatikusan titkosítja az Azure-lemezeket és az Azure Storage-fiókokban tárolt adatokkal. További információ a titkosítás használatáról Microsoft Azure az [Azure-titkosítás áttekintésében](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Azure szinapszis-titkosítás

Ez a szakasz segít jobban megérteni, hogy az ügyfél által felügyelt kulcs titkosítása engedélyezve van és ki van kényszerítve a szinapszis-munkaterületeken. Ez a titkosítás meglévő kulcsokat vagy Azure Key Vault által generált új kulcsokat használ. A rendszer egyetlen kulcsot használ a munkaterület összes elemének titkosítására. A szinapszis-munkaterületek a 2048-es és 3072-es, bájt méretű kulcsokkal rendelkező RSA-kulcsokat támogatják

> [!NOTE]
> A szinapszis-munkaterületek nem támogatják Elliptic-Curve titkosítási (ECC-) kulcsok használatát a titkosításhoz.

A következő szinapszis-összetevőkben lévő adatforgalom a munkaterület szintjén konfigurált ügyfél által felügyelt kulccsal van titkosítva:
* SQL-készletek
 * Dedikált SQL-készletek
 * Kiszolgáló nélküli SQL-készletek
* Apache Spark-készletek
* Azure Data Factory integrációs modulokat, folyamatokat és adatkészleteket.

## <a name="workspace-encryption-configuration"></a>Munkaterület titkosítási konfigurációja

A munkaterületek konfigurálhatók úgy, hogy lehetővé tegyék a dupla titkosítást egy ügyfél által felügyelt kulccsal a munkaterület létrehozásakor. Az új munkaterület létrehozásakor a "biztonság" lapon válassza a "dupla titkosítás engedélyezése ügyfél által felügyelt kulcs használatával" lehetőséget. Megadhat egy kulcs-azonosító URI-t, vagy kijelölhet egy, a munkaterülettel **azonos régióban** található kulcstartók listájából. Magának a Key Vaultnak engedélyezve kell lennie a **kiürítési védelemmel**.

> [!IMPORTANT]
> A kettős titkosítás konfigurációs beállítása a munkaterület létrehozása után nem módosítható.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Ez az ábra azt a beállítást mutatja be, amely lehetővé teszi, hogy a munkaterületet egy ügyfél által felügyelt kulccsal való dupla titkosításra engedélyezze.":::

### <a name="key-access-and-workspace-activation"></a>Kulcs-hozzáférés és munkaterület aktiválása

Az ügyfél által felügyelt kulcsokkal rendelkező Azure szinapszis titkosítási modell magában foglalja a munkaterületet, amely a Azure Key Vault kulcsait használja a titkosításhoz és a visszafejtéshez szükség szerint. A kulcsok elérhetővé válnak a munkaterület számára hozzáférési házirend vagy [Azure Key Vault RBAC hozzáférés](../../key-vault/general/rbac-guide.md)használatával. Amikor engedélyeket ad meg egy Azure Key Vault hozzáférési házirenden keresztül, válassza a házirend létrehozásakor a ["csak alkalmazás"](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) lehetőséget (válassza ki a munkaterület felügyelt identitását, és ne adja hozzá engedélyezett alkalmazásként).

 A munkaterület által felügyelt identitásnak meg kell adnia a Key Vault számára szükséges engedélyeket a munkaterület aktiválása előtt. Ez a munkaterületek aktiválásának szakaszos megközelítése biztosítja, hogy a munkaterületen lévő adatforgalom titkosítva legyen az ügyfél által felügyelt kulccsal. Vegye figyelembe, hogy a titkosítás engedélyezhető vagy letiltható a dedikált SQL-készletekben – az egyes készletek nem engedélyezettek alapértelmezés szerint a titkosításhoz.

#### <a name="permissions"></a>Engedélyek

Az inaktív adatok titkosításához vagy visszafejtéséhez a munkaterület által felügyelt identitásnak a következő engedélyekkel kell rendelkeznie:
* WrapKey (kulcs beszúrása Key Vault új kulcs létrehozásakor).
* UnwrapKey (a visszafejtési kulcs lekéréséhez).
* Get (a kulcs nyilvános részének beolvasásához)

#### <a name="workspace-activation"></a>Munkaterület aktiválása

Miután létrehozta a munkaterületet (a dupla titkosítás engedélyezve), a rendszer a "függő" állapotban marad, amíg az aktiválás sikeres lesz. A munkaterületet aktiválni kell, mielőtt teljes mértékben használni tudja az összes funkciót. Például csak akkor hozhat létre új dedikált SQL-készletet, ha az aktiválás sikeres. Adja meg a munkaterület felügyelt identitásának hozzáférését a kulcstartóhoz, és kattintson az aktiválás hivatkozásra a munkaterület Azure Portal-szalagcímben. Miután az aktiválás sikeresen befejeződik, a munkaterület készen áll arra, hogy a benne lévő összes adattal védve legyen az ügyfél által felügyelt kulccsal. Ahogy azt korábban említettük, a Key vaultnak engedélyezve kell lennie az aktiváláshoz, ha a kiürítési védelem sikeres.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Ez az ábra a munkaterülethez tartozó aktiválási hivatkozást tartalmazó szalagcímet jeleníti meg.":::


### <a name="manage-the-workspace-customer-managed-key"></a>A munkaterület ügyfél által felügyelt kulcsának kezelése 

A Azure Portal **titkosítási** lapján lévő adatok titkosításához használt ügyfél által felügyelt kulcs módosítható. Itt is kiválaszthat egy új kulcsot a kulcs azonosítójának használatával, vagy kiválaszthatja azokat a kulcstartók közül, amelyekhez a munkaterülettel azonos régióban férhet hozzá. Ha egy másik kulcstartóban lévő kulcsot választ a korábban használt adatok közül, adja meg a munkaterület által felügyelt identitás "Get", "wrap" és "kicsomagolás" engedélyeit az új kulcstartón. A munkaterület érvényesíti az új kulcstartóhoz való hozzáférését, és a munkaterületen lévő összes adattal újra titkosítva lesz az új kulccsal.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Ez az ábra a Azure Portal munkaterület-titkosítási szakaszát mutatja be.":::

>[!IMPORTANT]
>Egy munkaterület titkosítási kulcsának módosításakor tartsa meg a kulcsot, amíg a munkaterületen egy új kulccsal lecseréli. Ez lehetővé teszi az adatvisszafejtést a régi kulccsal, mielőtt újra titkosítja az új kulccsal.

Az Azure Key Vault-szabályzatok automatikusan, a kulcsok rendszeres elforgatásához vagy a kulcsok műveleteihez új kulcs-verziók létrehozását okozhatják. Megadhatja, hogy a munkaterületen lévő összes adattal újra titkosítja az aktív kulcs legújabb verzióját. A-re történő újratitkosításhoz módosítsa a kulcsát a Azure Portal egy ideiglenes kulcsra, majd váltson vissza a titkosításhoz használni kívánt kulcsra. Ha például az aktív kulcs Key1 legújabb verziójának használatával szeretné frissíteni az adattitkosítást, módosítsa a munkaterület ügyfél által felügyelt kulcsát az ideiglenes kulcsra, Key2. Várjon, amíg a Key2 befejeződik a titkosítás. Ezt követően állítsa vissza a munkaterület által felügyelt Key1 a munkaterületen a Key1 legújabb verziójára.

> [!NOTE]
> Az Azure szinapszis Analytics aktívan dolgozik azon funkciók hozzáadásán, amelyekkel automatikusan újratitkosíthatja az új kulcsokat a verziók létrehozásakor. Amíg ez a funkció elérhetővé válik, a munkaterület egységességének biztosításához kényszerítse az adattitkosítást a fenti eljárással.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>SQL-transzparens adattitkosítás szolgáltatás által felügyelt kulcsokkal

Az SQL transzparens adattitkosítás (TDE) elérhető a dedikált SQL-készletek számára a munkaterületeken, amelyek *nem* engedélyezettek a kettős titkosításhoz. Az ilyen típusú munkaterületeken a szolgáltatás által felügyelt kulcs a dedikált SQL-készletekben található adat kettős titkosításának biztosítására szolgál. A szolgáltatás által felügyelt kulccsal rendelkező TDE engedélyezhető vagy letiltható az egyes dedikált SQL-készleteknél.

## <a name="next-steps"></a>Következő lépések

[A beépített Azure-szabályzatok használatával titkosíthatja a szinapszis-munkaterületek titkosítási védelmét](../policy-reference.md)

