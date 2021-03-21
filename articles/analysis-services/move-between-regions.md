---
title: Azure Analysis Services áthelyezése másik régióba | Microsoft Docs
description: Ismerteti, hogyan helyezhető át egy Azure Analysis Services erőforrás egy másik régióba.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 049ff6d14c3967481eb73037814082fa261154e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96497928"
---
# <a name="move-analysis-services-to-a-different-region"></a>Analysis Services áthelyezése másik régióba

Ez a cikk azt ismerteti, hogyan helyezhető át egy Analysis Services kiszolgálói erőforrás egy másik Azure-régióba. A kiszolgálót több okból is áthelyezheti egy másik régióba, például ha egy Azure-régiót szeretne kihasználni a felhasználók számára, hogy csak bizonyos régiókban támogatott szolgáltatási csomagokat használjanak, vagy hogy megfeleljen a belső házirend-és irányítási követelményeknek. 

Ebben és a kapcsolódó csatolt cikkekben a következőket sajátíthatja el:

> [!div class="checklist"]
> 
> * Forrás-kiszolgáló modell adatbázisának biztonsági mentése [blob Storage-tárolóba](../storage/blobs/storage-blobs-introduction.md).
> * Forrás-kiszolgálói [erőforrás-sablon](../azure-resource-manager/templates/overview.md)exportálása.
> * A Storage [közös hozzáférésű aláírása (SAS)](../storage/common/storage-sas-overview.md)beszerzése.
> * Módosítsa az erőforrás-sablont.
> * A sablon üzembe helyezése új célkiszolgáló létrehozásához.
> * Állítsa vissza a modell-adatbázist az új célkiszolgálóra.
> * Ellenőrizze az új célkiszolgáló és adatbázis ellenőrzését.
> * Törölje a forráskiszolgáló-kiszolgálót.

Ez a cikk azt ismerteti, hogyan lehet egy erőforrás-sablon használatával áttelepíteni egyetlen Analysis Services **kiszolgálót egy másik** régióra *és* erőforráscsoporthoz ugyanabban az előfizetésben. A sablon használatával megtartja a konfigurált kiszolgáló tulajdonságait, hogy a célkiszolgáló ugyanazokkal a tulajdonságokkal legyen konfigurálva, kivéve a régiót és az erőforráscsoportot, mint a forráskiszolgáló. Ez a cikk nem ismerteti a társított erőforrások áthelyezését, amelyek lehetnek ugyanahhoz az erőforráscsoporthoz, például az adatforrás, a tároló és az átjáró erőforrásai. 

Mielőtt egy kiszolgálót másik régióba helyez át, javasoljuk, hogy hozzon létre egy részletes tervet. Vegye fontolóra az olyan további erőforrásokat, mint az átjárók és a tárolók, amelyek áthelyezésére is szükség lehet. Minden csomag esetében fontos egy vagy több próbaverziós áthelyezési művelet végrehajtása a tesztelési kiszolgálókkal az üzemi kiszolgáló áthelyezése előtt.

> [!IMPORTANT]
> Az ügyfélalkalmazások és a kapcsolati karakterláncok a teljes kiszolgálónév használatával csatlakoznak Analysis Serviceshoz, amely egy olyan URI, amely tartalmazza a kiszolgálót tartalmazó régiót. Például: `asazure://westcentralus.asazure.windows.net/advworks01`. Ha egy kiszolgálót egy másik régióba helyez át, akkor gyakorlatilag egy új kiszolgálói erőforrást hoz létre egy másik régióban, amely a kiszolgálónév URI-n belül más régióval fog rendelkezni. A parancsfájlokban használt ügyfélalkalmazások és kapcsolati karakterláncok az új kiszolgáló neve URI használatával csatlakozhatnak az új kiszolgálóhoz. A [kiszolgálónév-alias](analysis-services-server-alias.md) használatával csökkentheti a kiszolgálónév URI-azonosítójának megváltoztatásához szükséges helyek számát, de a régió áthelyezése előtt kell megvalósítani.

> [!IMPORTANT]
> Az Azure-régiók eltérő IP-címtartományt használnak. Ha a kiszolgáló és/vagy a Storage-fiók régióhoz konfigurált tűzfal-kivételek vannak, akkor szükség lehet egy másik IP-címtartomány konfigurálására. További információ: [Analysis Services hálózati kapcsolattal kapcsolatos gyakori kérdések](analysis-services-network-faq.md).

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy adatbázis biztonsági másolatát a célkiszolgálóra a forráskiszolgáló régiójában lévő Storage-tárolóból. Bizonyos esetekben a biztonsági másolatok egy másik régióból való visszaállítása gyenge teljesítményt biztosíthat, különösen a nagyméretű adatbázisok esetében. Az adatbázis-visszaállítás során a legjobb teljesítmény érdekében telepítse át vagy hozzon létre egy új tárolót a célkiszolgáló régiójában. Másolja a. ABF biztonságimásolat-fájlokat a forrás régió tárolóból a cél régióba tároló tárolóba, mielőtt visszaállítja az adatbázist a célkiszolgálóra. A jelen cikk hatókörén kívül bizonyos esetekben – különösen a nagyon nagy méretű adatbázisokban – a forráskiszolgáló adatbázisát, újbóli létrehozását, majd feldolgozását a célkiszolgálón, az adatbázis-adatok betöltéséhez pedig költséghatékonyabb lehet, mint a Backup/Restore használata.

