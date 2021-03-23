---
title: Az Azure HPC cache-ben az ügyfelek által összefoglalt kulcsok használatával titkosíthatja az adattitkosítást
description: A Azure Key Vault használata az Azure HPC cache használatával a titkosítási kulcs elérésének szabályozásához a Microsoft által felügyelt alapértelmezett titkosítási kulcsok használata helyett
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: f587de4ee2ce051cb771db90d7f9ce00ce66b07f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772705"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Ügyfél által felügyelt titkosítási kulcsok használata az Azure HPC cache-hez

A Azure Key Vault használatával szabályozhatja az Azure HPC cache-ben tárolt adatai titkosításához használt kulcsok tulajdonjogát. Ez a cikk azt ismerteti, hogyan használhatók az ügyfél által felügyelt kulcsok a gyorsítótár-adattitkosításhoz.

> [!NOTE]
> A rendszer alapértelmezés szerint a Microsoft által felügyelt kulcsok használatával titkosítja az Azure-ban tárolt összes, a gyorsítótár-lemezeket is tartalmazó adatforgalomat. A cikkben leírt lépéseket csak akkor kell végrehajtania, ha az adattitkosításhoz használt kulcsokat szeretné kezelni.

Az Azure HPC cache-t a [virtuális gépek gazdagépének titkosítása](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) is védi a gyorsítótárazott adatait tároló felügyelt lemezeken, még akkor is, ha felvesznek egy ügyfél-kulcsot a gyorsítótár-lemezekhez. Az ügyfél által felügyelt kulcs dupla titkosításhoz való hozzáadásával további biztonsági szint biztosítható a magas biztonsági igényeket kielégítő ügyfelek számára. A részletekért olvassa el az [Azure Disk Storage kiszolgálóoldali titkosítását ismertető oldalt](../virtual-machines/disk-encryption.md) .

Három lépésben engedélyezheti az ügyfél által felügyelt kulcsos titkosítást az Azure HPC cache-ben:

1. Állítson be egy Azure Key Vault a kulcsok tárolásához.
1. Az Azure HPC-gyorsítótár létrehozásakor válassza az ügyfél által felügyelt kulcs titkosítása lehetőséget, és adja meg a használni kívánt kulcstartót és kulcsot.
1. A gyorsítótár létrehozása után engedélyezze, hogy hozzáférjen a kulcstartóhoz.

A titkosítás nincs teljesen beállítva egészen addig, amíg az újonnan létrehozott gyorsítótárból nem engedélyezte (3. lépés). Ennek az az oka, hogy a gyorsítótár identitását át kell adni a Key vaultnak, hogy az egy erre a felhasználóra vonatkozó engedélyt adjon. Ezt a gyorsítótár létrehozása előtt nem teheti meg, mert az identitás nem létezik a gyorsítótár létrehozása előtt.

