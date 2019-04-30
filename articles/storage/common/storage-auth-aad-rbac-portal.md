---
title: Az Azure AD hozzáférési jogosultsága ahhoz, hogy az RBAC - Azure Storage blob és üzenetsor adatok kezelése az Azure portal használatával |} A Microsoft Docs
description: Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Portal használatával hozzáférés hozzárendelése a tárolókhoz és a rendszerbiztonsági tagok üzenetsorok. Az Azure Storage támogatja a beépített és egyéni RBAC-szerepkörök az Azure AD-n keresztül hitelesítést.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8214ff821bad8a46eb710c8b9506d337715db103
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484355"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Hozzáférés engedélyezése az Azure blob és üzenetsor adatok RBAC használata az Azure Portalon

Az Azure Active Directory (Azure AD) keresztül biztonságos erőforrásokhoz való hozzáférési jogosultságok engedélyezi [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md). Az Azure Storage blob vagy üzenetsor adatok eléréséhez használt gyakori jogosultságkészletek építőelemekkel beépített RBAC-szerepkör egy csoportját határozza meg. 

Az RBAC szerepkör van rendelve egy Azure AD rendszerbiztonsági tag, amikor az Azure biztosít hozzáférést az ezeket az erőforrásokat, hogy a rendszerbiztonsági tag esetében. Hozzáférés az előfizetéshez, az erőforráscsoport, a storage-fiókban vagy egy tároló vagy üzenetsor szintjét hatóköre. Az Azure AD rendszerbiztonsági tag lehet, hogy egy felhasználó, csoport, egy alkalmazás egyszerű szolgáltatást, vagy egy [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md).

