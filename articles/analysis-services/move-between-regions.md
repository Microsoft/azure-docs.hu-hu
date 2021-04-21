---
title: Áthelyezés Azure Analysis Services másik régióba | Microsoft Docs
description: Leírja, hogyan lehet áthelyezni Azure Analysis Services erőforrást egy másik régióba.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 2b698ffaddb4bc818eaabda34022ab58ff05fe5f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786350"
---
# <a name="move-analysis-services-to-a-different-region"></a>Áthelyezés Analysis Services másik régióba

Ez a cikk azt ismerteti, hogyan lehet áthelyezni egy Analysis Services-kiszolgálói erőforrást egy másik Azure-régióba. A kiszolgálót több okból is áthelyezheti egy másik régióba, például hogy kihasználja egy, a felhasználókhoz közelebbi Azure-régió előnyeit, hogy csak bizonyos régiókban támogatott szolgáltatásterveket használjon, vagy hogy megfeleljen a belső szabályzatok és irányítási követelményeknek. 

Ebben és a kapcsolódó hivatkozott cikkekben a következővel ismerkedik meg:

> [!div class="checklist"]
> 
> * Biztonsági másolat készítése egy forráskiszolgáló-modelladatbázisról [a Blob Storage-be.](../storage/blobs/storage-blobs-introduction.md)
> * Exportálja a forráskiszolgálói [erőforrássablont.](../azure-resource-manager/templates/overview.md)
> * Tároló közös [hozzáférésű jogosultsága (SAS) lekérte.](../storage/common/storage-sas-overview.md)
> * Módosítsa az erőforrássablont.
> * Telepítse a sablont egy új célkiszolgáló létrehozásához.
> * Modelladatbázis visszaállítása az új célkiszolgálóra.
> * Ellenőrizze az új célkiszolgálót és adatbázist.
> * Törölje a forráskiszolgálót.

Ez a cikk azt ismerteti, hogyan lehet egy erőforrássablonnal  egyetlen Analysis Services-kiszolgálót  alapszintű konfigurációval egy másik régióba és erőforráscsoportba átemelni ugyanabban az előfizetésben. A sablonok használata megőrzi a konfigurált kiszolgálótulajdonságokat, és gondoskodik arról, hogy a célkiszolgáló ugyanazokkal a tulajdonságokkal konfigurálható, kivéve a régiót és az erőforráscsoportot, mint a forráskiszolgáló. Ez a cikk nem ismerteti az olyan társított erőforrások áthelyezését, amelyek ugyanannak az erőforráscsoportnak lehetnek részei, például az adatforrás, a tárterület és az átjáró erőforrásai. 

Mielőtt átköltöztet egy kiszolgálót egy másik régióba, javasoljuk, hogy hozzon létre egy részletes tervet. Vegye figyelembe az olyan további erőforrásokat, mint az átjárók és a tárterület, amelyek esetleg át is lesznek helyezték. Bármilyen csomag esetén fontos egy vagy több próbaát áthelyezési műveletet végrehajtani tesztkiszolgálók használatával az éles kiszolgálók áthelyezése előtt.

> [!IMPORTANT]
> Az ügyfélalkalmazások és a kapcsolati sztringek a Analysis Services a teljes kiszolgálónévvel csatlakoznak, amely egy olyan URI, amely tartalmazza a kiszolgáló régióját. Például: `asazure://westcentralus.asazure.windows.net/advworks01`. Amikor egy kiszolgálót egy másik régióba költöztet, hatékonyan hoz létre egy új kiszolgálói erőforrást egy másik régióban, amely a kiszolgálónév URI-jának egy másik régiójával fog működni. A parancsfájlok által használt ügyfélalkalmazások és kapcsolati sztringek az új kiszolgálóhoz kell csatlakozniuk az új kiszolgálónév URI-jának használatával. A [Kiszolgálónév alias használatával](analysis-services-server-alias.md) csökkenthető azon helyek száma, ahol a kiszolgálónév URI-ját módosítani kell, de a régió áthelyezése előtt meg kell valósítanunk.

> [!IMPORTANT]
> Az Azure-régiók különböző IP-címtartományokat használnak. Ha tűzfal-kivételek vannak konfigurálva arra a régióra, ahol a kiszolgáló és/vagy a tárfiók található, előfordulhat, hogy egy másik IP-címtartományt kell konfigurálnia. További információ: Gyakori kérdések a hálózati [Analysis Services kapcsolatban.](analysis-services-network-faq.md)

> [!NOTE]
> Ez a cikk az adatbázis biztonsági másolatának célkiszolgálóra való visszaállítását ismerteti a forráskiszolgáló régiójában található tárolóból. Bizonyos esetekben a biztonsági másolatok másik régióból való visszaállítása gyenge teljesítményt eredményez, különösen nagy méretű adatbázisok esetén. Az adatbázis-visszaállítás során a legjobb teljesítmény érdekében hozzon létre egy új tárolót a célkiszolgáló régiójában. Az adatbázis célkiszolgálóra való visszaállítása előtt másolja át az .abf biztonsági mentési fájlokat a forrás-régió tárolóból a cél-régió tárolóba. Bár ez a cikk nem terjed ki erre a cikkre, bizonyos esetekben, különösen a nagyon nagy méretű adatbázisok esetén, az adatbázisok szkriptelhetősége a forráskiszolgálóról, az újralétrehozás, majd a célkiszolgálón az adatbázisadatok betöltése céljából történő feldolgozása költséghatékonyabb lehet, mint a biztonsági mentés/visszaállítás használata.

