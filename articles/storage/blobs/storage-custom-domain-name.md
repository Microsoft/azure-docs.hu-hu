---
title: Egyéni tartomány leképezása egy Azure Blob Storage végpontra
titleSuffix: Azure Storage
description: Leképez egy egyéni tartományt egy Blob Storage webes végpontra egy Azure Storage-fiókban.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 45ae3d80202bfb29074461f899798d278eb0895b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538361"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Egyéni tartomány leképezása egy Azure Blob Storage végpontra

Az egyéni tartományokat leképezheti blob-szolgáltatásvégpontra vagy statikus [webhelyvégpontra.](storage-blob-static-website.md) 

> [!NOTE] 
> Ez a leképezés csak altartományok esetén működik (például: `www.contoso.com` ). Ha azt szeretné, hogy a webes végpont elérhető legyen a gyökértartományon (például: ), akkor a következőt kell `contoso.com` Azure CDN. Útmutatásért tekintse meg a cikk Egyéni tartomány [leképezés HTTPS-kompatibilis használatával](#enable-https) című szakaszát. Mivel a cikk erre a szakaszra lép az egyéni tartomány gyökértartományának engedélyezéséhez, a HTTPS engedélyezésére vonatkozó lépés ebben a szakaszban nem kötelező. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Csak HTTP-t engedélyező egyéni tartomány leképezés

Ez a módszer egyszerűbb, de csak HTTP-hozzáférést tesz lehetővé. Ha a tárfiók a [](../common/storage-require-secure-transfer.md) HTTPS protokollon keresztüli biztonságos átvitelre van konfigurálva, engedélyeznie kell a HTTPS-hozzáférést az egyéni tartományhoz. 

A HTTPS-hozzáférés engedélyezéséhez tekintse meg a cikk [Https-kompatibilis egyéni](#enable-https) tartomány leképezés című szakaszát. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Egyéni tartomány leképezés

> [!IMPORTANT]
> A konfiguráció befejezése közben az egyéni tartomány rövid időre elérhetetlenné válik a felhasználók számára. Ha a tartomány jelenleg olyan szolgáltatói szerződéssel (SLA- t) használó alkalmazást támogat, amely nulla állásidőt igényel, kövesse [a](#zero-down-time) jelen cikk Egyéni tartomány leképezése állásidővel című szakaszának lépéseit, hogy a felhasználók a DNS-leképezés ideje alatt is hozzáférnek a tartományhoz.

Ha nem tudja, hogy a tartomány rövid időre nem érhető el a felhasználók számára, kövesse az alábbi lépéseket.

:heavy_check_mark: 1. lépés: A tárolási végpont gazdagépnevének lekért neve.

:heavy_check_mark: 2. lépés: Canonical name (CNAME) rekord létrehozása a tartományszolgáltatóval.

:heavy_check_mark: 3. lépés: Az egyéni tartomány regisztrálása az Azure-ban. 

:heavy_check_mark: 4. lépés: Az egyéni tartomány tesztelése.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1. lépés: A tárolási végpont gazdagépnevének lekért neve 

Az állomásnév a tárolóvégpont URL-címe a protokollazonosító és a záró perjel nélkül. 

1. A [Azure Portal](https://portal.azure.com)a tárfiókhoz.

2. A menüpanel Beállítások **menüjében válassza a** Tulajdonságok **lehetőséget.**  

3. Másolja az Elsődleges  Blob-szolgáltatásvégpont vagy az Elsődleges statikus webhely **végpontjának értékét** egy szövegfájlba. 
  
   > [!NOTE]
   > A Data Lake Storage-végpont nem támogatott (például: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Távolítsa el a protokollazonosítót (például: ) és a záró `HTTPS` perjelet a sztringből. Az alábbi táblázat példákat tartalmaz.

   | A végpont típusa |  endpoint | állomásnév |
   |------------|-----------------|-------------------|
   |blob szolgáltatás  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statikus webhely  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ezt az értéket félre kell tenni későbbre.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>2. lépés: Canonical Name (CNAME) rekord létrehozása a tartományszolgáltatóval

Hozzon létre egy CNAME rekordot, amely az állomásnévre mutat. A CNAME rekord egy DNS-rekordtípus, amely leképez egy forrástartománynevet egy céltartománynévre.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd a DNS-beállítás kezeléséhez kattintson a lapra.

   A lap a Tartománynév,  **DNS** vagy Névkiszolgáló kezelése nevű **szakaszban található.**

2. Keresse meg a CNAME-rekordok kezelésére vonatkozó szakaszt. 

   Előfordulhat, hogy meg kell keresnie egy speciális beállítási oldalt, és meg kell keresnie a **CNAME,** **alias vagy** **altartományokat.**

3. Hozzon létre egy CNAME rekordot. A rekord részeként adja meg a következő elemeket: 

   - Az altartomány aliasa, például `www` vagy `photos` . Az altartomány szükséges, a gyökértartományok nem támogatottak. 
      
   - A cikk korábbi, A [](#endpoint) tárolási végpont gazdagépnevének lekért állomásneve szakaszban kapott állomásnév. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>3. lépés: Egyéni tartomány regisztrálása az Azure-ban

##### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)a tárfiókhoz.

2. A menüpanel blobszolgáltatás területén **válassza** az Egyéni **tartomány lehetőséget.**

   > [!NOTE]
   > Ez a beállítás nem jelenik meg olyan fiókokban, amelyeken engedélyezve van a hierarchikus névtér funkció. Ezen fiókok esetében használja a PowerShellt vagy az Azure CLI-t a lépés befejezéséhez.

   ![egyéni tartomány beállítás](./media/storage-custom-domain-name/custom-domain-button.png "egyéni tartomány")

   Megnyílik **az Egyéni tartomány** panel.

3. A **Tartománynév szövegmezőbe** írja be az egyéni tartomány nevét, beleértve az altartományt is  
   
   Ha például a tartománya *contoso.com* és az altartomány *aliasa www,* írja be a következőt: `www.contoso.com` . Ha az altartománya photos , *írja* be a(a) `photos.contoso.com` et.

4. Az egyéni tartomány regisztráláshoz kattintson a Mentés **gombra.**

   Miután a CNAME rekord propagálva lett a tartománynév-kiszolgálókon (DNS) keresztül, és ha a felhasználók a megfelelő engedélyekkel rendelkezik, megtekinthetik a blobadatokat az egyéni tartomány használatával.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Futtassa a következő PowerShell-parancsot

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- Cserélje le `<resource-group-name>` a helyőrzőt az erőforráscsoport nevére.

- Cserélje le `<storage-account-name>` a helyőrzőt a tárfiók nevére.

- Cserélje le `<custom-domain-name>` a helyőrzőt az egyéni tartomány nevére, beleértve az altartományt is.

  Ha például a tartománya *contoso.com* és az altartomány aliasa *www,* írja be a következőt: `www.contoso.com` . Ha az altartomány fényképek, *írja* be a (kép) `photos.contoso.com` szövegrészt.

Miután a CNAME rekord propagálva lett a tartománynév-kiszolgálókon (DNS), és ha a felhasználók a megfelelő engedélyekkel rendelkezik, az egyéni tartomány használatával megtekinthetik a blobadatokat.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Futtassa a következő PowerShell-parancsot

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- Cserélje le `<resource-group-name>` a helyőrzőt az erőforráscsoport nevére.

- Cserélje le `<storage-account-name>` a helyőrzőt a tárfiók nevére.

- Cserélje le `<custom-domain-name>` a helyőrzőt az egyéni tartomány nevére, beleértve az altartományt is.

  Ha például a tartománya *contoso.com* és az altartomány aliasa *www,* írja be a következőt: `www.contoso.com` . Ha az altartomány fényképek, *írja* be a (kép) `photos.contoso.com` szövegrészt.

Miután a CNAME rekord propagálva lett a tartománynév-kiszolgálókon (DNS), és ha a felhasználók a megfelelő engedélyekkel rendelkezik, az egyéni tartomány használatával megtekinthetik a blobadatokat.

---

#### <a name="step-4-test-your-custom-domain"></a>4. lépés: Az egyéni tartomány tesztelése

Annak megerősítéséhez, hogy az egyéni tartomány le van-e leképezve a Blob szolgáltatásvégpontra, hozzon létre egy blobot egy nyilvános tárolóban a tárfiókban. Ezután egy webböngészőben egy URI használatával férhet hozzá a blobhoz a következő formátumban: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ha például egy webes űrlapot az egyéni altartományban található tárolóban `myforms` photos.contoso.com, használhatja a következő URI-t: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Egyéni tartomány leképezés nulla állásidővel

> [!NOTE]
> Ha nem biztos abban, hogy a tartomány rövid időre nem érhető el [a](#map-a-domain) felhasználók számára, fontolja meg a cikk Egyéni tartomány leképezés szakaszának lépéseit. Egyszerűbb, kevesebb lépésből áll.  

Ha a tartomány jelenleg olyan szolgáltatói szerződéssel (SLA) rendelkezik, amely nulla állásidőt igényel, kövesse az alábbi lépéseket annak biztosításához, hogy a felhasználók a DNS-leképezés ideje alatt hozzáférnek a tartományhoz. 

:heavy_check_mark: 1. lépés: A tárolási végpont gazdagépnevének lekért neve.

:heavy_check_mark: 2. lépés: Köztes canonical name (CNAME) rekord létrehozása a tartományszolgáltatóval.

:heavy_check_mark: 3. lépés: Az egyéni tartomány előzetes regisztrálása az Azure-ban.

:heavy_check_mark: 4. lépés: CNAME rekord létrehozása a tartományszolgáltatóval.

:heavy_check_mark: 5. lépés: Az egyéni tartomány tesztelése.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1. lépés: A tárolási végpont gazdagépnevének lekért neve 

Az állomásnév a tárolási végpont URL-címe a protokollazonosító és a záró perjel nélkül. 

1. A [Azure Portal](https://portal.azure.com)a tárfiókhoz.

2. A menüpanelen a Beállítások **alatt válassza** a Tulajdonságok **lehetőséget.**  

3. Másolja az Elsődleges **Blob** szolgáltatásvégpont vagy az Elsődleges statikus webhely **végpontjának** értékét egy szövegfájlba. 

   > [!NOTE]
   > A Data Lake Storage-végpont nem támogatott (például: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Távolítsa el a protokollazonosítót (például: ) és a záró `HTTPS` perjelet a sztringből. Az alábbi táblázat példákat tartalmaz.

   | Végpont típusa |  endpoint | állomásnév |
   |------------|-----------------|-------------------|
   |blob szolgáltatás  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statikus webhely  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ezt az értéket későbbre félre kell tenni.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>2. lépés: Köztes canonical name (CNAME) rekord létrehozása a tartományszolgáltatónál

Hozzon létre egy ideiglenes CNAME rekordot, amely az állomásnévre mutat. A CNAME rekord egy olyan DNS-rekordtípus, amellyel egy forrástartománynév leképezhető egy céltartománynévre.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd a DNS-beállítások kezeléséhez kattintson a lapra.

   A lap a Tartománynév,  **DNS** vagy Névkiszolgáló kezelése nevű **szakaszban található.**

2. Keresse meg a CNAME-rekordok kezelésére vonatkozó szakaszt. 

   Előfordulhat, hogy meg kell keresnie egy speciális beállítási oldalt, és meg kell keresnie a **CNAME,** **alias vagy** **altartományokat.**

3. Hozzon létre egy CNAME rekordot. A rekord részeként adja meg a következő elemeket: 

   - Az altartomány aliasa, például `www` vagy `photos` . Az altartomány szükséges, a gyökértartományok nem támogatottak.

     Adja hozzá `asverify` az altartományt az aliashoz. Például: `asverify.www` vagy `asverify.photos` .
       
   - A cikk korábbi, A [](#endpoint) tárolási végpont gazdagépnevének lekért állomásneve szakaszban kapott állomásnév. 

     Adja hozzá az `asverify` altartományt az állomásnévhez. Példa: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>3. lépés: Egyéni tartomány előzetes regisztrálása az Azure-ban

Amikor előre regisztrálja az egyéni tartományt az Azure-ban, engedélyezi az Azure számára az egyéni tartomány felismerését anélkül, hogy módosítania kell a tartomány DNS-rekordját. Így ha módosítja a tartomány DNS-rekordját, az állásidő nélkül lesz leképezve a blobvégpontra.

##### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)a tárfiókhoz.

2. A menüpanel blobszolgáltatás területén **válassza** az Egyéni **tartomány lehetőséget.**

   > [!NOTE]
   > Ez a beállítás nem jelenik meg olyan fiókokban, amelyeken engedélyezve van a hierarchikus névtér funkció. Ezeknél a fiókoknál használja a PowerShellt vagy az Azure CLI-t a lépés befejezéséhez.

   ![egyéni tartomány beállítás](./media/storage-custom-domain-name/custom-domain-button.png "egyéni tartomány")

   Megnyílik **az Egyéni tartomány** panel.

3. A **Tartománynév szövegmezőbe** írja be az egyéni tartomány nevét, beleértve az altartományt is  
   
   Ha például a tartománya *contoso.com* és az altartomány aliasa *www,* írja be a következőt: `www.contoso.com` . Ha az altartomány fényképek, *írja* be a (kép) `photos.contoso.com` szövegrészt.

4. Jelölje be **a Közvetett CNAME-érvényesítés használata** jelölőnégyzetet.

5. Az egyéni tartomány regisztráláshoz kattintson a Mentés **gombra.**
  
   Ha a regisztráció sikeres, a portál értesíti, hogy a tárfiók frissítése sikeres volt. Az Azure ellenőrizte az egyéni tartományt, de a tartományba való forgalom még nincs a tárfiókhoz irányítva, amíg létre nem hoz egy CNAME rekordot a tartományszolgáltatóval. Ezt a következő szakaszban fogja megtenni.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Futtassa a következő PowerShell-parancsot

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- Cserélje le `<resource-group-name>` a helyőrzőt az erőforráscsoport nevére.

- Cserélje le `<storage-account-name>` a helyőrzőt a tárfiók nevére.

- Cserélje le `<custom-domain-name>` a helyőrzőt az egyéni tartomány nevére, beleértve az altartományt is.

  Ha például a tartománya *contoso.com* és az altartomány aliasa *www,* írja be a következőt: `www.contoso.com` . Ha az altartomány fényképek, *írja* be a (kép) `photos.contoso.com` szövegrészt.

A tartományba való forgalom még nincs a tárfiókhoz irányítva, amíg létre nem hoz egy CNAME rekordot a tartományszolgáltatóval. Ezt a következő szakaszban fogja megtenni.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Futtassa a következő PowerShell-parancsot

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- Cserélje le `<resource-group-name>` a helyőrzőt az erőforráscsoport nevére.

- Cserélje le `<storage-account-name>` a helyőrzőt a tárfiók nevére.

- Cserélje le `<custom-domain-name>` a helyőrzőt az egyéni tartomány nevére, beleértve az altartományt is.

  Ha például a tartománya *contoso.com* és az altartomány aliasa *www,* írja be a következőt: `www.contoso.com` . Ha az altartomány fényképek, *írja* be a (kép) `photos.contoso.com` szövegrészt.

A tartományba való adatforgalom még nincs a tárfiókhoz irányítva, amíg létre nem hoz egy CNAME rekordot a tartományszolgáltatóval. Ezt a következő szakaszban fogja megtenni.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>4. lépés: CNAME rekord létrehozása a tartományszolgáltatóval

Hozzon létre egy ideiglenes CNAME rekordot, amely az állomásnévre mutat.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd a DNS-beállítás kezeléséhez kattintson a lapra.

   A lap egy Tartománynév,  **DNS** vagy Névkiszolgáló kezelése nevű **szakaszban található.**

2. Keresse meg a CNAME-rekordok kezelésére vonatkozó szakaszt. 

   Előfordulhat, hogy meg kell keresnie egy speciális beállítási oldalt, és meg kell keresnie a **CNAME,** **alias vagy** **altartományokat.**

3. Hozzon létre egy CNAME rekordot. A rekord részeként adja meg a következő elemeket: 

   - Az altartomány aliasa, például `www` vagy `photos` . Az altartomány szükséges, a gyökértartományok nem támogatottak.
      
   - A cikk korábbi, A [](#endpoint-2) tárolási végpont gazdagépnevének lekért állomásneve szakaszban kapott állomásnév. 

#### <a name="step-5-test-your-custom-domain"></a>5. lépés: Az egyéni tartomány tesztelése

Annak megerősítéséhez, hogy az egyéni tartomány le van leképezve a blobszolgáltatás végpontjára, hozzon létre egy blobot egy nyilvános tárolóban a tárfiókban. Ezután egy webböngészőben egy URI használatával férhet hozzá a blobhoz a következő formátumban: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ha például az egyéni altartományban található tárolóban lévő webes űrlapot `myforms` *photos.contoso.com,* használhatja a következő URI-t: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Egyéni tartományleképezés eltávolítása

Egyéni tartományleképezés eltávolításához törölje az egyéni tartomány regisztrációját. Használja az alábbi eljárások egyikét.

#### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)a tárfiókhoz.

2. A menüpanel blobszolgáltatás területén **válassza** az Egyéni **tartomány lehetőséget.**  
   Megnyílik **az Egyéni tartomány** panel.

3. Törölje az egyéni tartománynevet tartalmazó szövegmező tartalmát.

4. Válassza ki a **Mentés** gombot.

Az egyéni tartomány sikeres eltávolítása után megjelenik egy portálértesítés a tárfiók sikeres frissítésével.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egyéni tartományregisztráció eltávolításához használja a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell-parancsmagot, majd adjon meg egy üres sztringet () az `""` argumentum `-CustomDomainName` értékeként.

* Parancsformátum:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Példaparancs:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Egyéni tartományregisztráció eltávolításához használja az [az storage account update](/cli/azure/storage/account) CLI parancsot, majd adjon meg egy üres sztringet ( ) az argumentum `""` `--custom-domain` értékeként.

* Parancsformátum:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Példaparancs:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Egyéni tartomány leképezés engedélyezett HTTPS-kapcsolatokkal

Ez a megközelítés több lépésből áll, de lehetővé teszi a HTTPS-hozzáférést. 

Ha nincs szüksége arra, hogy a felhasználók HTTPS-kapcsolaton keresztül hozzáférjenek a blobhoz vagy a webes tartalmakhoz, tekintse meg a cikk Csak [HTTP-kompatibilis](#enable-http) egyéni tartomány leképezés című szakaszát. 

1. Engedélyezze [Azure CDN](../../cdn/cdn-overview.md) blobon vagy webes végponton. 

   További Blob Storage: [Azure Storage-fiók integrálása](../../cdn/cdn-create-a-storage-account-with-cdn.md)a Azure CDN. 

   Statikus webhelyvégpontért [lásd: Statikus](static-website-content-delivery-network.md)webhely integrálása a Azure CDN.

2. [Leképezés Azure CDN egy egyéni tartományra.](../../cdn/cdn-map-content-to-custom-domain.md)

3. [Engedélyezze a HTTPS-t egy Azure CDN tartományon.](../../cdn/cdn-custom-ssl.md)

   > [!NOTE] 
   > Ha frissíti a statikus webhelyet, a CDN-végpont kiürítése segítségével törölje a gyorsítótárazott tartalmakat a CDN peremhálózati kiszolgálóin. További információkért lásd az [Azure CDN-végpontok végleges törléséről](../../cdn/cdn-purge-endpoint.md) szóló cikket.

4. (Nem kötelező) Tekintse át az alábbi útmutatót:

   * [Közös hozzáférésű jogosultságkivonatok (SAS)-jogkivonatok a Azure CDN.](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures)

   * [HTTP–HTTPS átirányítás a következővel: Azure CDN.](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection)

   * [Díjszabás és számlázás a Blob Storage használata Azure CDN.](../../cdn/cdn-storage-custom-domain-https.md#pricing-and-billing)

## <a name="next-steps"></a>Következő lépések

* [Tudnivalók a statikus webhely-üzemeltetésről az Azure Blob Storage-ban](storage-blob-static-website.md)