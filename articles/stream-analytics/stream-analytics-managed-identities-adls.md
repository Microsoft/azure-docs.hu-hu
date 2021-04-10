---
title: Azure Stream Analytics hitelesítése Azure Data Lake Storage Gen1
description: Ez a cikk azt ismerteti, hogyan használhatók a felügyelt identitások a Azure Stream Analytics-feladatok hitelesítéséhez Azure Data Lake Storage Gen1 kimenetre.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18
ms.openlocfilehash: d5b406d260b1c6518d9227c51d84ab7eb3754329
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598150"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Stream Analytics hitelesítése Azure Data Lake Storage Gen1 felügyelt identitások használatával

Azure Stream Analytics támogatja a felügyelt identitások hitelesítését Azure Data Lake Storage (ADLS) Gen1 kimenettel. Az identitás egy Azure Active Directoryban regisztrált felügyelt alkalmazás, amely egy adott Stream Analytics feladatnak felel meg, és használható a megcélozható erőforráshoz való hitelesítésre. A felügyelt identitások megszüntetik a felhasználó-alapú hitelesítési módszerek korlátozásait, például a jelszó-változtatások vagy a felhasználói jogkivonatok lejárata miatti újrahitelesítést, amelyek 90 naponta történnek. Emellett a felügyelt identitások segítenek a Stream Analytics feladatok automatizálásában, amelyek Azure Data Lake Storage Gen1nak a kimenetet.