> [!NOTE]
> Ha helyszíni adatátjárót használ az adatforrások csatlakoztatásához, az átjáró-erőforrást is át kell áthelyeznie a célkiszolgálói régióba. További információ: Helyszíni adatátjáró [telepítése és konfigurálása.](analysis-services-gateway-install.md)

## <a name="prerequisites"></a>Előfeltételek

- **Azure Storage-fiók:** Egy .abf biztonsági mentési fájl tárolásához szükséges.
- **SQL Server Management Studio (SSMS):** A modelladatbázisok biztonsági mentéséhez és visszaállításához szükséges.
- **Azure PowerShell**. Csak akkor szükséges, ha úgy dönt, hogy ezt a feladatot a PowerShell használatával befejezi.

## <a name="prepare"></a>Előkészítés

### <a name="backup-model-databases"></a>Modelladatbázisok biztonsági mentése

Ha **a tárolási beállítások** még nincsenek konfigurálva a forráskiszolgálóhoz, kövesse a [Tárolóbeállítások konfigurálása lépéseit.](analysis-services-backup.md#configure-storage-settings)

A tárolási beállítások konfigurálásakor kövesse a [Biztonsági](analysis-services-backup.md#backup) mentés lépéseit egy modelladatbázis .abf biztonsági mentésének létrehozásához a storage-tárolóban. Később vissza kell állítania az .abf biztonsági mentést az új célkiszolgálóra.


### <a name="export-template"></a>Sablon exportálása

A sablon a forráskiszolgáló konfigurációs tulajdonságait tartalmazza.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Sablon exportálása az Azure Portallal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza **a Minden erőforrás** lehetőséget, majd válassza ki Analysis Services kiszolgálót.

3. Válassza > **Beállítások**  >  **exportálása sablon lehetőséget.**

4. A **Sablon exportálása** **panelen** válassza a Letöltés lehetőséget.

5. Keresse meg a portálról letöltött .zip fájlt, majd csomagolja ki egy mappába.

   A zip-fájl tartalmazza az új kiszolgáló üzembe helyezéséhez szükséges sablont és paramétereket tartalmazó .json-fájlokat.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sablon exportálása a PowerShell használatával:

1. Jelentkezzen be az Azure-előfizetésbe a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal, és kövesse a képernyőn megjelenő utasításokat:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Ha az identitása több előfizetéshez is társítva van, akkor állítsa be az aktív előfizetést az áthelyezni kívánt kiszolgálói erőforrás előfizetésére.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportálja a forráskiszolgáló sablonját. Ezek a parancsok fájlnévként mentik a ResourceGroupName nevű JSON-sablont az aktuális könyvtárba.

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

### <a name="get-storage-shared-access-signature-sas"></a>Tároló közös hozzáférésű jogosultságának (SAS) lekért használata

Amikor célkiszolgálót helyez üzembe sablonból, egy felhasználódelegált SAS-jogkivonatra (URI-ként) van szükség az adatbázis biztonsági másolatát tartalmazó tároló megadásához.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Közös hozzáférésű jogosultság aláírása a portál használatával:

1. A portálon válassza ki a kiszolgálóadatbázis biztonsági mentéséhez használt tárfiókot.

2. Válassza **Storage Explorer** lehetőséget, majd bontsa ki a **BLOBTÁROLÓK et.** 

3. Kattintson a jobb gombbal a Storage-tárolóra, majd válassza a **Közös hozzáférésű jogosultság jogosultságának leírása lehetőséget.**

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="SAS beszerzése":::

4. A **Közös hozzáférésű jogosultság jogosultsága beállításban** válassza a **Létrehozás lehetőséget.** Alapértelmezés szerint az SAS 24 óra múlva lejár.

5. Másolja és mentse az **URI-t.** 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Közös hozzáférésű jogosultság jogosultsága PowerShell használatával való lekért létrehozásához kövesse a Felhasználódelegálás SAS létrehozása tárolóhoz vagy [blobhoz a PowerShell használatával lépéseit.](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)

---

### <a name="modify-the-template"></a>A sablon módosítása

Egy szövegszerkesztővel módosíthatja az exportált template.js, és módosíthatja a régió és a blobtároló tulajdonságait. 

A sablon módosítása:

1. Egy szövegszerkesztőben a **location** tulajdonságban adja meg az új célterületet. A **backupBlobContainerUri tulajdonságba** illessze be a tároló URI-ját SAS-kulccsal. 

    Az alábbi példa az advworks1 kiszolgáló célterületét a következőre állítja be, és meghatározza a storage-tároló URI-ját közös hozzáférésű `South Central US` jogosultságaláírással: 

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

Az Azure-régiókat lásd: [Azure-helyek.](https://azure.microsoft.com/global-infrastructure/locations/) A régiók PowerShell-rel való lekért felhasználásával futtassa a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancsot.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Áthelyezés

Egy új kiszolgálói erőforrás másik régióban való üzembe  helyezéséhez az előző szakaszokban exportált és módosítotttemplate.jsaz erőforráscsoportot kell használnia.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A portálon válassza az **Erőforrás létrehozása lehetőséget.**

2. A **Keresés a Marketplace-en mezőbe írja** be a sablon üzembe **helyezését,** majd nyomja le az ENTER **billentyűt.**

3. Válassza a **Template deployment** lehetőséget.

4. Válassza a **Létrehozás** lehetőséget.

5. Válassza **a Build your own template (Saját sablon létrehozása a szerkesztőben) lehetőséget.**

6. Válassza **a Fájl betöltése** lehetőséget, majd  kövesse az utasításokat aztemplate.jsexportált és módosított fájlba való betöltéséhez.

7. Ellenőrizze, hogy a sablonszerkesztőben az új célkiszolgáló megfelelő tulajdonságai megjelenik-e.

8. Kattintson a **Mentés** gombra.

9. Adja meg vagy válassza ki a tulajdonságértékeket:

    - **Előfizetés**: Válassza ki az Azure-előfizetést.
    
    - **Erőforráscsoport:** Válassza az **Új létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét. Kiválaszthat egy meglévő erőforráscsoportot, ha még nem tartalmaz Analysis Services azonos nevű erőforrás-kiszolgálót.
    
    - **Hely:** Válassza ki a sablonban megadott régiót.

10. Válassza **az Áttekintés és létrehozás lehetőséget.**

11. Tekintse át a feltételeket és az alapvető beállításokat, majd válassza a **Létrehozás lehetőséget.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A sablon üzembe helyezéséhez használja az alábbi parancsokat:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>A célkiszolgáló URI-jának lekérte

Ahhoz, hogy csatlakozzon az új célkiszolgálóhoz az SSMS-ről a modelladatbázis visszaállításához, be kell szereznie az új célkiszolgáló URI-ját.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A kiszolgálói URI lekért száma a portálon:

1. A portálon ugrás az új célkiszolgálói erőforrásra.

2. Az Áttekintés **lapon** másolja ki a **Kiszolgálónév** URI-ját.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A kiszolgáló URI-jának PowerShell használatával való lekértéhez használja a következő parancsokat:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Másolja **ki a ServerFullName nevet** a kimenetből.

---

### <a name="restore-model-database"></a>Modelladatbázis visszaállítása

A modelladatbázis .abf biztonsági mentésének az új célkiszolgálóra való visszaállításához kövesse a Visszaállítással leírt lépéseket. [](analysis-services-backup.md#restore)

Nem kötelező: A modelladatbázis visszaállítása után feldolgozható a modell és a táblák az adatforrásból származó adatok frissítéséhez. A modell és a tábla feldolgozása az SSMS használatával:

1. Az SSMS-ban kattintson a jobb gombbal a folyamatadatbázis > **adatbázisra.**

2. **Bontsa ki a Táblák** gombra, majd kattintson a jobb gombbal egy táblára. A **Folyamattábla(ok) mezőben válassza** ki az összes táblát, majd kattintson az **OK gombra.**

## <a name="verify"></a>Ellenőrzés

1. A portálon ugrás az új célkiszolgálóra.

2. Az Áttekintés lap Modellek a Analysis Services **oldalán** ellenőrizze, hogy a visszaállított modellek megjelennek-e.

3. Használjon ügyfélalkalmazást, például Power BI Excelt a modellhez való csatlakozáshoz az új kiszolgálón. Ellenőrizze, hogy megjelennek-e modellobjektumok, például táblák, intézkedések, hierarchiák. 

4. Automatizálási szkriptek futtatása. Ellenőrizze, hogy sikeresen le voltak-e hajtva.

Nem kötelező: [Az ALM Toolkit](http://alm-toolkit.com/) egy nyílt forráskódú eszköz a Power BI  adatkészletek és Analysis Services modelladatbázisok összehasonlítására és kezelésére.  Az eszközkészlettel csatlakozhat a forrás- és a célkiszolgáló adatbázisához, és összehasonlíthatja őket. Ha az adatbázis migrálása sikeres, a modellobjektumok is ugyanazt a definíciót fogják tartalmazni. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután ellenőrizte, hogy az ügyfélalkalmazások tudnak-e csatlakozni az új kiszolgálóhoz, és hogy az automatizálási szkriptek megfelelően futnak-e, törölje a forráskiszolgálót. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

A forráskiszolgáló törlése a portálról:

A forráskiszolgáló Áttekintés **lapján** válassza a **Törlés lehetőséget.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A forráskiszolgáló PowerShell használatával való törléséhez használja a Remove-AzAnalysisServicesServer parancsot.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> A régió áthelyezése után ajánlott, hogy az új célkiszolgáló ugyanabban a régióban használjon tárolót a biztonsági másolatok készítéséhez, nem pedig a forráskiszolgáló régiójában található tárolót.
