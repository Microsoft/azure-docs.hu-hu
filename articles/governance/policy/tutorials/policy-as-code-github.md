---
title: 'Oktatóanyag: Azure Policy implementálása kóddal a GitHubon'
description: Ebben az oktatóanyagban egy Azure Policy fog megvalósítani az exportálással, GitHub-műveletekkel és GitHub-munkafolyamatokkal rendelkező kód-munkafolyamattal.
ms.date: 10/20/2020
ms.topic: tutorial
ms.openlocfilehash: 76a46adc3fc8efab4f7a2d6e656e83c2537dd037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92326146"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Oktatóanyag: Azure Policy implementálása kóddal a GitHubon

A kód-munkafolyamatok Azure Policy lehetővé teszik a szabályzat-definíciók és-hozzárendelések kódként való kezelését, a definíciók frissítésének életciklusának szabályozását, valamint a megfelelőségi eredmények érvényesítésének automatizálását. Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Policy-funkciókat a GitHubon egy életciklus-folyamat létrehozásához. Ezek a feladatok többek között a következők:

> [!div class="checklist"]
> - Szabályzat-definíciók és-hozzárendelések exportálása a GitHubra
> - Leküldéses házirend-objektumok frissítve a GitHubban az Azure-ba
> - Megfelelőségi vizsgálat elindítása a GitHub-műveletből

Ha szeretne hozzárendelni egy szabályzatot a meglévő erőforrások jelenlegi megfelelőségi állapotának azonosításához, a gyors útmutató cikkei ezt ismertetik.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- Az oktatóanyagban használt tervezési minták megismeréséhez tekintse át a [Azure Policy tervezése programkód-munkafolyamattal](../concepts/policy-as-code.md) című részt.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Azure Policy objektumok exportálása a Azure Portalból

A házirend-definíció Azure Portalból való exportálásához kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

1. A Azure Policy lap bal oldalán válassza a **definíciók** lehetőséget.

1. Használja a **definíciók exportálása** gombot, vagy válasszon egy házirend-definíció sorában található három pontot, majd válassza az **Exportálás definícióját**.

