---
title: Statikus webhely üzemeltetése az Azure Storage-ban
description: Megtudhatja, hogyan szolgálhat ki statikus tartalmat (HTML-, CSS-, JavaScript-és képfájlokat) közvetlenül egy Azure Storage GPv2-fiókban található tárolóból.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2ba0e836f1be97681d677158f5cb81c5b033e915
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008501"
---
# <a name="host-a-static-website-in-azure-storage"></a>Statikus webhely üzemeltetése az Azure Storage-ban

A statikus tartalmat (HTML-, CSS-, JavaScript-és képfájlokat) közvetlenül egy [általános célú v2-](../common/storage-account-create.md) vagy [BlockBlobStorage](storage-blob-create-account-block-blob.md) -fiókban lévő tárolóból is kiszolgálhatja. További információ: [statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website.md).

Ez a cikk bemutatja, hogyan engedélyezheti a statikus webhelyek üzemeltetését az Azure Portal, az Azure CLI vagy a PowerShell használatával.

## <a name="enable-static-website-hosting"></a>Statikus webhely üzemeltetésének engedélyezése

A statikus webhely üzemeltetése olyan szolgáltatás, amelyet engedélyeznie kell a Storage-fiókban.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A kezdéshez jelentkezzen be a [Azure Portalba](https://portal.azure.com/) .

2. Keresse meg a Storage-fiókját, és jelenítse meg a fiók áttekintését.

3. Válassza a **statikus webhely** lehetőséget a statikus webhelyek konfigurációs oldalának megjelenítéséhez.

4. Válassza az engedélyezve lehetőséget a statikus webhely-üzemeltetési **funkció** engedélyezéséhez a Storage-fiókhoz.

5. Az **index dokumentum neve** mezőben adja meg az alapértelmezett index lapot (például: *index.html*). 

   Az alapértelmezett index lap akkor jelenik meg, ha a felhasználó a statikus webhely gyökerére navigál.  

6. A **hiba-dokumentum elérési útja** mezőben válasszon egy alapértelmezett hibaüzenetet (például: *404.html*). 

   Az alapértelmezett hiba lap akkor jelenik meg, ha a felhasználó olyan oldalra próbál navigálni, amely nem szerepel a statikus webhelyén.

7. Kattintson a **Mentés** gombra. A Azure Portal mostantól megjeleníti a statikus webhely végpontját. 

    ![Statikus webhely üzemeltetésének engedélyezése Storage-fiókhoz](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

Az [Azure Command-Line Interface (CLI)](/cli/azure/)használatával engedélyezheti a statikus webhely üzemeltetését.

1. Először nyissa meg a [Azure Cloud Shell](../../cloud-shell/overview.md), vagy ha helyileg [telepítette](/cli/azure/install-azure-cli) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

3. A statikus webhely üzemeltetésének engedélyezése.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

   * Cserélje le a `<error-document-name>` helyőrzőt a felhasználók számára megjelenő hibaüzenet nevére, ha egy böngésző olyan oldalt kér a webhelyen, amely nem létezik.

   * Cserélje le a `<index-document-name>` helyőrzőt az index dokumentum nevére. Ez a dokumentum általában "index.html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

A Azure PowerShell modul használatával engedélyezheti a statikus webhely üzemeltetését.

1. Nyisson meg egy Windows PowerShell-parancssori ablakot.

2. Ellenőrizze, hogy rendelkezik-e Azure PowerShell modul az 0,7-es vagy újabb verziójával.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

3. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

4. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

5. Szerezze be a használni kívánt Storage-fiókot meghatározó Storage-fiók környezetét.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevére.

   * Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

6. A statikus webhely üzemeltetésének engedélyezése.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Cserélje le a `<error-document-name>` helyőrzőt a felhasználók számára megjelenő hibaüzenet nevére, ha egy böngésző olyan oldalt kér a webhelyen, amely nem létezik.

   * Cserélje le a `<index-document-name>` helyőrzőt az index dokumentum nevére. Ez a dokumentum általában "index.html".

---

## <a name="upload-files"></a>Fájlok feltöltése 

### <a name="portal"></a>[Portál](#tab/azure-portal)

Ezek az utasítások bemutatják, hogyan tölthet fel fájlokat a Azure Portalban megjelenő Storage Explorer verziójának használatával. Azonban használhatja a Azure Portalon kívül futó [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) verzióját is. Használhatja a [AzCopy](../common/storage-use-azcopy-v10.md), a PowerShellt, a CLI-t vagy bármely olyan egyéni alkalmazást, amely képes fájlokat feltölteni a fiókja **$web** tárolóba. A fájloknak a Visual Studio Code használatával történő feltöltését bemutató lépésenkénti oktatóanyagért lásd [: oktatóanyag: statikus webhely üzemeltetése blob Storageon](./storage-blob-static-website-host.md).

1. Válassza a **Storage Explorer (előzetes verzió)** lehetőséget.

2. Bontsa ki a **blob-tárolók** csomópontot, majd válassza ki a **$web** tárolót.

3. A fájlok feltöltéséhez kattintson a **feltöltés** gombra.

   ![Fájlok feltöltése](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Ha a böngészőben szeretné megjeleníteni a fájl tartalmát, győződjön meg arról, hogy a fájl tartalomtípusa a következőre van beállítva: `text/html` . 

   ![Tartalomtípusok keresése](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > A Storage Explorer automatikusan beállítja ezt a tulajdonságot `text/html` a gyakran felismerhető bővítményekhez, például: `.html` . Bizonyos esetekben azonban ezt saját magának kell beállítania. Ha nem állítja be ezt a tulajdonságot `text/html` , a böngésző felszólítja a felhasználót, hogy a tartalom megjelenítése helyett letöltse a fájlt. A tulajdonság beállításához kattintson a jobb gombbal a fájlra, majd kattintson a **Tulajdonságok** parancsra.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Objektumok feltöltése a *$web* tárolóba egy forrás könyvtárából.

Ez a példa feltételezi, hogy Azure Cloud Shell-munkamenetből futtat parancsokat.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Ha a böngésző arra kéri a felhasználókat, hogy a tartalom megjelenítése helyett letöltsék a fájlt, akkor `--content-type 'text/html; charset=utf-8'` a parancshoz hozzáfűzheti. 

* Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

* Cserélje le a `<source-path>` helyőrzőt a feltölteni kívánt fájlok helyének elérési útjára.

> [!NOTE]
> Ha az Azure CLI-t használja, az elérési utat használhatja a helyi számítógép bármely helyére (például: `C:\myFolder` ).
>
> Azure Cloud Shell használata esetén olyan fájlmegosztást kell hivatkoznia, amely látható a Cloud Shell számára. Ez a hely lehet a megosztott felhőalapú fájlmegosztás vagy egy meglévő fájlmegosztás, amelyet a Cloud Shell lehet csatlakoztatni. Ennek megismeréséhez tekintse meg [a fájlok megőrzése Azure Cloud Shellban](../../cloud-shell/persisting-shell-storage.md)című témakört.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Objektumok feltöltése a *$web* tárolóba egy forrás könyvtárából.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Ha a böngésző arra kéri a felhasználókat, hogy a tartalom megjelenítése helyett letöltsék a fájlt, akkor `-Properties @{ ContentType = "text/html; charset=utf-8";}` a parancshoz hozzáfűzheti.

* Cserélje le a `<path-to-file>` helyőrző értékét a feltölteni kívánt fájl teljes elérési útjára (például: `C:\temp\index.html` ).

* Cserélje le a `<blob-name>` helyőrző értékét arra a névre, amelyet az eredményül kapott blobhoz kíván adni (például: `index.html` ).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Webhely URL-címének megkeresése

A webhely oldalait egy böngészőből tekintheti meg a webhely nyilvános URL-címének használatával.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Storage-fiók fiók áttekintése oldalán megjelenő ablaktáblán válassza a **statikus webhely** lehetőséget. A webhely URL-címe az **elsődleges végpont** mezőben jelenik meg.

![Azure Storage – statikus webhelyek mérőszámai metrika](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A statikus webhely nyilvános URL-címének megkereséséhez használja a következő parancsot:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

* Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevére.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő parancs használatával keresse meg a statikus webhely nyilvános URL-címét:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevére.

* Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Metrikák engedélyezése a statikus webhelyek oldalain

A metrikák engedélyezése után a **$web** tárolóban található fájlokra vonatkozó forgalmi statisztikát a metrikák irányítópultján kell jelenteni.

1. Kattintson a **metrikák** elemre a Storage-fiók menü **figyelés** szakaszában.

   > [!div class="mx-imgBorder"]
   > ![Metrikák hivatkozása](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > A metrikák adatait a rendszer a különböző metrikák API-kkal összekapcsolva hozza létre. A portál csak az adott időkereten belül használt API-tagokat jeleníti meg, hogy csak az adatvisszaadó tagokra összpontosítsanak. Annak érdekében, hogy ki tudja választani a szükséges API-tagot, az első lépés az időkeret kibontása.

2. Kattintson az időkeret gombra, válasszon ki egy időkeretet, majd kattintson az **alkalmaz** gombra.

   ![Azure Storage – statikus webhelyek metrikáinak időtartománya](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Válassza a **blob** elemet a *névtér* legördülő listából.

   ![Azure Storage – statikus webhelyek mérőszámai – névtér](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Ezután válassza ki a **kimenő** forgalom metrikáját.

   ![Képernyőkép, amely megjeleníti az Azure Storage statikus webhelyeinek kimenő metrikáját.](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Válassza ki az **összeg** elemet az *összesítési* választóból.

   ![Azure Storage – statikus webhelyek metrikáinak összesítése](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Kattintson a **szűrő hozzáadása** gombra, és válassza az **API-név** lehetőséget a *tulajdonság* -választóban.

   ![Az Azure Storage statikus webhelyeinek metrikai API-neve](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Jelölje be a **GetWebContent** melletti jelölőnégyzetet az *értékek* kiválasztásakor a metrikák jelentésének feltöltéséhez.

   ![Azure Storage – statikus webhelyek metrikái GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > A **GetWebContent** jelölőnégyzet csak akkor jelenik meg, ha az API-tag egy adott időkereten belül volt használva. A portál csak az adott időkereten belül használt API-tagokat jeleníti meg, hogy csak az adatvisszaadó tagokra összpontosítsanak. Ha nem talál egy adott API-tagot ebben a listában, bontsa ki az időkeretet.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan konfigurálhat egy egyéni tartományt a statikus webhellyel. Lásd: [egyéni tartomány leképezése egy Azure Blob Storage-végpontra](storage-custom-domain-name.md).