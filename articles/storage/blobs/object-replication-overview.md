---
title: Objektum-replikáció áttekintése
titleSuffix: Azure Storage
description: Az objektum-replikáció aszinkron módon másolja a blokk blobokat egy forrás Storage-fiók és egy cél fiók között. Az objektumok replikálásával csökkentheti az olvasási kérelmek késését, növelheti a számítási feladatok hatékonyságát, optimalizálhatja az adatterjesztést, és csökkentheti a költségeket.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 391c33e72f45e7c0c0b56128b32a8e73399e417a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834323"
---
# <a name="object-replication-for-block-blobs"></a>Objektum-replikálás blokk-blobokhoz

Az objektum-replikáció aszinkron módon másolja a blokk blobokat egy forrás Storage-fiók és egy cél fiók között. Az objektumok replikálása által támogatott egyes forgatókönyvek a következők:

- **Minimalizálja a késést.** Az objektumok replikálása csökkentheti az olvasási kérések késését azáltal, hogy lehetővé teszi az ügyfelek számára, hogy a szorosabb fizikai közelségben lévő régióból származó adatok felhasználását használják.
- **A számítási feladatok hatékonyságának növelése.** Az objektumok replikálásával a számítási feladatok különböző régiókban is feldolgozhatják ugyanezeket a blokk-blobokat.
- **Az adateloszlás optimalizálása.** Egyetlen helyen feldolgozhatja vagy elemezheti az adatforrásokat, majd csak az eredményeket replikálhatja további régiókba.
- **A költségek optimalizálása.** Az adatok replikálása után csökkentheti a költségeket az archiválási szintre való áthelyezéssel a életciklus-kezelési házirendek használatával.

Az alábbi ábra azt mutatja be, hogy az objektum-replikáció hogyan replikálja a blokk-blobokat az egyik régióban található forrás-Storage-fiókból két különböző régióban.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Az objektum-replikáció működését bemutató ábra":::

Az objektumok replikálásának konfigurálásáról az objektum- [replikáció konfigurálása](object-replication-configure.md)című cikk nyújt tájékoztatást.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Az objektumreplikáció előfeltételei

Az objektum-replikációhoz a következő Azure Storage-funkciók is engedélyezve vannak:

- [Módosítási hírcsatorna](storage-blob-change-feed.md): engedélyezni kell a forrásoldali fiókon. További információ a változási csatornák engedélyezéséről: [a módosítási hírcsatorna engedélyezése és letiltása](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Blob verziószámozása](versioning-overview.md): engedélyezni kell a forrás-és a célhelyen is. A verziószámozás engedélyezéséről a [blob verziószámozásának engedélyezése és kezelése](versioning-enable.md)című témakörben olvashat bővebben.

A módosítási hírcsatorna és a blob verziószámozásának engedélyezése további költségeket eredményezhet. További részletekért tekintse meg az [Azure Storage díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/).

Az objektum-replikáció csak általános célú v2-alapú Storage-fiókok esetén támogatott. A forrás-és a cél fióknak általános célú v2-nek kell lennie. 

## <a name="how-object-replication-works"></a>Az objektum-replikáció működése

Az objektum-replikáció aszinkron módon másolja a tárolóban lévő blobokat a konfigurált szabályoknak megfelelően. A blob tartalmát, a blobhoz társított bármely verziót, valamint a blob metaadatait és tulajdonságait a rendszer a forrás tárolójából a cél tárolóba másolja.

