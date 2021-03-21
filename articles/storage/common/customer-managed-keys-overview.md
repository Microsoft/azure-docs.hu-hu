---
title: Ügyfél által felügyelt kulcsok a fiók titkosításához
titleSuffix: Azure Storage
description: A Storage-fiókban tárolt adatvédelmet saját titkosítási kulccsal is elvégezheti. Felhasználó által kezelt kulcs megadásakor a megadott kulccsal védi és szabályozza az adatokat titkosító kulcs hozzáférését. A felhasználó által kezelt kulcsokkal rugalmasabban kezelhető a hozzáférés-vezérlés.
services: storage
author: tamram
ms.service: storage
ms.date: 03/09/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 358be21a1f7f13c0c0ce1bf0e57dda53174216b2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517531"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz

A Storage-fiókban tárolt adatvédelmet saját titkosítási kulccsal is elvégezheti. Felhasználó által kezelt kulcs megadásakor a megadott kulccsal védi és szabályozza az adatokat titkosító kulcs hozzáférését. A felhasználó által kezelt kulcsokkal rugalmasabban kezelhető a hozzáférés-vezérlés.

Az ügyfél által felügyelt kulcsok tárolására Azure Key Vault vagy Azure Key Vault felügyelt hardveres biztonsági modul (HSM) (előzetes verzió) használata szükséges. Létrehozhatja saját kulcsait, és tárolhatja azokat a kulcstartóban vagy a felügyelt HSM-ben, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Storage-fióknak és a Key vaultnak vagy a felügyelt HSM-nek ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet.

További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/general/overview.md)

> [!IMPORTANT]
>
> Azure Key Vault felügyelt HSM-ben tárolt, ügyfél által felügyelt kulcsokkal való titkosítás jelenleg **előzetes** verzióban érhető el. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .
>
> A Azure Key Vault és Azure Key Vault felügyelt HSM a konfigurációhoz ugyanazokat az API-kat és felügyeleti interfészeket támogatja.

## <a name="about-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsok

Az alábbi ábra azt mutatja be, hogyan használja az Azure Storage a Azure Active Directory és a Key Vault vagy a Managed HSM használatával a kérelmeket az ügyfél által felügyelt kulcs segítségével:

