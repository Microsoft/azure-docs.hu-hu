---
title: TLS/SSL-tanúsítványok hozzáadása és kezelése
description: Hozzon létre egy ingyenes tanúsítványt, importáljon egy App Service tanúsítványt, importáljon egy Key Vault tanúsítványt, vagy vásároljon egy App Service tanúsítványt a Azure App Serviceban.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 03/02/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 99dc8cb2acf06faae16df6d3a48c4d38b1be46d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577784"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>TLS-/SSL-tanúsítvány hozzáadása az Azure App Service-ben

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ebből a cikkből megtudhatja, hogyan hozhat létre, tölthet fel vagy importálhat privát tanúsítványt vagy nyilvános tanúsítványt App Serviceba. 

Miután hozzáadta a tanúsítványt a App Service app vagy [Function alkalmazáshoz](../azure-functions/index.yml), biztonságossá teheti az [Egyéni DNS-nevet](configure-ssl-bindings.md) , vagy [használhatja azt az alkalmazás kódjában](configure-ssl-certificate-in-code.md).

> [!NOTE]
> Az alkalmazásba feltöltött tanúsítványt egy olyan központi telepítési egység tárolja, amely az alkalmazás erőforráscsoport-és régió-kombinációjának (belső nevén *webtárhely*) van kötve. Így a tanúsítvány elérhetővé válik más alkalmazások számára ugyanabban az erőforráscsoport-és régió-kombinációban. 

A következő táblázat felsorolja azokat a beállításokat, amelyekkel tanúsítványokat adhat hozzá a App Serviceban:

