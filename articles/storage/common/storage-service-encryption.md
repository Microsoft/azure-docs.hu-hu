---
title: Inaktív adatok Azure Storage-titkosítása
description: Az Azure Storage védi az adatait úgy, hogy automatikusan titkosítja azt, mielőtt megőrzi a felhőben. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiókban lévő adatok titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti.
services: storage
author: tamram
ms.service: storage
ms.date: 03/23/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 0688e14b77d885132d6c3fbaa44bed117cc7cf9d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641119"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Inaktív adatok Azure Storage-titkosítása

Az Azure Storage kiszolgálóoldali titkosítás (SSE) használatával automatikusan titkosítja az adatait, amikor a felhőben is megmarad. Az Azure Storage-titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

## <a name="about-azure-storage-encryption"></a>Tudnivalók az Azure Storage-titkosításról

Az Azure Storage-ban tárolt adatai a 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás hasonló a Windows rendszeren a BitLocker-titkosításhoz.

Az Azure Storage-titkosítás minden Storage-fiók esetében engedélyezve van, beleértve a Resource Manager és a klasszikus Storage-fiókokat is. Az Azure Storage-titkosítás nem tiltható le. Mivel az adatai alapértelmezés szerint biztonságosak, nem kell módosítania a kódot vagy az alkalmazásokat, hogy kihasználhassa az Azure Storage-titkosítás előnyeit.

A Storage-fiókban lévő adatok titkosítása a teljesítmény szintjétől (standard vagy prémium), a hozzáférési szinttől (gyakori vagy ritka elérésű) vagy a telepítési modelltől (Azure Resource Manager vagy klasszikus) függetlenül történik. Az archív szint összes blobja is titkosítva van. Az összes Azure Storage redundancia-beállítás támogatja a titkosítást, és az elsődleges és a másodlagos régióban lévő összes érték titkosítva van, ha a Geo-replikáció engedélyezve van. Minden Azure Storage-erőforrás titkosítva van, beleértve a blobokat, a lemezeket, a fájlokat, a várólistákat és a táblákat. Az összes objektum metaadatai is titkosítva vannak. Az Azure Storage-titkosításhoz nem jár további díj.

Az Azure Storage-ba az 2017. október 20. után írt összes blokk blob, blob hozzáfűzése vagy Page blob titkosítva van. Az ezen dátum előtt létrehozott blobokat a háttérben futó folyamat továbbra is titkosítja. A 2017. október 20. előtt létrehozott blob titkosításának kényszerítéséhez újra lehet írni a blobot. A Blobok titkosítási állapotának vizsgálatával kapcsolatban lásd [a Blobok titkosítási állapotának ellenőrzését](../blobs/storage-blob-encryption-status.md)ismertető témakört.

További információ az Azure Storage-titkosítás alapjául szolgáló kriptográfiai modulokról [: a kriptográfiai API: Next Generation](/windows/desktop/seccng/cng-portal).

Az Azure Managed Disks titkosításával és kulcskezelő szolgáltatásával kapcsolatos információkért lásd: [Az Azure Managed Disks kiszolgálóoldali titkosítása](../../virtual-machines/disk-encryption.md).

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Alapértelmezés szerint a Microsoft által felügyelt kulcsokkal titkosítva vannak az új Storage-fiókok. Továbbra is használhatja a Microsoft által felügyelt kulcsokat az adatok titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, két lehetőség közül választhat. Bármelyik kulcskezelő típust használhatja, vagy mindkettőt:

- Megadhat egy *ügyfél által felügyelt kulcsot* , amelyet a blob Storage-ban és a Azure Filesban lévő adattitkosításhoz és visszafejtéshez használhat. az ügyfél által felügyelt kulcsokat az Azure Key Vault vagy Azure Key Vault felügyelt hardveres biztonsági modell (HSM) (előzetes verzió <sup>) szerint kell</sup> tárolni. Az ügyfél által felügyelt kulcsokkal kapcsolatos további információkért lásd: [ügyfél által felügyelt kulcsok használata az Azure Storage encryption](./customer-managed-keys-overview.md)szolgáltatáshoz.
- Megadhat egy *ügyfél által megadott kulcsot* a blob Storage-műveletekhez. A blob Storage-hoz tartozó olvasási vagy írási kérelmet készítő ügyfél tartalmazhat egy titkosítási kulcsot a kérelemben, amely részletesen szabályozza a Blobok titkosításának és visszafejtésének módját. Az ügyfél által biztosított kulcsokkal kapcsolatos további információkért lásd: [titkosítási kulcs megadása a blob Storage-hoz való kérelemben](../blobs/encryption-customer-provided-keys.md).