> [!IMPORTANT]
> Mivel a blob-adatblokkokat a rendszer aszinkron módon replikálja, a forrás-és a célcím nem azonnal szinkronizálva van. Jelenleg nincs SLA arra vonatkozóan, hogy mennyi időt vesz igénybe az adatreplikálás a célkiszolgálóra. Megtekintheti a replikálás állapotát a forrás blobon annak megállapításához, hogy a replikáció befejeződött-e. További információ: [blob replikálási állapotának megkeresése](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>BLOB verziószámozása

Az objektum-replikációhoz a blob verziószámozásának engedélyezése szükséges a forrás-és a célhelyen is. Ha a forrás fiókban egy replikált blob módosul, a rendszer a blob egy új verzióját hozza létre a forrás fiókban, amely a blob előző állapotát tükrözi a módosítás előtt. A forrás fiók aktuális verziója (vagy az alap blob) a legújabb frissítéseket tükrözi. A rendszer a frissített aktuális verziót és az új korábbi verziót is replikálja a cél fiókba. További információ arról, hogy az írási műveletek hogyan érintik a Blobok verzióját: [verziószámozás az írási műveletekben](versioning-overview.md#versioning-on-write-operations).

Ha törli a forrás fiókban található blobot, a rendszer a blob aktuális verzióját egy korábbi verzióban rögzíti, majd törli. A blob összes korábbi verziója még az aktuális verzió törlése után is megmarad. Ezt az állapotot a rendszer replikálja a cél fiókba. További információ arról, hogy a törlési műveletek milyen hatással vannak a blob-verziókra: [verziószámozás a törlési műveletekben](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Pillanatképek

Az objektum-replikáció nem támogatja a blob-pillanatképeket. A rendszer nem replikálja a forrás fiókban található Blobok pillanatképeit a célhelyre.

### <a name="blob-tiering"></a>BLOB-rétegek

Az objektum-replikáció akkor támogatott, ha a forrás-és a cél fiók a gyakori vagy a ritka elérésű szinten van. Előfordulhat, hogy a forrás-és a cél fiók különböző szinten van. Az objektumok replikációja azonban sikertelen lesz, ha a forrás-vagy a célcím egyik blobja át lett helyezve az archiválási szintre. A blob rétegekkel kapcsolatos további információkért lásd: [hozzáférési szintek az Azure Blob Storage – gyakori, ritka elérésű és archív](storage-blob-storage-tiers.md).

### <a name="immutable-blobs"></a>Nem módosítható blobok

Az objektum-replikáció nem támogatja a nem módosítható blobokat. Ha a forrás vagy a cél tároló időalapú adatmegőrzési szabályzattal vagy jogi megtartással rendelkezik, akkor az objektum replikációja sikertelen lesz. A nem módosítható Blobokkal kapcsolatos további információkért lásd: [az üzleti szempontból kritikus blob-adatok tárolása a](storage-blob-immutable-storage.md)nem módosítható tárolóval.

## <a name="object-replication-policies-and-rules"></a>Objektum-replikációs házirendek és szabályok

Az objektumok replikálásának konfigurálásakor létre kell hoznia egy replikációs házirendet, amely megadja a forrás Storage-fiókot és a célként megadott fiókot. A replikációs házirend egy vagy több olyan szabályt tartalmaz, amely a forrás tárolót és a célhelyet adja meg, és jelzi, hogy a rendszer melyik blokk blobokat replikálja a forrás tárolóban.

Az objektumok replikálásának konfigurálása után az Azure Storage rendszeresen ellenőrzi a forrás fiók változási csatornáját, és aszinkron módon replikálja az írási vagy törlési műveleteket a célhelyre. A replikációs késés a replikált blokk blob méretétől függ.

### <a name="replication-policies"></a>Replikációs házirendek

Az objektumok replikálásának konfigurálásakor a rendszer az Azure Storage erőforrás-szolgáltatón keresztül létrehoz egy replikációs házirendet a forrás és a cél fiókon is. A replikációs házirendet egy házirend-azonosító azonosítja. A forrás-és a célhelyen lévő házirendnek ugyanazzal a házirend-AZONOSÍTÓval kell rendelkeznie ahhoz, hogy a replikálás megtörténjen.

A Storage-fiók legfeljebb két célobjektum forrásaként használható. Előfordulhat, hogy a forrás-és a cél fiók ugyanabban a régióban vagy különböző régiókban található. Emellett különböző előfizetésekben és különböző Azure Active Directory (Azure AD-) bérlőben is lehetnek. Csak egy replikációs házirend hozható létre az egyes forrásoldali fiókokhoz vagy a cél fiókokhoz tartozó párokhoz.

### <a name="replication-rules"></a>Replikációs szabályok

A replikációs szabályok azt határozzák meg, hogy az Azure Storage hogyan replikálja a blobokat a forrás tárolóból a célként megadott tárolóba. Az egyes replikációs házirendekhez legfeljebb 10 replikációs szabályt adhat meg. Mindegyik replikációs szabály egyetlen forrás-és cél tárolót határoz meg, és minden forrás és cél tároló csak egy szabályban használható.

A replikációs szabályok létrehozásakor alapértelmezés szerint csak a forrás-tárolóba felvenni kívánt új blokk-blobokat másolja a rendszer. Megadhatja, hogy az új és a meglévő blokk blobokat is másolja, vagy megadhat egy egyéni másolási hatókört, amely a megadott időpontból létrehozott blokk-blobokat másolja.

Egy vagy több szűrőt is megadhat egy replikációs szabály részeként a blokk Blobok előtag alapján történő szűréséhez. Egy előtag megadásakor a rendszer csak a forrás tárolóban lévő előtaggal egyező blobokat másolja a célhelyre.

A forrás-és a cél tárolóknak is léteznie kell, mielőtt megadhatja őket egy szabályban. A replikációs házirend létrehozása után a cél tárolóba való írási műveletek nem engedélyezettek. A céltárolóba történő írásra tett kísérlet sikertelen lesz a következő hibakóddal: 409 (ütközés). Ha olyan célhelyre szeretne írni, amelyhez replikációs szabály van konfigurálva, törölnie kell az adott tárolóhoz konfigurált szabályt, vagy el kell távolítania a replikációs házirendet. A cél tárolóba való olvasási és törlési műveletek akkor engedélyezettek, ha a replikációs házirend aktív.

A [blob szint beállítása](/rest/api/storageservices/set-blob-tier) műveletet a cél tárolóban lévő blobon hívhatja át az archiválási szintre. Az archiválási szinttel kapcsolatos további információkért lásd [: Azure Blob Storage: gyors, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="replication-status"></a>A replikálás állapota

A forrás fiókban megtekintheti a Blobok replikálási állapotát. További információ: [blob replikálási állapotának megkeresése](object-replication-configure.md#check-the-replication-status-of-a-blob).

Ha a forrás fiókban található blob replikációs állapota hibát jelez, akkor vizsgálja meg a következő lehetséges okokat:

- Győződjön meg arról, hogy az objektum-replikációs házirend konfigurálva van a célhelyen.
- Ellenőrizze, hogy a cél tároló még létezik-e.
- Ha a forrás blob egy ügyfél által megadott kulccsal lett titkosítva egy írási művelet részeként, akkor az objektum replikálása sikertelen lesz. Az ügyfél által biztosított kulcsokkal kapcsolatos további információkért lásd: [titkosítási kulcs megadása a blob Storage-hoz való kérelemben](encryption-customer-provided-keys.md).

## <a name="billing"></a>Számlázás

Az objektum-replikálás további költségekkel jár a forrás-és a célhelyek olvasási és írási műveleteinél, valamint a kimenő adatforgalomért a forrás-fiókból a cél fiókba történő adatreplikálási költségekkel, valamint a változási adatcsatorna feldolgozásával kapcsolatos költségek beolvasásával.

## <a name="next-steps"></a>Következő lépések

- [Objektumreplikáció konfigurálása](object-replication-configure.md)
- [A hírcsatornák támogatásának módosítása az Azure-ban Blob Storage](storage-blob-change-feed.md)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)
