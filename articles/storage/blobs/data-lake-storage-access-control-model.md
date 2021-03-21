---
title: Hozzáférés-vezérlési modell a Azure Data Lake Storage Gen2hoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a tárolót, a könyvtárat és a fájl szintű hozzáférést hierarchikus névtérrel rendelkező fiókokban.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.openlocfilehash: 9fa7f586db5a32640c16db5802b56d6481e8889d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439289"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Hozzáférés-vezérlési modell Azure Data Lake Storage Gen2

Data Lake Storage Gen2 a következő engedélyezési mechanizmusokat támogatja:

- Megosztott kulcs engedélyezése
- Közös hozzáférésű aláírás (SAS) engedélyezése
- Szerepköralapú hozzáférés-vezérlés (Azure RBAC)
- Hozzáférés-vezérlési listák (ACL-ek)

A [megosztott kulcs és az SAS-hitelesítés](#shared-key-and-shared-access-signature-sas-authorization) hozzáférést biztosít egy felhasználóhoz (vagy alkalmazáshoz) anélkül, hogy identitást kellene beAzure Active Directory (Azure ad). Ezzel a két hitelesítési formával az Azure RBAC és az ACL-ek nem lépnek érvénybe.

Az Azure RBAC és a hozzáférés-vezérlési listákhoz a felhasználónak (vagy alkalmazásnak) is rendelkeznie kell identitással az Azure AD-ben.  Az Azure RBAC lehetővé teszi a "durva gabona" hozzáférésének megadását a Storage-fiókadatok számára, például olvasási vagy írási hozzáférést a Storage-fiók **összes** adatához, míg az ACL-ek lehetővé teszik a "részletes" hozzáférést, például az írási hozzáférést egy adott könyvtárhoz vagy fájlhoz.  

Ez a cikk az Azure RBAC és hozzáférés-vezérlési listákra koncentrál, valamint azt, hogy a rendszer hogyan értékeli ki őket együtt a Storage-fiók erőforrásainak engedélyezési döntéseihez.

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Szerepköralapú hozzáférés-vezérlés (Azure RBAC)

Az Azure RBAC szerepkör-hozzárendeléseket használ a [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal)engedélyeinek készletének alkalmazásához. A rendszerbiztonsági tag egy olyan objektum, amely egy Azure Active Directory (AD) szolgáltatásban definiált felhasználó, csoport, szolgáltatásnév vagy felügyelt identitást jelöl. Egy engedélyezési csoport olyan rendszerbiztonsági tag számára biztosíthatja a "durva magvas" hozzáférési szintet, mint **például az olvasási** vagy írási hozzáférés egy Storage-fiókban vagy egy tárolóban lévő **összes** adattal. 

A következő szerepkörök lehetővé teszik a rendszerbiztonsági tag számára, hogy hozzáférjenek a Storage-fiókban található adathoz.

|Szerepkör|Leírás|
|--|--|
| [Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Teljes hozzáférés a blob Storage-tárolók és-adattárakhoz. Ez a hozzáférés lehetővé teszi a rendszerbiztonsági tag számára egy elem tulajdonosának beállítását, valamint az összes elem hozzáférés-vezérlési listájának módosítását. |
| [Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | BLOB Storage-tárolók és-Blobok olvasási, írási és törlési hozzáférése. Ez a hozzáférés nem teszi lehetővé a rendszerbiztonsági tag számára egy elem tulajdonjogának beállítását, de módosíthatja a rendszerbiztonsági tag tulajdonában lévő elemek hozzáférés-vezérlési listáját. |
| [Storage-blobadatok olvasója](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | BLOB Storage-tárolók és Blobok olvasása és listázása. |

A [tulajdonos](../../role-based-access-control/built-in-roles.md#owner), a [közreműködő](../../role-based-access-control/built-in-roles.md#contributor), az [olvasó](../../role-based-access-control/built-in-roles.md#reader)és a [Storage-fiók közreműködő](../../role-based-access-control/built-in-roles.md#storage-account-contributor) szerepkörei lehetővé teszik a rendszerbiztonsági tag számára a Storage-fiókok kezelését, de nem biztosítanak hozzáférést az adott fiókon belüli adathoz. Ezek a szerepkörök (az **olvasó** kivételével) azonban hozzáférhetnek a tároló kulcsaihoz, amelyek különböző eszközökön is használhatók az adateléréshez.

## <a name="access-control-lists-acls"></a>Hozzáférés-vezérlési lista (ACL-ek)

A hozzáférés-vezérlési listák lehetővé teszi a "finomabb gabona" elérési szintjének alkalmazását a címtárakhoz és a fájlokhoz. Az *ACL* egy engedélyezési konstrukció, amely *ACL-bejegyzéseket* tartalmaz. Minden ACL-bejegyzés egy hozzáférési szinttel társítja a rendszerbiztonsági tag-t.  További információ: [hozzáférés-vezérlési listák (ACL-ek) Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="how-permissions-are-evaluated"></a>Az engedélyek kiértékelésének módja

A rendszerbiztonsági tag-alapú hitelesítés során az engedélyeket a következő sorrendben értékeli ki a rendszer.

: az &nbsp; &nbsp; Azure-beli szerepkör-hozzárendelések először a kiértékelésre kerülnek, és elsőbbséget élveznek minden ACL-hozzárendelésnél.

: kettő: &nbsp; &nbsp; Ha a művelet teljes mértékben engedélyezve van az Azure szerepkör-hozzárendelés alapján, akkor az ACL-eket a rendszer egyáltalán nem értékeli ki.

: három: &nbsp; &nbsp; Ha a művelet nem teljes mértékben engedélyezve van, akkor az ACL-eket a rendszer kiértékeli.

> [!div class="mx-imgBorder"]
> ![a adatközpont tárterületének engedélyezési folyamata](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

Mivel a rendszer kiértékeli a hozzáférési engedélyeket, **nem** használhat ACL-t a szerepkör-hozzárendelés által már megadott hozzáférés **korlátozására** . Ennek az az oka, hogy a rendszer először kiértékeli az Azure szerepkör-hozzárendeléseket, és ha a hozzárendelés megfelelő hozzáférési jogosultságot biztosít, akkor az ACL-eket figyelmen kívül hagyja 

Az alábbi ábrán három gyakori művelet engedélyezési folyamata látható: a könyvtár tartalmának listázása, egy fájl olvasása és egy fájl írása.

> [!div class="mx-imgBorder"]
> ![Példa a "Lake Storage" engedélyezési folyamatára](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Engedélyek tábla: az Azure RBAC és az ACL összekapcsolása

Az alábbi táblázat bemutatja, hogyan egyesítheti az Azure-szerepköröket és-ACL-bejegyzéseket, hogy a rendszerbiztonsági tag el tudja végezni a **művelet** oszlopban felsorolt műveleteket. Ez a táblázat egy fiktív címtár-hierarchia minden szintjét jelképező oszlopot mutat be. Van egy oszlop a tároló gyökérkönyvtárához ( `/` ), egy **Oregon** nevű alkönyvtárhoz, a **Portland** nevű Oregon könyvtár alkönyvtárához és egy **Data.txt** nevű Portland-könyvtárban található szövegfájlhoz. Ezekben az oszlopokban az engedélyek megadásához szükséges ACL-bejegyzés [rövid formáját](data-lake-storage-access-control.md#short-forms-for-permissions) ábrázolja. **N/A** (_nem alkalmazható_) az oszlopban jelenik meg, ha a művelet VÉGREHAJTÁSához nincs szükség ACL-bejegyzésre.

|    Művelet             | Hozzárendelt Azure-szerepkör               |    /        | Oregon     | Portland | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Olvasási Data.txt            |   Storage-blobadatok tulajdonosa        | N.A.      | N.A.      | N.A.       | N.A.    |  
|                          |   Storage-blobadatok közreműködője  | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok olvasója       | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Nincsenek                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Hozzáfűzés Data.txt       |   Storage-blobadatok tulajdonosa        | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok közreműködője  | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok olvasója       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   Nincsenek                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Data.txt törlése          |   Storage-blobadatok tulajdonosa        | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok közreműködője  | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok olvasója       | `--X`    | `--X`    | `-WX`     | N/A    |
|                          |   Nincsenek                           | `--X`    | `--X`    | `-WX`     | N/A    |
| Data.txt létrehozása          |   Storage-blobadatok tulajdonosa        | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok közreműködője  | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok olvasója       | `--X`    | `--X`    | `-WX`     | N/A    |
|                          |   Nincsenek                           | `--X`    | `--X`    | `-WX`     | N/A    |
| Listáját                   |   Storage-blobadatok tulajdonosa        | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok közreműködője  | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok olvasója       | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Nincsenek                           | `R-X`    | N.A.      | N.A.       | N.A.    |
| /Oregon/listázása            |   Storage-blobadatok tulajdonosa        | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok közreműködője  | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok olvasója       | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Nincsenek                           | `--X`    | `R-X`    | N.A.       | N.A.    |
| /Oregon/Portland/listázása   |   Storage-blobadatok tulajdonosa        | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok közreműködője  | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Storage-blobadatok olvasója       | N.A.      | N.A.      | N.A.       | N.A.    |
|                          |   Nincsenek                           | `--X`    | `--X`    | `R-X`     | N/A    |


> [!NOTE] 
> Azure Storage Explorer tároló tartalmának megtekintéséhez a rendszerbiztonsági tagoknak az [Azure ad használatával kell bejelentkezniük Storage Explorerba](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource), és (minimum) olvasási hozzáféréssel (R--) kell rendelkezniük a tároló gyökérkönyvtárához ( `\` ). Ez az engedélyezési szint lehetővé teszi számukra a gyökérmappa tartalmának listázását. Ha nem szeretné, hogy a gyökérmappa tartalma látható legyen, hozzárendelheti az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört. Ezzel a szerepkörrel listázhatja a fiókban lévő tárolókat, a tároló tartalmait azonban nem. Ezt követően hozzáférés-vezérlési listákat adhat meg az egyes címtárakhoz és fájlokhoz.   

## <a name="security-groups"></a>Biztonsági csoportok

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Azure-beli szerepkör-hozzárendelések és ACL-bejegyzések korlátai

A csoportok használatával kevésbé valószínű, hogy túllépi a szerepkör-hozzárendelések maximális számát az előfizetésben, valamint az ACL-bejegyzések maximális számát fájlonként vagy címtárban. A következő táblázat ezeket a korlátozásokat ismerteti.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Megosztott kulcs és közös hozzáférésű aláírás (SAS) engedélyezése

A Azure Data Lake Storage Gen2 támogatja a [megosztott kulcs](/rest/api/storageservices/authorize-with-shared-key) -és [sas](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) -metódusokat is a hitelesítéshez. Ezen hitelesítési módszerek egyik jellemzője, hogy a hívóhoz nem tartozik identitás, ezért a rendszerbiztonsági tag engedély-alapú engedélyezése nem hajtható végre.

Megosztott kulcs esetén a hívó gyakorlatilag "felügyelői" hozzáférést kap, ami teljes hozzáférést biztosít az összes erőforráshoz, többek között az adatokhoz, a tulajdonos beállításához és a hozzáférés-vezérlési listák módosításához.

Az SAS-tokenek a jogkivonat részeként tartalmazzák az engedélyezett engedélyeket. Az SAS-jogkivonatban található engedélyeket a rendszer hatékonyan alkalmazza az összes hitelesítési döntésre, de nem végez további ACL-ellenőrzéseket.

## <a name="next-steps"></a>Következő lépések

A hozzáférés-vezérlési listákkal kapcsolatos további információkért lásd:  [hozzáférés-vezérlési listák (ACL-ek) Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).