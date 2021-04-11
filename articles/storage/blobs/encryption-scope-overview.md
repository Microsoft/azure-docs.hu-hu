---
title: BLOB Storage titkosítási hatókörök
description: A titkosítási hatókörök lehetővé teszik a titkosítás kezelését a tároló vagy egy egyedi blob szintjén. A titkosítási hatókörök használatával biztonságos határokat hozhat létre az azonos Storage-fiókban található, de különböző ügyfelekhez tartozó adategységek között.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640454"
---
# <a name="encryption-scopes-for-blob-storage"></a>BLOB Storage titkosítási hatókörök

A titkosítási hatókörök lehetővé teszik a titkosítás kezelését olyan kulccsal, amely egy tárolóra vagy egy egyedi blobra terjed ki. A titkosítási hatókörök használatával biztonságos határokat hozhat létre az azonos Storage-fiókban található, de különböző ügyfelekhez tartozó adategységek között.

További információ a titkosítási hatókörök használatáról: [titkosítási hatókörök létrehozása és kezelése](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>A titkosítási hatókörök működése

Alapértelmezés szerint a Storage-fiók egy olyan kulccsal van titkosítva, amely a teljes Storage-fiókra terjed ki. Titkosítási hatókör meghatározásakor olyan kulcsot kell megadnia, amely egy tárolóra vagy egy egyedi blobra is kiterjed. Ha a titkosítási hatókört egy blobra alkalmazza, a blob titkosítva lesz ezzel a kulccsal. Ha a titkosítási hatókört egy tárolóra alkalmazza a rendszer, az a tárolóban lévő Blobok alapértelmezett hatóköre lesz, így az adott tárolóba feltöltött összes blob ugyanazzal a kulccsal titkosítva lehet. A tároló beállítható úgy, hogy a tároló összes blobjának alapértelmezett titkosítási hatókörét érvényesítse, vagy hogy egy adott blobot az alapértelmezetttől eltérő titkosítási hatókörrel töltse fel a tárolóba.

A titkosítási hatókörrel létrehozott Blobok olvasási műveletei transzparens módon történnek, feltéve, hogy a titkosítási hatókör nincs letiltva.

### <a name="key-management"></a>Kulcskezelés

Ha meghatároz egy titkosítási hatókört, megadhatja, hogy a hatókör védett-e egy Microsoft által felügyelt kulccsal, vagy egy ügyfél által felügyelt kulccsal, amelyet a Azure Key Vault tárol. Ugyanazon a Storage-fiókon belül a különböző titkosítási hatókörök a Microsoft által felügyelt vagy az ügyfél által felügyelt kulcsokat egyaránt használhatják. Az ügyfél által felügyelt kulcsból egy Microsoft által felügyelt kulcsra, vagy fordítva is lehetőség van arra, hogy egy titkosítási hatókört a felhasználó által felügyelt kulccsal lehessen védelemmel ellátni. Az ügyfél által felügyelt kulcsokkal kapcsolatos további információkért lásd: [ügyfél által felügyelt kulcsok az Azure Storage encryption szolgáltatáshoz](../common/customer-managed-keys-overview.md). A Microsoft által kezelt kulcsokkal kapcsolatos további információkért lásd: [a titkosítási kulcsok kezelésének ismertetése](../common/storage-service-encryption.md#about-encryption-key-management).

Ha ügyfél által felügyelt kulccsal határoz meg titkosítási hatókört, akkor a kulcs verziójának automatikus vagy manuális frissítését is választhatja. Ha úgy dönt, hogy automatikusan frissíti a kulcs verzióját, akkor az Azure Storage naponta ellenőrzi a Key vaultot vagy a felügyelt HSM-t az ügyfél által felügyelt kulcs új verziójára, és automatikusan frissíti a kulcsot a legújabb verzióra. További információ az ügyfél által felügyelt kulcs verziószámának frissítéséről: [a kulcs verziójának frissítése](../common/customer-managed-keys-overview.md#update-the-key-version).

Előfordulhat, hogy a Storage-fiók legfeljebb 10 000 titkosítási hatókörrel rendelkezik, amelyek olyan ügyfél által felügyelt kulcsokkal vannak védve, amelyekhez a rendszer automatikusan frissíti a kulcs verzióját. Ha a Storage-fióknak már van olyan 10 000 titkosítási hatóköre, amelyet a rendszer automatikusan frissít az ügyfél által felügyelt kulcsokkal, akkor a kulcs verzióját manuálisan kell frissíteni az ügyfél által felügyelt kulcsokkal védett további titkosítási hatókörökhöz.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>A tárolók és Blobok titkosítási hatókörei

Tároló létrehozásakor megadhat egy alapértelmezett titkosítási hatókört a tárolóba később feltöltött blobokhoz. Amikor megad egy tároló alapértelmezett titkosítási hatókörét, eldöntheti, hogyan kényszeríti ki az alapértelmezett titkosítási hatókört:

- Megkövetelheti, hogy a tárolóba feltöltött összes blob az alapértelmezett titkosítási hatókört használja. Ebben az esetben a tárolóban lévő összes blob ugyanazzal a kulccsal van titkosítva.
- Engedélyezheti, hogy az ügyfél felülbírálja a tároló alapértelmezett titkosítási hatókörét, így a Blobok az alapértelmezett hatókörtől eltérő titkosítási hatókörrel is feltölthetők. Ebben az esetben előfordulhat, hogy a tárolóban lévő Blobok különböző kulcsokkal vannak titkosítva.

A következő táblázat összefoglalja a Blobok feltöltési műveleteinek viselkedését attól függően, hogy az alapértelmezett titkosítási hatókör hogyan legyen konfigurálva a tárolóhoz:

| A tárolóban definiált titkosítási hatókör... | BLOB feltöltése az alapértelmezett titkosítási hatókörrel... | BLOB feltöltése az alapértelmezett hatókörtől eltérő titkosítási hatókörrel... |
|--|--|--|
| Alapértelmezett titkosítási hatókör megengedett felülbírálásokkal | Sikeres | Sikeres |
| Olyan alapértelmezett titkosítási hatókör, amelynek felülbírálásai tiltottak | Sikeres | Sikertelen |

A tároló létrehozásának időpontjában meg kell adni egy alapértelmezett titkosítási hatókört.

Ha nincs megadva alapértelmezett titkosítási hatókör a tárolóhoz, akkor feltöltheti a blobot a Storage-fiókhoz definiált titkosítási hatókör használatával. A titkosítási hatókört meg kell adni a blob feltöltésének időpontjában.

## <a name="disabling-an-encryption-scope"></a>Titkosítási hatókör letiltása

Ha letilt egy titkosítási hatókört, a titkosítási hatókörön végrehajtott összes további olvasási vagy írási művelet sikertelen lesz a 403-as HTTP-hibakódnál (tiltott). Ha újra engedélyezi a titkosítási hatókört, az olvasási és írási műveletek általában újra bekerülnek.

Ha a titkosítási hatókör le van tiltva, már nem számítunk fel díjat. Tiltsa le azokat a titkosítási hatóköröket, amelyek nem szükségesek a szükségtelen költségek elkerülése érdekében.

Ha a titkosítási hatókört ügyfél által felügyelt kulccsal védi, és a Key vaultban törli a kulcsot, az adatai elérhetetlenné válnak. Ügyeljen arra, hogy a titkosítási hatókört is letiltsa, hogy ne kelljen fizetnie.

Ne feledje, hogy az ügyfél által felügyelt kulcsokat a rendszer a Key vaultban lévő, helyreállítható törlési és kiürítési védelemmel védi, és a törölt kulcsra a tulajdonságok által meghatározott viselkedés vonatkozik. További információkért tekintse meg a következő témakörök egyikét az Azure Key Vault dokumentációjában:

- [A Soft delete használata a PowerShell-lel](../../key-vault/general/key-vault-recovery.md)
- [A Soft delete használata a parancssori felülettel](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Titkosítási hatókört nem lehet törölni.

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](../common/storage-service-encryption.md)
- [Titkosítási hatókörök létrehozása és kezelése](encryption-scope-manage.md)
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](../common/customer-managed-keys-overview.md)
- [Mi az Azure Key Vault?](../../key-vault/general/overview.md)