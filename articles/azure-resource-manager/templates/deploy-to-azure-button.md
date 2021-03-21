---
title: Üzembe helyezés az Azure-ban gomb
description: Azure Resource Manager-sablonok GitHub-tárházból való üzembe helyezéséhez használja a gombot.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: abe59f377474540e9209691df8b1d1a7b806c26d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028743"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Sablonok üzembe helyezése a GitHub-tárházból a központi telepítés gomb használatával

Ez a cikk bemutatja, hogyan helyezhetők üzembe sablonok egy GitHub-tárházból a **telepítés az Azure** -ban gomb használatával. A gombot közvetlenül a GitHub-tárházban található _readme.MD_ -fájlhoz is hozzáadhatja. Azt is megteheti, hogy a gombot egy olyan weblapra adja, amely hivatkozik a tárházra.

A központi telepítési hatókör meghatározása a sablon sémája alapján történik. További információkért lásd:

- [erőforráscsoportok](deploy-to-resource-group.md)
- [előfizetések](deploy-to-subscription.md)
- [felügyeleti csoportok](deploy-to-management-group.md)
- [bérlők](deploy-to-tenant.md)

## <a name="use-common-image"></a>Közös rendszerkép használata

A gombnak a weboldalához vagy adattárhoz való hozzáadásához használja az alábbi képet:

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

A rendszerkép a következőképpen jelenik meg:

![Üzembe helyezés az Azure-ban gomb](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>URL-cím létrehozása sablon üzembe helyezéséhez

A sablon URL-címének létrehozásához Kezdje a tárházban található sablon nyers URL-címével. A nyers URL-cím megjelenítéséhez válassza a **RAW** elemet.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="Nyers kiválasztása":::

Az URL-cím formátuma:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Ezután alakítsa át az URL-címet egy URL-kódolású értékre. Használhat online kódolót, vagy futtathat egy parancsot. A következő PowerShell-példa azt szemlélteti, hogyan kódolhat egy értéket az URL-cím.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

Az URL-cím kódolásakor a példában szereplő URL-cím értéke a következő.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Az egyes hivatkozások ugyanazzal az alap URL-címmel kezdődnek:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Adja hozzá az URL-kódolású sablon hivatkozását az alap URL-cím végéhez.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

A hivatkozás teljes URL-címe.

A sablont általában nyilvános tárházban kell üzemeltetni. Ha privát tárházat használ, meg kell adnia egy jogkivonatot a sablon nyers tartalmának eléréséhez. A GitHub által generált jogkivonat csak rövid ideig érvényes. Gyakran frissítenie kell a hivatkozást.

Ha a git-t egy GitHub-tárház helyett az [Azure Repos](/azure/devops/repos/git/) használatával használja, továbbra is használhatja az **üzembe helyezés az Azure** -ban gombot. Győződjön meg arról, hogy a tárház nyilvános. A sablon beszerzéséhez használja az [Items műveletet](/rest/api/azure/devops/git/items/get) . A kérelemnek a következő formátumúnak kell lennie:

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

A kérelem URL-címének kódolása.

## <a name="create-deploy-to-azure-button"></a>Üzembe helyezés létrehozása az Azure-ban gomb

Végül helyezze össze a hivatkozást és a képet.

Ha a Markdown-t a GitHub-tárházban vagy egy weblapon szeretné hozzáadni a _readme.MD_ -fájlhoz, használja a következőt:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

HTML esetén használja a következőt:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

A git és az Azure-tárház esetében a gomb formátuma a következő:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A teljes megoldás teszteléséhez válassza a következő gombot:

[![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

A portálon egy ablaktábla jelenik meg, amely lehetővé teszi a paraméterek értékének egyszerű megadását. A paraméterek előre ki vannak töltve a sablon alapértelmezett értékeivel.

![A portál használata az üzembe helyezéshez](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Következő lépések

- A sablonokkal kapcsolatos további tudnivalókért tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
