---
title: Válassza ki, hogyan engedélyezze a hozzáférést az üzenetsor-kezeléshez az Azure CLI-vel
titleSuffix: Azure Storage
description: Itt adhatja meg, hogy az Azure CLI-vel hogyan engedélyezze az adatműveleteket a várólista-adatokon. Az adatműveletek az Azure AD hitelesítő adataival, a fiók hozzáférési kulcsával vagy egy közös hozzáférés-aláírási (SAS-) jogkivonattal is engedélyezhető.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2f7092d8ce184d7021774814e96935e46d1ffb56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363168"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Válassza ki, hogyan engedélyezze a hozzáférést az üzenetsor-kezeléshez az Azure CLI-vel

Az Azure Storage olyan bővítményeket biztosít az Azure CLI-hez, amelyek lehetővé teszik annak megadását, hogy hogyan kívánja engedélyezni az üzenetsor-adatokon végzett műveleteket A következő módokon engedélyezheti az adatműveleteket:

- Egy Azure Active Directory (Azure AD) rendszerbiztonsági tag. A Microsoft az Azure AD-beli hitelesítő adatok használatát javasolja a kiváló biztonság és a könnyű használat érdekében.
- A fiók-hozzáférési kulccsal vagy egy közös hozzáférésű aláírási (SAS-) jogkivonattal.

## <a name="specify-how-data-operations-are-authorized"></a>Az adatműveletek engedélyezésének meghatározása

A várólista-információk olvasására és írására szolgáló Azure CLI-parancsok a választható `--auth-mode` paramétert is tartalmazzák. Adja meg ezt a paramétert, hogy jelezze, hogyan legyen engedélyezve az adatművelet:

- Állítsa be a `--auth-mode` paramétert `login` úgy, hogy bejelentkezzen egy Azure ad rendszerbiztonsági tag használatával (ajánlott).
- Állítsa a `--auth-mode` paramétert az örökölt `key` értékre, hogy megpróbálja lekérni az engedélyezéshez használni kívánt fiók-hozzáférési kulcsot. Ha kihagyja a `--auth-mode` paramétert, akkor az Azure CLI a hozzáférési kulcs lekérését is megkísérli.

A paraméter használatához győződjön `--auth-mode` meg arról, hogy telepítette az Azure CLI v 2.0.46 vagy újabb verzióját. Futtassa a parancsot a `az --version` telepített verziójának vizsgálatához.

> [!NOTE]
> Ha egy Storage-fiók zárolva van egy Azure Resource Manager **írásvédett** zárolással, a [kulcsok listázása](/rest/api/storagerp/storageaccounts/listkeys) művelet nem engedélyezett ehhez a Storage-fiókhoz. A **kulcsok listázása** post művelet, és az összes post művelet meg lesz akadályozva, ha **írásvédett** zárolás van konfigurálva a fiókhoz. Emiatt, ha a fiók **írásvédett** zárolással van zárolva, a fiók kulcsaival nem rendelkező felhasználóknak az Azure ad hitelesítő adatait kell használniuk a várólista adatainak eléréséhez.