|Beállítás|Leírás|
|-|-|
| Ingyenes App Service felügyelt tanúsítvány létrehozása (előzetes verzió) | Ingyenes és könnyen használható privát tanúsítvány, ha csak az [Egyéni tartományt](app-service-web-tutorial-custom-domain.md) kell biztonságossá tennie app Serviceban. |
| App Service-tanúsítvány vásárlása | Az Azure által felügyelt privát tanúsítvány. A szolgáltatás ötvözi az automatizált tanúsítványok kezelését, valamint a megújítási és exportálási lehetőségek rugalmasságát. |
| Tanúsítvány importálása Key Vault | Hasznos, ha a [Azure Key Vault](../key-vault/index.yml) használatával kezeli a [PKCS12/pfx-profil-tanúsítványokat](https://wikipedia.org/wiki/PKCS_12). Lásd a [privát tanúsítványra vonatkozó követelményeket](#private-certificate-requirements). |
| Privát tanúsítvány feltöltése | Ha már rendelkezik egy külső szolgáltatótól származó privát tanúsítvánnyal, feltöltheti azt. Lásd a [privát tanúsítványra vonatkozó követelményeket](#private-certificate-requirements). |
| Nyilvános tanúsítvány feltöltése | A nyilvános tanúsítványok nem használhatók az egyéni tartományok biztonságossá tételéhez, de betöltheti őket a kódra, ha szüksége van rájuk a távoli erőforrásokhoz való hozzáféréshez. |

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy app Service alkalmazást](./index.yml).
- Privát tanúsítvány esetén győződjön meg arról, hogy az megfelel [a app Service összes követelményének](#private-certificate-requirements).
- **Csak az ingyenes tanúsítvány**:
    - Képezze le a tartományt, amelyhez tanúsítványt szeretne App Service. További információ: [oktatóanyag: meglévő egyéni DNS-név leképezése Azure app Service](app-service-web-tutorial-custom-domain.md).
    - A gyökértartomány (például a contoso.com) esetében győződjön meg arról, hogy az alkalmazás nem rendelkezik konfigurált [IP-korlátozásokkal](app-service-ip-restrictions.md) . A tanúsítvány létrehozása és a gyökértartomány rendszeres megújítása attól függ, hogy az alkalmazás elérhető-e az internetről.

## <a name="private-certificate-requirements"></a>Magánjellegű tanúsítványokra vonatkozó követelmények

Az [ingyenes app Service felügyelt tanúsítvány](#create-a-free-managed-certificate-preview) és a [app Service tanúsítvány](#import-an-app-service-certificate) már megfelel a app Service követelményeinek. Ha úgy dönt, hogy feltölt vagy importál egy privát tanúsítványt App Serviceba, a tanúsítványnak meg kell felelnie a következő követelményeknek:

* [Jelszóval védett pfx-fájlként](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)lett exportálva, a Triple des használatával titkosítva.
* Legalább 2048 bit hosszúságú titkos kulcsot kell tartalmaznia.
* Tartalmaznia kell a tanúsítványláncban lévő összes köztes tanúsítványt.

Egy TLS-kötésben lévő egyéni tartomány biztonságossá tételéhez a tanúsítvány további követelményeket támaszt:

* Kibővített [kulcshasználat használata](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) a kiszolgálói hitelesítéshez (OID = 1.3.6.1.5.5.7.3.1)
* A tanúsítványt megbízható hitelesítésszolgáltatónak kell aláírnia.

> [!NOTE]
> **Az elliptikus görbéjű titkosítási (ECC-) tanúsítványok** együttműködnek az App Service-szel, ez a cikk azonban erre nem tér ki. A hitelesítésszolgáltatóval együttműködve dolgozzák ki az ECC-tanúsítványok létrehozására szolgáló lépéseket.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-managed-certificate-preview"></a>Ingyenes felügyelt tanúsítvány létrehozása (előzetes verzió)

> [!NOTE]
> Az ingyenes felügyelt tanúsítvány létrehozása előtt győződjön meg arról, hogy [teljesítette az alkalmazás előfeltételeit](#prerequisites) .

Az ingyenes App Service felügyelt tanúsítvány egy kulcsrakész megoldás az egyéni DNS-név biztonságossá tételéhez App Serviceban. Ez egy teljesen működőképes TLS/SSL-tanúsítvány, amelyet a App Service felügyel, és automatikusan megújul. Az ingyenes tanúsítvány a következő korlátozásokkal jár:

- A nem támogatja a helyettesítő tanúsítványokat.
- Nem exportálható.
- App Service Environment (bekapcsoló) esetén nem támogatott.
- Nem támogatott a Traffic Manager rendszerbe integrált legfelső szintű tartományokkal.

> [!NOTE]
> Az ingyenes tanúsítványt a DigiCert bocsátja ki. Egyes legfelső szintű tartományok esetében explicit módon engedélyeznie kell a DigiCert tanúsítvány-kiállítóként, ha létrehoz egy [CAA tartományi rekordot](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) a következő értékkel: `0 issue digicert.com` .
> 

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>bal oldali menüjében válassza a app Services lehetőséget   >  **\<app-name>** .

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL**  >  **-Beállítások titkos kulcsú tanúsítványok (. pfx)**  >  **app Service felügyelt tanúsítvány létrehozása** lehetőséget.

![Ingyenes tanúsítvány létrehozása App Service](./media/configure-ssl-certificate/create-free-cert.png)

Válassza ki az egyéni tartományt egy ingyenes tanúsítvány létrehozásához, majd válassza a **Létrehozás** lehetőséget. Minden támogatott egyéni tartományhoz csak egy tanúsítványt hozhat létre.

Ha a művelet befejeződik, megjelenik a tanúsítvány a **titkos kulcs tanúsítványainak** listájában.

![Az ingyenes tanúsítvány létrehozása befejeződött](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Ha egy egyéni tartományt ezzel a tanúsítvánnyal kíván biztonságossá tenni, akkor is létre kell hoznia egy tanúsítvány kötését. Kövesse a [kötés létrehozása](configure-ssl-bindings.md#create-binding)című témakör lépéseit.
>

## <a name="import-an-app-service-certificate"></a>App Service-tanúsítvány importálása

Ha az Azure-ban vásárol egy App Service-tanúsítvány, az Azure a következő feladatokat kezeli:

- A GoDaddy vásárlási folyamatát gondoskodik.
- A tanúsítvány tartományon belüli ellenőrzését végzi.
- A tanúsítványt [Azure Key Vault](../key-vault/general/overview.md)tárolja.
- A tanúsítvány megújítását kezeli (lásd: [tanúsítvány megújítása](#renew-certificate)).
- A tanúsítvány automatikus szinkronizálása az importált másolatokkal App Service alkalmazásokban.

App Service tanúsítvány megvásárlásához nyissa meg a következőt: a [tanúsítvány megkezdése](#start-certificate-order).

Ha már rendelkezik működő App Service tanúsítvánnyal, a következőket teheti:

- [Importálja a tanúsítványt a app Serviceba](#import-certificate-into-app-service).
- [Kezelheti a tanúsítványt](#manage-app-service-certificates), például megújíthatja, visszaállította és exportálhatja.
> [!NOTE]
> App Service tanúsítványok jelenleg nem támogatottak az Azure nemzeti felhőkben.

### <a name="start-certificate-order"></a>A tanúsítvány megrendelésének indítása

A <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">app Service-tanúsítvány létrehozás lapon</a>indítson el egy app Service-tanúsítványt.

![App Service a tanúsítvány megvásárlásának elindítása](./media/configure-ssl-certificate/purchase-app-service-cert.png)

A következő táblázat segítséget nyújt a tanúsítvány konfigurálásában. Ha végzett, kattintson a **Létrehozás** gombra.

| Beállítás | Leírás |
|-|-|
| Név | A App Service tanúsítvány rövid neve. |
| Naked domain Host neve | Itt adhatja meg a legfelső szintű tartományt. A kiállított tanúsítvány a  legfelső szintű tartományt és az `www` altartományt is védi. A kiállított tanúsítványban a köznapi név mező tartalmazza a gyökértartomány tartományát, a tulajdonos alternatív neve mező pedig a `www` tartományt tartalmazza. Csak az altartományok védelméhez adja meg az altartomány teljes tartománynevét (például: `mysubdomain.contoso.com` ).|
| Előfizetés | Az előfizetés, amely a tanúsítványt fogja tartalmazni. |
| Erőforráscsoport | Az erőforráscsoport, amely a tanúsítványt fogja tartalmazni. Használhat új erőforráscsoportot, vagy kiválaszthatja ugyanazt az erőforráscsoportot, mint a App Service alkalmazás, például:. |
| Tanúsítvány SKU | Meghatározza a létrehozandó tanúsítvány típusát, legyen az egy standard tanúsítvány vagy egy [helyettesítő tanúsítvány](https://wikipedia.org/wiki/Wildcard_certificate). |
| Jogi feltételek | Ide kattintva erősítse meg, hogy elfogadja a jogi feltételeket. A tanúsítványok a GoDaddyből szerezhetők be. |

> [!NOTE]
> App Service az Azure-ból vásárolt tanúsítványokat a GoDaddy bocsátja ki. Egyes legfelső szintű tartományok esetében explicit módon engedélyeznie kell a GoDaddy tanúsítványt kiállítóként egy [CAA-tartománybeli rekord](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) létrehozásával a következő értékkel: `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Tárolás Azure Key Vault

A tanúsítvány megvásárlásának befejeződése után még néhány lépést el kell végeznie a tanúsítvány használatának megkezdése előtt. 

Válassza ki a tanúsítványt a [app Service tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd kattintson a **tanúsítvány-konfiguráció**  >  **1. lépés: tároló** elemre.

![App Service tanúsítvány Key Vault-tárolójának konfigurálása](./media/configure-ssl-certificate/configure-key-vault.png)

A [Key Vault](../key-vault/general/overview.md) egy Azure-szolgáltatás, amely segít megvédeni a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkokat. Ez a App Service-tanúsítványok számára választható tároló.

A **Key Vault állapota** lapon kattintson a Key Vault adattár elemre egy új tároló létrehozásához, vagy válasszon egy meglévő **tárat** . Ha úgy dönt, hogy létrehoz egy új tárat, a következő táblázat segítségével konfigurálja a tárolót, és kattintson a Létrehozás gombra. Hozza létre az új Key Vault ugyanabban az előfizetésben és erőforráscsoporthoz, mint a App Service alkalmazás.

| Beállítás | Leírás |
|-|-|
| Név | Egy egyedi név, amely alfanumerikus karaktereket és kötőjeleket tartalmaz. |
| Erőforráscsoport | Javaslatként válassza ki ugyanazt az erőforráscsoportot, mint a App Service-tanúsítványt. |
| Hely | Válassza ki ugyanazt a helyet, mint a App Service alkalmazás. |
| Tarifacsomag | További információ: [Azure Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/). |
| Hozzáférési szabályzatok| Meghatározza az alkalmazásokat és az engedélyezett hozzáférést a tároló erőforrásaihoz. Később is konfigurálhatja, ha a [Key Vault hozzáférési házirendhez](../key-vault/general/assign-access-policy-portal.md)tartozó lépéseket követve. |
| Virtual Network hozzáférés | A tár bizonyos Azure-beli virtuális hálózatokhoz való hozzáférésének korlátozása. Később is konfigurálhatja, a [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](../key-vault/general/network-security.md) című témakör lépéseit követve. |

Miután kiválasztotta a tárolót, zárjuk be a **Key Vault adattár** lapot. Az **1. lépés: tárolási** lehetőségnek zöld pipa jelzést kell mutatnia a sikeres művelethez. Tartsa nyitva a lapot a következő lépéshez.

### <a name="verify-domain-ownership"></a>A tartomány tulajdonjogának igazolása

Az utolsó lépésben használt **tanúsítvány-konfiguráció** lapon kattintson a **2. lépés: ellenőrzés** elemre.

![Tartomány ellenőrzése App Service tanúsítványhoz](./media/configure-ssl-certificate/verify-domain.png)

Válassza ki **app Service ellenőrzést**. Mivel már leképezte a tartományt a webalkalmazásra (lásd: [Előfeltételek](#prerequisites)), már ellenőrizve van. Ezt a lépést csak az **ellenőrzés** gombra kattintva fejezheti be. Kattintson a **refresh (frissítés** ) gombra, amíg az üzenet **tanúsítványa nem ellenőrzött** .

> [!NOTE]
> A tartomány-ellenőrzési módszerek négyféle típusa támogatott: 
> 
> - **App Service** – a legkényelmesebb lehetőség, ha a tartomány már le van képezve egy app Service alkalmazásra ugyanabban az előfizetésben. Kihasználja azt a tényt, hogy a App Service alkalmazás már ellenőrizte a tartomány tulajdonjogát.
> - **Tartomány** – az [Azure-ból vásárolt app Service tartomány](manage-custom-dns-buy-domain.md)ellenőrzése. Az Azure automatikusan hozzáadja az ellenőrző TXT-rekordot az Ön számára, és befejezi a folyamatot.
> - **E-mail – ellenőrizze** a tartományt úgy, hogy e-mailt küld a tartományi rendszergazdának. A beállítás kiválasztásakor a rendszer útmutatást ad.
> - **Manuális** – ellenőrizze a TARTOMÁNYT egy HTML-oldal használatával (csak **normál** tanúsítvány esetén) vagy egy DNS TXT-rekorddal. A beállítás kiválasztásakor a rendszer útmutatást ad.

### <a name="import-certificate-into-app-service"></a>Tanúsítvány importálása App Serviceba

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>bal oldali menüjében válassza a app Services lehetőséget   >  **\<app-name>** .

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL**  >  **-Beállítások titkos kulcsú tanúsítványok (. pfx)**  >  **Importálás app Service-tanúsítvány** elemet.

![App Service tanúsítvány importálása App Service](./media/configure-ssl-certificate/import-app-service-cert.png)

Válassza ki az imént megvásárolt tanúsítványt, és kattintson **az OK gombra**.

Ha a művelet befejeződik, megjelenik a tanúsítvány a **titkos kulcs tanúsítványainak** listájában.

![A tanúsítvány importálása App Service befejeződött](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Ha egy egyéni tartományt ezzel a tanúsítvánnyal kíván biztonságossá tenni, akkor is létre kell hoznia egy tanúsítvány kötését. Kövesse a [kötés létrehozása](configure-ssl-bindings.md#create-binding)című témakör lépéseit.
>

## <a name="import-a-certificate-from-key-vault"></a>Tanúsítvány importálása Key Vault

Ha a Azure Key Vault használatával kezeli a tanúsítványokat, a PKCS12/pfx-profil-tanúsítványt Key Vaultból App Service importálhatja, feltéve, hogy [megfelel a követelményeknek](#private-certificate-requirements).

### <a name="authorize-app-service-to-read-from-the-vault"></a>App Service engedélyezése a tárolóból való olvasásra
Alapértelmezés szerint a App Service erőforrás-szolgáltató nem fér hozzá a Key Vaulthoz. Ha Key Vault szeretne használni a tanúsítvány telepítéséhez, engedélyeznie kell [az erőforrás-szolgáltatónak az olvasási hozzáférést a](../key-vault/general/assign-access-policy-cli.md)kulcstartóhoz. 

`abfa0a7c-a6b6-4736-8310-5855508787cd`  az erőforrás-szolgáltató egyszerű neve a App Servicehoz, és az összes Azure-előfizetés esetében azonos. Azure Government felhőalapú környezet esetében használja `6a02c803-dafd-4136-b4c3-5a6f318b4714` helyette az erőforrás-szolgáltatói szolgáltatás egyszerű nevét.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Tanúsítvány importálása a tárolóból az alkalmazásba

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>bal oldali menüjében válassza a app Services lehetőséget   >  **\<app-name>** .

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL**  >  **-Beállítások titkos kulcsú tanúsítványok (. pfx)**  >  **Key Vault tanúsítvány importálása** lehetőséget.

![Key Vault tanúsítvány importálása App Service](./media/configure-ssl-certificate/import-key-vault-cert.png)

A következő táblázat segítségével választhatja ki a tanúsítványt.

| Beállítás | Leírás |
|-|-|
| Előfizetés | Az előfizetés, amelyhez a Key Vault tartozik. |
| Key Vault | Az importálni kívánt tanúsítvánnyal rendelkező tároló. |
| Tanúsítvány | Válassza ki a PKCS12/pfx-profil-tanúsítványok listáját a tárolóban. A tár összes PKCS12/pfx-profil-tanúsítványa szerepel a ujjlenyomatai megfelelnek, de a App Serviceban nem támogatottak. |

Ha a művelet befejeződik, megjelenik a tanúsítvány a **titkos kulcs tanúsítványainak** listájában. Ha az importálás hibát jelez, a tanúsítvány nem felel meg a [app Service követelményeinek](#private-certificate-requirements).

![A tanúsítvány importálása Key Vault befejeződött](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Ha Key Vault új tanúsítvánnyal frissíti a tanúsítványt, a App Service 48 órán belül automatikusan szinkronizálja a tanúsítványt.

> [!IMPORTANT] 
> Ha egy egyéni tartományt ezzel a tanúsítvánnyal kíván biztonságossá tenni, akkor is létre kell hoznia egy tanúsítvány kötését. Kövesse a [kötés létrehozása](configure-ssl-bindings.md#create-binding)című témakör lépéseit.
>

## <a name="upload-a-private-certificate"></a>Privát tanúsítvány feltöltése

Miután beszerezte a tanúsítványt a hitelesítésszolgáltatótól, kövesse az ebben a szakaszban ismertetett lépéseket, hogy készen álljon a App Servicere.

### <a name="merge-intermediate-certificates"></a>Köztes tanúsítványok egyesítése

Ha a hitelesítésszolgáltató több tanúsítványt biztosít a tanúsítványláncban, sorrendben kell egyesítenie a tanúsítványokat.

Ehhez nyissa meg a kapott tanúsítványokat egy szövegszerkesztőben.

Hozzon létre egy _mergedcertificate.crt_ nevű fájlt az egyesített tanúsítvány számára. Egy szövegszerkesztőben másolja ebbe a fájlba az egyes tanúsítványok tartalmát. A tanúsítványok sorrendjének egyeznie kell a tanúsítványláncban lévő sorrenddel, a saját tanúsítvánnyal kezdve és a főtanúsítvánnyal végződve. Az alábbi példához hasonlóan néz ki:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Tanúsítvány exportálása PFX-fájlba

Exportálja az egyesített TLS/SSL-tanúsítványt annak a titkos kulcsnak a használatával, amelyhez a tanúsítványkérelem létrejött.

Ha OpenSSL használatával hozta létre a tanúsítványkérést, akkor létrehozott egy titkoskulcsfájlt. A tanúsítvány PFX-fájlba exportáláshoz futtassa az alábbi parancsot. Cserélje le a helyőrzők _&lt; titkos kulcs-fájl>_ és az _&lt; egyesített-Certificate-file>_ a titkos kulcs és az egyesített tanúsítványfájl elérési útjaira.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Amikor a rendszer megkéri, adjon meg egy exportálási jelszót. Ezt a jelszót fogja használni a TLS/SSL-tanúsítvány későbbi App Service való feltöltésekor.

Ha az IIS vagy a _Certreq.exe_ használatával hozta létre a tanúsítványkérést, telepítse a tanúsítványt a helyi számítógépre, majd [exportálja a tanúsítványt PFX-fájlba](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Tanúsítvány feltöltése a App Serviceba

Most már készen áll a tanúsítvány feltöltésére App Service.

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>bal oldali menüjében válassza a app Services lehetőséget   >  **\<app-name>** .

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL**  >  **-Beállítások titkos kulcsú tanúsítványok (. pfx)**  >  **feltöltési tanúsítvány** elemet.

![Privát tanúsítvány feltöltése App Service](./media/configure-ssl-certificate/upload-private-cert.png)

A **PFX Certificate File** (PFX-tanúsítványfájl) mezőben válassza ki a PFX-fájlt. A **Certificate password** (Tanúsítvány jelszava) területen írja be a PFX-fájl exportálásakor létrehozott jelszót. Ha elkészült, kattintson a **feltöltés** gombra. 

Ha a művelet befejeződik, megjelenik a tanúsítvány a **titkos kulcs tanúsítványainak** listájában.

![A tanúsítvány feltöltése befejeződött](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Ha egy egyéni tartományt ezzel a tanúsítvánnyal kíván biztonságossá tenni, akkor is létre kell hoznia egy tanúsítvány kötését. Kövesse a [kötés létrehozása](configure-ssl-bindings.md#create-binding)című témakör lépéseit.
>

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

A nyilvános tanúsítványokat *. cer* formátumban támogatja a rendszer. 

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>bal oldali menüjében válassza a app Services lehetőséget   >  **\<app-name>** .

Az alkalmazás bal oldali navigációs sávján kattintson a **TLS/SSL**  >  **-Beállítások nyilvános tanúsítványok (. cer)** nyilvános  >  **kulcsú tanúsítvány feltöltése** elemre.

A **név** mezőben adja meg a tanúsítvány nevét. A **CER-tanúsítvány fájljában** válassza ki a CER-fájlt.

Kattintson a **Feltöltés** gombra.

![Nyilvános tanúsítvány feltöltése App Service](./media/configure-ssl-certificate/upload-public-cert.png)

A tanúsítvány feltöltése után másolja a tanúsítvány ujjlenyomatát, és tekintse meg [a tanúsítvány elérhetővé tételét](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>App Service tanúsítványok kezelése

Ebből a szakaszból megtudhatja, hogyan kezelheti a [app Service-tanúsítvány importálása](#import-an-app-service-certificate)során megvásárolt app Service-tanúsítványokat.

- [Kulcs újragenerálásának tanúsítványa](#rekey-certificate)
- [Tanúsítvány megújítása](#renew-certificate)
- [Tanúsítvány exportálása](#export-certificate)
- [Tanúsítvány törlése](#delete-certificate)

### <a name="rekey-certificate"></a>Kulcs újragenerálásának tanúsítványa

Ha úgy gondolja, hogy a tanúsítvány titkos kulcsa sérült, visszaállíthatja a tanúsítványt. Válassza ki a tanúsítványt a [app Service tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd a bal oldali navigációs sávon válassza a **regenerálás és szinkronizálás** lehetőséget.

A folyamat elindításához kattintson a **generálás** gombra. Ez a folyamat 1-10 percet is igénybe vehet.

![App Service tanúsítvány újragenerálása](./media/configure-ssl-certificate/rekey-app-service-cert.png)

A tanúsítvány újraírásával a tanúsítvány egy új, a hitelesítésszolgáltatótól kiállított tanúsítvánnyal rendelkezik.

Az újragenerálási művelet befejezése után kattintson a **szinkronizálás** elemre. A szinkronizálási művelet automatikusan frissíti a tanúsítványhoz tartozó állomásnév-kötéseket App Service anélkül, hogy leállást okozna az alkalmazásai számára.

> [!NOTE]
> Ha nem kattint a **szinkronizálás** gombra, a app Service 48 órán belül automatikusan szinkronizálja a tanúsítványt.

### <a name="renew-certificate"></a>Tanúsítvány megújítása

Ha bármikor be szeretné kapcsolni a tanúsítvány automatikus megújítását, válassza ki a tanúsítványt a [app Service tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd a bal oldali navigációs menüben kattintson a **beállítások automatikus megújítása** elemre. Alapértelmezés szerint az App Service-tanúsítványok egy éves érvényességi időtartammal rendelkeznek.

Válassza **a** be lehetőséget, majd kattintson a **Mentés** gombra. A tanúsítványok a lejárat előtt 30 nappal automatikusan megújítva, ha az automatikus megújítás be van kapcsolva.

![App Service tanúsítvány automatikus megújítása](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Ha ehelyett manuálisan szeretné megújítani a tanúsítványt, kattintson a **manuális megújítás** elemre. A tanúsítvány lejárata előtt manuálisan megújíthatja a tanúsítványt 60 nappal.

Miután befejeződött a megújítási művelet, kattintson a **szinkronizálás** elemre. A szinkronizálási művelet automatikusan frissíti a tanúsítványhoz tartozó állomásnév-kötéseket App Service anélkül, hogy leállást okozna az alkalmazásai számára.

> [!NOTE]
> Ha nem kattint a **szinkronizálás** gombra, a app Service 48 órán belül automatikusan szinkronizálja a tanúsítványt.

### <a name="export-certificate"></a>Tanúsítvány exportálása

Mivel az App Service-tanúsítvány [Key Vault titkos kulcs](../key-vault/general/about-keys-secrets-certificates.md), exportálhatja azt, és más Azure-szolgáltatásokhoz vagy az Azure-on kívül is használhatja.

Ha a App Service-tanúsítvány PFX-fájlként szeretné exportálni, futtassa az alábbi parancsokat a [Cloud Shell](https://shell.azure.com). Ha az [Azure CLI-t telepítette](/cli/azure/install-azure-cli), helyileg is futtathatja. Cserélje le a helyőrzőket az [app Service-tanúsítvány létrehozásakor](#start-certificate-order)használt nevekre.

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

A letöltött *appservicecertificate. pfx* fájl egy nyers PKCS12/pfx-profil-fájl, amely a nyilvános és a privát tanúsítványokat is tartalmazza. Minden egyes promptnál használjon üres karakterláncot az importálási jelszó és a PEM-pass kifejezéshez.

### <a name="delete-certificate"></a>Tanúsítvány törlése 

Egy App Service tanúsítvány törlése végleges és visszafordíthatatlan. App Service-tanúsítvány erőforrás törlésének eredménye a visszavont tanúsítvány. A tanúsítvány App Serviceban lévő kötések érvénytelenné válnak. A véletlen törlés megelőzése érdekében az Azure zárolást helyez el a tanúsítványon. App Service tanúsítvány törléséhez először el kell távolítania a tanúsítvány törlési zárolását.

Válassza ki a tanúsítványt a [app Service tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd a bal oldali navigációs sávon válassza a **zárolások** lehetőséget.

Keresse meg a tanúsítvány zárolását a **delete** zárolási típussal. A jobb oldalon válassza a **Törlés** lehetőséget.

![App Service tanúsítvány zárolásának törlése](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Most törölheti a App Service tanúsítványt. A bal oldali navigációs sávon válassza az **Áttekintés**  >  **Törlés** lehetőséget. A megerősítő párbeszédpanelen írja be a tanúsítvány nevét, majd kattintson az **OK gombra**.

## <a name="automate-with-scripts&quot;></a>Automatizálás szkriptekkel

### <a name=&quot;azure-cli&quot;></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 &quot;Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>További erőforrások

* [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure App Service](configure-ssl-bindings.md)
* [HTTPS kényszerítése](configure-ssl-bindings.md#enforce-https)
* [A TLS 1.1/1.2 kényszerítése](configure-ssl-bindings.md#enforce-tls-versions)
* [TLS-/SSL-tanúsítvány használata a kódban az Azure App Service-ben](configure-ssl-certificate-in-code.md)
* [Gyakori kérdések: App Service tanúsítványok](./faq-configuration-and-management.md)
