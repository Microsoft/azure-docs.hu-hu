---
title: Költségadatok exportálása Azure Storage-fiók SAS-kulccsal
description: Ez a cikk segítséget nyújt a partnereknek SAS-kulcs létrehozásában és a Cost Management exportálás konfigurálásában.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509668"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Költségadatok exportálása Azure Storage-fiók SAS-kulccsal

Az alábbi információk csak a Microsoft-partnerekre vonatkoznak.

A partnerek gyakran nem rendelkeznek saját Azure-előfizetésekkel a saját Microsoft partneri szerződéssel társított bérlőn. A Microsoft partneri szerződés tervével rendelkező partnerek, akik számlázási fiókjuk globális rendszergazdái, a költségadatok exportálásával és másolásával egy másik bérlő Storage-fiókjába exportálhatók és másolhatók a közös elérésű szolgáltatás (SAS) kulcsa segítségével. Más szóval egy SAS-kulccsal rendelkező Storage-fiók lehetővé teszi, hogy a partner olyan Storage-fiókot használjon, amely a partneri szerződésen kívül esik az exportált információk fogadásához. Ez a cikk segítséget nyújt a partnereknek SAS-kulcs létrehozásában és a Cost Management exportálás konfigurálásában.

## <a name="requirements"></a>Követelmények

- Microsoft partneri szerződéssel rendelkező partnernek kell lennie, és az Azure-csomag ügyfeleinek kell lennie.
- A partner szervezet számlázási fiókjához globális rendszergazdának kell lennie.
- Rendelkeznie kell hozzáféréssel egy olyan Storage-fiók konfigurálásához, amely a partner szervezetének egy másik bérlője. Ön felelős azért, hogy fenntartsa az engedélyeket és az adatelérést, amikor az exportálja az adatait a Storage-fiókjába.

## <a name="configure-azure-storage-with-a-sas-key"></a>Az Azure Storage konfigurálása SAS-kulccsal

Szerezze be a Storage-fiók SAS-jogkivonatát, vagy hozzon létre egyet a Azure Portal használatával. A Azure Portal létrehozásához kövesse az alábbi lépéseket. Az SAS-kulcsokkal kapcsolatos további tudnivalókért lásd: [korlátozott hozzáférés biztosítása az adathoz közös hozzáférésű aláírásokkal (SAS).](../../storage/common/storage-sas-overview.md)

1. Navigáljon a Azure Portal Storage-fiókjához.
    - Ha a fiókja több bérlőhöz fér hozzá, váltson át könyvtárakra a Storage-fiók eléréséhez. Válassza ki a fiókját a Azure Portal jobb felső sarkában, majd válassza a **könyvtárak váltása** lehetőséget.
    - Előfordulhat, hogy a megfelelő bérlői fiókkal kell bejelentkeznie a Azure Portalba a Storage-fiók eléréséhez.
1. A bal oldali menüben válassza a **közös hozzáférési aláírás** lehetőséget.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="A konfigurált Azure Storage-beli megosztott hozzáférési aláírást ábrázoló képernyőkép." lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. Konfigurálja a jogkivonatot az előző képen azonosított beállításokkal.
    1. Válassza ki a **blobot** az _engedélyezett szolgáltatások_ számára.
    1. Válassza ki a **szolgáltatást**, a **tárolót** és az **objektumot** az _engedélyezett erőforrástípusok_ közül.
    1. Válassza az **olvasás**, **írás**, **Törlés**, **Listázás**, **Hozzáadás** és **Létrehozás** lehetőséget az _engedélyezett engedélyekhez_.
    1. Válassza ki a lejárat és a dátumok elemet. Győződjön meg arról, hogy a lejárta előtt frissíti az export SAS-tokent. Minél hosszabb a lejárat előtt konfigurált időszak, annál hosszabb ideig fut az exportálás, mielőtt új SAS-tokent kellene létrehoznia.
1. Csak az _engedélyezett protokollokhoz_ válassza a **https** lehetőséget.
1. Válassza az **alapszintű** lehetőséget az _előnyben részesített útválasztási_ szinten.
1. Válassza ki a **key1** az _aláíró kulcshoz_. Ha elforgatja vagy frissíti az SAS-token aláírásához használt kulcsot, újra kell létrehoznia egy új SAS-tokent az exportáláshoz.
1. Válassza a **SAS és kapcsolati sztring létrehozása** lehetőséget.
    A **sas-jogkivonat** látható értéke az exportálások konfigurálásakor szükséges jogkivonat.