> [!IMPORTANT]
> Ha kihagyja a `--auth-mode` paramétert, vagy beállítja a értékre `key` , az Azure CLI a fiók hozzáférési kulcsát próbálja használni az engedélyezéshez. Ebben az esetben a Microsoft azt javasolja, hogy a hozzáférési kulcsot a parancson vagy a `AZURE_STORAGE_KEY` környezeti változón keresztül adja meg. A környezeti változókról további információt a [környezeti változók beállítása az engedélyezési paraméterekhez](#set-environment-variables-for-authorization-parameters)című szakaszban talál.
>
> Ha nem adja meg a hozzáférési kulcsot, az Azure CLI megkísérli meghívni az Azure Storage erőforrás-szolgáltatót az egyes műveletek lekéréséhez. Az erőforrás-szolgáltató hívását igénylő számos adatművelet végrehajtása szabályozást eredményezhet. Az erőforrás-szolgáltatói korlátokkal kapcsolatos további információkért tekintse [meg az Azure Storage erőforrás-szolgáltató skálázhatósági és teljesítménybeli céljait](../common/scalability-targets-resource-provider.md)ismertető témakört.

## <a name="authorize-with-azure-ad-credentials"></a>Engedélyezés Azure AD-beli hitelesítő adatokkal

Ha Azure AD-beli hitelesítő adatokkal jelentkezik be az Azure CLI-be, az OAuth 2,0 hozzáférési tokent ad vissza. Ezt a tokent a rendszer automatikusan használja az Azure CLI-vel a következő adatműveletek engedélyezéséhez Queue Storage. A támogatott műveletek esetében már nem kell átadnia egy fiók kulcsát vagy SAS-jogkivonatát a paranccsal.

Engedélyeket rendelhet az Azure AD rendszerbiztonsági tag számára az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával az üzenetsor-adathoz. Az Azure Storage-beli Azure-szerepkörökkel kapcsolatos további információkért lásd: [hozzáférési jogosultságok kezelése az Azure Storage-adatokhoz az Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Az adatműveletek meghívására vonatkozó engedélyek

Az Azure Storage-bővítmények az üzenetsor-adatokon végrehajtott műveletek esetében támogatottak. A hívható műveletek attól függnek, hogy az Azure AD rendszerbiztonsági tag milyen engedélyekkel rendelkezik, amelyekkel bejelentkezhet az Azure CLI-be. A várólisták engedélyei az Azure RBAC keresztül rendelhetők hozzá. Ha például hozzá van rendelve a **tárolási várólista Adatolvasói** szerepköréhez, futtathat olyan parancsfájl-parancsokat, amelyek az adatok egy várólistából való olvasására szolgálnak. Ha a **Storage-várólista adatközreműködői** szerepkört rendelte hozzá, akkor futtathat parancsfájl-parancsokat, amelyek egy várólistát vagy a bennük található adat olvasását, írását vagy törlését írják le, írhatják vagy törölhetik.

További információ az egyes Azure Storage-műveletekhez szükséges engedélyekről a várólistán: [tárolási műveletek hívása OAuth-jogkivonatokkal](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Példa: művelet engedélyezése üzenetsor létrehozásához Azure AD-beli hitelesítő adatokkal

Az alábbi példa bemutatja, hogyan hozhat létre várólistát az Azure CLI-vel az Azure AD-beli hitelesítő adataival. A várólista létrehozásához be kell jelentkeznie az Azure CLI-be, és szüksége lesz egy erőforráscsoport és egy Storage-fiókra.

1. A várólista létrehozása előtt rendelje hozzá a [tárolási üzenetsor adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) szerepkörét. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekkel kell rendelkeznie az adatműveletek elvégzéséhez a Storage-fiókon. Az Azure-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adatokhoz való hozzáféréshez](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Az Azure-beli szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

1. Az [`az storage queue create`](/cli/azure/storage/queue#az-storage-queue-create) Azure ad-beli `--auth-mode` `login` hitelesítő adataival hozza létre az üzenetsor létrehozásához szükséges paramétert a parancs meghívásával. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Engedélyezés a fiók hozzáférési kulcsával

Ha rendelkezik a fiók kulcsával, meghívhat bármilyen Azure Storage-adatműveletet. Általánosságban elmondható, hogy a fiók kulcsának használata kevésbé biztonságos. Ha a fiók kulcsa biztonságban van, a fiókban lévő összes adattal kapcsolatban lehet sérült.

Az alábbi példa bemutatja, hogyan hozhat létre üzenetsor-kapcsolatot a fiók hozzáférési kulcsa alapján. Adja meg a fiók kulcsát, és adja meg a `--auth-mode` paramétert az `key` értékkel:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Engedélyezés SAS-jogkivonattal

Ha SAS-jogkivonattal rendelkezik, meghívhatja az SAS által engedélyezett adatműveleteket. Az alábbi példa bemutatja, hogyan hozhat létre üzenetsor-tokent egy SAS-jogkivonat használatával:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Környezeti változók beállítása az engedélyezési paraméterekhez

A környezeti változókban megadhatja az engedélyezési paramétereket, így elkerülhető, hogy azok az Azure Storage-adatműveletek minden hívásán bekerüljenek. Az alábbi táblázat az elérhető környezeti változókat ismerteti.

| Környezeti változó | Leírás |
|--|--|
| **AZURE_STORAGE_ACCOUNT** | A tárfiók neve. Ezt a változót a Storage-fiók kulcsával vagy egy SAS-tokenrel együtt kell használni. Ha egyik sincs jelen, az Azure CLI a hitelesített Azure AD-fiók használatával megkísérli lekérni a Storage-fiók hozzáférési kulcsát. Ha egyszerre nagy számú parancs fut, az Azure Storage erőforrás-szolgáltató szabályozási korlátja is elérhető. Az erőforrás-szolgáltatói korlátokkal kapcsolatos további információkért tekintse [meg az Azure Storage erőforrás-szolgáltató skálázhatósági és teljesítménybeli céljait](../common/scalability-targets-resource-provider.md)ismertető témakört. |
| **AZURE_STORAGE_KEY** | A tárfiókkulcs. Ezt a változót a Storage-fiók nevével együtt kell használni. |
| **AZURE_STORAGE_CONNECTION_STRING** | Egy kapcsolati sztring, amely tartalmazza a Storage-fiók kulcsát vagy egy SAS-tokent. Ezt a változót a Storage-fiók nevével együtt kell használni. |
| **AZURE_STORAGE_SAS_TOKEN** | Közös hozzáférésű aláírási (SAS) jogkivonat. Ezt a változót a Storage-fiók nevével együtt kell használni. |
| **AZURE_STORAGE_AUTH_MODE** | Az engedélyezési mód, amellyel a parancsot futtatni kívánja. A megengedett értékek a következők: `login` (ajánlott) vagy `key` . Ha megadja `login` , az Azure CLI az Azure ad hitelesítő adatait használja az adatok műveletének engedélyezéséhez. Ha az örökölt módot adta meg `key` , az Azure CLI megkísérli lekérdezni a fiók hozzáférési kulcsát, és engedélyezi a parancsot a kulccsal. |

## <a name="next-steps"></a>Következő lépések

- [Az Azure parancssori felületének használata Azure-szerepkörök hozzárendeléséhez a blob-és üzenetsor-adateléréshez](../common/storage-auth-aad-rbac-cli.md)
- [Hozzáférés engedélyezése a blob-és üzenetsor-szolgáltatásokhoz az Azure-erőforrások felügyelt identitásával](../common/storage-auth-aad-msi.md)
