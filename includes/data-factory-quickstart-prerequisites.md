---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: include
ms.custom: include file
ms.date: 06/27/2019
ms.openlocfilehash: 0c21c576cbb99ccc96bf7edf733769a9b28b9344
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612526"
---
## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription"></a>Azure-előfizetés

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Azure-szerepkörök

Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. Az előfizetésben található engedélyek megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com), válassza ki a felhasználónevét a jobb felső sarokban, válassza a "**...**" ikont, és válassza a **saját engedélyek** lehetőséget. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést.

A Data Factory gyermekerőforrásai – beleértve az adatkészletek, társított szolgáltatások, folyamatok, eseményindítók és integrációs modulok – létrehozására és kezelésére az alábbi követelmények vonatkoznak:

- A Azure Portal alárendelt erőforrásainak létrehozásához és kezeléséhez a **Data Factory közreműködő** szerepkörhöz kell tartoznia az erőforráscsoport szintjén vagy felett.
- A gyermekerőforrások PowerShell-lel vagy az SDK-val való létrehozásához és kezeléséhez a **közreműködő** szerepkör is elegendő az erőforráscsoport szintjén vagy felette.

Ha szeretne példautasításokat látni arra, hogyan kell egy felhasználót a szerepkörhöz adni, olvassa el a [Szerepkörök hozzáadása](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) című cikket.

További információkért tekintse meg a következő cikkeket:

- [Data Factory közreműködője szerepkör](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Szerepkörök és engedélyek az Azure Data Factoryhoz](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage-fiók

Ebben a rövid útmutatóban egy általános célú Azure Storage-fiókot (kifejezetten blob Storage-t) használunk *forrás* -és *célhelyként* . Ha nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a hozzon létre [egy Storage-fiókot](../articles/storage/common/storage-account-create.md) . 

#### <a name="get-the-storage-account-name"></a>A Storage-fiók nevének beolvasása

Ehhez a rövid útmutatóhoz szüksége lesz az Azure Storage-fiók nevére. A következő eljárás a Storage-fiók nevének beszerzéséhez nyújt lépéseket: 

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
2. A Azure Portal menüben válassza a **minden szolgáltatás** lehetőséget, majd válassza a **Storage**-  >  **fiókok** lehetőséget. Bármelyik oldalon megkeresheti és kiválaszthatja a *Storage-fiókokat* is.
3. A **Storage-fiókok** lapon szűrje a Storage-fiókot (ha szükséges), majd válassza ki a Storage-fiókját. 

Bármelyik oldalon megkeresheti és kiválaszthatja a *Storage-fiókokat* is.

#### <a name="create-a-blob-container"></a>Blobtároló létrehozása

Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre az Azure Blob Storage-ban.

1. A Storage-fiók lapon válassza az **áttekintő**  >  **tárolók** lehetőséget.
2. A *\<Account name>*  -  **tárolók** lap eszköztárán válassza a **tároló** elemet.
3. Az **Új tároló** párbeszédablakban adja meg az **adftutorial** nevet, és kattintson az **OK** gombra. A *\<Account name>*  -  **tárolók** lap frissül, hogy tartalmazza a **adftutorial** a tárolók listájában.

   :::image type="content" source="media/data-factory-quickstart-prerequisites/list-of-containers.png" alt-text="Tárolók listája":::


#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Bemeneti mappa és fájl hozzáadása a blob-tárolóhoz

Ebben a szakaszban létrehoz egy **input** nevű mappát a létrehozott tárolóban, majd feltölt egy mintát a bemeneti mappába. Mielőtt elkezdené, nyisson meg egy szövegszerkesztőt, például a **jegyzettömböt**, és hozzon létre egy **emp.txt** nevű fájlt a következő tartalommal:

```emp.txt
John, Doe
Jane, Doe
```

Mentse a fájlt a **C:\ADFv2QuickStartPSH** mappába. (Ha a mappa még nem létezik, hozza létre.) Ezután térjen vissza a Azure Portal, és kövesse az alábbi lépéseket:

1. A *\<Account name>*  -  megmaradt **tárolók** lapon válassza a **adftutorial** lehetőséget a tárolók frissített listájából.

   1. Ha bezárta az ablakot, vagy egy másik lapra ment, jelentkezzen be újra a [Azure Portalba](https://portal.azure.com) .
   1. A Azure Portal menüben válassza a **minden szolgáltatás** lehetőséget, majd válassza a **Storage**-  >  **fiókok** lehetőséget. Bármelyik oldalon megkeresheti és kiválaszthatja a *Storage-fiókokat* is.
   1. Válassza ki a Storage-fiókját, majd válassza a **tárolók**  >  **adftutorial** lehetőséget.

2. A **adftutorial** -tároló lapja eszköztárán válassza a **feltöltés** lehetőséget.
3. A **blob feltöltése** lapon kattintson a **fájlok** mezőre, majd keresse meg és válassza ki a **emp.txt** fájlt.
4. Bontsa ki a **speciális** fejlécet. A lap ekkor megjelenik:

   :::image type="content" source="media/data-factory-quickstart-prerequisites/upload-blob-advanced.png" alt-text="Kattintás a Speciális hivatkozásra":::

5. A **feltöltés mappába** mezőbe írja be a **bemenet** értéket.
6. Kattintson a **Feltöltés** gombra. A listában meg kell jelennie az **emp.txt** fájlnak és a feltöltés állapotának.
7. Válassza a **Bezárás** ikont ( **X**) a **blob feltöltése** oldal bezárásához.

Tartsa megnyitva a **adftutorial** -tároló lapot. A segítségével ellenőrizheti ennek a rövid útmutatónak az eredményét.