1. Válassza a **Bejelentkezés a GitHub** gombbal lehetőséget. Ha még nem hitelesítette a GitHubot, hogy engedélyezze Azure Policy az erőforrás exportálását, tekintse át a megnyíló új ablakban a [GitHub-műveletre](https://github.com/features/actions) vonatkozó igényeket, és válassza a **AzureGitHubActions engedélyezése** lehetőséget az exportálási folyamat folytatásához. Ha elkészült, az új ablak saját bezárása.

1. Az **alapvető** beállítások lapon adja meg a következő beállításokat, majd válassza a **házirendek** lapot vagy a **Tovább: házirendek** gombot az oldal alján.

   - **Adattár szűrője**: állítsa a saját _Tárházak_ lehetőségre, hogy csak az Ön tulajdonában lévő adattárakat, illetve az _összes tárházat_ lássuk, hogy a GitHub-művelethez való hozzáférése megtörténjen.
   - **Adattár**: állítsa be azt a tárházat, amelybe exportálni szeretné a Azure Policy erőforrásokat.
   - **Ág**: az ág beállítása a tárházban. Az alapértelmezetttől eltérő ág használata jó módszer a frissítések érvényesítésére, mielőtt a forráskódba összevonja őket.
   - **Könyvtár**: a _legfelső szintű mappa_ , amelybe exportálni szeretné a Azure Policy erőforrásokat. A könyvtár alatti almappákat a rendszer az exportált erőforrások alapján hozza létre.

1. A **házirendek** lapon állítsa be a keresési hatókört úgy, hogy kijelöli a három pontot, és kiválasztja a felügyeleti csoportok, előfizetések vagy erőforráscsoportok kombinációját.
   
1. A **házirend-definíció (k) hozzáadása** gombbal keresheti meg azt a hatókört, amelybe exportálni kívánja az objektumokat. A megnyíló oldalsó ablakban válassza ki az exportálandó objektumokat. Szűrje a kijelölést a keresőmező vagy a típus alapján. Miután kiválasztotta az összes exportálandó objektumot, használja az oldal alján található **Hozzáadás** gombot.

1. Minden kiválasztott objektumnál válassza ki a kívánt exportálási beállításokat, például a _definíciót_ vagy a definíciót, valamint a házirend-definícióhoz tartozó _hozzárendelés (eke)_ t. Ezután válassza a **felülvizsgálat + exportálás** lapot vagy a **Tovább: felülvizsgálat + exportálás** gombot az oldal alján.

   > [!NOTE]
   > Ha a beállítás _definícióját és a hozzárendelés (eke)_ t választja, csak a szűrő által beállított hatókörön belüli szabályzat-hozzárendelések lesznek exportálva.

1. A **felülvizsgálat + exportálás** lapon ellenőrizze a részletek egyezését, majd használja az oldal alján található **Exportálás** gombot.

1. Tekintse meg a GitHub-tárház, az ág és a _gyökér szintű mappát_ , és ellenőrizze, hogy a kiválasztott erőforrások mostantól a verziókövetés felé lettek-e exportálva.

A Azure Policy-erőforrások a kiválasztott GitHub-adattár és a _gyökér szintű mappa_ következő struktúrájába lesznek exportálva:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>Leküldéses házirend-objektumok frissítve a GitHubban az Azure-ba

1. A házirend-objektumok exportálásakor a rendszer egy nevű [GitHub-munkafolyamat](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) `.github/workflows/manage-azure-policy-<randomLetters>.yml` -fájlt is létrehoz a kezdéshez.

   > [!NOTE]
   > A GitHub-munkafolyamat fájlja minden exportáláskor jön létre. A fájl minden példánya az exportálási művelet során felmerülő beállításokra jellemző.

1. Ez a munkafolyamat-fájl a [Azure Policy kezelése](https://github.com/marketplace/actions/manage-azure-policy) művelettel küldi el a módosításokat a GitHub-tárházban lévő exportált házirend-objektumokon a Azure Policyba. Alapértelmezés szerint a művelet csak azokat a fájlokat veszi figyelembe és szinkronizálja, amelyek eltérnek az Azure-ban meglévőtől. A `assignments` művelet paraméterét is használhatja, hogy csak az adott hozzárendelési fájlokon végzett módosításokat szinkronizálja. Ezzel a paraméterrel csak egy adott környezetre vonatkozó házirend-hozzárendeléseket lehet alkalmazni. További információ: [Azure Policy adattár kezelése – readme](https://github.com/Azure/manage-azure-policy).

1. Alapértelmezés szerint a munkafolyamatot manuálisan kell elindítani. Ehhez használja a GitHubon a **műveleteket** , válassza ki a `manage-azure-policy-<randomLetters>` munkafolyamatot, válassza a **munkafolyamat futtatása** lehetőséget, majd futtassa újra a **munkafolyamatot** .

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="Képernyőfelvétel a művelet lap, a munkafolyamat és a munkafolyamat futtatása gombokról a GitHub webes felületén.":::

   > [!NOTE]
   > A munkafolyamat-fájlnak az észleléshez és a manuális futtatáshoz az alapértelmezett ág kell lennie.

1. A munkafolyamat szinkronizálja a házirend-objektumokon végzett módosításokat az Azure-ban, és megadja a naplókban található állapotot.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="Képernyőkép a működés közbeni munkafolyamatról és a naplókban naplózott adatokról.":::

1. A munkafolyamat a nyomon követéshez Azure Policy-objektumok adatait is hozzáadja `properties.metadata` .

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Képernyőfelvétel: a Azure Portal Azure Policy definíciójának frissítése a GitHub művelettel kapcsolatos metaadatokkal.":::

### <a name="trigger-compliance-scans-using-github-action"></a>Megfelelőségi vizsgálatok indítása a GitHub-művelettel

A [Azure Policy megfelelőség-ellenőrzési művelettel](https://github.com/marketplace/actions/azure-policy-compliance-scan) elindíthat egy igény szerinti megfelelőségi vizsgálatot a [GitHub-munkafolyamatból](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) egy vagy több erőforráson, erőforráscsoporton vagy előfizetésen, és megváltoztathatja a munkafolyamat elérési útját az erőforrások megfelelőségi állapota alapján. A munkafolyamatot úgy is beállíthatja, hogy ütemezett időpontban fusson, hogy a legújabb megfelelőségi állapotot a megfelelő időben beszerezze. Opcionálisan ez a GitHub-művelet is jelentést készíthet a beolvasott erőforrások megfelelőségi állapotáról további elemzéshez vagy archiváláshoz.

Az alábbi példa egy előfizetés megfelelőségi vizsgálatát futtatja. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>Áttekintés

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> - Exportált szabályzat-definíciók és-hozzárendelések a GitHubhoz
> - A GitHubról az Azure-ba frissített leküldéses házirend-objektumok
> - Megfelelőségi vizsgálat elindítása a GitHub-műveletből

## <a name="next-steps"></a>Következő lépések

A szabályzatdefiníciók szerkezetéről szóló további információkért lásd az alábbi cikket:

> [!div class="nextstepaction"]
> [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)