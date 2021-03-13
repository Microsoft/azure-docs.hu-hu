---
title: Válassza ki, hogyan engedélyezze a blob-adathozzáférést a Azure Portal
titleSuffix: Azure Storage
description: Amikor a Azure Portal használatával fér hozzá a blob-adataihoz, a portálon az Azure Storage-ba irányuló kérések is elérhetők. Az Azure Storage-ba érkező kérések hitelesítése és engedélyezése az Azure AD-fiók vagy a Storage-fiók elérési kulcsa alapján lehetséges.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperf-fy21q1
ms.openlocfilehash: a12936f8f9f84dacfab4850253df665ae7758be1
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613246"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Válassza ki, hogyan engedélyezze a blob-adathozzáférést a Azure Portal

Amikor a [Azure Portal](https://portal.azure.com)használatával fér hozzá a blob-adataihoz, a portálon az Azure Storage-ba irányuló kérések is elérhetők. Az Azure Storage-ba irányuló kérelem az Azure AD-fiók vagy a Storage-fiók hozzáférési kulcsa alapján engedélyezhető. A portál jelzi, hogy melyik módszert használja, és lehetővé teszi a kettő közötti váltást, ha rendelkezik a megfelelő engedélyekkel.  

Azt is megadhatja, hogyan engedélyezze az egyes Blobok feltöltési műveleteit a Azure Portalban. Alapértelmezés szerint a portál a már használt módszert használja a blob-feltöltési műveletek engedélyezéséhez, de lehetősége van módosítani ezt a beállítást a blob feltöltésekor.

## <a name="permissions-needed-to-access-blob-data"></a>A blob-adateléréshez szükséges engedélyek

Attól függően, hogy hogyan kívánja engedélyezni a blob-adatAzure Portalhoz való hozzáférést, külön engedélyekre van szüksége. A legtöbb esetben ezek az engedélyek az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával érhetők el. További információ az Azure RBAC: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>A fiók elérési kulcsának használata

Ahhoz, hogy a blob-információk hozzáférhessenek a fiók elérési kulcsához, hozzá kell rendelnie egy Azure-szerepkört, amely tartalmazza az Azure RBAC műveletet **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelet**. Ez az Azure-szerepkör beépített vagy egyéni szerepkör lehet. A **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet** támogató beépített szerepkörök a következők:

- A Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör
- A Azure Resource Manager [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör
- A [Storage-fiók közreműködői](../../role-based-access-control/built-in-roles.md#storage-account-contributor) szerepköre

Amikor megpróbál hozzáférni a blob-adathoz a Azure Portalban, a portál először ellenőrzi, hogy van-e hozzárendelve szerepkör a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelethez**. Ha ezzel a művelettel társított egy szerepkört, akkor a portál a fiók kulcsát használja a Blobok adatokhoz való hozzáféréshez. Ha nem rendelt hozzá szerepkört ezzel a művelettel, akkor a portál az Azure AD-fiókjával próbál hozzáférni az adataihoz.

> [!IMPORTANT]
> Ha egy Storage-fiók zárolva van egy Azure Resource Manager **írásvédett** zárolással, a [kulcsok listázása](/rest/api/storagerp/storageaccounts/listkeys) művelet nem engedélyezett ehhez a Storage-fiókhoz. A **kulcsok listázása** post művelet, és az összes post művelet meg lesz akadályozva, ha **írásvédett** zárolás van konfigurálva a fiókhoz. Ezért ha a fiók **írásvédett** zárolással van zárolva, a felhasználóknak Azure ad-beli hitelesítő adatokkal kell rendelkezniük a blob adatainak a portálon való eléréséhez. További információ a Blobok adatainak a portálon az Azure AD-vel való eléréséről: [Azure ad-fiók használata](#use-your-azure-ad-account).

> [!NOTE]
> A klasszikus előfizetés-rendszergazdai szerepkörök szolgáltatás rendszergazdája és Co-Administrator tartalmazza a Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkörének megfelelőt. A **tulajdonosi** szerepkör magában foglalja az összes műveletet, beleértve a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet**, így az egyik rendszergazdai szerepkörrel rendelkező felhasználó is elérheti a blob-adataikat a fiók kulcsával. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Az Azure AD-fiók használata

Ha az Azure AD-fiókkal szeretné elérni a Azure Portal a blob-adatokat, a következő utasítások mindegyikének igaznak kell lennie az Ön számára:

- A Azure Resource Manager [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört legalább a Storage-fiók szintjére vagy magasabbra kell rendelni. Az **olvasó** szerepkör biztosítja a legtöbb korlátozott engedélyt, de egy másik Azure Resource Manager szerepkör is elfogadható, amely hozzáférést biztosít a Storage-fiókok felügyeleti erőforrásaihoz.
- Olyan beépített vagy egyéni szerepkört rendelt hozzá, amely hozzáférést biztosít a blob-adathoz.

Az **olvasó** szerepkör-hozzárendelés vagy egy másik Azure Resource Manager szerepkör-hozzárendelés szükséges ahhoz, hogy a felhasználó megtekinthesse és navigáljon a tárolási fiókok felügyeleti erőforrásaihoz a Azure Portal. A Blobok adataihoz hozzáférést biztosító Azure-szerepkörök nem biztosítanak hozzáférést a Storage-fiókok felügyeleti erőforrásaihoz. A blob-információk a portálon való eléréséhez a felhasználónak engedélyekre van szüksége a Storage-fiók erőforrásainak megkereséséhez. További információ erről a követelményről: [az olvasó szerepkör kiosztása a portálhoz való hozzáféréshez](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

A blob-adataihoz való hozzáférést támogató beépített szerepkörök a következők:

- [Storage blob-adattulajdonos](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): a Azure Data Lake Storage Gen2 POSIX hozzáférés-vezérléséhez.
- [Storage blob adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): olvasási/írási/törlési engedélyek a blobokhoz.
- [Storage blob Adatolvasó](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): írásvédett engedélyek a blobokhoz.

Az egyéni szerepkörök a beépített szerepkörök által biztosított azonos engedélyek különböző kombinációit támogatják. Az egyéni Azure-szerepkörök létrehozásával kapcsolatos további információkért lásd: Azure-beli [Egyéni szerepkörök](../../role-based-access-control/custom-roles.md) és [a szerepkör-definíciók ismertetése az Azure-erőforrásokhoz](../../role-based-access-control/role-definitions.md).

> [!IMPORTANT]
> A Azure Portal Storage Explorer előzetes verziója nem támogatja az Azure AD-beli hitelesítő adatok használatát a Blobok adatainak megtekintésére és módosítására. Storage Explorer a Azure Portal mindig a fiók kulcsait használja az adateléréshez. A Azure Portal Storage Explorer használatához olyan szerepkört kell hozzárendelni, amely tartalmazza a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet**.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Navigáljon a Azure Portal lévő blobokhoz

A portálon található blob-információk megtekintéséhez navigáljon a Storage-fiók **áttekintéséhez** , és kattintson a **blobokra** mutató hivatkozásokra. Azt is megteheti, hogy a menü **tárolók** szakaszára navigál.

:::image type="content" source="media/authorize-data-operations-portal/blob-access-portal.png" alt-text="A blob-adatAzure Portalban való böngészést bemutató képernyőkép":::

## <a name="determine-the-current-authentication-method"></a>Az aktuális hitelesítési módszer meghatározása

Amikor megnyit egy tárolót, a Azure Portal jelzi, hogy jelenleg a fiók-hozzáférési kulcsot vagy az Azure AD-fiókot használja a hitelesítéshez.

### <a name="authenticate-with-the-account-access-key"></a>Hitelesítés a fiók hozzáférési kulcsával

Ha a fiók-hozzáférési kulccsal végzi a hitelesítést, a hitelesítési módszerként megadott **elérési kulcsot** fogja látni a portálon:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Képernyőfelvétel, amely az aktuálisan hozzáférő tárolók számára a fiók kulcsa":::

Ha az Azure AD-fiók használatára szeretne váltani, kattintson a képre Kiemelt hivatkozásra. Ha rendelkezik a megfelelő engedélyekkel az Ön által hozzárendelt Azure-szerepkörökön keresztül, folytathatja a folytatást. Ha azonban nem rendelkezik a megfelelő engedélyekkel, a következőhöz hasonló hibaüzenet jelenik meg:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Hiba jelenik meg, ha az Azure AD-fiók nem támogatja a hozzáférést":::

Figyelje meg, hogy nem jelennek meg Blobok a listában, ha az Azure AD-fiókja nem rendelkezik a megtekintéséhez szükséges engedélyekkel. Kattintson a **kapcsolóra** a Hívóbetű eléréséhez, és használja a hozzáférési kulcsot a hitelesítéshez.

### <a name="authenticate-with-your-azure-ad-account"></a>Hitelesítés az Azure AD-fiókkal

Ha az Azure AD-fiókkal végzi a hitelesítést, akkor a portálon hitelesítési módszerként megadott **Azure ad-felhasználói fiókot** fogja látni:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Képernyőfelvétel, amely az Azure AD-fiókkal jelenleg a tárolókat éri el":::

Ha a fiók-hozzáférési kulcs használatára szeretne váltani, kattintson a képre Kiemelt hivatkozásra. Ha rendelkezik hozzáféréssel a fiók kulcsához, akkor folytathatja a folytatást. Ha azonban nincs hozzáférése a fiók kulcsához, a következőhöz hasonló hibaüzenet jelenik meg:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="Hiba jelenik meg, ha nincs hozzáférése a fiók kulcsához":::

Figyelje meg, hogy a listában nem jelennek meg Blobok, ha nem rendelkezik hozzáféréssel a fiók kulcsaihoz. Kattintson a **Váltás az Azure ad felhasználói fiókra** hivatkozásra az Azure ad-fiók ismételt hitelesítésre való használatához.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>A blob-feltöltési művelet engedélyezésének meghatározása

Amikor feltölt egy blobot a Azure Portalból, megadhatja, hogy a rendszer a fiók hozzáférési kulcsával vagy az Azure AD-beli hitelesítő adataival hitelesítse és engedélyezze a műveletet. Alapértelmezés szerint a portál az aktuális hitelesítési módszert használja, ahogy azt az [aktuális hitelesítési módszer meghatározása](#determine-the-current-authentication-method)című rész mutatja.

A blob-feltöltési műveletek engedélyezésének megadásához kövesse az alábbi lépéseket:

1. A Azure Portal Navigáljon arra a tárolóra, ahová fel kívánja tölteni a blobot.
1. Kattintson a **Feltöltés** gombra.
1. Bontsa ki a **speciális** szakaszt a blob speciális tulajdonságainak megjelenítéséhez.
1. A **Hitelesítés típusa** mezőben adja meg, hogy szeretné-e engedélyezni a feltöltési műveletet az Azure ad-fiókjával vagy a fiók hozzáférési kulcsával, ahogy az alábbi képen látható:

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="A blob-feltöltés engedélyezési módjának módosítását bemutató képernyőkép":::

## <a name="next-steps"></a>Következő lépések

- [Azure-blobok és-várólisták hozzáférésének hitelesítése Azure Active Directory használatával](../common/storage-auth-aad.md)
- [Az Azure Portal használata a blob- és üzenetsoradatok elérésére szolgáló Azure-szerepkör hozzárendeléséhez](../common/storage-auth-aad-rbac-portal.md)
- [Az Azure CLI-vel hozzárendelhet egy Azure-szerepkört a blob-és üzenetsor-adat eléréséhez](../common/storage-auth-aad-rbac-cli.md)
- [A Azure PowerShell modul használatával rendeljen hozzá egy Azure-szerepkört a blob-és üzenetsor-információ eléréséhez](../common/storage-auth-aad-rbac-powershell.md)
