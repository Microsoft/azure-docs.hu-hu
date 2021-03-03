---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: b0be074c995fcc62f63a4a2ebf4149d3040e7b6b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751134"
---
## <a name="best-practices"></a>Ajánlott eljárások

Ez a szakasz az ACL-ek rekurzív beállítására vonatkozó ajánlott eljárásokat ismerteti. 

#### <a name="handling-runtime-errors"></a>Futásidejű hibák elhárítása

Futásidejű hiba számos okból történhet (például leállás vagy ügyfél-csatlakozási probléma). Ha futásidejű hibát tapasztal, indítsa újra a rekurzív ACL-folyamatot. Az ACL-ek újra alkalmazhatók az elemekre anélkül, hogy negatív hatással lenne. 

#### <a name="handling-permission-errors-403"></a>Engedélyek kezelésére vonatkozó hibák (403)

Ha egy rekurzív ACL-folyamat futtatásakor hozzáférés-vezérlési kivételt tapasztal, előfordulhat, hogy az AD [rendszerbiztonsági tag](../articles/role-based-access-control/overview.md#security-principal) nem rendelkezik megfelelő engedéllyel ahhoz, hogy ACL-t alkalmazzon egy vagy több alárendelt elemhez a címtár-hierarchiában. Ha egy engedélyezési hiba történik, a folyamat leáll, és a folytatási jogkivonat van megadva. Javítsa ki az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával dolgozza fel a fennmaradó adatkészletet. A már sikeresen feldolgozott címtárakat és fájlokat nem kell újra feldolgozni. Azt is megteheti, hogy újraindítja a rekurzív ACL-folyamatot. Az ACL-ek újra alkalmazhatók az elemekre anélkül, hogy negatív hatással lenne. 

#### <a name="credentials"></a>Hitelesítő adatok 

Azt javasoljuk, hogy hozzon létre egy Azure AD rendszerbiztonsági tag, amely hozzá lett rendelve a [Storage blob-adat tulajdonosi](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepköréhez a cél Storage-fiók vagy tároló hatókörében. 

#### <a name="performance"></a>Teljesítmény 

A késés csökkentése érdekében javasoljuk, hogy futtassa a rekurzív ACL-folyamatot egy olyan Azure-beli virtuális gépen (VM), amely ugyanabban a régióban található, mint a Storage-fiók. 

#### <a name="acl-limits"></a>ACL-korlátok

A címtárra vagy fájlra érvényes ACL-ek maximális száma 32 hozzáférési ACL-ek és 32 alapértelmezett ACL-ek. További információért lásd: [Hozzáférés-vezérlés az Azure Data Lake Storage Gen2-ben](../articles/storage/blobs/data-lake-storage-access-control.md).