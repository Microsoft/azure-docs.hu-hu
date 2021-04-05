---
title: Azure Remote Rendering-fiók létrehozása
description: Útmutató az Azure-beli távoli rendereléshez szükséges fiók létrehozásához
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 83bd4a7ae0082d24f7ac617719e628f4db4baeb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98197632"
---
# <a name="create-an-azure-remote-rendering-account"></a>Azure Remote Rendering-fiók létrehozása

Ez a fejezet végigvezeti az **Azure távoli renderelési** szolgáltatáshoz tartozó fiók létrehozásának lépésein. A rövid útmutatók vagy oktatóanyagok végrehajtásához érvényes fiók szükséges.

## <a name="create-an-account"></a>Fiók létrehozása

A következő lépések szükségesek ahhoz, hogy fiókot hozzon létre az Azure távoli renderelési szolgáltatáshoz:

1. Ugrás a [vegyes valóság előnézeti oldalára](https://aka.ms/MixedRealityPrivatePreview)
1. Kattintson az "erőforrás létrehozása" gombra
1. A keresőmezőbe ("keresés a piactéren") írja be a "távoli renderelés" kifejezést, és nyomja le az ENTER billentyűt.
1. Az eredmények listájában kattintson a "távoli renderelés" csempére.
1. A következő képernyőn kattintson a "létrehozás" gombra. Megnyílik egy űrlap, amely létrehoz egy új távoli renderelési fiókot:
    1. Az "erőforrás neve" beállítása a fiók nevére
    1. Előfizetés frissítése, ha szükséges
    1. Az "erőforráscsoport" beállítása tetszőleges erőforráscsoporthoz
    1. Válasszon ki egy régiót a "location" legördülő listából, ahol ezt az erőforrást létre kell hozni a alkalmazásban. Lásd: Megjegyzések az alábbi [fiók-régiókban](create-an-account.md#account-regions) .
1. A fiók létrehozása után navigáljon hozzá, és:
    1. Az *Áttekintés* lapon jegyezze fel a fiók azonosítóját.
    1. A *beállítások > hozzáférési kulcsok* lapon jegyezze fel az "elsődleges kulcsot" – Ez a fiók titkos fiókjának kulcsa.

### <a name="account-regions"></a>Fiók régiói
A fiók létrehozásakor megadott hely határozza meg, hogy a fiók erőforrása melyik régióhoz van rendelve. Ez a létrehozás után nem módosítható. A fiókkal azonban bármely [támogatott régióban](./../reference/regions.md)távoli renderelési munkamenethez kapcsolódhat, függetlenül a fiók helyétől.

### <a name="retrieve-the-account-information"></a>Fiókadatok beolvasása

A mintáknak és az oktatóanyagoknak meg kell adniuk a fiók AZONOSÍTÓját és kulcsát. A PowerShell-minta parancsfájljaihoz használt fájlban lévő **arrconfig.js** például:

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

A *régió* lehetőség kitöltéséhez tekintse meg az [elérhető régiók listáját](../reference/regions.md) .

A (z) és a (z) értékeit a **`arrAccountId`** **`arrAccountKey`** portálon találja, az alábbi lépések szerint:

* Nyissa meg az [Azure Portalt](https://www.portal.azure.com)
* A **"távoli renderelési fiók"** kifejezés megkeresése – a **"legutóbbi erőforrások"** listában kell szerepelnie. A felső menüsorban is megkeresheti a keresést. Ebben az esetben ellenőrizze, hogy a használni kívánt előfizetés ki van-e választva az alapértelmezett előfizetés-szűrőben (a keresősáv melletti szűrő ikon):

![Előfizetés-szűrő](./media/azure-subscription-filter.png)

Ha a fiókjára kattint, megjelenik a képernyő, amely azonnal megjeleníti a  **fiók azonosítóját** :

![Azure-fiók azonosítója](./media/azure-account-id.png)

A kulcshoz kattintson a bal oldali panelen a **hozzáférési kulcsok** elemre. A következő oldalon egy elsődleges és egy másodlagos kulcs látható:

![Azure-hozzáférési kulcsok](./media/azure-account-primary-key.png)

A értéke lehet **`arrAccountKey`** elsődleges vagy másodlagos kulcs.

## <a name="link-storage-accounts"></a>Storage-fiókok csatolása

Ez a bekezdés a Storage-fiókok távoli renderelési fiókjához való csatolását ismerteti. Ha egy Storage-fiók csatolva van, nem szükséges SAS URI-t előállítani, amikor a fiókjában lévő adataival szeretne kommunikálni, például egy modell betöltésekor. Ehelyett a Storage-fiókok nevét használhatja közvetlenül a [modell betöltése szakaszban](../concepts/models.md#loading-models)leírtak szerint.

Az ebben a bekezdésben szereplő lépéseket minden olyan Storage-fiókhoz el kell végrehajtani, amely ezt a hozzáférési módszert használja. Ha még nem hozott létre Storage-fiókokat, a [modell konvertálása](../quickstarts/convert-model.md#storage-account-creation)a gyors üzembe helyezéshez című szakasz megfelelő lépéseit követheti.

Most feltételezzük, hogy van egy Storage-fiókja. Keresse meg a Storage-fiókot a portálon, és lépjen a **Access Control (iam)** lapra az adott Storage-fiókhoz:

![A Storage-fiók IAM](./media/azure-storage-account.png)

Győződjön meg arról, hogy rendelkezik tulajdonosi engedélyekkel ezen a Storage-fiókon, hogy hozzá tudja adni a szerepkör-hozzárendeléseket. Ha nincs hozzáférése, a **szerepkör-hozzárendelés hozzáadása** lehetőség le lesz tiltva.

A szerepkör hozzáadásához kattintson a **Hozzáadás** gombra a "szerepkör-hozzárendelés hozzáadása" csempén.

![A Storage-fiók IAM szerepkör-hozzárendelés hozzáadása](./media/azure-add-role-assignment.png)

* Rendeljen hozzá **Storage blob-adatközreműködői** szerepkört a fenti képernyőképen látható módon.
* Válassza a **távoli renderelési fiókhoz**  rendelt felügyelt identitás elemet a **hozzáférés hozzárendelése** legördülő listához.
* Válassza ki az előfizetését és a távoli renderelési fiókot az utolsó legördülő listában.
* A módosítások mentéséhez kattintson a Save (Mentés) gombra.

> [!WARNING]
> Ha a távoli megjelenítési fiók nem szerepel a listáján, tekintse meg ezt a [hibakeresési szakaszt](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account).

> [!IMPORTANT]
> Az Azure-beli szerepkör-hozzárendeléseket az Azure Storage gyorsítótárazza, így akár 30 percet is igénybe vehet, amikor hozzáférést biztosít a távoli renderelési fiókjához, és amikor hozzáfér a Storage-fiókhoz. A részletekért tekintse meg az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) dokumentációját](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected) .

## <a name="next-steps"></a>Következő lépések

* [Hitelesítés](authentication.md)
* [Az Azure frontend API-k használata a hitelesítéshez](frontend-apis.md)
* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)