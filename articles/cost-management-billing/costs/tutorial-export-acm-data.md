---
title: Oktatóanyag – Az Azure Cost Managementből exportált adatok létrehozása és kezelése
description: Ez a cikk bemutatja, hogyan hozhatja létre és kezelheti az Azure Cost Managementből exportált adatokat külső rendszerekkel való használat céljából.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: af050ae95b4ab161028229299a8de5ed3426430b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482833"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Oktatóanyag: Exportált adatok létrehozása és kezelése

Ha elolvasta a Költségelemzés című oktatóanyagot, akkor ismeri a Cost Management-adatok manuális letöltésének módját. Azonban létrehozhat egy ismétlődő feladatot, amely naponta, hetente vagy havonta automatikusan exportálja a Cost Management-adatokat az Azure Storage-ba. Az exportált adatok CSV formátumúak, és a Cost Management által gyűjtött összes adatot tartalmazzák. Ezt követően az Azure Storage-beli exportált adatokat külső rendszerekkel is használhatja, illetve saját egyéni adataival kombinálhatja őket. Az exportált adatokat külső rendszerben, például egy irányítópulton vagy egyéb pénzügyi rendszerben is használhatja.

Tekintse meg a [Storage-exportálások Azure Cost Managementtel történő ütemezését ismertető](https://www.youtube.com/watch?v=rWa_xI1aRzo) videót az Azure-költségadatok Azure Storage-ba való ütemezett exportálásáról. További videók megtekintéséhez látogasson el a [Cost Management YouTube-csatornájára](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Az oktatóanyagban szereplő példák bemutatják, hogyan exportálhatja a Cost Management-adatokat, majd hogyan ellenőrizheti, hogy az adatok exportálása sikeres volt-e.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Napi rendszerességű exportálás létrehozása
> * Az adatgyűjtés sikerességének ellenőrzése

## <a name="prerequisites"></a>Előfeltételek
Az adatexportálás számos Azure-fióktípushoz elérhető, beleértve a [Nagyvállalati Szerződéssel (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) és a [Microsoft Ügyfélszerződéssel](get-started-partners.md) rendelkező ügyfeleket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](understand-cost-mgt-data.md). Az alábbi Azure-engedélyek és -hatókörök előfizetésenként támogatottak felhasználó vagy csoport általi adatexportáláshoz. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

- Tulajdonos: Ütemezett exportálásokat hozhat létre, módosíthat vagy törölhet egy előfizetéshez.
- Közreműködő– Létrehozhat, módosíthat vagy törölhet saját ütemezett exportálásokat. Módosíthatja a mások által létrehozott ütemezett exportálási feladatok nevét.
- Olvasó: Ütemezheti az exportálásokat, amelyekhez engedéllyel rendelkezik.

Azure Storage-fiókok esetén:
- A konfigurált tárfiók módosításához írási engedélyekre van szükség függetlenül attól, hogy az exportálásra milyen engedélyek vonatkoznak.
- Az Azure Storage-fiókot blob- vagy fájltároláshoz kell konfigurálni.

Új előfizetés esetén nem használhatja azonnal a Cost Management szolgáltatásait. Akár 48 órára is szükség lehet, hogy a Cost Management összes szolgáltatását használhassa.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

## <a name="create-a-daily-export"></a>Napi rendszerességű exportálás létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Adatexportálás létrehozásához, megtekintéséhez vagy ütemezéséhez nyissa meg a kívánt hatókört az Azure Portalon, majd válassza a **Költségelemzés** lehetőséget a menüben. Például lépjen az **Előfizetések** pontra, válasszon ki egy előfizetést a listában, majd válassza a **Költségelemzés** menüpontot. A Költségelemzés lap tetején kattintson a **Beállítások**, majd az **Exportálások** elemre.

> [!NOTE]
> - Az előfizetéseken túl erőforráscsoportokhoz, felügyeleti csoportokhoz, részlegekhez és regisztrációkhoz is hozhat létre exportálásokat. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).
>- Amikor partnerként jelentkezik be a számlázási fiók hatókörébe vagy egy ügyfél bérlőjébe, adatokat exportálhat abba az Azure Storage-fiókba, amely össze van kapcsolva a partner tárfiókjával. Ehhez azonban aktív előfizetéssel kell rendelkeznie a CSP-bérlő esetében.

