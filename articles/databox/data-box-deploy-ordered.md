---
title: Útmutató a megrendeléshez Azure Data Box | Microsoft Docs
description: Ebben az oktatóanyagban megismeri a Azure Data Box hibrid megoldást, amely lehetővé teszi a helyszíni adatok Importálását az Azure-ba, valamint a Azure Data Box.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: ed539df9fec3c66b3881257ff1ce68d03cf4c382
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878595"
---
# <a name="tutorial-order-azure-data-box"></a>Oktatóanyag: Az Azure Data Box megrendelése

Az Azure Data Box egy hibrid megoldás, amellyel gyorsan, könnyen és megbízhatóan importálhat helyszíni adatokat az Azure-ba. Az adatokat egy, a Microsoft által biztosított 80 TB-os (használható kapacitású) tárolóeszközre továbbítja, majd továbbítja az eszközt. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag leírja, hogyan rendelheti meg az Azure Data Box szolgáltatást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:  

> [!div class="checklist"]
>
> * A Data Box üzembe helyezésének előfeltételei
> * A Data Box megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/portal)

Az eszköz üzembe helyezése előtt Data Box a következő konfigurációs előfeltételeket a szolgáltatás és az eszköz számára:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

Az Azure-ba való bejelentkezés és az Azure CLI-parancsok futtatása kétféleképpen lehetséges:

* Telepítheti a CLI-t, és helyileg futtathatja a CLI-parancsokat.
* A parancssori felület parancsai a Azure Portal a Azure Cloud Shell.

Az oktatóanyaghoz Windows PowerShell Azure CLI-t használjuk, de bármelyik lehetőséget választhatja.

### <a name="for-azure-cli"></a>Az Azure CLI-hez

Mielőtt hozzákezd, győződjön meg az alábbiakról:

#### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

