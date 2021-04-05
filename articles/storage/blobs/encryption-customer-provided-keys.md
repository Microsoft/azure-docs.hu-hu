---
title: Titkosítási kulcs megadása a blob Storage-kérelemben
titleSuffix: Azure Storage
description: Az Azure Blob Storage-ra irányuló kérelmeket használó ügyfelek számára a kérések alapján a titkosítási kulcs megadására van lehetőség. A kérelemben szereplő titkosítási kulcs részletesen szabályozható a blob Storage-műveletek titkosítási beállításai között.
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcc5c02c4a37e205622470260d3c620ad76d07d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97694694"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>Titkosítási kulcs megadása a blob Storage-kérelemben

Az Azure Blob Storage-ra irányuló kérelmeket használó ügyfelek számára lehetőség van egy AES-256 titkosítási kulcs megadására kéréses alapon. A kérelemben szereplő titkosítási kulcs részletesen szabályozható a blob Storage-műveletek titkosítási beállításai között. A felhasználó által megadott kulcsok Azure Key Vault vagy egy másik kulcstárolóban tárolhatók.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>Olvasási és írási műveletek titkosítása

Ha egy ügyfélalkalmazás titkosítási kulcsot biztosít a kérelemhez, az Azure Storage a blob-adatok olvasása és írása során transzparens módon végzi a titkosítást és a visszafejtést. Az Azure Storage a blob tartalma mellett a titkosítási kulcs SHA-256 kivonatát írja. A kivonattal ellenőrizheti, hogy a blobon végzett összes további művelet ugyanazt a titkosítási kulcsot használja-e.

Az Azure Storage nem tárolja és nem kezeli azt a titkosítási kulcsot, amelyet az ügyfél a kérelemmel küld. A kulcs biztonságosan el lesz távolítva, amint a titkosítási vagy a visszafejtési folyamat befejeződött.

Amikor egy ügyfél egy ügyfél által megadott kulcs használatával hoz létre vagy frissít egy blobot a kérelemben, az adott blobra vonatkozó további olvasási és írási kérelmeknek is meg kell adniuk a kulcsot. Ha a kulcs nincs megadva egy olyan blobra vonatkozó kérelemben, amely már egy ügyfél által megadott kulccsal lett titkosítva, akkor a kérelem meghiúsul a 409-as hibakódtal (ütközés).

Ha az ügyfélalkalmazás egy titkosítási kulcsot küld a kérelemre, és a Storage-fiók egy Microsoft által felügyelt kulccsal vagy egy ügyfél által felügyelt kulccsal is titkosítva van, akkor az Azure Storage a titkosítás és a visszafejtés iránti kérelemben megadott kulcsot használja.

A titkosítási kulcsnak a kérés részeként történő elküldéséhez az ügyfélnek biztonságos kapcsolatot kell létesítenie az Azure Storage szolgáltatással a HTTPS használatával.

Minden blob-pillanatképhez saját titkosítási kulcs tartozhat.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Ügyfél által biztosított kulcsok megadására vonatkozó kérések fejlécei

REST-hívások esetén az ügyfelek a következő fejlécek segítségével biztonságosan továbbítják a titkosítási kulcs információit a blob Storage-kérelemre:

|Kérelem fejléce | Leírás |
|---------------|-------------|
|`x-ms-encryption-key` |Írási és olvasási kérelmek esetén egyaránt szükséges. Base64 kódolású AES-256 titkosítási kulcs értéke. |
|`x-ms-encryption-key-sha256`| Írási és olvasási kérelmek esetén egyaránt szükséges. A titkosítási kulcs Base64 kódolású SHA256. |
|`x-ms-encryption-algorithm` | Írási kérésekhez szükséges, az olvasási kérelmek esetében nem kötelező. Meghatározza az adattitkosításhoz használt algoritmust a megadott kulccsal.  A fejléc értékének a következőnek kell lennie: `AES256` . |

A kérelemben szereplő titkosítási kulcsok megadása nem kötelező. Ha azonban egy írási művelethez a fent felsorolt fejlécek egyikét adja meg, akkor mindegyiket meg kell adnia.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Az ügyfél által biztosított kulcsokat támogató blob Storage-műveletek

A blob Storage következő műveletei támogatják az ügyfél által biztosított titkosítási kulcsok küldését egy kérelemre:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Tiltási lista](/rest/api/storageservices/put-block-list)
- [Put blokk](/rest/api/storageservices/put-block)
- [Blokk elhelyezése URL-címről](/rest/api/storageservices/put-block-from-url)
- [Oldal elhelyezése](/rest/api/storageservices/put-page)
- [Oldal elhelyezése az URL-címről](/rest/api/storageservices/put-page-from-url)
- [Blokk hozzáfűzése](/rest/api/storageservices/append-block)
- [BLOB tulajdonságainak beállítása](/rest/api/storageservices/set-blob-properties)
- [BLOB metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata)
- [BLOB beolvasása](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [BLOB metaadatainak beolvasása](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Ügyfél által biztosított kulcsok elforgatása

A Blobok titkosításához használt titkosítási kulcs elforgatásához töltse le a blobot, majd töltse fel újra az új titkosítási kulccsal.

> [!IMPORTANT]
> A Azure Portal nem használható olyan tárolóba vagy blobba való olvasásra vagy írásra, amely a kérelemben megadott kulccsal van titkosítva.
>
> Ügyeljen arra, hogy a blob Storage-ba irányuló kérelemben megadott titkosítási kulcsot a Azure Key Vault. Ha a titkosítási kulcs nélküli tárolón vagy blobon próbálkozik írási művelettel, a művelet sikertelen lesz, és nem fog tudni hozzáférni az objektumhoz.

## <a name="next-steps"></a>Következő lépések

- [Ügyfél által megadott kulcs meghatározása a blob Storage-hoz a .NET-tel](storage-blob-customer-provided-key.md)
- [Inaktív adatok Azure Storage-titkosítása](../common/storage-service-encryption.md)