![Ábra, amely bemutatja, hogyan működnek az ügyfél által felügyelt kulcsok az Azure Storage-ban](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

Az alábbi lista a diagram számozott lépéseit ismerteti:

1. Egy Azure Key Vault rendszergazda engedélyeket biztosít a titkosítási kulcsokhoz a Storage-fiókhoz társított felügyelt identitás számára.
2. Egy Azure Storage-rendszergazda a Storage-fiókhoz tartozó ügyfél által felügyelt kulccsal konfigurálja a titkosítást.
3. Az Azure Storage a Storage-fiókhoz társított felügyelt identitást használja a Azure Key Vaulthoz való hozzáférés hitelesítéséhez Azure Active Directory használatával.
4. Az Azure Storage becsomagolja a fiók titkosítási kulcsát a Azure Key Vault ügyfél kulcsával.
5. Az olvasási/írási műveletek esetében az Azure Storage kéréseket küld Azure Key Vaultnak a titkosítási és visszafejtési műveletek elvégzéséhez.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Ügyfél által felügyelt kulcsok a várólistákhoz és a táblákhoz

A várólista-és Table Storage-ban tárolt adatkészleteket nem védi automatikusan az ügyfél által felügyelt kulcs, ha az ügyfél által felügyelt kulcsok engedélyezve vannak a Storage-fiókhoz. Ezeket a szolgáltatásokat a Storage-fiók létrehozásakor is beállíthatja a védelembe.

További információ arról, hogyan hozható létre olyan Storage-fiók, amely támogatja az ügyfél által kezelt kulcsokat a várólistákhoz és a táblákhoz: [hozzon létre egy fiókot, amely támogatja a táblák és a várólisták ügyfél által felügyelt kulcsait](account-encryption-key-create.md).

A blob Storage-ban és a Azure Filesban lévő adatvédelmet mindig az ügyfél által felügyelt kulcsok védik, ha az ügyfél által felügyelt kulcsok konfigurálva vannak a Storage-fiókhoz.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Ügyfél által felügyelt kulcsok engedélyezése egy Storage-fiókhoz

Az ügyfél által felügyelt kulcs konfigurálásakor az Azure Storage a társított kulcstartóban vagy a felügyelt HSM-ben az ügyfél által felügyelt kulccsal együtt becsomagolja a fiók gyökérszintű adattitkosítási kulcsát. Az ügyfél által felügyelt kulcsok engedélyezése nem befolyásolja a teljesítményt, és azonnal érvénybe lép.

Ha engedélyezi vagy letiltja az ügyfelek által felügyelt kulcsokat, vagy ha módosítja a kulcsot vagy a kulcs verzióját, a rendszer megváltoztatja a gyökérszintű titkosítási kulcs védelmét, de az Azure Storage-fiókban lévő adatait nem szükséges újra titkosítani.

Az ügyfél által felügyelt kulcsok csak a meglévő Storage-fiókokon engedélyezhetők. A Key vaultot vagy a Managed HSM-t úgy kell konfigurálni, hogy engedélyeket adjon a Storage-fiókhoz társított felügyelt identitásnak. A felügyelt identitás csak a Storage-fiók létrehozása után érhető el.

Bármikor válthat az ügyfél által felügyelt kulcsok és a Microsoft által felügyelt kulcsok között. A Microsoft által kezelt kulcsokkal kapcsolatos további információkért lásd: [a titkosítási kulcsok kezelésének ismertetése](storage-service-encryption.md#about-encryption-key-management).

Ha meg szeretné tudni, hogyan konfigurálhatja az Azure Storage-titkosítást az ügyfél által felügyelt kulcsokkal a kulcstartóban, tekintse meg a [titkosítás konfigurálása a Azure Key Vaultban tárolt ügyfél által felügyelt kulcsokkal](customer-managed-keys-configure-key-vault.md)témakört Az ügyfél által felügyelt kulcsok felügyelt HSM-ben való konfigurálásával kapcsolatban lásd: [a titkosítás konfigurálása a Azure Key Vault felügyelt HSM-ben (előzetes verzió) tárolt, ügyfél által felügyelt kulcsokkal](customer-managed-keys-configure-key-vault-hsm.md).

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure AD szolgáltatásban az Azure-erőforrások felügyelt identitásait használják. A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket. Ha az ügyfél által felügyelt kulcsokat a Azure Portal konfigurálja, a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alatt lévő Storage-fiókhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a Storage-fiókot egyik Azure AD-címtárból a másikba, a Storage-fiókhoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok többé nem fognak működni. További információ: **előfizetés továbbítása Azure ad-címtárak között** [Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)kapcsolatban.  

Az Azure Storage encryption a 2048, 3072 és 4096 méretű RSA-és RSA-HSM-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért lásd: [a kulcsok ismertetése](../../key-vault/keys/about-keys.md).

A Key Vault vagy a Managed HSM használata a kapcsolódó költségekkel jár. További információ: [Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

Ha ügyfél által felügyelt kulcsokkal konfigurálja a titkosítást, két lehetőség közül választhat a kulcs verziójának frissítéséhez:

- **A kulcs verziójának automatikus frissítése:** Ha új verzió érhető el, az ügyfél által felügyelt kulcs automatikus frissítése, ha engedélyezi a titkosítást az ügyfél által felügyelt kulcsokkal a Storage-fiókhoz, hagyja ki a kulcs verzióját. Ha a kulcs verziója nincs megadva, az Azure Storage naponta ellenőrzi a Key vaultot vagy a felügyelt HSM-t az ügyfél által felügyelt kulcs új verziójára. Az Azure Storage automatikusan a kulcs legújabb verzióját használja.
- **A kulcs verziójának manuális frissítése:** Ha az Azure Storage-titkosításhoz egy kulcs adott verzióját szeretné használni, akkor a kulcs verziószámát a Storage-fiókhoz tartozó ügyfél által felügyelt kulcsokkal engedélyezheti. Ha megadja a kulcs verzióját, az Azure Storage ezt a verziót használja a titkosításhoz, amíg manuálisan nem frissíti a kulcs verzióját.

    Ha a kulcs verziója explicit módon meg van adva, akkor manuálisan kell frissítenie a Storage-fiókot, hogy az új verzió létrehozásakor az új kulcs-verzió URI-JÁT használja. A következő témakörből megtudhatja, hogyan frissítheti a Storage-fiókot a kulcs új verziójának használatára: a [titkosítás konfigurálása az Azure Key Vault tárolt, ügyfél által felügyelt kulcsokkal](customer-managed-keys-configure-key-vault.md) , illetve a [titkosítás konfigurálása Azure Key Vault Managed HSM-ben (előzetes verzió) tárolt ügyfél által felügyelt kulcsokkal](customer-managed-keys-configure-key-vault-hsm.md).

A kulcs verziójának frissítésekor a rendszer megváltoztatja a gyökérszintű titkosítási kulcs védelmét, de az Azure Storage-fiókban lévő adatai nem kerülnek újra titkosításra. Nincs szükség további műveletre a felhasználótól.

> [!NOTE]
> A kulcs elforgatásához hozzon létre egy új verziót a Key vaultban vagy a felügyelt HSM-ben a megfelelőségi szabályzatoknak megfelelően. Manuálisan is elforgathatja a kulcsot, vagy létrehozhat egy függvényt, amellyel elforgathatja azt egy ütemezett időpontban.

## <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Bármikor visszavonhatja a Storage-fiók hozzáférését az ügyfél által felügyelt kulcshoz. Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonása után vagy a kulcs letiltását vagy törlését követően az ügyfelek nem hívhatnak meg egy blobba vagy annak metaadataiba beolvasott vagy írt műveleteket. A következő műveletek bármelyikének meghívására tett kísérletek sikertelenek lesznek: 403 (tiltott) hibakód minden felhasználó esetében:

- [Blobok listázása](/rest/api/storageservices/list-blobs)a `include=metadata` kérelem URI-ja paraméterének meghívásakor
- [BLOB beolvasása](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [BLOB metaadatainak beolvasása](/rest/api/storageservices/get-blob-metadata)
- [BLOB metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata)
- [Pillanatkép-blob](/rest/api/storageservices/snapshot-blob), a `x-ms-meta-name` kérelem fejlécének hívásakor
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [BLOB másolása URL-címről](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Put blokk](/rest/api/storageservices/put-block)
- [Blokk elhelyezése URL-címről](/rest/api/storageservices/put-block-from-url)
- [Blokk hozzáfűzése](/rest/api/storageservices/append-block)
- [Blokk hozzáfűzése URL-címről](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Oldal elhelyezése](/rest/api/storageservices/put-page)
- [Oldal elhelyezése az URL-címről](/rest/api/storageservices/put-page-from-url)
- [Növekményes másolási blob](/rest/api/storageservices/incremental-copy-blob)

A műveletek ismételt meghívásához állítsa vissza a hozzáférést az ügyfél által felügyelt kulcshoz.

Az ebben a szakaszban nem szereplő adatműveletek az ügyfél által felügyelt kulcsok visszavonása, illetve a kulcs letiltását vagy törlését követően is folytatódnak.

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a [PowerShellt](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) vagy az [Azure CLI](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys)-t.

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Ügyfél által felügyelt kulcsok az Azure Managed Disks szolgáltatáshoz

Az ügyfél által felügyelt kulcsok az Azure Managed Disks titkosításának kezelésére is használhatók. Az ügyfél által felügyelt kulcsok eltérően működnek a felügyelt lemezeken, mint az Azure Storage-erőforrások esetében. További információkért lásd: az Azure Managed [Disks szolgáltatás kiszolgálóoldali titkosítása](../../virtual-machines/disk-encryption.md) a Windows rendszerhez vagy az [Azure Managed Disks](../../virtual-machines/disk-encryption.md) for Linux szolgáltatás kiszolgálóoldali titkosítása.

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Titkosítás konfigurálása az Azure Key Vaultban tárolt, ügyfél által kezelt kulcsokkal](customer-managed-keys-configure-key-vault.md)
- [A titkosítás konfigurálása Azure Key Vault felügyelt HSM-ben (előzetes verzió) tárolt, ügyfél által felügyelt kulcsokkal](customer-managed-keys-configure-key-vault-hsm.md)