## <a name="create-a-new-export-with-a-sas-token"></a>Új Exportálás létrehozása SAS-jogkivonattal

Navigáljon az **exportáláshoz** a Számlázási fiók hatókörében, és hozzon létre egy új exportálást a következő lépésekkel.

1. Válassza a **Létrehozás** lehetőséget.
1. Konfigurálja az Exportálás részleteit úgy, ahogy a normál exportálást tenné. Beállíthatja, hogy az Exportálás meglévő könyvtárat vagy tárolót használjon, vagy megadhat egy új könyvtárat vagy tárolót, az Exportálás pedig létrehozza azokat.
1. A tároló konfigurálásakor válassza **a sas-jogkivonat használata** lehetőséget.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="Képernyőfelvétel: az új Exportálás, ahol kiválaszthatja az SAS-tokent." lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. Adja meg a Storage-fiók nevét, és illessze be az SAS-tokenbe.
1. Adja meg a létrehozandó meglévő tárolót vagy könyvtárat, vagy azonosítsa az újakat.
1. Válassza a **Létrehozás** lehetőséget.

Az SAS-jogkivonat-alapú Exportálás csak akkor működik, ha a jogkivonat érvényes marad. A jogkivonat alaphelyzetbe állítása az aktuális lejárata előtt, vagy az exportálás nem fog működni. Mivel a jogkivonat hozzáférést biztosít a Storage-fiókhoz, a jogkivonatot a többi bizalmas adathoz hasonlóan körültekintően kell ellátni. Ön felelős az engedélyek és az adathozzáférés fenntartásához, amikor az exportálja az adatait a Storage-fiókjába.

## <a name="troubleshoot-exports-using-sas-tokens"></a>Az SAS-tokenekkel való exportálás hibáinak megoldása

Az alábbi gyakori problémák az SAS-jogkivonat-alapú exportálások konfigurálásakor vagy használatakor fordulnak elő.

- Nem jelenik meg az SAS-kulcs lehetőség a Azure Portalban.
  - Győződjön meg arról, hogy Ön olyan partner, amely rendelkezik Microsoft partneri szerződéssel, és hogy globális rendszergazdai jogosultsággal rendelkezik a számlázási fiókhoz. Csak a SAS-kulccsal exportálható felhasználók.

- Az Exportálás konfigurálására tett kísérlet során a következő hibaüzenet jelenik meg:

    **Győződjön meg arról, hogy az SAS-jogkivonat a blob Service esetében érvényes, a tároló-és objektum-erőforrástípusok esetében érvényes, és rendelkezik a következő engedélyekkel: Create Read Write delete. (Tárolási szolgáltatás hibakódja: AuthorizationResourceTypeMismatch)**

    - Győződjön meg arról, hogy helyesen konfigurálja és hozza létre az SAS-kulcsot az Azure Storage-ban.

- Az Exportálás létrehozása után a teljes SAS-kulcs nem látható.
  - A kulcs nem jelenik meg a várt viselkedésnél. A SAS-exportálás konfigurálása után a kulcs biztonsági okokból rejtve marad.

- A Storage-fiók nem érhető el abban a bérlőben, ahol az Exportálás be van állítva.
  - A várt viselkedés. Ha a Storage-fiók egy másik bérlőben található, először a Azure Portalban kell megkeresnie az adott bérlőt, hogy megtalálja a Storage-fiókot.

- Az Exportálás az SAS-tokenekkel kapcsolatos hiba miatt meghiúsul.
  - Az Exportálás csak akkor működik, ha az SAS-jogkivonat érvényes marad. Hozzon létre egy új kulcsot, és futtassa az exportálást.

## <a name="next-steps"></a>Következő lépések

- A Cost Management adatok exportálásával kapcsolatos további információkért lásd: [adatok létrehozása és exportálása](tutorial-export-acm-data.md).
- A nagy mennyiségű használati adatok exportálásával kapcsolatos információkért lásd: [nagyméretű adatkészletek lekérése exportálással](ingest-azure-usage-at-scale.md).