Az alábbi táblázat összehasonlítja az Azure Storage-titkosítás legfontosabb felügyeleti lehetőségeit.

| Kulcskezelő paraméter | Microsoft által felügyelt kulcsok | Felhasználó által kezelt kulcsok | Ügyfél által biztosított kulcsok |
|--|--|--|--|
| Titkosítási/visszafejtési műveletek | Azure | Azure | Azure |
| Az Azure Storage szolgáltatásai támogatottak | Mind | BLOB Storage, Azure Files<sup>1, 2</sup> | Blob Storage |
| Kulcstároló | Microsoft Key Store | Azure Key Vault vagy Key Vault HSM | Ügyfél saját kulcstárolója |
| Kulcs rotációs felelőssége | Microsoft | Ügyfél | Ügyfél |
| Kulcs vezérlő | Microsoft | Ügyfél | Ügyfél |

<sup>1</sup> . az ügyfél által felügyelt kulcsok üzenetsor-tárolással történő létrehozását támogató fiók létrehozásával kapcsolatos információkért lásd: [hozzon létre egy fiókot, amely támogatja az ügyfél által felügyelt kulcsokat a várólistákhoz](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> . az ügyfél által felügyelt kulcsokat tartalmazó fiók létrehozásával kapcsolatos információkért lásd: [hozzon létre egy fiókot, amely támogatja az ügyfél által felügyelt kulcsokat a táblázatokhoz](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> A Microsoft által felügyelt kulcsok megfelelőségi követelmények szerint elforgatva. Ha meghatározott kulcs-elforgatási követelményekkel rendelkezik, a Microsoft javasolja, hogy az ügyfél által felügyelt kulcsokra váltson, így Ön is kezelheti és naplózhatja a rotációs szolgáltatást.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Az adattitkosítás megkettőzése az infrastruktúra titkosításával

Azok az ügyfelek, akik magas szintű megbízhatóságot igényelnek, az adatbiztonságuk is lehetővé teszi az 256 bites AES-titkosítást az Azure Storage-infrastruktúra szintjén. Ha az infrastruktúra-titkosítás engedélyezve van, a Storage-fiókban tárolt adatforgalom kétszer, &mdash; a szolgáltatási szinten, az infrastruktúra szintjén pedig &mdash; két különböző titkosítási algoritmussal és két különböző kulccsal van titkosítva. Az Azure Storage-beli adattárolók kettős titkosítása védelmet nyújt olyan forgatókönyvek esetében, amelyekben az egyik titkosítási algoritmus vagy kulcs sérülhet. Ebben az esetben a további titkosítási réteg továbbra is védi az adatait.

A szolgáltatási szintű titkosítás támogatja a Microsoft által felügyelt kulcsok vagy az ügyfél által felügyelt kulcsok használatát Azure Key Vault. Az infrastruktúra-szintű titkosítás a Microsoft által felügyelt kulcsokra támaszkodik, és mindig külön kulcsot használ.

Az infrastruktúra-titkosítást lehetővé tevő Storage-fiókok létrehozásával kapcsolatos további információkért lásd: [a Storage-fiók létrehozása infrastruktúra-titkosítással, amely lehetővé teszi az adatok kettős titkosítását](infrastructure-encryption-enable.md).

## <a name="next-steps"></a>Következő lépések

- [Mi az Azure Key Vault?](../../key-vault/general/overview.md)
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
- [BLOB Storage titkosítási hatókörök](../blobs/encryption-scope-overview.md)
- [Titkosítási kulcs megadása a blob Storage-kérelemben](../blobs/encryption-customer-provided-keys.md)
