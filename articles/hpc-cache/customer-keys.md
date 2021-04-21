---
title: Ügyfél által használt kulcsok használata az adatok titkosításához a Azure HPC Cache
description: Az Azure Key Vault használata Azure HPC Cache titkosítási kulcshoz való hozzáférés szabályozása az alapértelmezett Microsoft által felügyelt titkosítási kulcsok használata helyett
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 36ce494c7fd51a1341834d5c231e32e60c5a32b9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751993"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Felhasználó által felügyelt titkosítási kulcsok használata a Azure HPC Cache

A Azure Key Vault használhatja az adatok titkosításához használt kulcsok tulajdonjogának szabályozására a Azure HPC Cache. Ez a cikk bemutatja, hogyan használhatja az ügyfél által kezelt kulcsokat a gyorsítótár-adattitkosításhoz.

> [!NOTE]
> Az Azure-ban tárolt összes adat, beleértve a gyorsítótárlemezeket is, alapértelmezés szerint a Microsoft által kezelt kulcsokkal van titkosítva. Csak akkor kell követnie a cikkben található lépéseket, ha kezelni szeretné az adatok titkosításához használt kulcsokat.

Azure HPC Cache a gyorsítótárazott adatokat tároló felügyelt lemezeken a virtuálisgép-gazdagép titkosítása is védelmet nyújt, még akkor is, ha hozzáad egy ügyfélkulcsot a gyorsítótárlemezek számára. [](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) Ha egy felhasználó által felügyelt kulcsot ad hozzá a kettős titkosításhoz, azzal magasabb szintű biztonságot nyújt a magas biztonsági igényű ügyfelek számára. További részletekért olvassa el az [Azure Disk Storage kiszolgálóoldali](../virtual-machines/disk-encryption.md) titkosítását.

Az ügyfél által felügyelt kulcstitkosítás engedélyezésének három lépése Azure HPC Cache:

1. Állítson be egy Azure Key Vault a kulcsok tárolására.
1. A kulcs Azure HPC Cache válassza az ügyfél által felügyelt kulcstitkosítás lehetőséget, és adja meg a használni kívánt kulcstartót és kulcsot.
1. A gyorsítótár létrehozása után engedélyezze számára a kulcstartóhoz való hozzáférést.

A titkosítás csak azután lesz teljesen beállítva, hogy engedélyezte az újonnan létrehozott gyorsítótárból (3. lépés). Ennek az az oka, hogy a gyorsítótár identitását át kell adni a kulcstartónak, hogy jogosult felhasználó legyen. Ez a gyorsítótár létrehozása előtt nem létezik, mert az identitás addig nem létezik, amíg létre nem jön a gyorsítótár.

