---
title: Egyéni Markdown csempe használata az Azure-irányítópultokon
description: Megtudhatja, hogyan adhat hozzá Markdown-csempét egy Azure-irányítópulthoz a statikus tartalom megjelenítéséhez
ms.date: 03/19/2021
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 8324b736565cfa353e48cf49b76e2784866f47f7
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774456"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Markdown csempe használata az Azure-irányítópultokon egyéni tartalom megjelenítéséhez

Az egyéni, statikus tartalmak megjelenítéséhez hozzáadhat egy Markdown csempét az Azure-irányítópultokhoz. Megjelenítheti például az alapszintű utasításokat, a képeket vagy a hiperhivatkozások egy készletét egy Markdown csempén.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Markdown csempe hozzáadása az irányítópulthoz

1. Válassza ki az **irányítópultot** a Azure Portal oldalsávon.

   ![A portál oldalsávját ábrázoló képernyőfelvétel](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Az irányítópult nézetében válassza ki azt az irányítópultot, amelyben az egyéni Markdown csempe megjelenjen, majd válassza a **Szerkesztés** lehetőséget.

   ![Az irányítópult szerkesztési nézetét bemutató képernyőkép](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. A **csempék gyűjteményében** keresse meg a **Markdown** nevű csempét, és válassza a **Hozzáadás** lehetőséget. Ekkor megjelenik a csempe az irányítópulton, és megnyílik a **Markdown szerkesztése** panel.

1. Adja meg a **cím** és az **alcím** értékét, amely a csempén jelenik meg, miután áthelyezte egy másik mezőre.

   ![A cím és alcím beírásának eredményeit bemutató képernyőkép](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Válassza ki az egyik lehetőséget, amely tartalmazza a Markdown tartalmakat: **beágyazott szerkesztés** vagy **tartalom beszúrása URL-cím használatával**.

   - Ha közvetlenül szeretné megadni a Markdown, válassza a **beágyazott szerkesztés** lehetőséget.

      ![A beágyazott tartalom bevitelét bemutató képernyőkép](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Válassza a **tartalom beszúrása URL-cím használatával** lehetőséget, ha a meglévő, online Markdown-tartalmat szeretné használni.

      ![Az URL-cím beírását bemutató képernyőkép](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > A további biztonság érdekében létrehozhat egy Markdown-fájlt, és tárolhatja azt egy [Azure Storage-fiók blobjában, ahol engedélyezve van a titkosítás](../storage/common/storage-service-encryption.md), majd mutasson a fájlra az URL-cím beállítás használatával. A Markdown-tartalom titkosítva van a Storage-fiók titkosítási beállításain keresztül. Csak a fájlra vonatkozó engedélyekkel rendelkező felhasználók láthatják a Markdown tartalmát az irányítópulton. Előfordulhat, hogy a Storage-fiókhoz be kell állítania egy [több eredetű erőforrás-megosztási (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) szabályt, hogy a Azure Portal ( _https://portal.azure.com/_ ) hozzáférhessen a blobban található Markdown-fájlhoz.

1. A **kész** gombra kattintva zárja be a **Markdown szerkesztése** panelt. A tartalom megjelenik a Markdown csempén, amelyet a jobb alsó sarokban lévő fogantyú húzásával méretezheti át.

   ![Az egyéni Markdown csempét ábrázoló képernyőfelvétel](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown és korlátozások

A Markdown csempén bármilyen egyszerű szöveg, Markdown szintaxis és HTML-tartalom kombinációja használható. A Azure Portal egy _jelölt_ nevű nyílt forráskódú függvénytárat használ, amely a csempén látható HTML-re alakítja át a tartalmat. A _megjelöléssel_ létrehozott HTML-kódot a portál előre feldolgozta a Megjelenítés előtt. Ez a lépés segít meggyőződni arról, hogy a Testreszabás nem befolyásolja a portál biztonságát vagy elrendezését. Az előzetes feldolgozás során a HTML bármely olyan része törlődik, amely potenciális fenyegetést jelent. A portál a következő típusú tartalmakat nem engedélyezi:

* JavaScript – `<script>` a címkék és a beágyazott JavaScript-értékelések el lesznek távolítva.
* iframe elemek – `<iframe>` a címkék el lesznek távolítva.
* Stílus – `<style>` a címkék el lesznek távolítva. A HTML-elemekben található beágyazott stílusú attribútumok nem támogatottak hivatalosan. Előfordulhat, hogy néhány beágyazott stílusú elem működik Önnek, de ha a portál elrendezését zavarja, bármikor leállíthatja a munkát. A Markdown csempe olyan alapszintű, statikus tartalomhoz készült, amely a portál alapértelmezett stílusait használja.

## <a name="next-steps"></a>Következő lépések

* Egyéni irányítópult létrehozásához tekintse meg [az irányítópultok létrehozása és megosztása a Azure Portal](../azure-portal/azure-portal-dashboards.md)