1. Válassza a **Hozzáadás** lehetőséget, majd írja be az exportálás nevét.
1. A **Metrika** elemhez válasszon a következők közül:
    - **Tényleges költség (használat és vásárlások)** – Válassza ezt a lehetőséget, ha a standard használatot és költségeket szeretné exportálni
    - **Amortizált költség (használat és vásárlások)** – Válassza ezt a lehetőséget, ha a vásárlások, például az Azure Reservations amortizált költségeit szeretné exportálni
1. Az **Exportálás típusa** elemhez válasszon a következő lehetőségek közül:
    - **Az aktuális hónap költségeinek** napi exportálása" Naponta biztosít egy új exportálási fájlt az aktuális havi költségekhez. Az aktuális adatokat a rendszer összesíti a korábbi napi exportálások adataival.
    - **Az elmúlt hét nap** költségeinek heti exportálása" – A költségek heti exportálását hozza létre az elmúlt hét napra az exportálás kiválasztott kezdő dátumának dátumtól függően.
    - **A múlt havi költségek** havi exportálása" A múlt havi költségek exportálását biztosítja az előző havi költségekről az exportálás létrehozásához használt hónaphoz képest. Ezután az ütemezés minden új hónap ötödik napján exportálást fog futtatni, amely tartalmazza az előző hónap költségeit.
    - **Az egyszeres exportálás** lehetővé teszi egy dátumtartomány választását az előzményadatok Azure Blob Storage-ba való exportálására. A kiválasztott naptól számítva legfeljebb 90 nap előzményadatai exportálhatók. Ez az exportálás azonnal lefut, és két órán belül elérhetővé válik a tárfiókjában.
        Az exportálás típusától függően kiválaszthat egy kezdődátumot, vagy megadhat **Kezdő** és **Záró** dátumot.
1. Adja meg az Azure-tárfiókhoz tartozó előfizetést, majd válasszon ki egy erőforráscsoportot, vagy hozzon létre egy újat.
1. Válassza ki a tárfiók nevét, vagy hozzon létre egy újat.
1. Válassza ki a helyet (Azure-régiót).
1. Adja meg a tárolót és a könyvtár elérési útját, ahová az exportálást irányítani szeretné.
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Új exportálási példa" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Tekintse át az exportálás adatait, majd kattintson a **Létrehozás** gombra.

Az új exportálás megjelenik az exportálások listájában. Alapértelmezés szerint az új exportálások engedélyezve vannak. Ha le szeretne tiltani vagy törölni szeretne egy ütemezett exportálást, kattintson a lista bármelyik elemére, majd a **Letiltás** vagy a **Törlés** parancsra.

Kezdetben 12–24 óra is eltelhet az exportálási feladat lefutásáig. Azonban több időt igénybe vehet, mire az adatok megjelennek az exportált fájlokban.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Amikor programozott módon hoz létre exportálást, manuálisan kell regisztrálnia az erőforrás-szolgáltatót abban az előfizetésben, amelyben a `Microsoft.CostManagementExports` tárfiók található. A regisztráció automatikusan megtörténik, amikor az exportálást a Azure Portal. További információ az erőforrás-szolgáltatók regisztrálásról: [Erőforrás-szolgáltató regisztrálása.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

Először készítse elő a környezetet az Azure CLI-hez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. A bejelentkezés után használja az [az costmanagement export list](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_list) parancsot az aktuális exportálások megtekintéséhez:

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* Az előfizetéseken túl erőforráscsoportokhoz és felügyeleti csoportokhoz is hozhat létre exportálásokat. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).
   >* Amikor partnerként jelentkezik be a számlázási fiók hatókörébe vagy egy ügyfél bérlőjébe, adatokat exportálhat abba az Azure Storage-fiókba, amely össze van kapcsolva a partner tárfiókjával. Ehhez azonban aktív előfizetéssel kell rendelkeznie a CSP-bérlő esetében.

1. Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévőt. Erőforráscsoport létrehozásához használja az [az group create](/cli/azure/group#az_group_create) parancsot:

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Hozzon létre egy tárfiókot az exportálások fogadásához, vagy használjon egy meglévő tárfiókot. Tárfiók létrehozásához használja az [az storage account create](/cli/azure/storage/account#az_storage_account_create) parancsot:

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Futtassa az [az costmanagement export create](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_create) parancsot az exportálás létrehozásához:

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   A **--type** paraméterhez az `ActualCost`, `AmortizedCost` vagy `Usage` közül választhat.

   Ez a példa a következőt használja: `MonthToDate`. Az exportálás naponta létrehoz egy exportálási fájlt a tárgyhavi költségekről. Az aktuális adatokat a rendszer összesíti az aktuális hónap korábbi napi exportálásainak adataival.

1. Az exportálási művelet részleteinek megtekintéséhez használja az [az costmanagement export show](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_show) parancsot:

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. Egy exportálás frissítéséhez használja az [az costmanagement export update](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_update) parancsot:

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   Ez a példa módosítja a kimeneti könyvtárat.

>[!NOTE]
>Kezdetben 12–24 óra is eltelhet az exportálási feladat lefutásáig. Azonban több időt igénybe vehet, mire az adatok megjelennek az exportált fájlokban.

Egy exportálás törléséhez használja az [az costmanagement export delete](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_delete) parancsot:

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Amikor programozott módon hoz létre exportálást, manuálisan kell regisztrálnia az erőforrás-szolgáltatót abban az előfizetésben, amelyben a `Microsoft.CostManagementExports` tárfiók található. A regisztráció automatikusan megtörténik, amikor az exportálást a Azure Portal. További információ az erőforrás-szolgáltatók regisztrálásról: [Erőforrás-szolgáltató regisztrálása.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

Először készítse elő a környezetet az Azure PowerShellhez:

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Amíg az **Az.CostManagement** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie az `Install-Module` parancsmag használatával. Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. A bejelentkezés után használja a [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport) parancsmagot az aktuális exportálások megtekintéséhez:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* Az előfizetéseken túl erőforráscsoportokhoz és felügyeleti csoportokhoz is hozhat létre exportálásokat. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).
   >* Amikor partnerként jelentkezik be a számlázási fiók hatókörébe vagy egy ügyfél bérlőjébe, adatokat exportálhat abba az Azure Storage-fiókba, amely össze van kapcsolva a partner tárfiókjával. Ehhez azonban aktív előfizetéssel kell rendelkeznie a CSP-bérlő esetében.

1. Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévőt. Erőforráscsoport létrehozásához használja a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmagot:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. Hozzon létre egy tárfiókot az exportálások fogadásához, vagy használjon egy meglévő tárfiókot. Tárfiók létrehozásához használja a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmagot:

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. Az exportálás létrehozásához futtassa a [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) parancsmagot:

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   A **DefinitionType** paraméterhez az `ActualCost`, `AmortizedCost` vagy `Usage` közül választhat.

   Ez a példa a következőt használja: `MonthToDate`. Az exportálás naponta létrehoz egy exportálási fájlt a tárgyhavi költségekről. Az aktuális adatokat a rendszer összesíti az aktuális hónap korábbi napi exportálásainak adataival.

1. Az exportálási művelete részleteinek megtekintéséhez használja a `Get-AzCostManagementExport` parancsmagot:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. Az exportálások az [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport) parancsmaggal frissíthetők:

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   Ez a példa módosítja a kimeneti könyvtárat.

>[!NOTE]
>Kezdetben 12–24 óra is eltelhet az exportálási feladat lefutásáig. Azonban több időt igénybe vehet, mire az adatok megjelennek az exportált fájlokban.

Az exportálások az [Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) parancsmaggal törölhetők:

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>Exportálás üzemezése

Az ütemezett exportálást meghatározza, hogy azt eredetileg a hét mely napján és mely időpontban hozta létre. Amikor ütemezett exportálást hoz létre, minden további exportálási eseményre ugyanazon gyakorisággal fog sor kerülni. Például ha az eddigi tárgyhavi költségek napi exportálása napi gyakoriságúra van beállítva, az exportálási feladat naponta fut le. Hasonlóan a heti exportáláshoz, az exportálási feladat minden héten ugyanazon a napon fut le, az ütemezésnek megfelelően. Az exportált adatok pontos kézbesítési időpontja nem garantált, és az exportált adatok a futtatás időpontjától számított négy órán belül állnak rendelkezésre.

Mindegyik exportálás egy új fájlt hoz létre, azaz a régebben exportált adatok nem lesznek felülírva.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Exportálás létrehozása több előfizetéshez

Ha Nagyvállalati Szerződéssel rendelkezik, akkor egy felügyeleti csoporton keresztül egyetlen tárolóban összesítheti az előfizetési költségek adatait, majd exportálhatja a felügyeleti csoport költségkezelési adatait.

Más előfizetési típusokhoz tartozó felügyeleti csoportok adatainak exportálása nem támogatott.

1. Ha még nem hozott létre felügyeleti csoportot, hozzon létre egyet, és rendeljen hozzá előfizetéseket.
1. Adja meg a felügyeleti csoport hatókörét a költségelemzésben, majd jelölje ki a **Válassza ezt a felügyeleti csoportot** lehetőséget.
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="A Válassza ezt a felügyeleti csoportot lehetőséget bemutató példa" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Hozzon létre egy megfelelő hatókörű exportálást, hogy lekérhesse a felügyeleti csoportba tartozó előfizetések költségkezelési adatait.
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Az új exportálás felügyeleti csoport hatókörrel való létrehozását bemutató példa":::

## <a name="verify-that-data-is-collected"></a>Az adatgyűjtés sikerességének ellenőrzése

A Cost Management-adatainak gyűjtését egyszerűen ellenőrizheti, az exportált CSV-fájlt pedig az Azure Storage Explorerrel tekintheti meg.

Az exportálási listában kattintson a tárfiók nevére. A tárfiók lapján válassza a Megnyitás az Explorerben lehetőséget. Ha megjelenik egy megerősítési mező, kattintson az **Igen** gombra, hogy megnyissa a fájlt az Azure Storage Explorerben.

![A tárfiók lapja mintául szolgáló adatokkal és a Megnyitás az Explorerben hivatkozással](./media/tutorial-export-acm-data/storage-account-page.png)

A Storage Explorerben navigáljon ahhoz a tárolóhoz, amelyet meg szeretne nyitni, majd válassza ki az aktuális hónapnak megfelelő mappát. Ekkor egy CSV-fájlokból álló lista jelenik meg. Válasszon ki egy elemet, majd kattintson a **Megnyitás** gombra.

![A Storage Explorerben megjelenő, mintául szolgáló adatok](./media/tutorial-export-acm-data/storage-explorer.png)

A fájl abban a programban vagy alkalmazásban nyílik meg, amelyik a CSV kiterjesztésű fájlok megnyitásához van beállítva. Íme egy példa az Excelben.

![Példa Excelben megjelenített exportált CSV-adatokra](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Exportált CSV-adatfájl letöltése

Az exportált CSV-fájlt az Azure Portalon is letöltheti. A következő lépések bemutatják, hogyan találja meg a költségelemzésben.

1. A költségelemzésben válassza a **Beállítások**, majd az **Exportálások** lehetőséget.
1. Az exportálások listájában válassza ki egy exportálás tárfiókját.
1. A tárfiókban válassza a **Tárolók** elemet.
1. A tárolók listájában válassza ki az adott tárolót.
1. Navigáljon a könyvtárakban és tárolóblobokban a kívánt dátumig.
1. Jelölje ki a CSV-fájlt, majd a válassza a **Letöltés** lehetőséget.

[![Példa exportálás letöltésére](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Az exportálás futtatási előzményeinek megtekintése

Az ütemezett exportálások futtatási előzményeinek megtekintéséhez válasszon ki egy exportálást az exportálások listáját tartalmazó oldalon. Az exportálások listáját tartalmazó oldalon gyorsan megtekintheti a korábbi exportálások futásidejét és a következő exportálás futtatásának időpontját is. Az alábbi példa a futtatási előzményeket mutatja be.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Képernyőkép az Exportálások panelről.":::

Válasszon ki egy exportálást a futtatási előzményei megtekintéséhez.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Képernyőkép egy exportálás futtatási előzményeiről.":::

## <a name="access-exported-data-from-other-systems"></a>Exportált adatokhoz való hozzáférés más rendszerekből

A Cost Management-adatok exportálásának egyik célja az adatok külső rendszerekből való elérése. Használhat például valamilyen irányítópultot vagy egyéb pénzügyi rendszert. Az ilyen rendszerek nagyban eltérnek egymástól, ezért ebben az esetben nem lenne praktikus példával szolgálni.  Az adatok alkalmazásokból való elérését [A Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md) című cikkel is elkezdheti.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Napi rendszerességű exportálás létrehozása
> * Az adatgyűjtés sikerességének ellenőrzése

Folytassa a következő oktatóanyaggal, ha optimalizálni és javítani szeretné a hatékonyságot a tétlen és kihasználatlan erőforrások azonosításával.

> [!div class="nextstepaction"]
> [Optimalizálási javaslatok áttekintése és végrehajtása](tutorial-acm-opt-recommendations.md)
