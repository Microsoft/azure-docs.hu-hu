---
title: Oktatóanyag a Azure Data Box rendeléséhez | Microsoft Docs
description: Ebben az oktatóanyagban megismerheti a Azure Data Box, egy hibrid megoldást, amely lehetővé teszi a helyszíni információk importálását az Azure-ba, valamint a Azure Data Box sorrendjét.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 281b22db692087f2876b4011563fee8c56bd476e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522393"
---
# <a name="tutorial-order-azure-data-box"></a>Oktatóanyag: Az Azure Data Box megrendelése

Az Azure Data Box egy hibrid megoldás, amellyel gyorsan, könnyen és megbízhatóan importálhat helyszíni adatokat az Azure-ba. Az adatok átvitele egy Microsoft által biztosított 80 – TB (felhasználható kapacitás) tárolóeszközre, majd az eszköz visszaszállítása. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag leírja, hogyan rendelheti meg az Azure Data Box szolgáltatást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:  

> [!div class="checklist"]
>
> * A Data Box üzembe helyezésének előfeltételei
> * A Data Box megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/portal)

Az eszköz telepítése előtt végezze el a következő konfigurálási előfeltételeket az Data Box szolgáltatáshoz és eszközhöz:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

Jelentkezzen be az Azure-ba, és futtassa az Azure CLI-parancsokat kétféleképpen:

* Telepítheti a CLI-t, és helyileg is futtathatja a CLI-parancsokat.
* A CLI-parancsokat a Azure Cloud Shell Azure Portal belül is futtathatja.

Az oktatóanyaghoz az Azure CLI-t használjuk a Windows PowerShellen keresztül, de bármelyik lehetőséget szabadon választhat.

### <a name="for-azure-cli"></a>Azure CLI esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

#### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

* Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -verziót 2.0.67 vagy újabb verzióra. Másik megoldásként az MSI-t is [telepítheti](https://aka.ms/installazurecliwindows).

**Bejelentkezés az Azure-ba**

Nyisson meg egy Windows PowerShell-parancssorablakot, és jelentkezzen be az Azure-ba az az [login](/cli/azure/reference-index#az-login) paranccsal:

```azurecli
PS C:\Windows> az login
```

A sikeres bejelentkezés kimenete:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**A Azure Data Box CLI bővítmény telepítése**

A Azure Data Box CLI-parancsok használata előtt telepítenie kell a bővítményt. Az Azure CLI-bővítmények hozzáférést biztosítanak azokhoz a kísérleti és kiadás előtti parancsokhoz, amelyek az alap CLI-vel még nincsenek szállítva. További információ a bővítményekről: [Bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).

A Azure Data Box-bővítmény telepítéséhez futtassa a következő `az extension add --name databox` parancsot:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Ha a bővítmény telepítése sikeresen megtörtént, a következő kimenet jelenik meg:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure által üzemeltetett interaktív rendszerhéj-környezetet [Azure Cloud Shell](https://shell.azure.com/)használhatja a BÖNGÉSZŐBEN a CLI-parancsok futtatásához. Azure Cloud Shell támogatja a bash vagy a Windows PowerShellt az Azure-szolgáltatásokkal. Az Azure CLI előre telepítve és konfigurálva van a fiókjával való használatra. Kattintson a Cloud Shell gombra a Azure Portal jobb felső részén található menüben:

![Cloud Shell menü kijelölése](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

A gomb egy interaktív felületet indít el, amelyet a jelen útmutatóban ismertetett lépések futtatására használhat.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Azure PowerShell

Mielőtt elkezdené, győződjön meg róla, hogy:

* Telepítse a Windows PowerShell 6.2.4 vagy újabb verzióját.
* Telepítse a Azure PowerShell (AZ) modult.
* Telepítse a Azure Data Box (az. DataBox) modult.
* Jelentkezzen be az Azure-ba.

#### <a name="install-azure-powershell-and-modules-locally"></a>Azure PowerShell és modulok helyi telepítése

**A Windows PowerShell telepítése vagy frissítése**

A Windows PowerShell 6.2.4 vagy újabb verziójára lesz szükség. Ha szeretné megtudni, hogy a PowerShell melyik verzióját telepítette, futtassa a következőt: `$PSVersionTable` .

A következő kimenet jelenik meg:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Ha a verzió alacsonyabb, mint a 6.2.4, frissítenie kell a Windows PowerShell verzióját. A Windows PowerShell legújabb verziójának telepítéséhez tekintse meg a következőt: [install Azure PowerShell](/powershell/scripting/install/installing-powershell).

**Azure PowerShell és Data Box modulok telepítése**

A Azure Data Box megrendeléséhez telepítenie kell a Azure PowerShell modulokat a Azure PowerShell használatához. A Azure PowerShell modulok telepítése:

1. Telepítse a [Azure PowerShell az modult](/powershell/azure/new-azureps-module-az).
2. Ezután telepítse az az. DataBox parancsot a parancs használatával `Install-Module -Name Az.DataBox` .

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyisson meg egy Windows PowerShell-parancssorablakot, és jelentkezzen be az Azure-ba a [Kapcsolódás-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) paranccsal:

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

A sikeres bejelentkezés kimenete:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Az Azure-ba a Windows PowerShell használatával történő bejelentkezéssel kapcsolatos részletes információkért lásd: [bejelentkezés Azure PowerShellsal](/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>A Data Box megrendelése

# <a name="portal"></a>[Portál](#tab/portal)

Az eszköz megrendeléséhez hajtsa végre a következő lépéseket a Azure Portal.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).
2. Válassza az **+ Erőforrás létrehozása** lehetőséget, és keressen rá az *Azure Data Box* kifejezésre. Válassza az **Azure Data Box** lehetőséget.

   ![Képernyőkép az új szakaszról Azure Data Box a keresőmezőbe](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép az Azure Data Box szakasz létrehozási lehetőségével](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg vagy válassza ki a következő információkat, majd válassza az **Alkalmaz** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Átvitel típusa     | Válassza az **Importálás az Azure-ba** lehetőséget.        |
    |Előfizetés     | Válasszon egy EA-, CSP- vagy Azure Sponsorship-előfizetést a Data Box szolgáltatáshoz. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Erőforráscsoport | Válasszon ki egy meglévő erőforráscsoportot. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. |
    |Forrásország/-régió    |    Válassza ki azt az országot vagy régiót, ahol az adatok jelenleg találhatók.         |
    |Azure-beli célrégió     |     Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat. <br> További információt a [regionális elérhetőséget](data-box-overview.md#region-availability) tárgyaló témakörben talál.            |

    [![Azure Data Box importálási sorrend ](media/data-box-deploy-ordered/select-data-box-import-04-b.png) elindítása](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. Válassza a **Data Box** lehetőséget. Egy megrendelés maximális felhasználható kapacitása 80 TB. Nagyobb mennyiségű adat esetén több rendelést is létrehozhat.

    ![Elérhető adatméretek: Data Box Disk, 40 terabájt; Data Box, 100 terabájt; Data Box Heavy, 1000 terabájt; Saját lemezek küldése, 1 terabájt](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. A **sorrendben** válassza az **alapok** lapot. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Tovább: adatok célhelye>** elemet.

    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés      | Az előfizetés automatikusan kitöltődik a korábbi kiválasztás alapján.|
    |Erőforráscsoport    | A korábban kiválasztott erőforráscsoport. |
    |Importálási rendelés neve | Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.    |

    ![Data Box importálási sorrend varázsló, alapbeállítások képernyő, a megfelelő információkkal kitöltve](media/data-box-deploy-ordered/select-data-box-import-06.png)

7. Az **adatcélhely** képernyőn válassza ki a Storage **-** fiókokat vagy a felügyelt lemezeket.

    Ha a Storage- **fiók (ok)** tároló célhelyként használja, a következő képernyő jelenik meg:

    ![Data Box importálási sorrend varázsló, az adatcélhely képernyő, és a Storage-fiókok kijelölve](media/data-box-deploy-ordered/select-data-box-import-07.png)

    A megadott Azure-régió alapján válasszon ki egy vagy több Storage-fiókot a meglévő Storage-fiókok szűrt listájából. A Data Box legfeljebb 10 tárfiókkal köthető össze. Létrehozhat egy új **Általános célú v1**, **Általános célú v2** fiókot vagy egy **Blob Storage-fiókot** is.

   > [!NOTE]
   > - Ha az Azure Premium FileStorage-fiókokat választja, a Storage-fiók megosztásán a kiépített kvóta a fájlmegosztás számára átmásolt adatméretre nő. A kvóta növelése után a rendszer nem módosítja újra, például ha valamilyen oknál fogva a Data Box nem tudja másolni az adatait.
   > - Ez a kvóta a számlázáshoz használatos. Miután feltöltötte az adatait az adatközpontba, módosítania kell a kvótát, hogy megfeleljen az igényeinek. További információ: a [számlázás ismertetése](../../articles/storage/files/understanding-billing.md).

    A virtuális hálózattal rendelkező tárfiókok támogatottak. Ahhoz, hogy a Data Box szolgáltatás működjön a biztonságos tárfiókok esetében, engedélyezze a megbízható szolgáltatásokat a tárfiók hálózati tűzfalának beállításai között. További információ: [Azure Data Box hozzáadása megbízható szolgáltatásként](../storage/common/storage-network-security.md#exceptions).

    Ha Data Box használatával **felügyelt lemezeket** hoz létre a helyszíni virtuális merevlemezekről (VHD-k), akkor a következő információkat is meg kell adnia:

    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforráscsoportok     | Hozzon létre új erőforráscsoportokat, ha felügyelt lemezeket szeretne létrehozni helyszíni VHD-kből. Meglévő erőforráscsoportot csak akkor használhat, ha az erőforráscsoport korábban lett létrehozva, amikor a Data Box szolgáltatás a felügyelt lemezek Data Box sorrendjét hozta létre. <br> Adjon meg több erőforráscsoportot, pontosvesszővel elválasztva. Legfeljebb 10 erőforráscsoport használata támogatott.|

    ![Data Box importálási sorrend varázsló, az adatcél képernyő, és a Managed Disks kiválasztva](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    A felügyelt lemezekhez megadott tárfiókot előkészítési tárfiókként használja a rendszer. A Data Box szolgáltatás lapblobként tölti fel a VHD-ket az előkészítési tárfiókba, majd felügyelt lemezekké konvertálja, és áthelyezi az erőforráscsoportba. További információ: [Az Azure-ba történő adatfeltöltés ellenőrzése](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

   > [!NOTE]
   > Ha egy oldal blobját nem sikerült felügyelt lemezre átalakítani, a Storage-fiókban marad, és a tárterületért kell fizetnie.

8. Kattintson a **Tovább gombra: a biztonság** lehetőségre.

    A **biztonsági** képernyő segítségével saját titkosítási kulcsot és saját eszközt használhat, és megoszthatja a jelszavakat, és választhatja a kettős titkosítás használatát.

    A **biztonsági** képernyőn megjelenő összes beállítás nem kötelező. Ha nem módosítja a beállításokat, az alapértelmezett beállítások lesznek érvényesek.

    ![A Data Box importálási sorrend varázsló biztonsági képernyője](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. Ha a saját ügyfél által felügyelt kulcsát szeretné használni az új erőforrás feloldási hitelesítő adatainak megvédéséhez, bontsa ki a **titkosítási típust**.

    Az ügyfél által felügyelt kulcs konfigurálása a Azure Data Box esetében nem kötelező. Alapértelmezés szerint a Data Box a Microsoft által felügyelt kulcs használatával védi a zárolási kulcsot.

    Az ügyfél által felügyelt kulcs nem befolyásolja, hogy az eszközön lévő adatforgalom hogyan legyen titkosítva. A kulcs csak az eszköz feloldási hitelesítő kulcsának titkosítására szolgál.

    Ha nem szeretne ügyfél által felügyelt kulcsot használni, ugorjon a 15. lépésre.

   ![A titkosítási típus beállításait megjelenítő biztonsági képernyő](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. A kulcs típusaként válassza az **ügyfél által felügyelt kulcs** lehetőséget. Ezután válassza ki **a Key Vault és a kulcs kiválasztása** lehetőséget.
   
    ![Biztonsági képernyő, ügyfél által felügyelt kulcs beállításai](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. Az előfizetést a Azure Key Vault panelen a **kulcs kiválasztásával** automatikusan kitölti a rendszer.

    - A **Key Vault** esetében választhat egy meglévő kulcstartót a legördülő listából.

      ![Kulcs kiválasztása Azure Key Vault képernyőn](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - Új kulcstartó létrehozásához az **új létrehozása** lehetőséget is választhatja. A **Key Vault létrehozása** képernyőn adja meg az erőforráscsoportot és a kulcstároló nevét. Győződjön meg arról, hogy a **Soft delete** és a **Purge Protection** engedélyezve van. Fogadja el az összes többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.

      ![Új Azure Key Vault beállítások létrehozása](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      Tekintse át a kulcstartó adatait, majd válassza a **Létrehozás** lehetőséget. Várjon pár percet, amíg a Key Vault létrehozása befejeződik.

      ![Új Azure Key Vault felülvizsgálati képernyő](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. A **Azure Key Vault kulcs kiválasztása** lehetőségnél kiválaszthat egy meglévő kulcsot a kulcstartóban.

    ![Meglévő kulcs kiválasztása Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    Ha új kulcsot szeretne létrehozni, válassza az **új létrehozása** lehetőséget. RSA-kulcsot kell használnia. A méret 2048 vagy nagyobb lehet. Adja meg az új kulcs nevét, fogadja el a többi alapértelmezett értéket, majd válassza a **Létrehozás** lehetőséget.

      ![Új kulcs létrehozása lehetőség](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      Értesítést kap, ha a kulcs létrejött a kulcstartóban.

13. Válassza ki a használni kívánt kulcs **verzióját** , majd válassza a **kiválasztás** lehetőséget.

      ![A Key vaultban létrehozott új kulcs](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    Ha új kulcs-verziót szeretne létrehozni, válassza az **új létrehozása** lehetőséget.

    ![Nyisson meg egy párbeszédpanelt új kulcs verziójának létrehozásához](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    Válassza ki az új kulcs verziójának beállításait, majd válassza a **Létrehozás** lehetőséget.

    ![Új kulcs verziójának létrehozása](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    A **Biztonság** képernyőn látható **titkosítási típus** beállításai megmutatják a kulcstartót és a kulcsot.

    ![Ügyfél által felügyelt kulcs és kulcstartó](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. Válassza ki azt a felhasználói identitást, amelyet az erőforráshoz való hozzáférés kezeléséhez fog használni. Válassza **a felhasználói azonosító kiválasztása** lehetőséget. A jobb oldali panelen válassza ki az előfizetést és a használni kívánt felügyelt identitást. Ezután kattintson a **Kiválasztás** gombra.

    A felhasználó által hozzárendelt felügyelt identitás egy önálló Azure-erőforrás, amely több erőforrás kezelésére is használható. További információ: [felügyelt identitások típusai](../active-directory/managed-identities-azure-resources/overview.md).  

    Ha új felügyelt identitást kell létrehoznia, kövesse a [szerepkör létrehozása, listázása, törlése vagy hozzárendelése egy felhasználóhoz rendelt felügyelt identitáshoz a Azure Portal használatával című témakör](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)útmutatását.
    
    ![Felhasználói azonosító kiválasztása](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    A felhasználói identitás a **titkosítási típus** beállításainál jelenik meg.

    ![A titkosítási típus beállításaiban megjelenő kiválasztott felhasználói azonosító](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Ha nem szeretné, hogy a rendszer által generált jelszavakat Azure Data Box alapértelmezetten használja, bontsa ki a **saját jelszó** használata a **biztonsági** képernyőn lehetőséget.

    A rendszer által létrehozott jelszavak biztonságosak, és csak akkor ajánlott, ha a szervezet más nem igényel.

    ![A saját jelszó beállításainak kibontása Data Box importálási sorrendhez](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Ha saját jelszavát szeretné használni az új eszközhöz, az **eszköz jelszavának beállításával** válassza a **saját jelszó használata** lehetőséget, és írjon be egy jelszót, amely megfelel a biztonsági követelményeknek.
     
     A jelszónak alfanumerikusnak kell lennie, és 12 – 15 karakterből kell állnia, és legalább egy nagybetűt, egy kisbetűt, egy speciális karaktert és egy számot kell tartalmaznia. 

     - Engedélyezett speciális karakterek: @ #-$% ^! + = ; : _ ( )
     - Nem megengedett karakterek: I i o 0
   
     ![A saját eszköz jelszavának használatának lehetőségei a Data Box importálási sorrendjének biztonsági képernyőjén](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - Saját jelszavak használata megosztásokhoz:

   1. A **jelszavak megosztása** beállításnál válassza a **saját jelszavak használata** lehetőséget, majd **válassza a megosztások jelszavait**.
     
       ![A saját megosztási jelszavainak használatára vonatkozó beállítások a Data Box importálási sorrendjének biztonsági képernyőjén](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    1. A sorrendben adja meg az egyes Storage-fiókok jelszavát. A jelszót a rendszer a Storage-fiók összes megosztásán használni fogja.
    
       A jelszónak alfanumerikusnak kell lennie, és 12 – 64 karakterből kell állnia, és legalább egy nagybetűt, egy kisbetűs betűt, egy speciális karaktert és egy számot kell tartalmaznia.

       - Engedélyezett speciális karakterek: @ #-$% ^! + = ; : _ ( )
       - Nem megengedett karakterek: I i o 0
     
    1. Ha az összes Storage-fiókhoz ugyanazt a jelszót szeretné használni, válassza a **Másolás az összesbe** lehetőséget. 

    1. Ha végzett, válassza a **Mentés** lehetőséget.
     
       ![A megosztási jelszavak megadására szolgáló képernyő a Data Box importálási sorrendjéhez](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

    A **Biztonság** képernyőn **megtekintheti vagy módosíthatja a jelszavakat** a jelszavak módosításához.

16. Ha a **biztonságban** engedélyezni szeretné a szoftveres kettős titkosítást, bontsa ki a **kettős titkosítás (a biztonságos környezetek esetében)** elemet, és válassza **a rendeléshez a dupla titkosítás engedélyezése** lehetőséget.

    ![Biztonsági képernyő Data Box importáláshoz, szoftveres titkosítás engedélyezése Data Box-rendeléshez](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    A szoftveres titkosítás a Data Box lévő adatok AES-256 bites titkosításán felül történik.

    > [!NOTE]
    > Ha engedélyezi ezt a beállítást, megteheti, hogy a rendelés feldolgozása és az Adatmásolás tovább tart. Ez a beállítás a megrendelés létrehozása után nem módosítható.

    A folytatáshoz válassza a **tovább lehetőséget: kapcsolattartási adatok** .

17. A **kapcsolattartási adatok** területen válassza a **+ szállítási címek hozzáadása** elemet.

    ![A kapcsolattartási adatok képernyőn adja meg a szállítási címeket a Azure Data Box importálási sorrendjéhez](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. A **Szállítási cím** területen adja meg a vezeték- és utónevét, a vállalata nevét és postai címét, valamint egy érvényes telefonszámot. Válassza a **Cím ellenőrzése** lehetőséget. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről.

    ![Képernyőkép a szállítási címek hozzáadása párbeszédpanelről, amelyen a szállítás lehetőség és a szállítási címek hozzáadása lehetőség látható.](media/data-box-deploy-ordered/select-data-box-import-10.png)

    Ha az önfelügyelt szállítást választotta, a megrendelés sikeres elhelyezése után e-mailben értesítést fog kapni. Az önfelügyelt szállítással kapcsolatos további információkért lásd: [saját üzemeltetésű szállítás használata](data-box-portal-customer-managed-shipping.md).

19. Válassza a **szállítási címek hozzáadása** lehetőséget a szállítási adatok sikeres ellenőrzése után. Vissza fog térni a **kapcsolattartási adatok** lapra.

20. Miután visszatért a **kapcsolattartási adatokhoz**, adjon hozzá egy vagy több e-mail-címet. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

    ![E-mail szakasz a kapcsolattartási adatokhoz a megrendelés varázslóban](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. Tekintse át a **felülvizsgálati** és a megrendeléssel kapcsolatos információkat a rendeléssel, kapcsolatfelvételsel, értesítéssel és adatvédelmi feltételekkel kapcsolatban. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

22. Válassza a **Megrendelés** lehetőséget. A megrendelés létrehozása néhány percet vesz igénybe.

    ![A megrendelés varázsló áttekintése és sorrendje képernyője](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi lépéseket követve rendeljen egy eszközt az Azure CLI használatával:

1. Írja le a Data Box sorrendjének beállításait. A beállítások közé tartozik a személyes/üzleti adatok, az előfizetés neve, az eszköz adatai és a szállítási információk. Ezeket a beállításokat paraméterekként kell használni, amikor a CLI-parancs futtatásával hozza létre a Data Box sorrendet. A következő táblázat a paraméterek beállításait tartalmazza `az databox job create` :

   | Beállítás (paraméter) | Description |  Mintaérték |
   |---|---|---|
   |resource-group| Használjon egy már létezőt, vagy hozzon létre újat. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |name| A létrehozandó rendelés neve. | "mydataboxorder"|
   |Kapcsolattartó neve| A szállítási címtől társított név. | "Gus Lengyelország"|
   |telefon| Annak a személynek vagy vállalatnak a telefonszáma, aki megkapja a rendelést.| "14255551234"
   |location| A legközelebbi Azure-régió, amely az eszköz szállítására kerül.| "USA nyugati régiója"|
   |SKU| A megrendeléshez megadott Data Box eszköz. Az érvényes értékek a következők: "DataBox", "DataBoxDisk" és "DataBoxHeavy"| "DataBox" |
   |e-mail-lista| A rendeléshez társított e-mail-címek.| "gusp@contoso.com" |
   |utca – cím 1| Az utca címe, ahová a rendelés szállítása történik. | "15700 NE 39. St" |
   |utca – Cím2| A másodlagos címek adatai, például az apartman száma vagy az épület száma. | "Építési 123" |
   |city| Az a város, ahová az eszközt el fogja szállítani. | Redmond |
   |állam vagy tartomány| Az az állapot, amelybe az eszközt szállítják.| WA |
   |ország| Az az ország, amelyet az eszköz el fog szállítani. | "Egyesült Államok" |
   |postai irányítószám| A szállítási címnek megfelelő irányítószám vagy postai irányítószám.| "98052"|
   |Vállalat neve| Annak a vállalatnak a neve, amelyhez dolgozni szeretne.| "Contoso, LTD" |
   |tárfiók| Az az Azure Storage-fiók, ahonnan importálni kívánja az adatait.| mystorageaccount|
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba  | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

2. A parancssorban válassza a választás vagy a terminál lehetőséget, futtassa az [Az adatmező-feladatok létrehozása](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-create) parancsot a Azure Data Box rendelés létrehozásához.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Íme egy példa a parancs használatára:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   A parancs futtatásának kimenete:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Az Azure CLI összes parancsa alapértelmezés szerint a JSON-t fogja használni kimeneti formátumként, hacsak nem módosítja. A kimeneti formátum a globális paraméter használatával módosítható `--output <output-format>` . A formátum a "Table" értékre való módosítása javítja a kimenet olvashatóságát.

   Az alábbi parancs ugyanezt a parancsot futtatta egy kis csípéssel a formázás módosításához:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   A parancs futtatásának kimenete:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Az eszköz megrendeléséhez hajtsa végre az alábbi lépéseket Azure PowerShell használatával:

1. Az importálási sorrend létrehozása előtt be kell szereznie a Storage-fiókot, és mentenie kell a Storage-fiók objektumot egy változóban.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Írja le a Data Box sorrendjének beállításait. A beállítások közé tartozik a személyes/üzleti adatok, az előfizetés neve, az eszköz adatai és a szállítási információk. Ezeket a beállításokat paraméterekként kell használni, amikor futtatja a PowerShell-parancsot a Data Box sorrend létrehozásához. A következő táblázat a [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob)használt paraméterek beállításait mutatja be.

    | Beállítás (paraméter) | Description |  Mintaérték |
    |---|---|---|
    |ResourceGroupName [kötelező]| Meglévő erőforráscsoport használata. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
    |Név [kötelező]| A létrehozandó rendelés neve. | "mydataboxorder"|
    |ContactName [kötelező]| A szállítási címtől társított név. | "Gus Lengyelország"|
    |Telefonszám [kötelező]| Annak a személynek vagy vállalatnak a telefonszáma, aki megkapja a rendelést.| "14255551234"
    |Hely [kötelező]| A legközelebbi Azure-régió, amely az eszköz szállítására kerül.| WestUS|
    |DataBoxType [kötelező]| A megrendeléshez megadott Data Box eszköz. Az érvényes értékek a következők: "DataBox", "DataBoxDisk" és "DataBoxHeavy"| "DataBox" |
    |EmailId [kötelező]| A rendeléshez társított e-mail-címek.| "gusp@contoso.com" |
    |StreetAddress1 [kötelező]| Az utca címe, ahová a rendelés szállítása történik. | "15700 NE 39. St" |
    |StreetAddress2| A másodlagos címek adatai, például az apartman száma vagy az épület száma. | "Építési 123" |
    |StreetAddress3| A harmadlagos címen lévő információk. | |
    |Város [kötelező]| Az a város, ahová az eszközt el fogja szállítani. | Redmond |
    |StateOrProvinceCode [kötelező]| Az az állapot, amelybe az eszközt szállítják.| WA |
    |Országhívószám [kötelező]| Az az ország, amelyet az eszköz el fog szállítani. | "Egyesült Államok" |
    |Irányítószám [kötelező]| A szállítási címnek megfelelő irányítószám vagy postai irányítószám.| "98052"|
    |CompanyName| Annak a vállalatnak a neve, amelyhez dolgozni szeretne.| "Contoso, LTD" |
    |StorageAccountResourceId [kötelező]| Az Azure Storage-fiók azonosítója, amelyből az adatok importálására kerül.| <AzStorageAccount>. azonosító |

3. A parancssorban válassza a választás vagy a terminál lehetőséget, a [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) használatával hozza létre a Azure Data Box sorrendjét.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   A parancs futtatásának kimenete:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>A megrendelés nyomon követése

# <a name="portal"></a>[Portál](#tab/portal)

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Lépjen a Data Box sorrendbe, majd az állapot megtekintéséhez lépjen az **Áttekintés** gombra. A portálon a megrendelés **Megrendelve** állapotban látható.

Ha nem áll rendelkezésre eszköz, értesítést fog kapni. Ha van elérhető eszköz, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a csomagot. Az eszköz előkészítése során a következő műveletek lesznek végrehajtva:

* SMB-megosztások létrehozása az eszközzel társított mindegyik tárfiókhoz.
* Hozzáférési hitelesítő adatok (felhasználónév és jelszó) kiosztása mindegyik megosztáshoz.
* Az eszköz zárolásának feloldásához szükséges jelszó létrehozása.
* A Data Box zárolásának célja, hogy megakadályozza az eszközhöz való illetéktelen hozzáférést a teljes eljárás során.

Az eszköz előkészítésének befejeztével a portálon a megrendelés **Feldolgozott** állapotban jelenik meg.

![A feldolgozott Data Box sorrend](media/data-box-overview/data-box-order-status-processed.png)

A Microsoft ezután előkészíti, majd feladja a csomagot egy regionális fuvarozónál. Az eszköz feladását követően Ön megkapja a fuvarlevélszámot. A portál a **Feladva** állapotot mutatja.

![Az elküldött Data Box sorrend](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Egyetlen megrendelés nyomon követése

Egy meglévő Azure Data Box-rendelés nyomkövetési információinak lekéréséhez futtassa a parancsot [`az databox job show`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-show) . A parancs információkat jelenít meg a rendeléssel kapcsolatban, például: név, Erőforráscsoport, követési információ, előfizetés-azonosító, kapcsolattartási adatok, szállítási típus és eszköz SKU.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `az databox job show` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforrás-csoport [kötelező]| A rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |név [kötelező]| A megjelenítendő sorrend neve. | "mydataboxorder"|
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

   Íme egy példa a parancsra, amelynek kimeneti formátuma a "Table" (táblázat) értékre van állítva:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   A parancs futtatásának kimenete:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> A lista sorrendje támogatott az előfizetés szintjén, és az erőforráscsoport egy opcionális paraméter (nem pedig egy szükséges paraméter).

### <a name="list-all-orders"></a>Az összes megrendelés listázása

Ha több eszközt is megrendelt, a futtatásával [`az databox job list`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-list) megtekintheti az összes Azure Data Box megrendelését. A parancs felsorolja az adott erőforráscsoporthoz tartozó összes rendelést. Emellett a kimenet: megrendelés neve, a szállítási állapot, az Azure-régió, a kézbesítés típusa és a rendelés állapota is látható. A megszakított megrendelések is szerepelnek a listában.
A parancs az egyes sorrendek időbélyegeit is megjeleníti.

```azurecli
az databox job list --resource-group <resource-group>
```

A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `az databox job list` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforrás-csoport [kötelező]| A rendeléseket tartalmazó erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

   Íme egy példa a parancsra, amelynek kimeneti formátuma a "Table" (táblázat) értékre van állítva:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   A parancs futtatásának kimenete:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Egyetlen megrendelés nyomon követése

Egy meglévő Azure Data Box-rendelés nyomkövetési információinak lekéréséhez futtassa a [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob)parancsot. A parancs információkat jelenít meg a rendeléssel kapcsolatban, például: név, Erőforráscsoport, követési információ, előfizetés-azonosító, kapcsolattartási adatok, szállítási típus és eszköz SKU.

> [!NOTE]
> `Get-AzDataBoxJob` egy és több megrendelés megjelenítésére szolgál. A különbség az, hogy megadja a rendelés nevét az egyes rendelésekhez.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `Get-AzDataBoxJob` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |ResourceGroup [kötelező]| A rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |Név [kötelező]| Annak a rendelésnek a neve, amelyre vonatkozóan információt szeretne kapni. | "mydataboxorder"|
   |ResourceId| A rendeléshez társított erőforrás azonosítója. |  |

   Íme egy példa a kimenettel rendelkező parancsra:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   A parancs futtatásának kimenete:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Az összes megrendelés listázása

Ha több eszközt is megrendelt, a futtatásával [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) megtekintheti az összes Azure Data Box megrendelését. A parancs felsorolja az adott erőforráscsoporthoz tartozó összes rendelést. Emellett a kimenet: megrendelés neve, a szállítási állapot, az Azure-régió, a kézbesítés típusa és a rendelés állapota is látható. A megszakított megrendelések is szerepelnek a listában.
A parancs az egyes sorrendek időbélyegeit is megjeleníti.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Íme egy példa a parancsra:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

A parancs futtatásának kimenete:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>A rendelés lemondása

# <a name="portal"></a>[Portál](#tab/portal)

A megrendelés megszakításához a Azure Portal lépjen az **Áttekintés** elemre, és válassza a **Mégse** lehetőséget a parancssáv alatt.

A megrendelést annak feladását követően bármikor megszakíthatja, mielőtt az Feldolgozott állapotba lép.

Egy megszakított megrendelés törléséhez lépjen az **Áttekintés** elemre, és válassza a **Törlés** elemet a parancssorból.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Rendelés visszavonása

Azure Data Box rendelés megszakításához futtassa a parancsot [`az databox job cancel`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-cancel) . Meg kell adnia a megrendelés megszakításának okát.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `az databox job cancel` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforrás-csoport [kötelező]| A törlendő rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |név [kötelező]| A törlendő megrendelés neve. | "mydataboxorder"|
   |ok [kötelező]| A megrendelés megszakításának oka. | "Hibás adatokat adtam meg, és a megrendelés megszakításához szükséges." |
   |igen| Ne kérjen megerősítést. | – Igen (-y)| – igen – y |
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

   Íme egy példa a kimenettel rendelkező parancsra:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   A parancs futtatásának kimenete:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Megrendelés törlése

Ha megszakította Azure Data Box rendelést, a futtatásával [`az databox job delete`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-delete) törölheti a sorrendet.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `az databox job delete` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforrás-csoport [kötelező]| A törlendő rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |név [kötelező]| A törlendő megrendelés neve. | "mydataboxorder"|
   |előfizetést| Az Azure-előfizetés neve vagy azonosítója (GUID). | "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" |
   |igen| Ne kérjen megerősítést. | – Igen (-y)| – igen – y |
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

Íme egy példa a kimenettel rendelkező parancsra:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   A parancs futtatásának kimenete:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Rendelés visszavonása

Azure Data Box rendelés megszakításához futtassa a [stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob)parancsot. Meg kell adnia a megrendelés megszakításának okát.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `Stop-AzDataBoxJob` :

| Paraméter | Leírás |  Mintaérték |
|---|---|---|
|ResourceGroup [kötelező]| A megszakítani kívánt rendeléshez tartozó erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
|Név [kötelező]| A törlendő megrendelés neve. | "mydataboxorder"|
|Ok [kötelező]| A megrendelés megszakításának oka. | "Hibás adatokat adtam meg, és a megrendelés megszakításához szükséges." |
|Force | Azt kényszeríti, hogy a parancsmag felhasználói megerősítés nélkül fusson. | -Force |

Íme egy példa a kimenettel rendelkező parancsra:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

A parancs futtatásának kimenete:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Megrendelés törlése

Ha megszakította Azure Data Box rendelést, a futtatásával [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) törölheti a sorrendet.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `Remove-AzDataBoxJob` :

| Paraméter | Leírás |  Mintaérték |
|---|---|---|
|ResourceGroup [kötelező]| A törlendő rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
|Név [kötelező]| A törlendő megrendelés neve. | "mydataboxorder"|
|Force | Azt kényszeríti, hogy a parancsmag felhasználói megerősítés nélkül fusson. | -Force |

Íme egy példa a kimenettel rendelkező parancsra:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

A parancs futtatásának kimenete:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a Azure Data Box cikkeket, például a következőket:

> [!div class="checklist"]
>
> * A Data Box üzembe helyezésének előfeltételei
> * A Data Box megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

A következő oktatóanyag a Data Box beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box beállítása](./data-box-deploy-set-up.md)