Ebből a cikkből megtudhatja, hogyan engedélyezheti a felügyelt identitást egy olyan Azure Stream Analytics feladatokhoz, amely az Azure Portalon, Azure Resource Manager sablonon üzemelő példányon és a Visual studióhoz készült Azure Stream Analytics eszközökön keresztül Azure Data Lake Storage Gen1 kimenetet eredményez

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Első lépésként hozzon létre egy új Stream Analytics feladatot, vagy nyisson meg egy meglévő feladatot a Azure Portalban. A képernyő bal oldalán található menüsávban válassza a **configure (Konfigurálás**) alatt található **felügyelt identitás** elemet.

   ![Stream Analytics felügyelt identitás konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Válassza a **rendszerhez rendelt felügyelt identitás használata** lehetőséget a jobb oldalon megjelenő ablakban. Kattintson a **Mentés** egy egyszerű szolgáltatásra lehetőségre a Azure Active Directory stream Analytics-feladatainak identitásához. Az újonnan létrehozott identitás életciklusát az Azure fogja felügyelni. Ha a Stream Analytics feladatot törli, a társított identitást (azaz az egyszerű szolgáltatásnevet) az Azure automatikusan törli.

   A konfiguráció mentésekor a szolgáltatásnév objektumazonosító (OID) az elsődleges AZONOSÍTÓként jelenik meg az alábbi ábrán látható módon:

   ![Stream Analytics egyszerű szolgáltatás azonosítója](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Az egyszerű szolgáltatásnév neve megegyezik a Stream Analytics feladatokkal. Ha például a feladatainak neve **MyASAJob**, a létrehozott egyszerű szolgáltatásnév neve is **MyASAJob**.

3. A ADLS Gen1 kimeneti fogadó kimeneti tulajdonságok ablakában kattintson a hitelesítési mód legördülő listára, és válassza a **felügyelt identitás** elemet.

4. Adja meg a többi tulajdonságot. Ha többet szeretne megtudni a ADLS-kimenet létrehozásáról, olvassa el a következő témakört: [create a Network Store a stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Amikor végzett, kattintson a **Mentés** gombra.

   ![Azure Data Lake Storage konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navigáljon a ADLS Gen1 áttekintés lapjára, és kattintson az **adatkezelő elemre.**

   ![Data Lake Storage konfigurálása – áttekintés](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Az adatkezelő ablaktáblán válassza a **hozzáférés** elemet, majd kattintson a **Hozzáadás** elemre a hozzáférés ablaktáblán.

   ![Data Lake Storage hozzáférés konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. A **felhasználó vagy csoport kiválasztása** ablaktáblán a szövegmezőbe írja be az egyszerű szolgáltatásnév nevét. Ne feledje, hogy az egyszerű szolgáltatásnév neve szintén a megfelelő Stream Analytics feladatoknak a neve. Ahogy elkezdi a egyszerű név beírását, az a szövegmező alatt fog megjelenni. Válassza ki a kívánt egyszerű szolgáltatásnév nevét, és kattintson a **kiválasztás** elemre.

   ![Egyszerű szolgáltatásnév kiválasztása](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. Az **engedélyek** ablaktáblán tekintse át az **írási** és **végrehajtási** engedélyeket, és rendelje hozzá **ehhez a mappához és az összes gyermekhez**. Ezután kattintson **az OK gombra**.

   ![Írási és végrehajtási engedélyek kiválasztása](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Az egyszerű szolgáltatásnév a **hozzáférés** ablaktáblán az alább látható módon szerepel a **hozzárendelt engedélyek** területen. Most visszatérhet, és elindíthatja a Stream Analytics feladatot.

   ![Stream Analytics hozzáférési lista a portálon](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   További információ a Data Lake Storage Gen1 fájlrendszer engedélyeiről: [Access Control a Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>A Visual studióhoz készült Stream Analytics eszközök

1. JobConfig.jsbekapcsolva beállításnál állítsa a **rendszer által hozzárendelt identitás használata** **igaz** értékre.

   ![Stream Analytics feladatok konfigurációjának felügyelt identitások](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. A ADLS Gen1 kimeneti fogadó kimeneti tulajdonságok ablakában kattintson a hitelesítési mód legördülő listára, és válassza a **felügyelt identitás** elemet.

   ![ADLS kimeneti felügyelt identitások](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Töltse ki a többi tulajdonságot, és kattintson a **Mentés** gombra.

4. Kattintson a **Küldés az Azure** -ba lehetőségre a lekérdezés-szerkesztőben.

   A feladatok elküldésekor az eszközök két dolgot tesznek:

   * A automatikusan létrehoz egy szolgáltatásnevet a Stream Analytics-feladatoknak a Azure Active Directoryban való identitásához. Az újonnan létrehozott identitás életciklusát az Azure fogja felügyelni. Ha a Stream Analytics feladatot törli, a társított identitást (azaz az egyszerű szolgáltatásnevet) az Azure automatikusan törli.

   * Az **írási** és **végrehajtási** engedélyek automatikus beállítása a feladatokhoz használt ADLS Gen1 előtag elérési útjához, és az összes gyermekhez rendelhető hozzá.

5. A Resource Manager-sablonokat a következő tulajdonsággal hozhatja létre [Stream Analytics CI.CD NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0-es vagy újabb verziójának használatával egy Build gépen (a Visual studión kívül). A következő szakaszban a Resource Manager-sablon telepítési lépéseit követve szerezze be a szolgáltatásnevet, és adjon hozzáférést a szolgáltatáshoz a PowerShell használatával.

## <a name="resource-manager-template-deployment"></a>Resource Manager-sablon üzembe helyezése

1. A felügyelt identitással rendelkező *Microsoft. StreamAnalytics/streamingjobs-* erőforrások létrehozásához a Resource Manager-sablon Resource (erőforrás) szakaszában a következő tulajdonságot is használhatja:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ez a tulajdonság azt jelzi, Azure Resource Manager a Azure Stream Analytics feladatokhoz tartozó identitás létrehozása és kezelése.

   **Példa feladatokra**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Példa a feladattípusra**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Jegyezze fel a válasz résztvevői AZONOSÍTÓját, hogy hozzáférést biztosítson a szükséges ADLS-erőforráshoz.

   A **bérlő azonosítója** annak az Azure Active Directory bérlőnek az azonosítója, ahol a szolgáltatásnevet létrehozták. Az egyszerű szolgáltatásnév az előfizetés által megbízhatónak ítélt Azure-bérlőn jön létre.

   A **típus** a felügyelt identitás típusát jelöli a felügyelt identitások típusaiban leírtak szerint. Csak a rendszer által hozzárendelt típus támogatott.

2. Hozzáférés biztosítása az egyszerű szolgáltatáshoz a PowerShell használatával. Ha hozzáférést szeretne biztosítani az egyszerű szolgáltatáshoz a PowerShell használatával, hajtsa végre a következő parancsot:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   A **PrincipalId** az egyszerű szolgáltatásnév azonosítója, és a portál képernyőjén jelenik meg a szolgáltatásnév létrehozása után. Ha a feladatot egy Resource Manager-sablon használatával hozta létre, az objektumazonosító a feladattípus Identity (identitás) tulajdonságában szerepel.

   **Példa**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   A fenti PowerShell-paranccsal kapcsolatos további tudnivalókért tekintse meg a [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) dokumentációt.

## <a name="remove-managed-identity"></a>Felügyelt identitás eltávolítása

A Stream Analytics feladatokhoz létrehozott felügyelt identitást csak akkor törli a rendszer, ha a feladatot törlik. A felügyelt identitást nem lehet törölni a feladatok törlése nélkül. Ha már nem szeretné használni a felügyelt identitást, módosíthatja a kimenet hitelesítési módszerét. A felügyelt identitás továbbra is érvényben marad, amíg el nem törli a feladatot, és a rendszer akkor fogja használni, ha a felügyelt identitások hitelesítését újra használta.

## <a name="limitations"></a>Korlátozások
Ez a funkció nem támogatja a következőket:

1. **Több-bérlős hozzáférés**: az adott stream Analytics feladathoz létrehozott egyszerű szolgáltatásnév azon a Azure Active Directory-bérlőn található, amelyen a feladatot létrehozták, és nem használható olyan erőforráson, amely egy másik Azure Active Directory bérlőn található. Ezért csak az MSI-t használhatja a ADLS 1. generációs erőforrásokhoz, amelyek ugyanabban a Azure Active Directory-bérlőn belül vannak, mint a Azure Stream Analytics feladatokkal. 

2. **[Felhasználóhoz rendelt identitás](../active-directory/managed-identities-azure-resources/overview.md)**: nem támogatott. Ez azt jelenti, hogy a felhasználó nem tudja megadni a saját egyszerű szolgáltatásnevet, amelyet a Stream Analytics feladataihoz használhatnak. Az egyszerű szolgáltatásnevet a Azure Stream Analytics hozza létre.

## <a name="next-steps"></a>Következő lépések

* [A stream Analytics kimenetének létrehozása a adattó-tárolóban](../data-lake-store/data-lake-store-stream-analytics.md)
* [A Visual Studióval helyileg tesztelheti Stream Analytics lekérdezéseit](stream-analytics-vs-tools-local-run.md)
* [Élő adatellenőrzés helyi tesztelése a Visual studióhoz készült Azure Stream Analytics Tools használatával](stream-analytics-live-data-local-testing.md) 