Ez a cikk ismerteti, hogyan használhatja az Azure Portalon rendeljen RBAC-szerepköröket. Az Azure Portalon egy egyszerű felületet biztosít az RBAC-szerepkörök hozzárendelése és a tárolási erőforrásokhoz való hozzáférés kezelése. RBAC-szerepkörök az Azure parancssori eszközeivel vagy az Azure Storage felügyeleti API-k blob és üzenetsor erőforrásokhoz is hozzárendelhetők. A tárolási erőforrások RBAC-szerepkörök kapcsolatos további információkért lásd: [hitelesítés hozzáférés az Azure-blobok és üzenetsorok az Azure Active Directoryval](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>A blobok és üzenetsorok RBAC-szerepkörök

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforrás-hatókör meghatározása 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Rendeljen RBAC-szerepköröket az Azure portal használatával

Miután meghatározta a megfelelő szerepkör-hozzárendelés hatókörét, lépjen ennek az erőforrásnak az Azure Portalon. Megjelenítés a **hozzáférés-vezérlés (IAM)** az erőforrás beállításainak és az alábbi lépéseket követve a szerepkör-hozzárendelések kezeléséhez:

1. Rendelje hozzá a megfelelő Azure Storage RBAC-szerepkörök az Azure AD rendszerbiztonsági tag hozzáférést.

1. Rendelje hozzá az Azure Resource Manager [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör tárolók vagy az Azure Portalon az Azure AD hitelesítő adataikkal üzenetsorok elérni kívánó felhasználók számára. 

A következő szakaszok ismertetik az egyes lépéseket részletesen.

### <a name="assign-a-built-in-rbac-role"></a>Beépített RBAC-szerepkör hozzárendelése

Mielőtt egy szerepkört rendel egy rendszerbiztonsági tag, mindenképp vegye figyelembe a hatókörébe tartozó engedélyeket oszt ki. Tekintse át a [erőforrás hatókörének meghatározásához](#determine-resource-scope) szakasz dönthet arról, hogy a megfelelő hatókörben.

Az itt bemutatott eljárás hozzárendel egy szerepkör hatóköre egy tároló, de várólista hatóköre szerepkör hozzárendelése ugyanazokat a lépéseket követheti: 

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz, és megjeleníti a **áttekintése** a fiókhoz.
1. A szolgáltatások területen válassza ki a **Blobok**. 
1. Keresse meg a helyet, amelynek meg szeretné rendelhet egy szerepkört, és megjeleníti a tároló beállításait. 
1. Válassza ki **hozzáférés-vezérlés (IAM)** a tárolóhoz a hozzáférés-vezérlési beállítások megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapján megtekintheti a szerepkör-hozzárendelések listáját.

    ![Képernyőfelvétel: a tároló hozzáférés-vezérlési beállításokkal](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. Kattintson a **szerepkör-hozzárendelés hozzáadása** gombra kattintva adhat hozzá egy új szerepkör.
1. Az a **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a hozzárendelni kívánt Azure Storage-szerepkört. Ezután keresse meg a rendszerbiztonsági tagot, amelyhez szeretné, hogy a szerepkör hozzárendelése.

    ![Képernyőfelvétel: hogyan kell egy RBAC szerepkör hozzárendelése](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Kattintson a **Save** (Mentés) gombra. Az identitás, akinek a szerepköre alatt a szerepkör a listán jelenik meg. Ha például az alábbi képen látható, hogy a hozzáadott felhasználó már rendelkezik olvasási engedéllyel a nevű tárolóban lévő adatok *minta-tároló*.

    ![Képernyőkép a lista megjelenítve egy szerepkörhöz rendelt felhasználók](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

A storage-fiók, erőforráscsoport vagy előfizetés hatóköre szerepkör hozzárendelése hasonló lépéseket követheti.

> [!NOTE]
> Az Azure Storage-fiók tulajdonosai akkor nem lesznek automatikusan hozzárendelve engedélyeket az adatok eléréséhez. Kell explicit módon saját magának egy RBAC szerepkör hozzárendelése az Azure Storage. Az előfizetés, erőforráscsoport, tárfiók, vagy egy tároló vagy üzenetsor szintjén rendelhet.
> 
> A hatókörrel rendelkező egy tároló vagy egy üzenetsor, ha a tárfiók engedélyezve hierarchikus névtérrel rendelkező szerepkör nem rendelhető hozzá.

### <a name="assign-the-reader-role-for-portal-access"></a>A portál elérésére szolgáló olvasó szerepkör hozzárendelése

Rendel egy beépített vagy egyéni szerepkört az Azure Storage egy rendszerbiztonsági tag, amikor a tárfiókban lévő adatokat a műveletek végrehajtásához, hogy a rendszerbiztonsági tag engedélyeket oszt ki. A beépített **Adatolvasó** szerepkörök adja meg az adatokat tároló vagy üzenetsorban, miközben a beépített olvasási engedélyeket **Data Közreműködője** szerepkörök adja meg az olvasási, írási és törlési engedélyek tárolóba vagy üzenetsor. A megadott erőforrás engedélyek hatóköre.  

Például, ha a **Storage-Blobadatok Közreműködője** Mary nevű tároló szintű felhasználói szerepkört **minta-tároló**, Mary nyújtott olvasni, írni és minden a blobok törlése a tároló.

Azonban, ha Mary szeretne egy blob megtekintése az Azure Portalon, akkor a **Storage-Blobadatok Közreműködője** szerepkör önmagában nem biztosít megfelelő engedélyekkel ahhoz, hogy megtekintse a blobba a portálon lépjen. További Azure AD-engedélyekről keresse meg a portálon keresztül, és láthatóvá vannak más erőforrások megtekintéséhez szükséges.

Ha a felhasználóknak képesnek kell lenniük az Azure Portal blobok elérése, majd rendelje hozzá őket egy további RBAC-szerepkör a [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör azoknak a felhasználóknak a storage-fiók vagy újabb szintjén is. A **olvasó** szerepkör egy Azure Resource Manager-szerepkör, amely lehetővé teszi a felhasználók megtekinthetik a tárfiók erőforrásainak, de nem módosíthatók. Nem biztosít az adatok Azure Storage-ban, de csak a fiók felügyeleti erőforrásokhoz olvasási engedéllyel.

Rendelje hozzá a következő lépésekkel a **olvasó** szerepkört, hogy egy felhasználó hozzáférhessen a blobok az Azure Portalról. Ebben a példában a hozzárendelés hatókörét a storage-fiók:

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz.
1. Válassza ki **hozzáférés-vezérlés (IAM)** a storage-fiókhoz tartozó hozzáférés-vezérlési beállítások megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapján megtekintheti a szerepkör-hozzárendelések listáját.
1. Az a **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a **olvasó** szerepkör. 
1. Az a **rendelhet hozzáféréseket** mezőben válassza **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**.
1. Keresse meg a rendszerbiztonsági tagot, amelyhez szeretné rendelni a szerepkört.
1. A szerepkör-hozzárendelés mentése.

> [!NOTE]
> Az Olvasó szerepkör hozzárendelése a csak blobok vagy üzenetsorok az Azure portal használatával elérni kívánó felhasználók számára szükség. 

## <a name="next-steps"></a>További lépések

- A tárolási erőforrások RBAC-szerepkörök kapcsolatos további információkért lásd: [hitelesítés hozzáférés az Azure-blobok és üzenetsorok az Azure Active Directoryval](storage-auth-aad.md). 
- RBAC kapcsolatos további információkért lásd: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md).
- Ismerje meg, hogyan hozzárendelése és kezelése az Azure PowerShell, az Azure CLI vagy a REST API az RBAC szerepkör-hozzárendeléseit, tanulmányozza a következő cikkeket:
    - [Kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)
    - [Kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure CLI-vel](../../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) a REST API-val kezelheti.](../../role-based-access-control/role-assignments-rest.md)
- A tárolókhoz és üzenetsorok, a storage-alkalmazásokban való elérésének hitelesítéséhez, lásd: [az Azure AD használata az Azure Storage-alkalmazások](storage-auth-aad-app.md).
