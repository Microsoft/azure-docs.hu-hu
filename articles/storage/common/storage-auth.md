---
title: Adatműveletek engedélyezése
titleSuffix: Azure Storage
description: Ismerje meg az Azure Storage-hoz való hozzáférés engedélyezésének különböző módjait, beleértve a Azure Active Directory, a megosztott kulcs engedélyezését vagy a közös hozzáférésű aláírásokat (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a8733723fdc59634609a4a79c3b12f12208ab63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470285"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Az Azure Storage-beli adathozzáférés engedélyezése

Minden alkalommal, amikor hozzáfér a Storage-fiókban lévő összes adathoz, az ügyfél HTTP/HTTPS-alapú kérést tesz elérhetővé az Azure Storage-ban. Minden, biztonságos erőforráshoz érkező kérést engedélyezni kell, a szolgáltatás ugyanis így győződik meg arról, hogy az ügyfél rendelkezik az adatok hozzáféréséhez szükséges engedélyekkel.

Az alábbi táblázat azokat a lehetőségeket ismerteti, amelyeket az Azure Storage biztosít az erőforrásokhoz való hozzáférés engedélyezéséhez:

| Azure-összetevő | Megosztott kulcs (Storage-fiók kulcsa) | Közös hozzáférésű jogosultságkód (SAS) | Azure Active Directory (Azure AD) | Helyszíni Active Directory Domain Services | Névtelen nyilvános olvasási hozzáférés |
| -------------- | -------------------------------- | ----------------------------- | --------------------------------- | ------------------------------------------------------ | ---------------------------- |
|Azure Blobs     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |[Támogatott](storage-auth-aad.md)         |Nem támogatott|[Támogatott](../blobs/anonymous-read-access-configure.md)         |
|Azure Files (SMB)     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |Nem támogatott         |[Támogatott, csak a HRE tartományi szolgáltatásokkal](../files/storage-files-active-directory-overview.md)         |[Támogatott, a hitelesítő adatokat szinkronizálni kell az Azure AD-vel](../files/storage-files-active-directory-overview.md)|Nem támogatott         |
|Azure Files (REST)     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |Nem támogatott         |Nem támogatott |Nem támogatott         |
|Azure Queues     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |[Támogatott](storage-auth-aad.md)         |Nem támogatott | Nem támogatott         |
|Azure-táblák     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |Nem támogatott         |Nem támogatott| Nem támogatott         |

Az egyes engedélyezési beállítások rövid ismertetését az alábbiakban találja:

- **Azure Active Directory (Azure ad) integráció** a blobokhoz és a várólistákhoz. Az Azure Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít az ügyfelek számára a Storage-fiók erőforrásaihoz való hozzáférés szabályozásához. A blobok és várólisták Azure AD-integrálásával kapcsolatos további információkért lásd: [Az Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md).

- **Azure Active Directory Domain Services (Azure AD DS) hitelesítés** a Azure Fileshoz. Azure Files támogatja az identitás-alapú hitelesítést az Azure-AD DS keresztül az SMB protokollon keresztül. Az Azure RBAC segítségével részletesen szabályozhatja az ügyfelek hozzáférését a Storage-fiókokban lévő Azure Files erőforrásaihoz. A tartományi szolgáltatásokkal történő Azure Files hitelesítéssel kapcsolatos további információkért tekintse meg az [áttekintést](../files/storage-files-active-directory-overview.md).

- Helyszíni **Active Directory Domain Services (AD DS vagy helyszíni AD DS) hitelesítés** Azure Fileshoz. Azure Files támogatja az identitás-alapú hitelesítést az SMB-n keresztül a AD DSon keresztül. A AD DS-környezet helyszíni gépeken vagy Azure-beli virtuális gépeken is üzemeltethető. A fájlokhoz való SMB-hozzáférés a tartományhoz csatlakozó gépekről AD DS hitelesítő adatokkal, akár helyszíni, akár az Azure-ban is támogatott. Használhatja az Azure RBAC-t a megosztási szintű hozzáférés-vezérléshez és az NTFS-DACL-hez a címtár/fájl szintű engedélyek kényszerítéséhez. A tartományi szolgáltatásokkal történő Azure Files hitelesítéssel kapcsolatos további információkért tekintse meg az [áttekintést](../files/storage-files-active-directory-overview.md).

- A Blobok, a fájlok, a várólisták és a táblák **megosztott kulcsos hitelesítése** . A megosztott kulcsot használó ügyfelek minden, a Storage-fiók elérési kulcsával aláírt kérelemmel továbbítanak egy fejlécet. További információ: [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key/).
- A blobokhoz, fájlokhoz, várólistákhoz és táblákhoz tartozó **közös hozzáférésű aláírások** . A közös hozzáférésű aláírások (SAS) korlátozott delegált hozzáférést biztosítanak egy Storage-fiók erőforrásaihoz. Korlátozásokat adhat hozzá az aláírás érvényességi időintervallumához vagy az általa biztosított engedélyekhez, és rugalmasságot biztosít a hozzáférés kezelésében. További információ: [Shared Access Signatures (SAS) használata](storage-sas-overview.md).
- **Névtelen nyilvános olvasási hozzáférés** tárolók és Blobok számára. Az engedélyezés nem szükséges. További információ: [Névtelen olvasási hozzáférés tárolók és Blobok kezelésére](../blobs/anonymous-read-access-configure.md).  

Alapértelmezés szerint az Azure Storage összes erőforrása védett, és csak a fiók tulajdonosa számára érhető el. Habár a fentiekben ismertetett engedélyezési stratégiák bármelyikét felhasználhatja az ügyfelek számára a Storage-fiók erőforrásaihoz való hozzáférés biztosítására, a Microsoft az Azure AD használatát javasolja, ha lehetséges a maximális biztonság és a könnyű használat.

## <a name="next-steps"></a>Következő lépések

- [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md)
- [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key/)
- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)
