---
title: Inaktív adatok Azure Storage-titkosítása
description: Az Azure Storage védi az adatait úgy, hogy automatikusan titkosítja azt, mielőtt megőrzi a felhőben. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiókban lévő adatok titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b2471ccd2a412c7cbae9d4e59412ac055697e3d7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180360"
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

## <a name="encryption-scopes-for-blob-storage-preview"></a>BLOB Storage titkosítási hatókörök (előzetes verzió)

Alapértelmezés szerint a Storage-fiók egy olyan kulccsal van titkosítva, amely a Storage-fiókra terjed ki. Dönthet úgy, hogy a Microsoft által felügyelt kulcsokat vagy az ügyfél által felügyelt kulcsokat használja Azure Key Vaultban, hogy megvédje és vezérelje az adatait titkosító kulcshoz való hozzáférést.

A titkosítási hatókörök lehetővé teszik a titkosítás beállítását a tároló szintjén vagy egy egyedi blobon. A titkosítási hatókörök használatával biztonságos határokat hozhat létre az azonos Storage-fiókban található, de különböző ügyfelekhez tartozó adategységek között.

Az Azure Storage erőforrás-szolgáltató használatával létrehozhat egy vagy több titkosítási hatókört egy Storage-fiókhoz. Titkosítási hatókör létrehozásakor meg kell adnia, hogy a hatókör védett-e egy Microsoft által felügyelt kulccsal, vagy egy ügyfél által felügyelt kulccsal, amelyet a Azure Key Vault tárol. Ugyanazon a Storage-fiókon belül a különböző titkosítási hatókörök a Microsoft által felügyelt vagy az ügyfél által felügyelt kulcsokat egyaránt használhatják.

Miután létrehozott egy titkosítási hatókört, megadhatja a titkosítási hatókört egy tároló vagy blob létrehozásához szükséges kérelemben. A titkosítási hatókör létrehozásával kapcsolatos további információkért lásd: [titkosítási hatókörök létrehozása és kezelése (előzetes verzió)](../blobs/encryption-scope-manage.md).

> [!NOTE]
> A titkosítási hatókörök nem támogatottak olvasási hozzáférésű geo-redundáns tárolással (RA-GRS) és olvasási hozzáférésű geo-Zone-redundáns tárolási (RA-GZRS) fiókokkal az előzetes verzió során.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> A titkosítási hatókörök előzetes verziója csak nem éles használatra készült. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.
>
> A váratlan költségek elkerülése érdekében ügyeljen arra, hogy letiltsa a jelenleg nem szükséges titkosítási hatóköröket.

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Tároló vagy blob létrehozása titkosítási hatókörrel

A titkosítási hatókörben létrehozott Blobok titkosítva vannak az adott hatókörhöz megadott kulccsal. A blob létrehozásakor megadhat egy egyéni blob titkosítási hatókörét, vagy megadhat egy alapértelmezett titkosítási hatókört is, ha tárolót hoz létre. Ha egy tároló szintjén egy alapértelmezett titkosítási hatókör van megadva, a tárolóban lévő összes blob titkosítva lesz az alapértelmezett hatókörhöz társított kulccsal.

Ha olyan tárolóban hoz létre blobot, amely rendelkezik alapértelmezett titkosítási hatókörrel, megadhat egy titkosítási hatókört, amely felülbírálja az alapértelmezett titkosítási hatókört, ha a tároló úgy van konfigurálva, hogy engedélyezze az alapértelmezett titkosítási hatókör felülbírálását. Az alapértelmezett titkosítási hatókör felülbírálásának megakadályozásához konfigurálja úgy a tárolót, hogy megtagadja az egyes Blobok felülbírálásait.

A titkosítási hatókörhöz tartozó Blobok olvasási műveletei transzparens módon történnek, feltéve, hogy a titkosítási hatókör nincs letiltva.

### <a name="disable-an-encryption-scope"></a>Titkosítási hatókör letiltása

Ha letilt egy titkosítási hatókört, a titkosítási hatókörön végrehajtott összes további olvasási vagy írási művelet sikertelen lesz a 403-as HTTP-hibakódnál (tiltott). Ha újra engedélyezi a titkosítási hatókört, az olvasási és írási műveletek általában újra bekerülnek.

Ha a titkosítási hatókör le van tiltva, már nem számítunk fel díjat. Tiltsa le azokat a titkosítási hatóköröket, amelyek nem szükségesek a szükségtelen költségek elkerülése érdekében.

Ha a titkosítási hatókör védett a Azure Key Vault ügyfél által felügyelt kulcsaival, akkor a titkosítási hatókör letiltásához a kulcstartóban is törölheti a társított kulcsot. Ne feledje, hogy a Azure Key Vault ügyfél által felügyelt kulcsait a rendszer a Soft delete és a Purge Protection védi, és a törölt kulcsra a tulajdonságok által meghatározott viselkedés vonatkozik. További információkért tekintse meg a következő témakörök egyikét az Azure Key Vault dokumentációjában:

- [A Soft delete használata a PowerShell-lel](../../key-vault/general/key-vault-recovery.md)
- [A Soft delete használata a parancssori felülettel](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> Titkosítási hatókört nem lehet törölni.

## <a name="next-steps"></a>Következő lépések

- [Mi az Azure Key Vault?](../../key-vault/general/overview.md)
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
- [BLOB Storage titkosítási hatókörök (előzetes verzió)](../blobs/encryption-scope-overview.md)