A gyorsítótár létrehozása után az ügyfél által felügyelt kulcsok és a Microsoft által felügyelt kulcsok között nem módosítható. Ha azonban a gyorsítótár ügyfél által felügyelt kulcsokat használ, igény szerint [módosíthatja](#update-key-settings) a titkosítási kulcsot, a kulcs verzióját és a kulcstartót.

## <a name="understand-key-vault-and-key-requirements"></a>A Key Vault és a legfontosabb követelmények ismertetése

A Key vaultnak és a kulcsnak meg kell felelnie az alábbi követelményeknek az Azure HPC cache-vel való együttműködéshez.

Key Vault tulajdonságai:

* **Előfizetés** – használja ugyanazt az előfizetést, mint amelyet a gyorsítótárhoz használ.
* **Régió** – a Key vaultnak ugyanabban a régióban kell lennie, mint az Azure HPC cache-nek.
* A standard szintű **díjszabás** elegendő az Azure HPC cache-vel való használatra.
* **Soft delete** – az Azure HPC cache lehetővé teszi a helyreállítható törlést, ha még nincs konfigurálva a Key vaultban.
* **Védelem kiürítése** – a kiürítés védelmét engedélyezni kell.
* **Hozzáférési szabályzat** – az alapértelmezett beállítások elegendőek.
* **Hálózati kapcsolat** – az Azure HPC cache-nek képesnek kell lennie a kulcstartó elérésére, a választott végponti beállításoktól függetlenül.

Kulcs tulajdonságai:

* **Kulcs típusa** – RSA
* **RSA-kulcs mérete** – 2048
* **Engedélyezve** – igen

Key Vault-hozzáférési engedélyek:

* Az Azure HPC-gyorsítótárat létrehozó felhasználónak az [Key Vault közreműködő szerepkörrel](../role-based-access-control/built-in-roles.md#key-vault-contributor)egyenértékű jogosultságokkal kell rendelkeznie. A Azure Key Vault beállításához és kezeléséhez ugyanazok az engedélyek szükségesek.

  További információért olvassa el [a Key Vault biztonságos elérését](../key-vault/general/secure-your-key-vault.md) ismertető témakört.

## <a name="1-set-up-azure-key-vault"></a>1. Azure Key Vault beállítása

A gyorsítótár létrehozása előtt beállíthat egy kulcstartót és egy kulcsot, vagy megteheti azt a gyorsítótár létrehozása részeként. Győződjön meg arról, hogy ezek az erőforrások megfelelnek a [fent](#understand-key-vault-and-key-requirements)ismertetett követelményeknek.

A gyorsítótár-létrehozási időpontnál meg kell adnia a gyorsítótár titkosításához használni kívánt tárolót, kulcsot és kulcs verzióját.

A részletekért olvassa el a [Azure Key Vault dokumentációját](../key-vault/general/overview.md) .

> [!NOTE]
> A Azure Key Vaultnak ugyanazt az előfizetést kell használnia, és ugyanabban a régióban kell lennie, mint az Azure HPC cache-nek. Győződjön meg arról, hogy a választott régió [támogatja mindkét terméket](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. a gyorsítótár létrehozása az ügyfél által felügyelt kulcsokkal engedélyezve

Az Azure HPC-gyorsítótár létrehozásakor meg kell adnia a titkosítási kulcs forrását. Kövesse az [Azure HPC cache létrehozása](hpc-cache-create.md)című témakör utasításait, majd a **lemez titkosítási kulcsainak** lapon válassza a Key vaultot és a kulcsot. A gyorsítótár létrehozása során új kulcstartót és kulcsot is létrehozhat.

> [!TIP]
> Ha a **lemez titkosítási kulcsainak** lapja nem jelenik meg, győződjön meg arról, hogy a gyorsítótár az egyik [támogatott régióban](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)található.

A gyorsítótárat létrehozó felhasználónak jogosultsággal kell rendelkeznie a [Key Vault közreműködő szerepkörrel](../role-based-access-control/built-in-roles.md#key-vault-contributor) vagy annál magasabb jogosultsággal.

1. Kattintson a gombra a magánkézben lévő felügyelt kulcsok engedélyezéséhez. A beállítás módosítása után megjelennek a Key Vault beállításai.

1. Kattintson a **Key Vault kiválasztása** elemre a kulcs kiválasztása lap megnyitásához. Válasszon ki vagy hozzon létre egy kulcstartót és kulcsot a gyorsítótárban lévő lemezek adatainak titkosításához.

   Ha a Azure Key Vault nem jelenik meg a listában, ellenőrizze a következő követelményeket:

   * A gyorsítótár ugyanabban az előfizetésben található, mint a Key Vault?
   * A gyorsítótár ugyanabban a régióban található, mint a Key Vault?
   * Van hálózati kapcsolat a Azure Portal és a Key Vault között?

1. A tár kiválasztása után válassza ki az egyes kulcsokat az elérhető beállítások közül, vagy hozzon létre egy új kulcsot. A kulcsnak 2048 bites RSA-kulcsnak kell lennie.

1. A kiválasztott kulcs verziószámának megadása. További információ a verziószámozásról: [Azure Key Vault dokumentáció](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Folytassa a többi specifikációval, és hozza létre a gyorsítótárat az [Azure HPC cache létrehozása](hpc-cache-create.md)című témakörben leírtak szerint.

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Azure Key Vault titkosítás engedélyezése a gyorsítótárból
<!-- header is linked from create article, update if changed -->

Néhány perc elteltével megjelenik az új Azure HPC-gyorsítótár a Azure Portal. Az **Áttekintés** lapon engedélyezheti a Azure Key Vault elérését, és engedélyezheti az ügyfél által felügyelt kulcs titkosítását.

> [!TIP]
> Előfordulhat, hogy a gyorsítótár a "központi telepítés folyamatban" üzenetek törlése előtt megjelenhet az erőforrások listájában. A sikeres értesítés megvárnia után egy-két perc múlva vizsgálja meg az erőforrások listáját.

Erre a kétlépéses folyamatra azért van szükség, mert az Azure HPC cache-példánynak identitásra van szüksége ahhoz, hogy átadja a Azure Key Vault az engedélyezéshez. A gyorsítótár-identitás addig nem létezik, amíg a kezdeti létrehozási lépések be nem fejeződik.

> [!NOTE]
> A gyorsítótár létrehozása után 90 percen belül engedélyeznie kell a titkosítást. Ha nem hajtja végre ezt a lépést, a gyorsítótár időtúllépést jelez, és sikertelen lesz. A sikertelen gyorsítótárat újra létre kell hozni, nem lehet rögzíteni.

A gyorsítótár a **kulcsra váró** állapotot jeleníti meg. A lap tetején található **titkosítás engedélyezése** gombra kattintva engedélyezheti a gyorsítótár számára a megadott kulcstartó elérését.

![a portál gyorsítótár-Áttekintés lapjának képernyőképe a titkosítás engedélyezése gomb (felső sor) és az állapot: várakozás a kulcsra](media/waiting-for-key.png)

Kattintson a **titkosítás engedélyezése** lehetőségre, majd az **Igen** gombra kattintva engedélyezze a gyorsítótár számára a titkosítási kulcs használatát. Ezzel a művelettel a kulcstartón is engedélyezheti a törlést és a kiürítést (ha még nincs engedélyezve).

![a portál gyorsítótár-Áttekintés oldalának képernyőképe egy, a tetején található szalagcím-üzenettel, amely arra kéri a felhasználót, hogy engedélyezze a titkosítást az Igen gombra kattintva.](media/enable-keyvault.png)

Miután a gyorsítótár hozzáfér a kulcstartóhoz, létrehozhatja és titkosíthatja a gyorsítótárazott fájlokat tároló lemezeket.

Miután engedélyezte a titkosítást, az Azure HPC cache több percen belül bekerül a telepítőből a titkosított lemezek és a kapcsolódó infrastruktúra létrehozásához.

## <a name="update-key-settings"></a>Kulcs beállításainak frissítése

A gyorsítótár kulcstárolójának, kulcsának vagy kulcsának verziószámát a Azure Portal lehet módosítani. Kattintson a gyorsítótár **titkosítási** beállítások hivatkozására a **Customer Key Settings** lap megnyitásához.

Az ügyfél által felügyelt kulcsok és a rendszer által felügyelt kulcsok közötti gyorsítótár nem módosítható.

![a "Customer Key Settings" (ügyfél-kulcsok beállításai) oldal képernyőképe, amelyet a beállítások > titkosítás elemre kattintva érhet el a gyorsítótár oldaláról Azure Portal](media/change-key-click.png)

Kattintson a kulcs **módosítása** hivatkozásra, majd kattintson a Key **Vault, a kulcs vagy a verzió módosítása** elemre a kulcs választójának megnyitásához.

![a Key Vault, a kulcs és a verzió kiválasztására szolgáló, a "kulcs kiválasztása a Azure Key Vaultból" lap képernyőképe](media/select-new-key.png)

Ugyanabban az előfizetésben és ugyanabban a régióban található kulcstartók jelennek meg a listában.

Miután kiválasztotta az új titkosítási kulcs értékeit, kattintson a **kiválasztás** elemre. Megjelenik egy megerősítő lap az új értékekkel. A kijelölés véglegesítéséhez kattintson a **Mentés** gombra.

![képernyőkép a megerősítő oldalról a bal felső sarokban található mentés gombbal](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>További információ az ügyfél által felügyelt kulcsokról az Azure-ban

Ezek a cikkek részletesen ismertetik a Azure Key Vault és az ügyfél által felügyelt kulcsok használatát az Azure-ban tárolt adattitkosításhoz:

* [Az Azure Storage-titkosítás áttekintése](../storage/common/storage-service-encryption.md)
* [Lemezek titkosítása az ügyfél által felügyelt kulcsokkal](../virtual-machines/disk-encryption.md#customer-managed-keys) – dokumentáció a Azure Key Vault felügyelt lemezekkel való használatához, amely hasonló forgatókönyv az Azure HPC cache-hez

## <a name="next-steps"></a>Következő lépések

Miután létrehozta az Azure HPC-gyorsítótárat és a jogosult Key Vault-alapú titkosítást, folytassa a gyorsítótár beállítását úgy, hogy hozzáférést biztosít az adatforrásokhoz.

* [Céltárak hozzáadása](hpc-cache-add-storage.md)