* Telepítse [az Azure CLI](/cli/azure/install-azure-cli) 2.0.67-es vagy újabb verzióját. Másik lehetőségként telepítheti az [MSI használatával is.](https://aka.ms/installazurecliwindows)

**Bejelentkezés az Azure-ba**

Nyisson meg egy Windows PowerShell, és jelentkezzen be az Azure-ba [az az login paranccsal:](/cli/azure/reference-index#az_login)

```azurecli
PS C:\Windows> az login
```

A sikeres bejelentkezés kimenete a következő:

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

**A Azure Data Box CLI-bővítmény telepítése**

A parancssori felület Azure Data Box használata előtt telepítenie kell a bővítményt. Az Azure CLI-bővítmények hozzáférést biztosítanak azokhoz a kísérleti és kiadás előtti parancsokhoz, amelyek az alap CLI-vel még nincsenek szállítva. További információ a bővítményekről: [Bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).

A bővítmény telepítéséhez futtassa Azure Data Box következő `az extension add --name databox` parancsot:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Ha a bővítmény telepítése sikeresen megtörtént, a következő kimenetet fogja látni:

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

A [cli Azure Cloud Shell](https://shell.azure.com/)futtatásához használhatja az Azure-ban üzemeltetett interaktív felületi környezetet a böngészőben. Azure Cloud Shell a Bash vagy Windows PowerShell Azure-szolgáltatásokkal. Az Azure CLI előre telepítve van, és a fiókjával való használatra van konfigurálva. Válassza Cloud Shell menü Jobb felső részén található Menü Azure Portal:

![Cloud Shell menü kiválasztása](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

A gomb elindít egy interaktív rendszerhéjat, amely az ebben az útmutatóban ismertetett lépések futtatására használható.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>A Azure PowerShell

Mielőtt hozzákezd, győződjön meg arról, hogy:

* Telepítse Windows PowerShell 6.2.4-es vagy újabb verzióra.
* Telepítse Azure PowerShell (AZ) modult.
* Telepítse Azure Data Box (Az.DataBox) modult.
* Jelentkezzen be az Azure-ba.

#### <a name="install-azure-powershell-and-modules-locally"></a>A Azure PowerShell és modulok helyi telepítése

**Telepítési vagy frissítési Windows PowerShell**

Telepítenie kell Windows PowerShell 6.2.4-es vagy újabb verzióját. A powershell telepített verziójának kiderítére futtassa a következőt: `$PSVersionTable` .

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

Ha a 6.2.4-esnél korábbi verziójú, frissítenie kell a Windows PowerShell. Az alkalmazás legújabb verziójának telepítéséhez Windows PowerShell Install Azure PowerShell ( [Telepítés)](/powershell/scripting/install/installing-powershell)Azure PowerShell.

**A Azure PowerShell és Data Box telepítése**

A modulokat a Azure PowerShell telepítéséhez Azure PowerShell kell Azure Data Box. A modul Azure PowerShell telepítése:

1. Telepítse az [Azure PowerShell Az modult.](/powershell/azure/new-azureps-module-az)
2. Ezután telepítse az Az.DataBoxot a `Install-Module -Name Az.DataBox` paranccsal.

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyisson meg egy Windows PowerShell, és jelentkezzen be az [Azure-ba a Connect-AzAccount paranccsal:](/powershell/module/az.accounts/Connect-AzAccount)

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

A sikeres bejelentkezés kimenete a következő:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Részletes információk az Azure-ba a Windows PowerShell való bejelentkezésről: Bejelentkezés az [Azure PowerShell.](/powershell/azure/authenticate-azureps)

---

## <a name="order-data-box"></a>A Data Box megrendelése

# <a name="portal"></a>[Portál](#tab/portal)

Az eszköz megrendeléséhez Azure Portal alábbi lépéseket kell követnie.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).
2. Válassza az **+ Erőforrás létrehozása** lehetőséget, és keressen rá az *Azure Data Box* kifejezésre. Válassza az **Azure Data Box** lehetőséget.

   ![Képernyőkép a keresőmezőben Azure Data Box új szakaszról](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép Azure Data Box szakaszról, a Létrehozás lehetőséggel](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg vagy válassza ki a következő információkat, majd válassza az **Alkalmaz** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Átvitel típusa     | Válassza az **Importálás az Azure-ba** lehetőséget.        |
    |Előfizetés     | Válasszon egy EA-, CSP- vagy Azure Sponsorship-előfizetést a Data Box szolgáltatáshoz. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Erőforráscsoport | Válasszon ki egy meglévő erőforráscsoportot. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. |
    |Forrásország/-régió    |    Válassza ki azt az országot vagy régiót, ahol az adatok jelenleg találhatók.         |
    |Azure-beli célrégió     |     Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat. <br> További információt a [regionális elérhetőséget](data-box-overview.md#region-availability) tárgyaló témakörben talál.            |

    [![Importálási Azure Data Box indítása ](media/data-box-deploy-ordered/select-data-box-import-04-b.png)](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. Válassza a **Data Box** lehetőséget. Egy rendelés maximális használható kapacitása 80 TB. Nagyobb mennyiségű adat esetén több rendelést is létrehozhat.

    ![Elérhető adatméretek: Data Box Disk, 40 terabájt; Data Box, 100 terabájt; Data Box Heavy, 1000 terabájt; Saját lemezek küldése (1 terabájt)](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. Az **Order (Megrendelés)** lapon válassza az **Basics (Alapvető beállítások)** lapot. Adja meg vagy válassza ki a következő adatokat, majd **válassza a Tovább: Adatcél>.**

    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés      | Az előfizetés automatikusan ki lesz töltve a korábbi kijelölés alapján.|
    |Erőforráscsoport    | A korábban kiválasztott erőforráscsoport. |
    |Rendelés nevének importálása | Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.    |

    ![Data Box Rendelés importálása varázsló Alapszintű beállítások képernyője, a megfelelő adatokkal](media/data-box-deploy-ordered/select-data-box-import-06.png)

7. Az **Adatcél képernyőn** válassza az **Adatcél** – tárfiókok vagy felügyelt lemezek lehetőséget.

    Ha **tárfiókokat használ tárolási** célként, a következő képernyő jelenik meg:

    ![Data Box Rendelés importálása varázsló, Adatcél képernyő, kijelölt tárfiókokkal](media/data-box-deploy-ordered/select-data-box-import-07.png)

    A megadott Azure-régió alapján válasszon ki egy vagy több tárfiókot a meglévő tárfiókok szűrt listájából. A Data Box legfeljebb 10 tárfiókkal köthető össze. Létrehozhat egy új **Általános célú v1**, **Általános célú v2** fiókot vagy egy **Blob Storage-fiókot** is.

   > [!NOTE]
   > - Ha az Azure Premium FileStorage-fiókokat választja, a tárfiókmegosztásra vonatkozó kvóta a fájlmegosztásba másolt adatok méretére nő. A kvóta a megnövelt kvótát nem módosítja újra, például ha a Data Box nem tudja másolni az adatokat.
   > - A rendszer ezt a kvótát használja a számlázáshoz. Miután feltöltötte az adatokat az adatközpontba, az igényeinek megfelelően módosítsa a kvótát. További információ: [A számlázás ismertetése.](../../articles/storage/files/understanding-billing.md)

    A virtuális hálózattal rendelkező tárfiókok támogatottak. Ahhoz, hogy a Data Box szolgáltatás működjön a biztonságos tárfiókok esetében, engedélyezze a megbízható szolgáltatásokat a tárfiók hálózati tűzfalának beállításai között. További információ: Azure Data Box [hozzáadása megbízható szolgáltatásként.](../storage/common/storage-network-security.md#exceptions)

    Ha felügyelt Data Box a helyszíni virtuális merevlemezről (VHD-kből) hoz létre felügyelt **lemezeket,** a következő információkat is meg kell adnia:

    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforráscsoportok     | Hozzon létre új erőforráscsoportokat, ha felügyelt lemezeket szeretne létrehozni helyszíni VHD-kből. Meglévő erőforráscsoportot csak akkor használhat, ha az erőforráscsoportot korábban, a felügyelt lemezekre vonatkozó Data Box létrehozásakor hozta létre a Data Box szolgáltatás. <br> Adjon meg több erőforráscsoportot, pontosvesszővel elválasztva. Legfeljebb 10 erőforráscsoport használata támogatott.|

    ![Data Box Rendelés importálása varázsló, Adatcél képernyő, az Managed Disks kijelölve](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    A felügyelt lemezekhez megadott tárfiókot előkészítési tárfiókként használja a rendszer. A Data Box szolgáltatás lapblobként tölti fel a VHD-ket az előkészítési tárfiókba, majd felügyelt lemezekké konvertálja, és áthelyezi az erőforráscsoportba. További információ: [Az Azure-ba történő adatfeltöltés ellenőrzése](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

   > [!NOTE]
   > Ha egy lapblob nem konvertálható felügyelt lemezre, az a tárfiókban marad, és a tárolásért díjat kell fizetnie.

8. A folytatáshoz válassza a **Tovább: Biztonság** lehetőséget.

    A **Biztonság** képernyőn használhatja a saját titkosítási kulcsát és a saját eszközét, megoszthatja a jelszavakat, és választhatja a dupla titkosítás használatát.

    A Biztonság képernyőn **az összes** beállítás megadása nem kötelező. Ha nem módosítja a beállításokat, az alapértelmezett beállítások érvényesek lesznek.

    ![A Rendelés importálása varázsló Data Box képernyője](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. Ha saját, ügyfél által felügyelt kulcsot szeretne használni az új erőforrás zárolásának feloldására használt hozzáférési kulcs védelméhez, bontsa ki a **Titkosítási típust.**

    Az ügyfél által felügyelt kulcs konfigurálása a Azure Data Box nem kötelező. Alapértelmezés szerint a Data Box Microsoft által felügyelt kulcsot használ a hozzáférési kulcs feloldásához.

    Az ügyfél által felügyelt kulcs nincs hatással az eszközön található adatok titkosítására. A kulcs csak az eszköz feloldási hozzáférési kulcsának titkosítására használható.

    Ha nem szeretne felhasználó által felügyelt kulcsot használni, ugorjon a 15. lépésre.

   ![A Biztonság képernyő a titkosítási típus beállításaival](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. Válassza **az Ügyfél által kezelt kulcs** lehetőséget a kulcs típusaként. Ezután válassza **a Kulcstartó és kulcs kiválasztása lehetőséget.**
   
    ![Biztonság képernyő, ügyfél által kezelt kulcs beállításai](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. A Kulcs **kiválasztása a Azure Key Vault** panelen az előfizetés automatikusan ki lesz töltve.

    - A **Key Vaulthoz** kiválaszthat egy meglévő kulcstartót a legördülő listából.

      ![Kulcs kiválasztása Azure Key Vault képernyőn](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - Új kulcstartó létrehozásához az **Új** létrehozása lehetőséget is választhatja. A **Kulcstartó létrehozása képernyőn** adja meg az erőforráscsoportot és a kulcstartó nevét. Győződjön meg arról, **hogy a Soft delete** és a **Purge protection engedélyezve** van. Fogadja el az összes többi alapértelmezett beállítást, majd válassza **a Felülvizsgálat + létrehozás lehetőséget.**

      ![Új Azure Key Vault létrehozása](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      Tekintse át a kulcstartó adatait, majd válassza a **Létrehozás lehetőséget.** Várjon néhány percet, amíg a Key Vault létrehozása befejeződik.

      ![Új Azure Key Vault képernyő](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. A **Select key from Azure Key Vault**(Kulcs kiválasztása a kulcstartóból) lehetőségnél kiválaszthat egy meglévő kulcsot a kulcstartóban.

    ![Meglévő kulcs kiválasztása a Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    Ha új kulcsot szeretne létrehozni, válassza az **Új létrehozása lehetőséget.** RSA-kulcsot kell használnia. A méret 2048 vagy nagyobb lehet. Adja meg az új kulcs nevét, fogadja el a többi alapértelmezett értéket, majd válassza a **Létrehozás lehetőséget.**

      ![Új kulcs létrehozása lehetőség](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      Értesítést kap, ha a kulcs létrejött a kulcstartóban.

13. Válassza ki **a használni** kívánt kulcs verzióját, majd válassza a **Kijelölés lehetőséget.**

      ![A Key Vaultban létrehozott új kulcs](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    Ha új kulcsverziót szeretne létrehozni, válassza az **Új létrehozása lehetőséget.**

    ![Új kulcsverzió létrehozásához nyisson meg egy párbeszédpanelt](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    Válassza ki az új kulcsverzió beállításait, majd válassza a **Létrehozás lehetőséget.**

    ![Új kulcsverzió létrehozása](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    A **Biztonság képernyőn** a Titkosítás típusa **beállításnál** megjelenik a kulcstartó és a kulcs.

    ![Kulcs és kulcstartó ügyfél által felügyelt kulcshoz](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. Válasszon ki egy felhasználói identitást, amely az erőforráshoz való hozzáférést kezeli. Válassza **a Felhasználói identitás kiválasztása lehetőséget.** A jobb oldalon található panelen válassza ki a használni kívánt előfizetést és felügyelt identitást. Ezután kattintson a **Kiválasztás** gombra.

    A felhasználó által hozzárendelt felügyelt identitás egy különálló Azure-erőforrás, amely több erőforrás kezelésére is használható. További információ: Felügyelt [identitástípusok.](../active-directory/managed-identities-azure-resources/overview.md)  

    Ha új felügyelt identitást kell létrehoznia, kövesse a Szerepkör [létrehozása, listába](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)való felsorolása, törlése vagy hozzárendelése felhasználó által hozzárendelt felügyelt identitáshoz a következő Azure Portal.
    
    ![Felhasználói identitás kiválasztása](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    A felhasználó identitása megjelenik a **Titkosítási típus beállításai** között.

    ![Egy kiválasztott felhasználói identitás jelenik meg a Titkosítási típus beállításainál](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Ha nem szeretné használni a rendszer által létrehozott jelszavakat, Azure Data Box a Biztonság képernyőn bontsa ki a **Saját** jelszó használata bontsa ki az **adatokat.**

    A rendszer által létrehozott jelszavak biztonságosak, és ajánlottak, hacsak a szervezet másként nem követeli meg.

    ![Kibontva a Saját jelszó használata lehetőség az importálási Data Box érdekében](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Ha saját jelszót használ az új eszközhöz, az Eszköz jelszavának beállítása beállításnál válassza a **Saját** jelszó használata lehetőséget, és írjon be egy olyan jelszót, amely megfelel a biztonsági követelményeknek.
     
     A jelszónak alfanumerikusnak kell lennie, és 12–15 karakter hosszúságúnak kell lennie, és tartalmaznia kell legalább egy nagybetűt, egy kisbetűt, egy speciális karaktert és egy számot. 

     - Engedélyezett speciális karakterek: @ # - $ % ^ ! + = ; : _ ( )
     - Nem engedélyezett karakterek: I i L o O 0
   
     ![Beállítások a saját eszköz jelszavának a Biztonság képernyőn történő Data Box rendeléshez](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - Saját jelszavak használata megosztáshoz:

   1. A **Jelszavak megosztásának beállítása beállításhoz válassza** a **Saját** jelszavak használata, majd **a Megosztások jelszavának kiválasztása lehetőséget.**
     
       ![Saját megosztási jelszavak használatának lehetőségei a Biztonság képernyőn importálási Data Box esetén](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    1. Adja meg az egyes tárfiókok jelszavát a sorrendben. A jelszó a tárfiók összes megosztásán használva lesz.
    
       A jelszónak alfanumerikusnak kell lennie, és 12–64 karakter hosszúságúnak kell lennie, és tartalmaznia kell legalább egy nagybetűt, egy kisbetűt, egy speciális karaktert és egy számot.

       - Engedélyezett speciális karakterek: @ # - $ % ^ ! + = ; : _ ( )
       - Nem engedélyezett karakterek: I i L o O 0
     
    1. Ha ugyanazt a jelszót az összes tárfiókhoz használni, válassza a Másolás **az összesbe lehetőséget.** 

    1. Ha végzett, válassza a **Mentés lehetőséget.**
     
       ![Képernyő megosztási jelszavak bevitelére Data Box rendeléshez](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

    A Biztonság **képernyőn** a Jelszavak megtekintése és **módosítása** segítségével módosíthatja a jelszavakat.

16. A **Biztonság** beállításban, ha engedélyezni szeretné a szoftveralapú dupla titkosítást, bontsa ki a Dupla titkosítás **(rendkívül** biztonságos környezetek esetén) bontsa ki, majd válassza a Dupla titkosítás engedélyezése a **rendeléshez lehetőséget.**

    ![Biztonsági képernyő Data Box importáláshoz, szoftveralapú titkosítás engedélyezése Data Box rendeléshez](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    A szoftveralapú titkosítás az adatok AES-256 bites titkosítása mellett történik a Data Box.

    > [!NOTE]
    > A beállítás engedélyezése tovább tarthat a rendelésfeldolgozással és az adatok másolásával. Ezt a beállítást a rendelés létrehozása után nem módosíthatja.

    A **folytatáshoz válassza a Tovább: Kapcsolattartási adatok** lehetőséget.

17. A **Kapcsolattartási adatok között** válassza a + Szállítási cím hozzáadása **lehetőséget.**

    ![A Kapcsolattartási adatok képernyőn adja hozzá a szállítási címeket a Azure Data Box rendeléshez](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. A **Szállítási cím** területen adja meg a vezeték- és utónevét, a vállalata nevét és postai címét, valamint egy érvényes telefonszámot. Válassza a **Cím ellenőrzése** lehetőséget. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről.

    ![Képernyőkép a Szállítási cím hozzáadása párbeszédpanelről a Ship using beállításokkal és a Szállítási cím hozzáadása lehetőséggel.](media/data-box-deploy-ordered/select-data-box-import-10.png)

    Ha az ön által felügyelt szállítást választotta, a rendelés sikeres elküldése után e-mailes értesítést fog kapni. Az önálló szállítással kapcsolatos további információkért lásd: Saját maga [által felügyelt szállítás használata.](data-box-portal-customer-managed-shipping.md)

19. A **szállítási adatok sikeres ellenőrzése** után válassza a Szállítási cím hozzáadása lehetőséget. Visszatér a Kapcsolattartási **adatok lapra.**

20. Miután visszatért a **Kapcsolattartási adatokhoz,** adjon meg egy vagy több e-mail-címet. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

    ![A Kapcsolattartási adatok e-mail szakasza a Rendelés varázslóban](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. Tekintse át a **megrendeléshez,** kapcsolattartási adatokhoz, értesítésekhez és adatvédelmi feltételekhez kapcsolódó Áttekintés és rendelés adatokat. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

22. Válassza a **Megrendelés** lehetőséget. A megrendelés létrehozása néhány percet vesz igénybe.

    ![A Rendelés varázsló Áttekintés és Rendelés képernyője](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az eszköz megrendeléséhez kövesse az alábbi lépéseket az Azure CLI használatával:

1. Írja le a rendelési Data Box beállításait. Ezek a beállítások tartalmazzák a személyes/üzleti adatait, az előfizetés nevét, az eszközadatokat és a szállítási adatokat. Ezeket a beállításokat paraméterekként kell használnia a parancssori felület parancsának futtatásakor a Data Box létrehozásához. Az alábbi táblázat a paraméterbeállításait mutatja `az databox job create` be:

   | Beállítás (paraméter) | Description |  Mintaérték |
   |---|---|---|
   |resource-group| Használjon egy már létezőt, vagy hozzon létre újat. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | "myresourcegroup"|
   |name| A létrehozott rendelés neve. | "mydataboxorder"|
   |kapcsolattartó neve| A szállítási címhez társított név. | "Fog Fog Foga"|
   |telefon| A rendelést megkapó személy vagy vállalkozás telefonszáma.| "14255551234"
   |location| Az eszköz szállításához legközelebbi Azure-régió.| "USA nyugati régiója"|
   |Sku| A Data Box eszköz konkrét adatokat. Érvényes értékek: "DataBox", "DataBoxDisk" és "DataBoxHeavy"| "DataBox" |
   |e-mail-lista| A rendeléshez társított e-mail-címek.| "gusp@contoso.com" |
   |street-address1| Annak a címnek a címe, ahová a rendelést ki fogják szállítják. | "15700 NE 39th St" |
   |street-address2| A másodlagos cím adatai, például a szám vagy az épület száma. | "Building 123" |
   |city| Az a város, amelybe az eszközt szállítják. | "Redmond" |
   |state-or-province| Az eszköz szállításának állapota.| "WA" |
   |ország| Az eszköz szállításának országa. | "Egyesült Államok" |
   |irányítószám| A szállítási címhez társított irányítószám vagy irányítószám.| "98052"|
   |vállalat neve| A vállalat neve, amelynél dolgozik.| "Contoso, LTD" |
   |tárfiók| Az Azure Storage-fiók, ahonnan adatokat szeretne importálni.| "mystorageaccount"|
   |Debug| Hibakeresési információkkal részletes naplózást  | --debug |
   |segítség| Jelenítse meg a parancs súgóinformációját. | --help -h |
   |csak show-errors| Csak a hibák megjelenítése, a figyelmeztetések mellőzése. | --only-show-errors |
   |output -o| Beállítja a kimeneti formátumot.  Megengedett értékek: json, jsonc, none, table, tsv, yaml, yamlc. Az alapértelmezett érték a json. | --output "json" |
   |lekérdezés| A JMESPath lekérdezési sztring. További információ: [JMESPath.](http://jmespath.org/) | --query <string>|
   |részletes| Részletes naplózást is tartalmazhat. | --verbose |

2. A választott parancssorban vagy terminálban futtassa [az az data box job create](/cli/azure/databox/job#az_databox_job_create) parancsot a Azure Data Box létrehozásához.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Példa a parancsok használatára:

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

   A parancs futtatásának kimenete a következő:

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

3. Alapértelmezés szerint minden Azure CLI-parancs json kimeneti formátumot használ, hacsak nem módosítja. A kimeneti formátumot a globális paraméterrel `--output <output-format>` módosíthatja. Ha a formátumot "table" formátumra módosítja, az javítja a kimenet olvashatóságát.

   Itt van ugyanaz a parancs, amit most futtattunk egy kis finomhangolással a formázás módosítása után:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   A parancs futtatásának kimenete a következő:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Eszköz megrendeléséhez Azure PowerShell alábbi lépéseket:

1. Az importálási rendelés létrehozása előtt be kell szereznie a tárfiókot, és mentenie kell a tárfiók objektumát egy változóban.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Írja le a rendelési Data Box beállításait. Ezek a beállítások tartalmazzák a személyes/üzleti adatait, az előfizetés nevét, az eszközadatokat és a szállítási adatokat. Ezeket a beállításokat paraméterekként kell használnia a PowerShell-parancs futtatásakor a Data Box létrehozásához. Az alábbi táblázat a [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob)parancshoz használt paraméterbeállításokat mutatja be.

    | Beállítás (paraméter) | Description |  Mintaérték |
    |---|---|---|
    |ResourceGroupName [Kötelező]| Használjon egy meglévő erőforráscsoportot. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | "myresourcegroup"|
    |Név [Kötelező]| A létrehozott rendelés neve. | "mydataboxorder"|
    |ContactName [Kötelező]| A szállítási címhez társított név. | "Fog Fog Foga"|
    |PhoneNumber [Kötelező]| A rendelést megkapó személy vagy vállalkozás telefonszáma.| "14255551234"
    |Hely [kötelező]| Az eszköz szállításához legközelebbi Azure-régió.| "WestUS"|
    |DataBoxType [Kötelező]| A Data Box eszköz konkrét adatokat. Érvényes értékek: "DataBox", "DataBoxDisk" és "DataBoxHeavy"| "DataBox" |
    |EmailId [Kötelező]| A rendeléshez társított e-mail-címek.| "gusp@contoso.com" |
    |StreetAddress1 [Kötelező]| Annak a címnek a címe, ahová a rendelést ki fogják szállítják. | "15700 NE 39th St" |
    |StreetAddress2| A másodlagos cím adatai, például a házszám vagy az épület száma. | "Building 123" |
    |StreetAddress3| A harmadlagos címinformáció. | |
    |Város [Kötelező]| Az a város, amelybe az eszközt szállítják. | "Redmond" |
    |StateOrProvinceCode [Kötelező]| Az eszköz szállításának állapota.| "WA" |
    |CountryCode [Kötelező]| Az eszköz szállításának országa. | "Egyesült Államok" |
    |Irányítószám [kötelező]| A szállítási címhez társított irányítószám vagy irányítószám.| "98052"|
    |CompanyName| A vállalat neve, amelynél dolgozik.| "Contoso, LTD" |
    |StorageAccountResourceId [Kötelező]| Az Azure Storage-fiók azonosítója, ahonnan adatokat szeretne importálni.| <AzStorageAccount>.id |

3. A választott parancssorban vagy terminálban a [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) paranccsal hozza létre Azure Data Box rendelését.

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

   A parancs futtatásának kimenete a következő:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>A megrendelés nyomon követése

# <a name="portal"></a>[Portál](#tab/portal)

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Az állapot megtekintéséhez Data Box meg a rendelést, majd az **Áttekintést.** A portálon a megrendelés **Megrendelve** állapotban látható.

Ha nem áll rendelkezésre eszköz, értesítést fog kapni. Ha van elérhető eszköz, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a csomagot. Az eszköz előkészítése során a következő műveletek lesznek végrehajtva:

* SMB-megosztások létrehozása az eszközzel társított mindegyik tárfiókhoz.
* Hozzáférési hitelesítő adatok (felhasználónév és jelszó) kiosztása mindegyik megosztáshoz.
* Az eszköz zárolásának feloldásához szükséges jelszó létrehozása.
* A Data Box zárolásának célja, hogy megakadályozza az eszközhöz való illetéktelen hozzáférést a teljes eljárás során.

Az eszköz előkészítésének befejeztével a portálon a megrendelés **Feldolgozott** állapotban jelenik meg.

![Egy Data Box megrendelés, amely fel lett feldolgozva](media/data-box-overview/data-box-order-status-processed.png)

A Microsoft ezután előkészíti, majd feladja a csomagot egy regionális fuvarozónál. Az eszköz feladását követően Ön megkapja a fuvarlevélszámot. A portál a **Feladva** állapotot mutatja.

![Egy Data Box megrendelés, amely fel lett küldve](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Egyetlen rendelés nyomon követése

Egy meglévő rendelés nyomkövetési információinak le Azure Data Box futtassa [`az databox job show`](/cli/azure/databox/job#az_databox_job_show) a parancsot. A parancs a rendeléssel kapcsolatos információkat jeleníti meg, például a nevet, az erőforráscsoportot, a követési adatokat, az előfizetés azonosítóját, a kapcsolattartási adatokat, a szállítmány típusát és az eszköz termékváltozatát.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   Az alábbi táblázatban a paraméterinformációi `az databox job show` láthatóak:

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforráscsoport [kötelező]| A rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | "myresourcegroup"|
   |name [Kötelező]| A megjelenítendő rendelés neve. | "mydataboxorder"|
   |Debug| Hibakeresési információkkal részletes naplózást | --debug |
   |segítség| Jelenítse meg a parancs súgóinformációját. | --help -h |
   |csak show-errors| Csak a hibák megjelenítése, a figyelmeztetések mellőzése. | --only-show-errors |
   |output -o| Beállítja a kimeneti formátumot.  Megengedett értékek: json, jsonc, none, table, tsv, yaml, yamlc. Az alapértelmezett érték json. | --output "json" |
   |lekérdezés| A JMESPath lekérdezési sztring. További információ: [JMESPath.](http://jmespath.org/) | --query <string>|
   |részletes| Részletes naplózást is tartalmaz. | --verbose (részletes) |

   Példa a parancsra, "table" kimeneti formátummal:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   A parancs futtatásának kimenete a következő:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> A listarend az előfizetés szintjén is támogatott, így az erőforráscsoport választható paraméter (és nem kötelező paraméter) lesz.

### <a name="list-all-orders"></a>Az összes rendelés listása

Ha több eszközt rendelt meg, a parancs futtatásával megtekintheti az [`az databox job list`](/cli/azure/databox/job#az_databox_job_list) összes Azure Data Box rendelését. A parancs felsorolja az adott erőforráscsoporthoz tartozó összes rendelést. A kimenetben is megjelenik: rendelés neve, szállítási állapot, Azure-régió, kézbesítési típus, rendelés állapota. A lemondott rendelések is szerepelnek a listában.
A parancs az egyes rendeléseket időbélyegzőit is megjeleníti.

```azurecli
az databox job list --resource-group <resource-group>
```

Az alábbi táblázatban a paraméterinformációi `az databox job list` láthatóak:

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforráscsoport [kötelező]| A rendeléseket tartalmazó erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | "myresourcegroup"|
   |Debug| Hibakeresési információk beburkolása részletes naplózáshoz | --debug |
   |segítség| Jelenítse meg a parancs súgóinformációját. | --help -h |
   |csak show-errors| Csak a hibák megjelenítése, a figyelmeztetések mellőzése. | --only-show-errors |
   |output -o| Beállítja a kimeneti formátumot.  Megengedett értékek: json, jsonc, none, table, tsv, yaml, yamlc. Az alapértelmezett érték a json. | --output "json" |
   |lekérdezés| A JMESPath lekérdezési sztring. További információ: [JMESPath.](http://jmespath.org/) | --query <string>|
   |részletes| Részletes naplózást is tartalmaz. | --verbose (részletes) |

   Példa a parancsra, "table" kimeneti formátummal:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   A parancs futtatásának kimenete a következő:

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

Egyetlen meglévő rendelés nyomkövetési információinak le Azure Data Box a [Get-AzDataBoxJob parancs futtatásával.](/powershell/module/az.databox/Get-AzDataBoxJob) A parancs megjeleníti a rendeléssel kapcsolatos információkat, például a név, az erőforráscsoport, a követési adatok, az előfizetés azonosítója, a kapcsolattartási adatok, a szállítmány típusa és az eszköz termékváltozata adatait.

> [!NOTE]
> `Get-AzDataBoxJob` A egy- és több rendelés megjelenítésére is használható. A különbség az, hogy egyetlen rendelés rendelésnevét kell megadnia.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   Az alábbi táblázatban a paraméter adatai `Get-AzDataBoxJob` láthatóak:

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |ResourceGroup [Kötelező]| A rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | "myresourcegroup"|
   |Név [Kötelező]| Annak a rendelésnek a neve, amelyről információt kell lekért. | "mydataboxorder"|
   |ResourceId| A rendeléshez társított erőforrás azonosítója. |  |

   Példa a parancsra a kimenettel:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   A parancs futtatásának kimenete a következő:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Az összes rendelés listása

Ha több eszközt rendelt meg, a futtatásával megtekintheti az [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) összes Azure Data Box rendelést. A parancs felsorolja az adott erőforráscsoporthoz tartozó összes rendelést. A kimenetben is megjelenik: rendelés neve, szállítási állapot, Azure-régió, kézbesítés típusa, rendelés állapota. A lemondott rendelések is szerepelnek a listában.
A parancs az egyes rendeléseket időbélyegzőket is megjeleníti.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Íme egy példa a parancsra:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

A parancs futtatásának kimenete a következő:

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

A rendelés megszakítása érdekében a Azure Portal válassza az **Áttekintés lehetőséget,** és válassza a **Mégse** lehetőséget a parancssávon.

A megrendelést annak feladását követően bármikor megszakíthatja, mielőtt az Feldolgozott állapotba lép.

A lemondott rendelés törléséhez kattintson az Áttekintés **gombra,** és válassza **a** Törlés lehetőséget a parancssávon.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Rendelés visszavonása

A rendelés megszakítása Azure Data Box futtassa a [`az databox job cancel`](/cli/azure/databox/job#az_databox_job_cancel) parancsot. Meg kell adnia a rendelés lemondásának okát.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   Az alábbi táblázatban a paraméterinformációi `az databox job cancel` láthatóak:

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforráscsoport [kötelező]| A törlési rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | "myresourcegroup"|
   |name [Kötelező]| A törlésre kerülő rendelés neve. | "mydataboxorder"|
   |ok [Kötelező]| A rendelés lemondásának oka. | "Hibás információkat tam meg, és vissza kellett mondani a rendelést." |
   |igen| Ne kér megerősítést. | --yes (-y)| 
   |Debug| Hibakeresési információk a részletes naplózáshoz | --debug (Hibakeresés) |
   |segítség| Jelenítse meg a parancs súgóinformációját. | --help -h |
   |csak show-errors| Csak a hibák megjelenítése, a figyelmeztetések mellőzése. | --only-show-errors |
   |output -o| Beállítja a kimeneti formátumot.  Megengedett értékek: json, jsonc, none, table, tsv, yaml, yamlc. Az alapértelmezett érték json. | --output "json" |
   |lekérdezés| A JMESPath lekérdezési sztring. További információ: [JMESPath.](http://jmespath.org/) | --query <string>|
   |részletes| Részletes naplózást is tartalmazhat. | --verbose |

   Példa a parancsra a kimenettel:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   A parancs futtatásának kimenete a következő:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Rendelés törlése

Ha lemondott egy Azure Data Box, a futtatásával törölheti [`az databox job delete`](/cli/azure/databox/job#az_databox_job_delete) a rendelést.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   Az alábbi táblázatban a paraméterinformációi `az databox job delete` láthatóak:

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforráscsoport [kötelező]| A törlési rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | "myresourcegroup"|
   |name [Kötelező]| A törlésre kerülő rendelés neve. | "mydataboxorder"|
   |előfizetést| Az Azure-előfizetés neve vagy azonosítója (GUID). | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |igen| Ne kér megerősítést. | --yes (-y)|
   |Debug| Hibakeresési információk a részletes naplózáshoz | --debug (Hibakeresés) |
   |segítség| Jelenítse meg a parancs súgóinformációját. | --help -h |
   |csak show-errors| Csak a hibák megjelenítése, a figyelmeztetések mellőzése. | --only-show-errors |
   |output -o| Beállítja a kimeneti formátumot.  Megengedett értékek: json, jsonc, none, table, tsv, yaml, yamlc. Az alapértelmezett érték json. | --output "json" |
   |lekérdezés| A JMESPath lekérdezési sztring. További információ: [JMESPath.](http://jmespath.org/) | --query <string>|
   |részletes| Részletes naplózást is tartalmaz. | --verbose (részletes) |

Példa a parancsra a kimenettel:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   A parancs futtatásának kimenete a következő:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Rendelés visszavonása

A rendelés megszakításához Azure Data Box [Stop-AzDataBoxJob parancsot.](/powershell/module/az.databox/stop-azdataboxjob) Meg kell adnia a rendelés lemondásának okát.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

Az alábbi táblázatban a paraméterinformációi `Stop-AzDataBoxJob` láthatóak:

| Paraméter | Leírás |  Mintaérték |
|---|---|---|
|ResourceGroup [Kötelező]| A megszakítani szükséges rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | "myresourcegroup"|
|Név [Kötelező]| A törlésre kerülő rendelés neve. | "mydataboxorder"|
|Ok [Kötelező]| A rendelés lemondásának oka. | "Hibás adatokat adtam meg, és vissza kellett mondani a rendelést." |
|Force | Kényszeríti a parancsmag futtatását a felhasználó megerősítése nélkül. | -Force |

Példa a parancsra a kimenettel:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

A parancs futtatásának kimenete a következő:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Rendelés törlése

Ha lemondott egy Azure Data Box, a futtatásával törölheti [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) a rendelést.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

Az alábbi táblázatban a paraméterinformációi `Remove-AzDataBoxJob` láthatóak:

| Paraméter | Leírás |  Mintaérték |
|---|---|---|
|ResourceGroup [Kötelező]| A törlési rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | "myresourcegroup"|
|Név [Kötelező]| A törölni szükséges rendelés neve. | "mydataboxorder"|
|Force | Kényszeríti a parancsmag futtatását a felhasználó megerősítése nélkül. | -Force |

Példa a parancsra a kimenettel:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

A parancs futtatásának kimenete a következő:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következő Azure Data Box a következő cikkekkel:

> [!div class="checklist"]
>
> * A Data Box üzembe helyezésének előfeltételei
> * A Data Box megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

A következő oktatóanyag a Data Box beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box beállítása](./data-box-deploy-set-up.md)