> [!NOTE]
> Ha helyszíni adatátjárót használ az adatforrásokhoz való kapcsolódáshoz, az átjáró erőforrását is át kell helyeznie a célkiszolgáló régiójába. További információ: helyszíni [adatátjáró telepítése és konfigurálása](analysis-services-gateway-install.md).

## <a name="prerequisites"></a>Előfeltételek

- **Azure Storage-fiók**: a. ABF biztonságimásolat-fájl tárolásához szükséges.
- **SQL Server Management Studio (SSMS)**: a modell-adatbázisok biztonsági mentéséhez és visszaállításához szükséges.
- **Azure PowerShell**. Csak akkor szükséges, ha a feladatot a PowerShell használatával szeretné befejezni.

## <a name="prepare"></a>Előkészítés

### <a name="backup-model-databases"></a>Biztonsági mentési modell adatbázisai

Ha a **tárolási beállítások** még nincsenek konfigurálva a forráskiszolgálón, kövesse a [tárolási beállítások konfigurálása](analysis-services-backup.md#configure-storage-settings)című témakör lépéseit.

A tárolási beállítások konfigurálásakor a [biztonsági mentés](analysis-services-backup.md#backup) lépéseit követve hozzon létre egy modellt tartalmazó adatbázist. ABF biztonsági mentést a tárolóban. Később visszaállítja a. ABF biztonsági mentést az új célkiszolgálóra.


### <a name="export-template"></a>Sablon exportálása

A sablon a forráskiszolgáló konfigurációs tulajdonságait tartalmazza.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Sablon exportálása az Azure Portallal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Analysis Services-kiszolgálót.

3. Válassza > **Beállítások**  >  **Exportálás sablon** lehetőséget.

4. Válassza a **Letöltés** lehetőséget a **sablon exportálása** panelen.

5. Keresse meg a portálról letöltött. zip fájlt, majd bontsa ki a fájlt egy mappába.

   A zip-fájl tartalmazza azokat a. JSON fájlokat, amelyek tartalmazzák az új kiszolgáló üzembe helyezéséhez szükséges sablont és paramétereket.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sablon exportálása a PowerShell használatával:

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal, és kövesse a képernyőn megjelenő utasításokat:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Ha az identitás egynél több előfizetéshez van társítva, akkor az aktív előfizetést az áthelyezni kívánt kiszolgáló-erőforrás előfizetéséhez állítsa be.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportálja a forráskiszolgáló sablonját. Ezek a parancsok egy JSON-sablont mentenek a ResourceGroupName az aktuális könyvtárba.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Storage közös hozzáférésű aláírásának (SAS) beolvasása

A célkiszolgáló sablonból történő telepítésekor egy felhasználói delegálási SAS-token (URI) szükséges az adatbázis biztonsági másolatát tartalmazó tároló megadásához.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Közös hozzáférésű aláírás beszerzése a portál használatával:

1. A portálon válassza ki a kiszolgáló-adatbázis biztonsági mentéséhez használt Storage-fiókot.

2. Válassza ki **Storage Explorer**, majd bontsa ki a **blob-tárolók** elemet. 

3. Kattintson a jobb gombbal a Storage-tárolóra, majd válassza a **közös hozzáférési aláírás beolvasása** elemet.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="SAS beszerzése":::

4. A **megosztott hozzáférés aláírása** lapon válassza a **Létrehozás** lehetőséget. Alapértelmezés szerint a SAS 24 órán belül lejár.

5. Másolja ki és mentse az **URI**-t. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha közös hozzáférésű aláírást szeretne kérni a PowerShell használatával, kövesse a [felhasználói delegálási sas létrehozása tárolóhoz vagy blobhoz a PowerShell](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)-lel című témakör lépéseit.

---

### <a name="modify-the-template"></a>A sablon módosítása

Szövegszerkesztővel módosítsa az exportált fájl template.jsét, és módosítsa a régiót és a blob-tároló tulajdonságait. 

A sablon módosítása:

1. Egy szövegszerkesztőben a **Location (hely** ) tulajdonságban határozza meg az új célhelyet. A **backupBlobContainerUri** tulajdonságban illessze be a Storage-tároló URI-ját sas-kulccsal. 

    Az alábbi példa beállítja a kiszolgáló advworks1 tartozó célhelyet, `South Central US` és megadja a tárolási tároló URI-ját közös hozzáférési aláírással: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Mentse a sablont.

#### <a name="regions"></a>Régiók

Az Azure-régiók beszerzéséhez tekintse meg az [Azure-telephelyeket](https://azure.microsoft.com/global-infrastructure/locations/). Ha a PowerShell használatával szeretne régiókat beolvasni, futtassa a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancsot.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Áthelyezés

Ha új kiszolgálói erőforrást szeretne üzembe helyezni egy másik régióban, akkor az előző szakaszban exportált és módosított fájl **template.js** fogja használni.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A portálon válassza az **erőforrás létrehozása** lehetőséget.

2. A **Keresés a piactéren** mezőbe írja be a **sablon központi telepítése** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza a **template Deployment** lehetőséget.

4. Válassza a **Létrehozás** lehetőséget.

5. Válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.

6. Válassza a **fájl betöltése** lehetőséget, majd kövesse az utasításokat az exportált és módosított fájl **template.js** betöltéséhez.

7. Ellenőrizze, hogy a sablon-szerkesztő megjeleníti-e az új célkiszolgáló helyes tulajdonságait.

8. Kattintson a **Mentés** gombra.

9. Adja meg vagy válassza ki a tulajdonságértékek értékét:

    - **Előfizetés**: Válassza ki az Azure-előfizetést.
    
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét. Kiválaszthat egy meglévő erőforráscsoportot, amely még nem tartalmaz ugyanazzal a névvel rendelkező Analysis Services kiszolgálót.
    
    - **Hely**: válassza ki ugyanazt a régiót, amelyet a sablonban megadott.

10. Válassza **a felülvizsgálat és létrehozás** lehetőséget.

11. Tekintse át a feltételeket és az alapismereteket, majd válassza a **Létrehozás** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő parancsok használatával telepítheti a sablont:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Célkiszolgáló URI-azonosítójának beolvasása

Ahhoz, hogy a SSMS a modell-adatbázis visszaállításához csatlakozhasson az új célkiszolgálóra, le kell kérnie az új célkiszolgáló URI azonosítóját.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A kiszolgáló URI-azonosítójának beszerzése a portálon:

1. A portálon nyissa meg az új célkiszolgáló erőforrását.

2. Az **Áttekintés** lapon másolja a **kiszolgálónév** URI-ját.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a kiszolgáló URI-ját a PowerShell használatával szeretné lekérni, használja a következő parancsokat:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Másolja a **ServerFullName** a kimenetből.

---

### <a name="restore-model-database"></a>Modell adatbázisának visszaállítása

Kövesse a [Restore (visszaállítás](analysis-services-backup.md#restore) ) című témakörben ismertetett lépéseket a modell-adatbázis visszaállításához. ABF biztonsági másolatot az új célkiszolgálóra.

Nem kötelező: a modell-adatbázis visszaállítása után dolgozza fel a modell és a táblák adatait az adatok adatforrásokból való frissítéséhez. A modell és a tábla feldolgozása a SSMS használatával:

1. A SSMS-ben kattintson a jobb gombbal a modell adatbázis > **folyamat adatbázis** elemre.

2. Bontsa ki a **táblák** csomópontot, kattintson a jobb gombbal a táblázatra. A **folyamat tábláiban** válassza a minden tábla lehetőséget, majd kattintson **az OK gombra**.

## <a name="verify"></a>Ellenőrzés

1. A portálon lépjen az új célkiszolgálóra.

2. Az Áttekintés lap **Analysis Services-kiszolgáló modelljei** területén ellenőrizze, hogy a visszaállított modellek megjelennek-e.

3. Egy ügyfélalkalmazás, például Power BI vagy Excel használatával kapcsolódhat a modellhez az új kiszolgálón. Ellenőrizze, hogy vannak-e olyan objektummodell-objektumok, mint a táblák, mértékek, hierarchiák. 

4. Futtasson Automation-parancsfájlokat. Ellenőrizze, hogy sikeresen végrehajtották-e a műveleteket.

Opcionális: a [Alm Toolkit](http://alm-toolkit.com/) egy *nyílt forráskódú* eszköz Power bi adatkészletek összehasonlításához és kezeléséhez *, valamint* a táblázatos modell-adatbázisok Analysis Servicesához. Az eszközkészlet használatával csatlakozhat a forrás-és a célkiszolgáló adatbázisaihoz, és összehasonlíthatja azokat. Ha az adatbázis-áttelepítés sikeres, a Model Objects ugyanaz lesz a definíció. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután meggyőződött arról, hogy az ügyfélalkalmazások csatlakozni tudnak az új kiszolgálóhoz, és az Automation-parancsfájlok megfelelően vannak végrehajtva, törölje a forráskiszolgáló-kiszolgálót. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

A forráskiszolgáló törlése a portálról:

A forráskiszolgáló **Áttekintés** lapján válassza a **Törlés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A forráskiszolgáló a PowerShell használatával történő törléséhez használja a Remove-AzAnalysisServicesServer parancsot.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Egy régió elvégzése után javasolt, hogy az új célkiszolgáló ugyanazon a régión lévő Storage-tárolót használja a biztonsági mentéshez, a forráskiszolgáló régiójában lévő tároló helyett.