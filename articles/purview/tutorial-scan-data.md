---
title: 'Oktatóanyag: az adatvizsgálat az Azure hatáskörébe (előzetes verzió)'
description: Ebben az oktatóanyagban egy alapszintű készletet futtat egy adatkészlet beállításához, majd az adatok adatforrásokból való vizsgálatát az Azure hatáskörébe-katalógusba.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: a88c837c986db346bcb4fbb1b15ceab6a304347d
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806223"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Oktatóanyag: az adatvizsgálat az Azure hatáskörébe (előzetes verzió)

> [!IMPORTANT]
> Az Azure-beli hatáskörébe jelenleg előzetes verzió érhető el. A [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak azok a további jogi feltételek, amelyek az Azure olyan szolgáltatásaira vonatkoznak, amelyek béta-, előzetes verziójú vagy egyéb módon még nem lettek nyilvánosan elérhetők.

Ebben az *egyrészes oktatóanyag-sorozatban* megismerheti, hogyan kezelheti az adatkezelést az adatközpontok között az Azure hatáskörébe (előzetes verzió). Az oktatóanyag ezen részében létrehozott adatbirtokot a rendszer a sorozat többi részéhez használja.

Az oktatóanyag-sorozat 1. részében a következőket fogja elsajátítani:

> [!div class="checklist"]
>
> * Hozzon létre egy adatkészletet különböző Azure-adaterőforrásokkal.
> * Az adatvizsgálat katalógusba.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a feladatok megkezdése előtt.
* Egy [Azure-beli hatáskörébe tartozó fiók](create-catalog-portal.md).
* Az adatközpont üzembe helyezésére szolgáló [alapszintű csomag](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) .

> [!NOTE]
> Az alapszintű csomag csak Windows rendszeren érhető el.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-data-estate"></a>Adattárház létrehozása

Ebben a szakaszban egy szimulált adatközpont létrehozásához futtatja a Starter Kit-parancsfájlokat. Az adatkészletek a vállalat tulajdonában lévő összes adattal kapcsolatos portfóliók. A Starter Kit parancsfájl a következő műveleteket hajtja végre:

* Létrehoz egy Azure Blob Storage-fiókot, és feltölti a fiókot az adatokkal.
* Létrehoz egy Azure Data Lake Storage Gen2 fiókot.
* Létrehoz egy Azure Data Factory példányt, és hozzárendeli a példányt az Azure hatáskörébe.
* Beállítja és elindítja a másolási tevékenység folyamatát az Azure Blob Storage és a Azure Data Lake Storage Gen2 fiókok között.
* Leküldi a társított vonalát Azure Data Factoryról az Azure hatáskörébe.

### <a name="prepare-to-run-the-starter-kit"></a>Felkészülés az alapszintű csomag futtatására

Az alábbi lépéseket követve állíthatja be a Starter Kit ügyfélszoftverét a Windows rendszerű gépen:

1. [Töltse le a Starter Kit csomagot](https://download.microsoft.com/download/9/7/9/979db3b1-0916-4997-a7fb-24e3d8f83174/PurviewStarterKitV4.zip), és bontsa ki annak tartalmát a kívánt helyre.


1. A számítógépen írja be a **PowerShell** kifejezést a Windows tálcán található keresőmezőbe. A keresés listában kattintson a jobb gombbal a **Windows PowerShell** elemre, majd válassza a **Futtatás rendszergazdaként** parancsot.

1. A PowerShell ablakában adja meg a következő parancsot, és cserélje le a `<path-to-starter-kit>` kibontott Starter Kit-fájlok mappájának elérési útjára.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Az Azure-parancsmagok telepítéséhez adja meg a következő parancsot.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Ha a figyelmeztetés megjelenik, a *folytatáshoz a NuGet-szolgáltató szükséges*, adja meg az **Y** értéket, majd nyomja le az ENTER billentyűt.

1. Ha megjelenik a figyelmeztetés, nem *megbízható tárház*, írja be **a** értéket, majd nyomja le az ENTER billentyűt.

Akár egy percet is igénybe vehet, amíg a PowerShell telepíti a szükséges modulokat.

### <a name="collect-data-needed-to-run-the-scripts"></a>A parancsfájlok futtatásához szükséges adatok gyűjtése

Mielőtt futtatná a PowerShell-szkripteket a katalógus elindításához, szerezze be a következő argumentumok értékeit a parancsfájlokban való használathoz:

* TenantID
   1. Az [Azure Portalon](https://portal.azure.com) válassza az **Azure Active Directory** lehetőséget.
   1. A bal oldali navigációs ablaktábla **kezelés** szakaszában válassza a **Tulajdonságok** lehetőséget. Ezután válassza ki a **bérlői azonosító** másolási ikonját, hogy mentse az értéket a vágólapra. Illessze be az értéket egy szövegszerkesztőben későbbi használatra.

* SubscriptionID
   1. A Azure Portal keresse meg és válassza ki az előfeltételként létrehozott Azure-beli hatáskörébe tartozó példány nevét.
   1. Válassza az **Áttekintés** szakaszt, és mentse a GUID **azonosítót az előfizetés-azonosítóhoz**.

   > [!NOTE]
   > - Győződjön meg arról, hogy ugyanazt az előfizetést használja, mint amelyikben az Azure-beli hatáskörébe tartozó fiókot létrehozta. Ez ugyanaz az előfizetés, amelyet az engedélyezési listán helyeztek el.
   > - Az alapszintű csomag futtatása után az Azure-beli törzsben néha hiányzik a Lineage. Ennek az az oka, hogy a Starter Kit által létrehozott Data Factory a hatáskörébe tartoznak a jogosultságok. A [**dokumentum hivatkozásának**](how-to-link-azure-data-factory.md#view-existing-data-factory-connections)  kiválasztásával győződjön meg arról, hogy a Data Factory megfelelően van konfigurálva, és megfelelő szerepkör van hozzárendelve a hatáskörébe


* Catalogname tulajdonságot: annak az Azure-beli hatáskörébe tartozó fióknak a neve, amelyet az [Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című részében hozott létre.

* CatalogResourceGroupName: az az erőforráscsoport neve, amelyben létrehozta az Azure-beli hatáskörébe tartozó fiókot.

### <a name="verify-permissions"></a>Engedélyek ellenőrzése

A következő lépésekkel adhatja hozzá a parancsfájlt futtató felhasználót az előfeltételként létrehozott Azure hatáskörébe tartozó fiókhoz. A felhasználóknak a *hatáskörébe* tartozó adatkezelő és a *hatáskörébe tartozó adatforrások rendszergazdai* szerepköreire van szükségük. 

Ha saját maga hozta létre az Azure-beli hatáskörébe tartozó fiókot, akkor automatikusan megkapja a hozzáférést, és kihagyhatja ezt a szakaszt.

1. Lépjen a Azure Portal hatáskörébe tartozó fiókok lapra, és válassza ki a módosítani kívánt Azure-beli hatáskörébe tartozó fiókot.

1. A fiók lapján válassza a **hozzáférés-vezérlés (iam)** fület és a **+ Hozzáadás** lehetőséget.

1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.

1. Adja meg a *szerepkörhöz* tartozó **hatáskörébe tartozó Adatkurátori szerepkört** .
 
1. Használja az alapértelmezett értéket a *hozzáféréshez való hozzárendeléshez*. Az alapértelmezett értéknek a **felhasználó, a csoport vagy az egyszerű szolgáltatásnév** kell lennie.

1. Adja meg a parancsfájlt futtató felhasználó nevét a **Select (kiválasztás**) mezőben.

1. Kattintson a **Mentés** gombra.

1. Ismételje meg az előző lépéseket a *szerepkör* a **hatáskörébe tartozó adatforrás-rendszergazdai szerepkör** beállításnál.

További információ: [katalógus engedélyei](catalog-permissions.md).

### <a name="run-the-client-side-setup-scripts"></a>Ügyféloldali telepítési parancsfájlok futtatása

A katalógus-konfiguráció befejezése után futtassa a következő parancsfájlokat a PowerShell-ablakban az eszközök létrehozásához, majd cserélje le a helyőrzőket a korábban összegyűjtött értékekre.

1. A következő parancs használatával navigáljon a Starter Kit könyvtárához. Cserélje le a `path-to-starter-kit` fájlt a kibontott fájl mappájának elérési útjára.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. A következő parancs beállítja a helyi számítógép végrehajtási szabályzatát. Ha a rendszer a végrehajtási házirend módosítására kéri, adja meg az **a** *értéket az Igen értékre* .

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Kapcsolódjon az Azure-hoz az alábbi parancs használatával. Cserélje le a `TenantID` és az `SubscriptionID` helyőrzőket.

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   A parancs futtatásakor megjelenik egy előugró ablak, amely a Azure Active Directory hitelesítő adataival jelentkezhet be.


1. Futtassa az alábbi parancsot a Starter Kit futtatásához. Cserélje le a,,,, `CatalogName` `TenantID` `SubscriptionID` `NewResourceGroupName` és `CatalogResourceGroupName` helyőrzőket. A esetében `NewResourceGroupName` adjon meg egy egyedi nevet (csak kisbetűs alfanumerikus karaktereket tartalmazó) az erőforráscsoport számára, amely az adatbirtokot fogja tartalmazni.

   > [!IMPORTANT]
   > A **newresourcegroupname** csak számokat és kisbetűket használ, és legfeljebb 17 karakterből állhat. **A nagybetűk és a speciális karakterek nem engedélyezettek.** Ez a megkötés a tárolási fiókok elnevezési szabályaiból származik.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

A környezet beállítása akár 10 percet is igénybe vehet. Ebben az időszakban a különböző előugró ablakok jelenhetnek meg, amelyeket figyelmen kívül hagyhat. Ne zárjuk be a **BlobDataCreator.exe** ablakot; a befejezés után automatikusan bezáródik.

Amikor megjelenik az üzenet `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` , várjon, amíg a **BlobDataCreator.exe** egy másik példánya elindul, és fejezze be a futtatást.

> [!IMPORTANT]
> Ha a "aktív feladatok száma" leáll, akkor kilép a blob Creator ablakból, és megnyomhatja a ENTER billentyűt a PowerShell-ablakban.

A folyamat befejezése után létrejön egy erőforráscsoport a megadott névvel. Az Azure Data Factory, az Azure Blob Storage és a Azure Data Lake Storage Gen2 fiókok mind szerepelnek ebben az erőforráscsoportban. Az erőforráscsoport a megadott előfizetésben található.

## <a name="scan-data-into-the-catalog"></a>Adatgyűjtés a katalógusba

A vizsgálat olyan folyamat, amellyel a katalógus közvetlenül kapcsolódik egy adott felhasználó által megadott időforráshoz. A katalógus a vállalat adatkészletét a vizsgálat, a Lineage, a portál és az API segítségével mutatja be. A célok között megvizsgálhatja, hogy mi a belső, a sémák kibontása és a szemantika megismerése. Ebben a szakaszban a Starter Kit-vel létrehozott adatforrások vizsgálatát állíthatja be.

Az alapszintű csomag parancsfájlja, amellyel létrehozott két adatforrást, az Azure Blob Storage-t és a Azure Data Lake Storage Gen2. Ezeket az adatforrásokat egyszerre is beszkennelheti a katalógusba.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Hitelesítés a tárolóban felügyelt identitással

A fiók létrehozásakor a rendszer automatikusan létrehozza az Azure-beli hatáskörébe tartozó fiókkal azonos nevű felügyelt identitást. Az adatai beolvasása előtt meg kell adnia az Azure hatáskörébe tartozó szerepkör-engedélyeket a Storage-fiókok számára.

1. Navigáljon a Starter Kit által létrehozott erőforráscsoporthoz, és válassza ki a blob Storage-fiókját.

1. Válassza a bal oldali navigációs menü **Access Control (iam)** elemét. Ezután válassza a **+ Hozzáadás** lehetőséget.

1. Állítsa be a szerepkört a **blob-Adatolvasó tárolásához** , és adja meg az Azure hatáskörébe tartozó fiók nevét a **Select** értékhez. Ezt követően válassza a **Mentés** lehetőséget, hogy ezt a szerepkör-hozzárendelést a hatáskörébe tartozó fiókjához adja.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Szerepkör-hozzárendelés hozzáadása":::

1. Ismételje meg a Azure Data Lake Storage Gen2 előző lépéseit.

### <a name="scan-your-data-sources"></a>Adatforrások vizsgálata

1. Nyissa meg az Azure-beli hatáskörébe tartozó erőforrást a [Azure Portalon](https://portal.azure.com) , és válassza a *hatáskörébe Studio megnyitása* lehetőséget. A rendszer automatikusan átveszi a hatáskörébe Studio kezdőlapját.

1. A katalógus weboldalán válassza a **források** lehetőséget, majd válassza a **regisztráció** lehetőséget. Ezután válassza az **Azure Blob Storage** és a **Folytatás** lehetőséget.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="BLOB Storage-erőforrás regisztrálása":::

1. A **források regisztrálása** lapon adjon meg egy **nevet**. Válassza ki annak az Azure Blob Storage-fióknak a **nevét** , amelyet korábban az alapszintű csomaggal hozott létre. A fiók neve `<YourResourceGroupName>adcblob` . Válassza a **Befejezés** gombot.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Az Azure Blob Storage-adatforrások regisztrálásának beállításait bemutató képernyőkép." border="true":::

1. Az **adatforrások** leképezése nézetben válassza az **új vizsgálat** lehetőséget az Azure Blob Storage csempén.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Képernyőfelvétel egy adatforrásból származó vizsgálat kiválasztásáról." border="true":::

1. A **vizsgálat** lapon adja meg a vizsgálat nevét, válassza ki a felügyelt identitást a **hitelesítő adatok** legördülő listából, majd **folytassa a művelettel**.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="BLOB Storage vizsgálata az Azure hatáskörébe":::

1. Az egyes blobokra szűkítheti a vizsgálat hatókörét. Ebben az oktatóanyagban minden olyan eszközt megvizsgálunk, amely minden ellenőrzést végez, és **folytatja** a keresést.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="A tárterület vizsgálatának hatóköre":::

1. Válassza ki az alapértelmezett ellenőrzési szabályt, és **folytassa** a beállítást.

1. Beolvasási triggert is beállíthat ismétlődő vizsgálatokhoz. Ebben az oktatóanyagban válassza a **egyszer** és a **Folytatás** lehetőséget.

1. A vizsgálat befejezéséhez válassza a **Mentés és Futtatás** lehetőséget.

1. Ismételje meg az előző lépéseket a Azure Data Lake Storage Gen2-fiók vizsgálatához.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
>
> * A Starter Kit parancsfájl futtatásával állítson be egy adatközpont-környezetet.
> * Az Azure-beli hatáskörébe tartozó katalógusba történő adatvizsgálat.

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan keresheti meg a kezdőlapot, és hogyan kereshet meg egy eszközt.

> [!div class="nextstepaction"]
> [Navigáljon a kezdőlapra, és keressen rá az objektumra](tutorial-asset-search.md)
