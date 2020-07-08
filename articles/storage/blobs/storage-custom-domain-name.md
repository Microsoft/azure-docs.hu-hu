---
title: Egyéni tartomány leképezése egy Azure Blob Storage-végpontra
titleSuffix: Azure Storage
description: Egyéni tartomány leképezése egy Blob Storage vagy webes végpontra egy Azure Storage-fiókban.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 95fd62584ef73f3f2f198c84913652f460fc5b1a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465541"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Egyéni tartomány leképezése egy Azure Blob Storage-végpontra

Az egyéni tartományt egy blob Service-végpontra vagy egy [statikus webhely](storage-blob-static-website.md) -végpontra is leképezheti. 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Ez a leképezés csak altartományok esetén működik (például: `www.contoso.com` ). Ha azt szeretné, hogy a webes végpont elérhető legyen a legfelső szintű tartományban (például: `contoso.com` ), akkor a Azure CDNt kell használnia. Útmutatásért tekintse meg a jelen cikk [egyéni tartomány leképezése HTTPS-kompatibilis](#enable-https) szakasszal című szakaszát. Mivel ennek a cikknek a szakasza az egyéni tartomány legfelső szintű tartományának engedélyezéséhez szükséges, az adott szakaszon belül a HTTPS engedélyezésének lépései nem kötelező. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Egyéni tartomány leképezése csak HTTP-engedélyezve

Ez a megközelítés egyszerűbb, de csak HTTP-hozzáférést tesz lehetővé. Ha a Storage-fiók úgy van konfigurálva, hogy [biztonságos átvitelt igényel](../common/storage-require-secure-transfer.md) a HTTPS protokollon keresztül, akkor engedélyeznie kell a https-hozzáférést az egyéni tartományhoz. 

A HTTPS-hozzáférés engedélyezéséhez tekintse meg a jelen cikk [egyéni tartomány leképezése HTTPS-kompatibilis](#enable-https) szakasszal című szakaszát. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Egyéni tartomány leképezése

> [!IMPORTANT]
> Az egyéni tartomány rövid ideig nem lesz elérhető a felhasználók számára a konfiguráció befejezése után. Ha a tartomány jelenleg olyan szolgáltatói szerződéssel (SLA) rendelkező alkalmazást támogat, amely nulla állásidőt igényel, akkor a jelen cikk az [egyéni tartomány leképezése nulla állásidővel](#zero-down-time) című szakaszának lépéseit követve biztosíthatja, hogy a felhasználók hozzáférhessenek a tartományhoz, miközben a DNS-megfeleltetés zajlik.

Ha nem aggódik amiatt, hogy a tartomány rövid ideig nem érhető el a felhasználók számára, kövesse az alábbi lépéseket.

: heavy_check_mark: 1. lépés: a tárolási végpont állomásnévének beolvasása.

: heavy_check_mark: 2. lépés: kanonikus név (CNAME) rekord létrehozása a tartományi szolgáltatóval.

: heavy_check_mark: 3. lépés: az egyéni tartomány regisztrálása az Azure-ban. 

: heavy_check_mark: 4. lépés: az egyéni tartomány tesztelése.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1. lépés: a tárolási végpont állomásnévének beolvasása 

Az állomásnév a tárolási végpont URL-címe a protokoll azonosítója és a záró perjel nélkül. 

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.

2. A menü **Beállítások**területén válassza a **Tulajdonságok**elemet.  

3. Másolja az **elsődleges blob szolgáltatási végpontját** vagy az **elsődleges statikus webhely végpontját** egy szövegfájlba. 

4. Távolítsa el a protokoll azonosítóját (*például*HTTPS) és a karakterlánc záró perjelét. A következő táblázat példákat tartalmaz.

   | Végpont típusa |  endpoint | állomásnév |
   |------------|-----------------|-------------------|
   |BLOB szolgáltatás  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statikus webhely  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ezt az értéket később adja meg.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>2. lépés: kanonikus név (CNAME) rekord létrehozása a tartományi szolgáltatóval

Hozzon létre egy CNAME rekordot, amely az állomásnévre mutat. A CNAME rekord egy olyan DNS-rekordtípus, amellyel egy forrástartománynév leképezhető egy céltartománynévre.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd nyissa meg a lapot a DNS-beállítások kezeléséhez.

   Előfordulhat, hogy a lapot a **tartománynév**, a **DNS**vagy a **Névkiszolgálók kezelése**nevű szakaszban találja.

2. Keresse meg a CNAME rekordok kezelésére szolgáló szakaszt. 

   Előfordulhat, hogy a speciális beállítások lapra kell lépnie, és meg kell keresnie a **CNAME**, **alias**vagy **altartományokat**.

3. Hozzon létre egy CNAME rekordot. A rekord részeként adja meg a következő elemeket: 

   - Az altartomány aliasa, például `www` vagy `photos` . Az altartomány megadása kötelező, a legfelső szintű tartományok nem támogatottak. 
      
   - A jelen cikk korábbi részében a [tárolási végpont gazdagépének beolvasása](#endpoint) szakaszban beszerzett állomásnév. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>3. lépés: az egyéni tartomány regisztrálása az Azure-ban

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.

2. A párbeszédpanel **blob Service**területén válassza az **egyéni tartomány**lehetőséget.  

   ![egyéni tartomány lehetőség](./media/storage-custom-domain-name/custom-domain-button.png "egyéni tartomány")

   Megnyílik az **egyéni tartomány** panel.

3. A **tartománynév** szövegmezőbe írja be az egyéni tartomány nevét, beleértve az altartományt is  
   
   Ha például a tartománya *contoso.com* , és az altartomány aliasa a *www*, írja be a következőt: `www.contoso.com` . Ha az altartomány *fényképek*, adja meg a értéket `photos.contoso.com` .

4. Az egyéni tartomány regisztrálásához kattintson a **Save (Mentés** ) gombra.

   Miután a CNAME rekordot propagálta a tartománynév-kiszolgálókon (DNS), és ha a felhasználók rendelkeznek a megfelelő engedélyekkel, megtekinthetik a Blobok adatait az egyéni tartomány használatával.

#### <a name="step-4-test-your-custom-domain"></a>4. lépés: az egyéni tartomány tesztelése

Annak ellenőrzéséhez, hogy az egyéni tartomány hozzá van-e rendelve a blob Service-végponthoz, hozzon létre egy blobot a Storage-fiókban lévő nyilvános tárolóban. Ezután egy webböngészőben nyissa meg a blobot egy URI használatával a következő formátumban:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ha például egy webes űrlapot szeretne elérni a *myForms* -tárolóban a *photos.contoso.com* egyéni altartományában, a következő URI-t használhatja:`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Egyéni tartomány leképezése nulla állásidővel

> [!NOTE]
> Ha nem aggódik amiatt, hogy a tartomány rövid ideig nem érhető el a felhasználók számára, akkor vegye figyelembe a jelen cikk az [egyéni tartomány leképezése](#map-a-domain) című szakaszában ismertetett lépéseket. Egyszerűbb megközelítés kevesebb lépéssel.  

Ha a tartomány jelenleg olyan szolgáltatói szerződéssel (SLA-val) rendelkező alkalmazást támogat, amely nulla állásidőt igényel, akkor kövesse az alábbi lépéseket annak biztosításához, hogy a felhasználók hozzáférhessenek a tartományhoz a DNS-megfeleltetés megtartása közben. 

: heavy_check_mark: 1. lépés: a tárolási végpont állomásnévének beolvasása.

: heavy_check_mark: 2. lépés: hozzon létre egy köztes kanonikus nevet (CNAME-rekordot) a tartományi szolgáltatóval.

: heavy_check_mark: 3. lépés: az egyéni tartomány előzetes regisztrálása az Azure-ban.

: heavy_check_mark: 4. lépés: CNAME-rekord létrehozása a tartományi szolgáltatóval.

: heavy_check_mark: 5. lépés: az egyéni tartomány tesztelése.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1. lépés: a tárolási végpont állomásnévének beolvasása 

Az állomásnév a tárolási végpont URL-címe a protokoll azonosítója és a záró perjel nélkül. 

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.

2. A menü **Beállítások**területén válassza a **Tulajdonságok**elemet.  

3. Másolja az **elsődleges blob szolgáltatási végpontját** vagy az **elsődleges statikus webhely végpontját** egy szövegfájlba. 

4. Távolítsa el a protokoll azonosítóját (*például*HTTPS) és a karakterlánc záró perjelét. A következő táblázat példákat tartalmaz.

   | Végpont típusa |  endpoint | állomásnév |
   |------------|-----------------|-------------------|
   |BLOB szolgáltatás  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statikus webhely  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ezt az értéket később adja meg.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>2. lépés: hozzon létre egy köztes Canonical Name (CNAME) rekordot a tartományi szolgáltatóval

Hozzon létre egy ideiglenes CNAME rekordot, amely az állomásnévre mutat. A CNAME rekord egy olyan DNS-rekordtípus, amellyel egy forrástartománynév leképezhető egy céltartománynévre.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd nyissa meg a lapot a DNS-beállítások kezeléséhez.

   Előfordulhat, hogy a lapot a **tartománynév**, a **DNS**vagy a **Névkiszolgálók kezelése**nevű szakaszban találja.

2. Keresse meg a CNAME rekordok kezelésére szolgáló szakaszt. 

   Előfordulhat, hogy a speciális beállítások lapra kell lépnie, és meg kell keresnie a **CNAME**, **alias**vagy **altartományokat**.

3. Hozzon létre egy CNAME rekordot. A rekord részeként adja meg a következő elemeket: 

   - Az altartomány aliasa, például `www` vagy `photos` . Az altartomány megadása kötelező, a legfelső szintű tartományok nem támogatottak.

     Adja hozzá az `asverify` altartományt az aliashoz. Például: `asverify.www` vagy `asverify.photos` .
       
   - A jelen cikk korábbi részében a [tárolási végpont gazdagépének beolvasása](#endpoint) szakaszban beszerzett állomásnév. 

     Adja hozzá az altartományt `asverify` az állomásnévhez. Példa: `asverify.mystorageaccount.blob.core.windows.net`.

4. Az egyéni tartomány regisztrálásához kattintson a **Save (Mentés** ) gombra.

   Ha a regisztráció sikeres, a portál értesíti arról, hogy a Storage-fiók frissítése sikeresen megtörtént. Az egyéni tartományt az Azure ellenőrizte, de a tartományra irányuló forgalom még nincs átirányítva a Storage-fiókba.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>3. lépés: az egyéni tartomány előzetes regisztrálása az Azure-ban

Ha előzetesen regisztrálja az egyéni tartományt az Azure-ban, lehetővé teszi, hogy az Azure felismerje az egyéni tartományt anélkül, hogy módosítania kellene a tartomány DNS-rekordját. Így ha módosítja a tartomány DNS-rekordját, a rendszer leképezi a blob-végpontot állásidő nélkül.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.

2. A párbeszédpanel **blob Service**területén válassza az **egyéni tartomány**lehetőséget.  

   ![egyéni tartomány lehetőség](./media/storage-custom-domain-name/custom-domain-button.png "egyéni tartomány")

   Megnyílik az **egyéni tartomány** panel.

3. A **tartománynév** szövegmezőbe írja be az egyéni tartomány nevét, beleértve az altartományt is  
   
   Ha például a tartománya *contoso.com* , és az altartomány aliasa a *www*, írja be a következőt: `www.contoso.com` . Ha az altartomány *fényképek*, adja meg a értéket `photos.contoso.com` .

4. Jelölje be a **közvetlen CNAME-ellenőrzés használata** jelölőnégyzetet.

5. Az egyéni tartomány regisztrálásához kattintson a **Save (Mentés** ) gombra.
  
   Miután a CNAME rekordot propagálta a tartománynév-kiszolgálókon (DNS), és ha a felhasználók rendelkeznek a megfelelő engedélyekkel, megtekinthetik a Blobok adatait az egyéni tartomány használatával.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>4. lépés: CNAME-rekord létrehozása a tartományi szolgáltatóval

Hozzon létre egy ideiglenes CNAME rekordot, amely az állomásnévre mutat.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd nyissa meg a lapot a DNS-beállítások kezeléséhez.

   Előfordulhat, hogy a lapot a **tartománynév**, a **DNS**vagy a **Névkiszolgálók kezelése**nevű szakaszban találja.

2. Keresse meg a CNAME rekordok kezelésére szolgáló szakaszt. 

   Előfordulhat, hogy a speciális beállítások lapra kell lépnie, és meg kell keresnie a **CNAME**, **alias**vagy **altartományokat**.

3. Hozzon létre egy CNAME rekordot. A rekord részeként adja meg a következő elemeket: 

   - Az altartomány aliasa, például `www` vagy `photos` . Az altartomány megadása kötelező, a legfelső szintű tartományok nem támogatottak.
      
   - A jelen cikk korábbi részében a [tárolási végpont gazdagépének beolvasása](#endpoint-2) szakaszban beszerzett állomásnév. 

#### <a name="step-5-test-your-custom-domain"></a>5. lépés: az egyéni tartomány tesztelése

Annak ellenőrzéséhez, hogy az egyéni tartomány hozzá van-e rendelve a blob Service-végponthoz, hozzon létre egy blobot a Storage-fiókban lévő nyilvános tárolóban. Ezután egy webböngészőben nyissa meg a blobot egy URI használatával a következő formátumban:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ha például egy webes űrlapot szeretne elérni a *myForms* -tárolóban a *photos.contoso.com* egyéni altartományában, a következő URI-t használhatja:`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Egyéni tartomány-hozzárendelés eltávolítása

Egyéni tartomány-hozzárendelés eltávolításához törölje az egyéni tartomány regisztrációját. Használja az alábbi eljárások egyikét.

#### <a name="portal"></a>[Portál](#tab/azure-portal)

Az egyéni tartomány beállításának eltávolításához tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.

2. A párbeszédpanel **blob Service**területén válassza az **egyéni tartomány**lehetőséget.  
   Megnyílik az **egyéni tartomány** panel.

3. Törölje az egyéni tartománynevet tartalmazó szövegmező tartalmát.

4. Válassza ki a **Mentés** gombot.

Miután sikeresen eltávolította az egyéni tartományt, egy portál értesítést kap arról, hogy a Storage-fiók frissítése sikerült

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Egyéni tartományi regisztráció eltávolításához használja az az [Storage Account Update](https://docs.microsoft.com/cli/azure/storage/account) CLI parancsot, majd adja meg az `""` `--custom-domain` argumentum értékének üres karakterláncát ().

* Parancs formátuma:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Példa a parancsra:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egyéni tartományi regisztráció eltávolításához használja a [set-AzStorageAccount PowerShell-](/powershell/module/az.storage/set-azstorageaccount) parancsmagot, majd adja meg az `""` argumentum értékének üres karakterláncát () `-CustomDomainName` .

* Parancs formátuma:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Példa a parancsra:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Egyéni tartomány leképezése HTTPS-támogatással

Ez a megközelítés több lépést is magában foglal, de lehetővé teszi a HTTPS-hozzáférést. 

Ha nincs szüksége arra, hogy a felhasználók HTTPS-kapcsolaton keresztül hozzáférjenek a blobhoz vagy a webes tartalomhoz, akkor tekintse meg a jelen cikk az [egyéni tartomány leképezése csak a http-kompatibilis](#enable-http) szakaszát. 

Egyéni tartomány leképezéséhez és a HTTPS-hozzáférés engedélyezéséhez tegye a következőket:

1. Engedélyezze a [Azure CDNt](../../cdn/cdn-overview.md) a blob vagy a webes végponton. 

   Blob Storage végpont esetén tekintse meg az [Azure Storage-fiók integrálása Azure CDNokkal](../../cdn/cdn-create-a-storage-account-with-cdn.md)című témakört. 

   Statikus webhely-végpont esetén lásd: [statikus webhely integrálása a Azure CDNsal](static-website-content-delivery-network.md).

2. [Azure CDN tartalom leképezése egyéni tartományra](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Engedélyezze a HTTPS-t egy Azure CDN egyéni tartományon](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > A statikus webhely frissítésekor ügyeljen arra, hogy a CDN peremhálózati kiszolgálókon törölje a gyorsítótárazott tartalmat a CDN-végpont törlésével. További információkért lásd az [Azure CDN-végpontok végleges törléséről](../../cdn/cdn-purge-endpoint.md) szóló cikket.

4. Választható Tekintse át a következő útmutatót:

   * [Közös hozzáférésű aláírási (SAS) tokenek Azure CDNokkal](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [HTTP-HTTPS átirányítás Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)használatával.

   * [Díjszabás és számlázás a blob Storage és a Azure CDN használata esetén](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>További lépések

* [Ismerje meg az Azure Blob Storage-beli statikus webhely üzemeltetését](storage-blob-static-website.md)
