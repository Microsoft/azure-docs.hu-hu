---
title: BLOB Storage titkosítási hatókörök (előzetes verzió)
description: A titkosítási hatókörök lehetővé teszik a titkosítás kezelését a tároló vagy egy egyedi blob szintjén. A titkosítási hatókörök használatával biztonságos határokat hozhat létre az azonos Storage-fiókban található, de különböző ügyfelekhez tartozó adategységek között.
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 35395a30f7d58b9edb3aa7622a35e8c4a62dc76f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211362"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>BLOB Storage titkosítási hatókörök (előzetes verzió)

A titkosítási hatókörök lehetővé teszik a titkosítás kezelését a tároló vagy egy egyedi blob szintjén. A titkosítási hatókörök használatával biztonságos határokat hozhat létre az azonos Storage-fiókban található, de különböző ügyfelekhez tartozó adategységek között.

Alapértelmezés szerint a Storage-fiók egy olyan kulccsal van titkosítva, amely a teljes Storage-fiókra terjed ki. A titkosítási hatókörrel megadhatja, hogy egy vagy több tárolót csak azokra a kulcsokra lehessen titkosítani, amelyek hatóköre csak ezekre a tárolóra terjed ki.

Dönthet úgy, hogy a Microsoft által felügyelt kulcsokat vagy az ügyfél által felügyelt kulcsokat használja Azure Key Vaultban, hogy megvédje és vezérelje az adatait titkosító kulcshoz való hozzáférést. Ugyanazon a Storage-fiókon belül a különböző titkosítási hatókörök a Microsoft által felügyelt vagy az ügyfél által felügyelt kulcsokat egyaránt használhatják.

Miután létrehozott egy titkosítási hatókört, megadhatja a titkosítási hatókört egy tároló vagy blob létrehozásához szükséges kérelemben. A titkosítási hatókör létrehozásával kapcsolatos további információkért lásd: [titkosítási hatókörök létrehozása és kezelése (előzetes verzió)](encryption-scope-manage.md).

> [!IMPORTANT]
> A titkosítási hatókörök jelenleg **előzetes** verzióban érhetők el. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .
>
> A váratlan költségek elkerülése érdekében ügyeljen arra, hogy letiltsa a jelenleg nem szükséges titkosítási hatóköröket.
>
> A titkosítási hatókörök nem támogatottak olvasási hozzáférésű geo-redundáns tárolással (RA-GRS) vagy olvasási hozzáférésű geo-Zone-redundáns tárolási (RA-GZRS) fiókokkal az előzetes verzió során.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Tároló vagy blob létrehozása titkosítási hatókörrel

A titkosítási hatókörben létrehozott Blobok titkosítva vannak az adott hatókörhöz megadott kulccsal. A blob létrehozásakor megadhat egy egyéni blob titkosítási hatókörét, vagy megadhat egy alapértelmezett titkosítási hatókört is, ha tárolót hoz létre. Ha egy tároló szintjén egy alapértelmezett titkosítási hatókör van megadva, a tárolóban lévő összes blob titkosítva lesz az alapértelmezett hatókörhöz társított kulccsal.

Ha olyan tárolóban hoz létre blobot, amely rendelkezik alapértelmezett titkosítási hatókörrel, megadhat egy titkosítási hatókört, amely felülbírálja az alapértelmezett titkosítási hatókört, ha a tároló úgy van konfigurálva, hogy engedélyezze az alapértelmezett titkosítási hatókör felülbírálását. Az alapértelmezett titkosítási hatókör felülbírálásának megakadályozásához konfigurálja úgy a tárolót, hogy megtagadja az egyes Blobok felülbírálásait.

A titkosítási hatókörhöz tartozó Blobok olvasási műveletei transzparens módon történnek, feltéve, hogy a titkosítási hatókör nincs letiltva.

## <a name="disable-an-encryption-scope"></a>Titkosítási hatókör letiltása

Ha letilt egy titkosítási hatókört, a titkosítási hatókörön végrehajtott összes további olvasási vagy írási művelet sikertelen lesz a 403-as HTTP-hibakódnál (tiltott). Ha újra engedélyezi a titkosítási hatókört, az olvasási és írási műveletek általában újra bekerülnek.

Ha a titkosítási hatókör le van tiltva, már nem számítunk fel díjat. Tiltsa le azokat a titkosítási hatóköröket, amelyek nem szükségesek a szükségtelen költségek elkerülése érdekében.

Ha az ügyfél által felügyelt kulcsokkal védi a titkosítási hatókört, akkor a kulcstárolóban található társított kulcsot is törölheti a titkosítási hatókör letiltásához. Ne feledje, hogy az ügyfél által felügyelt kulcsokat a rendszer a Key vaultban lévő, helyreállítható törlési és kiürítési védelemmel védi, és a törölt kulcsra a tulajdonságok által meghatározott viselkedés vonatkozik. További információkért tekintse meg a következő témakörök egyikét az Azure Key Vault dokumentációjában:

- [A Soft delete használata a PowerShell-lel](../../key-vault/general/key-vault-recovery.md)
- [A Soft delete használata a parancssori felülettel](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> Titkosítási hatókört nem lehet törölni.

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](../common/storage-service-encryption.md)
- [Titkosítási hatókörök létrehozása és kezelése (előzetes verzió)](encryption-scope-manage.md)
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](../common/customer-managed-keys-overview.md)
- [Mi az Azure Key Vault?](../../key-vault/general/overview.md)