A gyorsítótár létrehozása után nem lehet módosítani az ügyfél által kezelt és a Microsoft által kezelt kulcsok között. Ha azonban a gyorsítótár ügyfél által [](#update-key-settings) kezelt kulcsokat használ, szükség szerint módosíthatja a titkosítási kulcsot, a kulcsverziót és a kulcstartót.

## <a name="understand-key-vault-and-key-requirements"></a>A key vault és a kulcskövetelmények

A kulcstartónak és a kulcsnak meg kell felelnie ezeknek a követelményeknek a Azure HPC Cache.

Kulcstartó tulajdonságai:

* **Előfizetés** – Használja ugyanazt az előfizetést, mint a gyorsítótárhoz.
* **Régió** – A kulcstartónak ugyanabban a régióban kell lennie, mint a Azure HPC Cache.
* **Tarifacsomag** – A Standard szint elegendő a Azure HPC Cache.
* **Soft delete** –Azure HPC Cache engedélyezi a soft delete parancsot, ha az még nincs konfigurálva a kulcstartón.
* **Véglegesen kiürítés elleni** védelem – Engedélyezni kell a véglegesen kiürítés elleni védelmet.
* **Hozzáférési szabályzat** – Az alapértelmezett beállítások elegendőek.
* **Hálózati kapcsolat** – Azure HPC Cache kulcstartónak a választott végpontbeállításoktól függetlenül hozzá kell férnie a kulcstartóhoz.

Fő tulajdonságok:

* **Kulcstípus** – RSA
* **RSA-kulcs mérete** – 2048
* **Engedélyezve** – Igen

Kulcstartó-hozzáférési engedélyek:

* Az új szerepkört Azure HPC Cache felhasználónak a közreműködői [szerepkörnek Key Vault rendelkeznie.](../role-based-access-control/built-in-roles.md#key-vault-contributor) Ugyanezek az engedélyek szükségesek a Azure Key Vault.

  További [információ: Secure access to a key vault](../key-vault/general/security-overview.md) (Biztonságos hozzáférés a kulcstartóhoz).

## <a name="1-set-up-azure-key-vault"></a>1. A Azure Key Vault

Beállíthatja a kulcstartót és a kulcsot a gyorsítótár létrehozása előtt, vagy a gyorsítótár létrehozásának részeként. Győződjön meg arról, hogy ezek az erőforrások megfelelnek a fent ismertetett [követelményeknek.](#understand-key-vault-and-key-requirements)

A gyorsítótár létrehozásakor meg kell adnia a gyorsítótár titkosítására használni kívánt tárolót, kulcsot és kulcsverziót.

A [részletekért olvassa Azure Key Vault dokumentációját.](../key-vault/general/overview.md)

> [!NOTE]
> A Azure Key Vault előfizetést kell használnia, és ugyanabban a régióban kell lennie, mint a Azure HPC Cache. Győződjön meg arról, hogy a választott régió [mindkét terméket támogatja.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Gyorsítótár létrehozása engedélyezett, felhasználó által kezelt kulcsokkal

A titkosítási kulcs forrását a titkosítási kulcs létrehozásakor Azure HPC Cache. Kövesse a [Create an Azure HPC Cache](hpc-cache-create.md)(Új kulcs létrehozása) Azure HPC Cache key vault and key (Kulcstartó és kulcs) adatokat a **Disk encryption keys (Lemeztitkosítási kulcsok)** lapon. A gyorsítótár létrehozása során létrehozhat egy új kulcstartót és kulcsot.

> [!TIP]
> Ha a **Lemeztitkosítási** kulcsok lap nem jelenik meg, győződjön meg arról, hogy a gyorsítótár a támogatott régiók [valamelyikében található.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)

A gyorsítótárat létrehozó felhasználónak legalább a közreműködői szerepkör Key Vault [kell](../role-based-access-control/built-in-roles.md#key-vault-contributor) lennie.

1. Kattintson a gombra a privát módon kezelt kulcsok engedélyezéséhez. A beállítás módosítása után megjelennek a Key Vault beállításai.

1. Kattintson **a Kulcstartó kiválasztása elemre** a kulcsválasztási oldal megnyitásához. Válassza ki vagy hozza létre a kulcstartót és a kulcsot a gyorsítótár lemezén található adatok titkosításához.

   Ha a Azure Key Vault nem jelenik meg a listában, ellenőrizze az alábbi követelményeket:

   * A gyorsítótár ugyanabban az előfizetésben található, mint a kulcstartó?
   * A gyorsítótár és a kulcstartó ugyanabban a régióban található?
   * Van hálózati kapcsolat a Azure Portal és a kulcstartó között?

1. A tároló kiválasztása után válassza ki az egyes kulcsokat az elérhető lehetőségek közül, vagy hozzon létre egy újat. A kulcsnak 2048 bites RSA-kulcsnak kell lennie.

1. Adja meg a kiválasztott kulcs verzióját. További információ a verziószámozásról: [Azure Key Vault dokumentációja.](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)

Folytassa a többi specifikációval, és hozza létre a gyorsítótárat a Create an Azure HPC Cache (Új alkalmazás [létrehozása) Azure HPC Cache.](hpc-cache-create.md)

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Titkosítás Azure Key Vault a gyorsítótárból
<!-- header is linked from create article, update if changed -->

Néhány perc múlva az új Azure HPC Cache megjelenik a Azure Portal. Az Áttekintés **lapon engedélyezze** a hozzáférését a Azure Key Vault és engedélyezze az ügyfél által felügyelt kulcstitkosítást.

> [!TIP]
> Előfordulhat, hogy a gyorsítótár megjelenik az erőforrások listájában, mielőtt az "üzembe helyezés folyamatban" üzenetek törlődnek. Egy-két perc után ellenőrizze az erőforrások listáját ahelyett, hogy megvárja a sikerességről szóló értesítést.

Erre a kétlépéses folyamatra azért van szükség, mert Azure HPC Cache-példánynak egy identitást kell átadnia a Azure Key Vault számára az engedélyezéshez. A gyorsítótár-identitás addig nem létezik, amíg a létrehozás kezdeti lépései be nem fejeződtek.

> [!NOTE]
> A titkosítást a gyorsítótár létrehozása után 90 percen belül engedélyeznie kell. Ha nem fejeződik be ez a lépés, a gyorsítótár időkorrekta lesz, és meghiúsul. A sikertelen gyorsítótárat újra létre kell hozni, nem javítható.

A gyorsítótárban a Várakozás a **kulcsra állapot látható.** Kattintson az **oldal tetején található** Titkosítás engedélyezése gombra, hogy engedélyezze a gyorsítótár számára a megadott kulcstartóhoz való hozzáférést.

![képernyőkép a portál gyorsítótáráttekintő oldaláról, kiemelve a Titkosítás engedélyezése gombot (felső sor) és az Állapot: Várakozás a kulcsra](media/waiting-for-key.png)

Kattintson **a Titkosítás engedélyezése elemre,** majd kattintson az **Igen** gombra a titkosítási kulcs használatának engedélyezéséhez a gyorsítótár számára. Ez a művelet a kulcstartóban is engedélyezi a törlés és végleges törlés elleni védelmet (ha még nincs engedélyezve).

![a portál gyorsítótáráttekintő oldalának képernyőképe, felül egy szalagcím-üzenettel, amely a titkosítás engedélyezését kéri a felhasználótól az igen gombra kattintva](media/enable-keyvault.png)

Miután a gyorsítótár hozzáférést kér a kulcstartóhoz, létrehozhatja és titkosíthatja a gyorsítótárazott adatokat tároló lemezeket.

Miután engedélyezte a titkosítást, Azure HPC Cache beállítás több percig is eltarthat, és létrehozza a titkosított lemezeket és a kapcsolódó infrastruktúrát.

## <a name="update-key-settings"></a>Kulcsbeállítások frissítése

A gyorsítótár kulcstartóját, kulcsát vagy kulcsverzióját a Azure Portal. Kattintson a gyorsítótár Titkosítási **beállítások hivatkozására** az **Ügyfélkulcs-beállítások lap megnyitásához.**

A gyorsítótár nem változtatható meg az ügyfél által kezelt és a rendszer által kezelt kulcsok között.

![képernyőkép az "Ügyfélkulcsok beállításai" lapról, amelyet a beállítások > titkosítás elemre kattintva ér el a Azure Portal](media/change-key-click.png)

Kattintson **a Kulcs módosítása hivatkozásra,** majd a **Kulcstartó,** kulcs vagy verzió módosítása elemre a kulcsválasztó megnyitásához.

![képernyőkép a "kulcs kiválasztása a Azure Key Vault" oldalról három legördülő választóval a kulcstartó, kulcs és verzió kiválasztásához](media/select-new-key.png)

A listában az ugyanabban az előfizetésben és régióban lévő kulcstartók jelennek meg.

Az új titkosítási kulcs értékeinek kiválasztása után kattintson a **Kijelölés gombra.** Megjelenik egy megerősítő oldal az új értékekkel. Kattintson **a Mentés gombra** a kijelölés végleges mentéshez.

![a megerősítési oldal képernyőképe a bal felső Mentés gombbal](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>További információ az Azure-beli, ügyfél által kezelt kulcsokról

Ezek a cikkek bővebben ismertetik, hogyan lehet Azure Key Vault felhasználó által felügyelt kulcsokkal titkosítani az adatokat az Azure-ban:

* [Az Azure Storage-titkosítás áttekintése](../storage/common/storage-service-encryption.md)
* [Lemeztitkosítás](../virtual-machines/disk-encryption.md#customer-managed-keys) ügyfél által felügyelt kulcsokkal – A felügyelt Azure Key Vault való használatának dokumentációja, amely hasonló az Azure HPC Cache

## <a name="next-steps"></a>Következő lépések

Miután létrehozta a Azure HPC Cache és engedélyezte a Key Vault-alapú titkosítást, folytassa a gyorsítótár beállítását úgy, hogy hozzáférést biztosít az adatforráshoz.

* [Céltárak hozzáadása](hpc-cache-add-storage.md)
