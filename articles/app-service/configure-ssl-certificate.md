---
title: TLS-/SSL-tanúsítványok hozzáadása és kezelése
description: Hozzon létre egy ingyenes tanúsítványt, importáljon App Service tanúsítványt, importáljon egy Key Vault-tanúsítványt, vagy vásároljon App Service tanúsítványt a Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 03/02/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1e05435f364cc30b351275439a04caff47c35512
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871794"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>TLS-/SSL-tanúsítvány hozzáadása az Azure App Service-ben

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez a cikk bemutatja, hogyan hozhat létre, tölthet fel vagy importálhat privát tanúsítványt vagy nyilvános tanúsítványt a App Service. 

Miután hozzáadta a tanúsítványt a App Service [](../azure-functions/index.yml)vagy függvényalkalmazáshoz, biztosíthatja vele az egyéni [DNS-név](configure-ssl-bindings.md) biztonságát, vagy használhatja azt az [alkalmazáskódban.](configure-ssl-certificate-in-code.md)

> [!NOTE]
> Az alkalmazásba feltöltött tanúsítványokat az alkalmazás erőforráscsoportja és régiója (belső neve webtér) kombinációjához kötött üzembe helyezési egység *tárolja.* Ez elérhetővé teszi a tanúsítványt az azonos erőforráscsoport- és régiókombinációban található más alkalmazások számára. 

Az alábbi táblázat a tanúsítványok hozzáadásának lehetőségeit sorolja fel a App Service:

|Beállítás|Leírás|
|-|-|
| Ingyenes felügyelt App Service létrehozása (előzetes verzió) | Ingyenes és könnyen használható privát tanúsítvány, ha csak az egyéni [](app-service-web-tutorial-custom-domain.md) tartományt kell biztonságossá App Service. |
| Tanúsítvány App Service vásárlása | Az Azure által felügyelt privát tanúsítvány. Egyesíti az automatizált tanúsítványkezelés egyszerűségét, valamint a megújítási és exportálási lehetőségek rugalmasságát. |
| Tanúsítvány importálása a Key Vault | Akkor [hasznos,](../key-vault/index.yml) ha Azure Key Vault [PKCS12-tanúsítványokat kezel.](https://wikipedia.org/wiki/PKCS_12) Lásd: [Privát tanúsítványkövetelmények.](#private-certificate-requirements) |
| Privát tanúsítvány feltöltése | Ha már rendelkezik egy külső szolgáltatótól származó privát tanúsítvánnyal, feltöltheti. Lásd: [Privát tanúsítványkövetelmények.](#private-certificate-requirements) |
| Nyilvános tanúsítvány feltöltése | A nyilvános tanúsítványok nem használhatók egyéni tartományok biztonságossá rendelésre, de betöltheti őket a kódba, ha távoli erőforrásokhoz való hozzáféréshez van szüksége rájuk. |

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy App Service alkalmazást.](./index.yml)
- Privát tanúsítványhoz győződjön meg arról, hogy az megfelel az összes követelménynek a [App Service.](#private-certificate-requirements)
- **Csak ingyenes tanúsítvány:**
    - Leképezi a tartományt, amely számára tanúsítványt szeretne App Service. További információ: [Oktatóanyag: Meglévő egyéni DNS-név leképezése a Azure App Service.](app-service-web-tutorial-custom-domain.md)
    - Gyökértartományok (például contoso.com ellenőrizze, hogy az alkalmazáshoz nincs-e [konfigurálva IP-korlátozás.](app-service-ip-restrictions.md) A tanúsítvány létrehozása és a gyökértartomány rendszeres megújítása attól függ, hogy az alkalmazás elérhető-e az internetről.

## <a name="private-certificate-requirements"></a>A privát tanúsítványra vonatkozó követelmények

Az [ingyenes App Service tanúsítvány és](#create-a-free-managed-certificate-preview) [a App Service tanúsítvány](#import-an-app-service-certificate) már megfelel az App Service. Ha úgy dönt, hogy feltölt vagy importál egy privát tanúsítványt a App Service, a tanúsítványnak az alábbi követelményeknek kell megfelelnie:

* Jelszóval [védett PFX-fájlként](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)exportálva, háromszoros DES használatával titkosítva.
* Legalább 2048 bit hosszúságú titkos kulcsot kell tartalmaznia.
* Tartalmaznia kell a tanúsítványláncban lévő összes köztes tanúsítványt.

Egy egyéni tartomány TLS-kötésben való biztonságossá rendeléséhez a tanúsítványra további követelmények vonatkoznak:

* Kibővített [kulcshasználatot](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) tartalmaz a kiszolgálóhitelesítéshez (OID = 1.3.6.1.5.5.7.3.1)
* A tanúsítványt megbízható hitelesítésszolgáltatónak kell aláírnia.

> [!NOTE]
> **Az elliptikus görbéjű titkosítási (ECC-) tanúsítványok** együttműködnek az App Service-szel, ez a cikk azonban erre nem tér ki. A hitelesítésszolgáltatóval együttműködve dolgozzák ki az ECC-tanúsítványok létrehozására szolgáló lépéseket.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-managed-certificate-preview"></a>Ingyenes felügyelt tanúsítvány létrehozása (előzetes verzió)

> [!NOTE]
> Ingyenes felügyelt tanúsítvány létrehozása előtt győződjön [](#prerequisites) meg arról, hogy megfelel az alkalmazás előfeltételeinek.

Az ingyenes App Service tanúsítvány egy kulcsrakulcsos megoldás az egyéni DNS-név biztonságossá App Service. Ez egy teljesen működőképes TLS-/SSL-tanúsítvány, amelyet a App Service automatikusan újít meg. Az ingyenes tanúsítványra a következő korlátozások vonatkoznak:

- Nem támogatja a helyettesítő tanúsítványokat.
- Nem exportálható.
- A nem támogatott a App Service Environment (ASE) esetén.
- Az integrált gyökértartományok esetén nem Traffic Manager.

> [!NOTE]
> Az ingyenes tanúsítványt a DigiCert bocsátotta ki. Egyes legfelső szintű tartományok esetén explicit módon engedélyeznie kell a DigiCertet tanúsítványkiállítóként, ha létrehoz egy [CAA-tartományrekordot](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) a következő értékkel: `0 issue digicert.com` .
> 

A bal <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menüben válassza a App Services  >  **\<app-name>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások** Titkoskulcs-tanúsítványok  >  **(.pfx)** Létrehozása felügyelt  >  **App Service lehetőséget.**

![Ingyenes tanúsítvány létrehozása a App Service](./media/configure-ssl-certificate/create-free-cert.png)

Válassza ki azt az egyéni tartományt, amely számára létre fog hozni egy ingyenes tanúsítványt, majd válassza a **Létrehozás lehetőséget.** Minden támogatott egyéni tartományhoz csak egy tanúsítványt hozhat létre.

A művelet befejezése után a tanúsítvány megjelenik a Titkoskulcs-tanúsítványok **listában.**

![Az ingyenes tanúsítvány létrehozása befejeződött](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Ha ezzel a tanúsítvánnyal egy egyéni tartományt is meg kell biztonságossá hoznia, akkor is létre kell hoznia egy tanúsítványkötést. Kövesse a Kötés [létrehozása lépéseit.](configure-ssl-bindings.md#create-binding)
>

## <a name="import-an-app-service-certificate"></a>Importálás App Service-tanúsítvány

Ha az Azure-App Service-tanúsítvány vásárol, az Azure a következő feladatokat kezeli:

- Gondoskodik a GoDaddytől származó vásárlási folyamatról.
- Elvégzi a tanúsítvány tartomány-ellenőrzését.
- A tanúsítványt a következő [Azure Key Vault:](../key-vault/general/overview.md).
- Kezeli a tanúsítvány megújítását [(lásd: Tanúsítvány megújítása).](#renew-certificate)
- Szinkronizálja automatikusan a tanúsítványt az importált másolatokkal App Service alkalmazásokban.

Ha tanúsítványt App Service, a Start certificate order (Tanúsítványrendelés [kezdése) menüben vásárolhat.](#start-certificate-order)

Ha már rendelkezik működő App Service tanúsítvánnyal, a következőt használhatja:

- [Importálja a tanúsítványt a App Service.](#import-certificate-into-app-service)
- [Kezelje a tanúsítványt,](#manage-app-service-certificates)például a megújítást, a kulcs kulcsát, majd exportálja azt.
> [!NOTE]
> App Service-tanúsítványok jelenleg nem támogatottak az Azure országos felhőiben.

### <a name="start-certificate-order"></a>Tanúsítványrendelés kezdése

Indítsa el a App Service rendelését a <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service-tanúsítvány lapon.</a>

![Tanúsítvány App Service kezdése](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Az alábbi táblázat segítséget a tanúsítvány konfigurálásában segít. Ha végzett, kattintson a **Létrehozás** gombra.

| Beállítás | Leírás |
|-|-|
| Név | A tanúsítvány rövid App Service neve. |
| Csupasz tartomány gazdaneve | Itt adja meg a gyökértartományt. A kiállított tanúsítvány a *gyökértartományt* és az altartományt `www` is biztonságossá biztosítja. A kiállított tanúsítvány köznév mezőjében a gyökértartomány, a Tulajdonos alternatív neve mezőben pedig a `www` tartomány található. Csak az altartományok biztonságossá minősítéséhez adja meg az altartomány teljes tartománynevét `mysubdomain.contoso.com` (például).|
| Előfizetés | A tanúsítványt tartalmazó előfizetés. |
| Erőforráscsoport | A tanúsítványt tartalmazó erőforráscsoport. Használhat új erőforráscsoportot, vagy választhatja ki például ugyanazt az erőforráscsoportot, App Service az alkalmazásból. |
| Tanúsítvány termékváltozata | Meghatározza a létrehozható tanúsítvány típusát, illetve azt, hogy egy szabványos tanúsítvány vagy [helyettesítő tanúsítvány.](https://wikipedia.org/wiki/Wildcard_certificate) |
| Jogi feltételek | Kattintson ide annak megerősítéséhez, hogy elfogadja a jogi feltételeket. A tanúsítványok a GoDaddytől szerezhetők be. |

> [!NOTE]
> App Service Azure-ban vásárolt tanúsítványokat a GoDaddy bocsátotta ki. Egyes legfelső szintű tartományok esetén explicit módon engedélyeznie kell a GoDaddyt tanúsítványkiállítóként egy [CAA-tartományrekord](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) létrehozásával a következő értékkel: `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Tárolás Azure Key Vault

A tanúsítványvásárlási folyamat befejezése után néhány további lépést is el kell végrehajtania a tanúsítvány használatának elkezdődhet. 

Válassza ki a tanúsítványt a [Tanúsítványok App Service lapon,](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) majd kattintson a **Tanúsítványkonfiguráció**  >  **1. lépés: Tárolás elemre.**

![A Key Vault tanúsítvány App Service konfigurálása](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) azure-szolgáltatás segít a felhőalkalmazások és -szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Ezt a tárolót választhatja a App Service számára.

A Key Vault **lapon** kattintson a Key Vault **elemre** egy új tároló létrehozásához, vagy válasszon ki egy meglévő tárolót. Ha úgy dönt, hogy új tárolót hoz létre, a következő táblázat segítségével konfigurálhatja a tárolót, majd kattintson a Létrehozás gombra. Hozza létre az új Key Vault ugyanabban az előfizetésben és erőforráscsoportban, mint az App Service alkalmazás.

| Beállítás | Leírás |
|-|-|
| Név | Alfanumerikus karaktereket és kötőjeleket tartalmazó egyedi név. |
| Erőforráscsoport | Javasoljuk, hogy válassza ki ugyanazt az erőforráscsoportot, mint a App Service tanúsítvány. |
| Hely | Válassza ki ugyanazt a helyet, mint App Service alkalmazás. |
| Tarifacsomag | További információt a [díjszabási Azure Key Vault talál.](https://azure.microsoft.com/pricing/details/key-vault/) |
| Hozzáférési szabályzatok| Meghatározza az alkalmazásokat és a tároló erőforrásaihoz való engedélyezett hozzáférést. Ezt később is konfigurálhatja a Következő lépésekkel: Key Vault [hozzáférési szabályzat hozzárendelése.](../key-vault/general/assign-access-policy-portal.md) |
| Virtual Network Access | A tároló hozzáférésének korlátozása bizonyos Azure-beli virtuális hálózatokra. Ezt később is konfigurálhatja a Tűzfalak és virtuális hálózatok konfigurálása [Azure Key Vault lépéseit követve.](../key-vault/general/network-security.md) |

Miután kiválasztotta a tárolót, zárja be a Key Vault **adattár lapját.** Az **1. lépés: Tárolás lehetőségnél** zöld pipának kell lennie a sikeresség érdekében. A következő lépéshez ne nyissa meg az oldalt.

### <a name="verify-domain-ownership"></a>A tartomány tulajdonjogának igazolása

Az előző **lépésben használt Tanúsítványkonfiguráció** lapon kattintson a **2. lépés: Ellenőrzés elemre.**

![A tanúsítvány tartományának App Service ellenőrzése](./media/configure-ssl-certificate/verify-domain.png)

Válassza **az App Service ellenőrzésére lehetőséget.** Mivel a tartományt már leképezte a webalkalmazásra (lásd: [Előfeltételek),](#prerequisites)már ellenőrizve van. A lépés **befejezéséhez** egyszerűen kattintson az Ellenőrzés gombra. Kattintson **a Frissítés gombra,** amíg meg nem **jelenik a Tanúsítvány tartomány** által ellenőrzött üzenet.

> [!NOTE]
> A tartomány-ellenőrzési módszerek négy típusa támogatott: 
> 
> - **App Service** – A legkényelmesebb lehetőség, ha a tartomány már le van leképezve egy App Service alkalmazásra ugyanabban az előfizetésben. Kihasználja azt a tényt, hogy App Service alkalmazás már ellenőrizte a tartomány tulajdonjogát.
> - **Tartomány** – Ellenőrizze, [hogy App Service-e az Azure-ban vásárolt tartománynév.](manage-custom-dns-buy-domain.md) Az Azure automatikusan hozzáadja az ellenőrző TXT-rekordot, és befejezi a folyamatot.
> - **E-mail** – A tartomány ellenőrzéséhez küldjön egy e-mailt a tartományi rendszergazdának. Az utasítások a beállítás kiválasztásakor biztosítanak útmutatást.
> - **Manuális** – Ellenőrizze a tartományt egy HTML-oldal **(csak** standard tanúsítvány) vagy egy DNS TXT-rekord használatával. Az utasítások a beállítás kiválasztásakor biztosítanak útmutatást.

### <a name="import-certificate-into-app-service"></a>Tanúsítvány importálása a App Service

A bal <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menüben válassza a App Services  >  **\<app-name>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások**  >  **Titkoskulcs-tanúsítványok (.pfx)**  >  **Importálása** App Service-tanúsítvány.

![Importálja App Service tanúsítványt a App Service](./media/configure-ssl-certificate/import-app-service-cert.png)

Válassza ki az előbb vásárolt tanúsítványt, majd kattintson az **OK gombra.**

A művelet befejezése után a tanúsítvány megjelenik a Titkoskulcs-tanúsítványok **listában.**

![Az App Service importálása befejeződött](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Ha ezzel a tanúsítvánnyal egy egyéni tartományt is meg kell biztonságossá hoznia, akkor is létre kell hoznia egy tanúsítványkötést. Kövesse a Kötés [létrehozása lépéseit.](configure-ssl-bindings.md#create-binding)
>

## <a name="import-a-certificate-from-key-vault"></a>Tanúsítvány importálása a Key Vault

Ha a Azure Key Vault használja a tanúsítványok kezelésére, importálhat egy PKCS12-tanúsítványt a Key Vault-App Service, ha az megfelel a [követelményeknek.](#private-certificate-requirements)

### <a name="authorize-app-service-to-read-from-the-vault"></a>A App Service olvasásának jogosultsága
Alapértelmezés szerint a App Service erőforrás-szolgáltató nem rendelkezik hozzáféréssel a Key Vault. Ha tanúsítványtelepítéshez Key Vault használni, engedélyeznie kell az erőforrás-szolgáltatónak a [KeyVaulthoz való olvasási hozzáférését.](../key-vault/general/assign-access-policy-cli.md) 

`abfa0a7c-a6b6-4736-8310-5855508787cd`  A az erőforrás-szolgáltató egyszerű szolgáltatásneve a App Service, és minden Azure-előfizetés esetén ugyanaz. A Azure Government környezetben használja a `6a02c803-dafd-4136-b4c3-5a6f318b4714` nevet az erőforrás-szolgáltató egyszerű szolgáltatásneveként.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Tanúsítvány importálása a tárolóból az alkalmazásba

A bal <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menüben válassza a App Services  >  **\<app-name>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások** Titkos kulcsú tanúsítványok  >  **(.pfx)** Importálása Key Vault  >  **lehetőséget.**

![Importálja Key Vault tanúsítványt a App Service](./media/configure-ssl-certificate/import-key-vault-cert.png)

Az alábbi táblázat segítségével kiválaszthatja a tanúsítványt.

| Beállítás | Leírás |
|-|-|
| Előfizetés | Az előfizetés, amelyhez Key Vault előfizetés tartozik. |
| Key Vault | Az importálni kívánt tanúsítvánnyal együtt. |
| Tanúsítvány | Válasszon a tárolóban található PKCS12-tanúsítványok listájából. A tárolóban található összes PKCS12-tanúsítvány megjelenik az ujjlenyomataik között, de nem mindegyik támogatott a App Service. |

A művelet befejezése után a tanúsítvány megjelenik a Titkoskulcs-tanúsítványok **listában.** Ha az importálás hibával meghiúsul, a tanúsítvány nem felel meg a [App Service.](#private-certificate-requirements)

![A Key Vault importálása befejeződött](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Ha a tanúsítványt a Key Vault új tanúsítvánnyal frissíti, a App Service 24 órán belül automatikusan szinkronizálja a tanúsítványt.

> [!IMPORTANT] 
> Ahhoz, hogy ezzel a tanúsítvánnyal egy egyéni tartományt biztosítsunk, létre kell hoznia egy tanúsítványkötést. Kövesse a Kötés [létrehozása lépéseit.](configure-ssl-bindings.md#create-binding)
>

## <a name="upload-a-private-certificate"></a>Privát tanúsítvány feltöltése

Miután beszerez egy tanúsítványt a tanúsítványszolgáltatótól, az ebben a szakaszban található lépéseket követve készítse elő a tanúsítványt App Service.

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

Exportálja az egyesített TLS-/SSL-tanúsítványt azzal a titkos kulccsal, amelyből a tanúsítványkérelem létre lett hozva.

Ha OpenSSL használatával hozta létre a tanúsítványkérést, akkor létrehozott egy titkoskulcsfájlt. A tanúsítvány PFX-fájlba exportáláshoz futtassa az alábbi parancsot. Cserélje le a _&lt; private-key-file>_ és _&lt; merged-certificate-file>_ a titkos kulcs és az egyesített tanúsítványfájl elérési útjára.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Amikor a rendszer megkéri, adjon meg egy exportálási jelszót. Ezt a jelszót a TLS-/SSL-tanúsítvány későbbi feltöltésekor App Service használni.

Ha az IIS vagy a _Certreq.exe_ használatával hozta létre a tanúsítványkérést, telepítse a tanúsítványt a helyi számítógépre, majd [exportálja a tanúsítványt PFX-fájlba](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Tanúsítvány feltöltése App Service

Most már feltöltheti a tanúsítványt a App Service.

A bal <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menüben válassza a App Services  >  **\<app-name>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások** Titkos kulcsú  >  **tanúsítványok (.pfx)**  >  **Tanúsítvány feltöltése lehetőséget.**

![Privát tanúsítvány feltöltése a App Service](./media/configure-ssl-certificate/upload-private-cert.png)

A **PFX Certificate File** (PFX-tanúsítványfájl) mezőben válassza ki a PFX-fájlt. A **Certificate password** (Tanúsítvány jelszava) területen írja be a PFX-fájl exportálásakor létrehozott jelszót. Ha végzett, kattintson a **Feltöltés gombra.** 

A művelet befejezése után a tanúsítvány megjelenik a Titkoskulcs-tanúsítványok **listában.**

![Tanúsítvány feltöltése befejeződött](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Ha ezzel a tanúsítvánnyal egy egyéni tartományt is meg kell biztonságossá hoznia, akkor is létre kell hoznia egy tanúsítványkötést. Kövesse a Kötés [létrehozása lépéseit.](configure-ssl-bindings.md#create-binding)
>

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

A nyilvános tanúsítványok *.cer formátumban támogatottak.* 

A bal <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menüben válassza a App Services  >  **\<app-name>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján kattintson a **TLS/SSL-beállítások** Nyilvános tanúsítványok  >  **(.cer) Nyilvános** kulcsú tanúsítvány  >  **feltöltése elemre.**

A **Név mezőbe** írja be a tanúsítvány nevét. A **TANÚSÍTVÁNY-tanúsítványfájlban** válassza ki a TANÚSÍTVÁNYfájlt.

Kattintson a **Feltöltés** gombra.

![Nyilvános tanúsítvány feltöltése a App Service](./media/configure-ssl-certificate/upload-public-cert.png)

A tanúsítvány feltöltése után másolja ki a tanúsítvány ujjlenyomatát, és tekintse meg [a Tanúsítvány elérhetővé tétele adatokat.](configure-ssl-certificate-in-code.md#make-the-certificate-accessible)

## <a name="manage-app-service-certificates"></a>Az App Service tanúsítványok kezelése

Ez a szakasz bemutatja, hogyan kezelheti a App Service tanúsítvány importálása című részben vásárolt App Service [tanúsítványt.](#import-an-app-service-certificate)

- [Tanúsítvány kulcsának újrakulcslott kulcsának megk](#rekey-certificate)
- [Tanúsítvány megújítása](#renew-certificate)
- [Tanúsítvány exportálása](#export-certificate)
- [Tanúsítvány törlése](#delete-certificate)

### <a name="rekey-certificate"></a>Tanúsítvány kulcsának újrakulcslott kulcsának megk

Ha úgy gondolja, hogy a tanúsítvány titkos kulcsa sérült, újrakulcsosíthatja a tanúsítványt. Válassza ki a tanúsítványt a [Tanúsítványok App Service lapon,](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) majd a bal oldali navigációs sávon válassza az **Újrakulcs** és szinkronizálás lehetőséget.

Kattintson **a Rekey (Kulcs** újrakulcsa) gombra a folyamat elkezdéshez. A folyamat 1–10 percet is igénybe vehet.

![Új kulcs kulcsának App Service tanúsítványhoz](./media/configure-ssl-certificate/rekey-app-service-cert.png)

A tanúsítvány újrakulcsolása a tanúsítványt a hitelesítésszolgáltató által kiadott új tanúsítvánnyal összesít.

Ha az újrakulcsművelet befejeződött, kattintson a Szinkronizálás **gombra.** A szinkronizálási művelet automatikusan frissíti a tanúsítvány gazdagépnév-kötéseit a App Service anélkül, hogy ez állásidőt okoz az alkalmazásokban.

> [!NOTE]
> Ha nem kattint a Szinkronizálás **elemre,** a App Service 24 órán belül automatikusan szinkronizálja a tanúsítványt.

### <a name="renew-certificate"></a>Tanúsítvány megújítása

Ha bármikor be kell kapcsolnia a tanúsítvány automatikus megújítását, válassza ki a tanúsítványt a [App Service Tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd kattintson a beállítások **automatikus** megújítása elemre a bal oldali navigációs sávon. Alapértelmezés szerint a App Service tanúsítványok egyéves érvényességi időtartammal vannak megszabadva.

Válassza **a Be lehetőséget,** majd kattintson a **Mentés gombra.** A tanúsítványok a lejárat előtt 30 nappal automatikusan megújulnak, ha az automatikus megújítás be van kapcsolva.

![Tanúsítvány automatikus App Service megújítása](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Ha ehelyett manuálisan meg kell újítania a tanúsítványt, kattintson a **Manuális megújítás elemre.** Kérheti a tanúsítvány manuális megújítását a lejárat előtt 60 nappal.

A megújítási művelet befejezése után kattintson a Szinkronizálás **elemre.** A szinkronizálási művelet automatikusan frissíti a tanúsítvány gazdagépnév-kötéseit a App Service anélkül, hogy ez állásidőt okoz az alkalmazásokban.

> [!NOTE]
> Ha nem kattint a Szinkronizálás **elemre,** a App Service 24 órán belül automatikusan szinkronizálja a tanúsítványt.

### <a name="export-certificate"></a>Tanúsítvány exportálása

Mivel a App Service-tanúsítvány titkos [Key Vault,](../key-vault/general/about-keys-secrets-certificates.md)exportálhatja annak PFX-másolatát, és használhatja más Azure-szolgáltatásokhoz vagy az Azure-n kívül is.

Az App Service-tanúsítvány PFX-fájlként való exportáláshoz futtassa a következő parancsokat a [Cloud Shell.](https://shell.azure.com) Helyileg is futtathatja, ha telepítette az [Azure CLI-t.](/cli/azure/install-azure-cli) Cserélje le a helyőrzőket a tanúsítvány létrehozásakor használt [App Service nevére.](#start-certificate-order)

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

A letöltött *appservicecertificate.pfx* fájl egy nyers PKCS12-fájl, amely a nyilvános és a privát tanúsítványokat is tartalmazza. Minden kérdésben használjon egy üres sztringet az importálási jelszóhoz és a PEM pass kifejezéshez.

### <a name="delete-certificate"></a>Tanúsítvány törlése 

A tanúsítvány App Service végleges és nem visszafordítható. Egy új App Service-tanúsítvány törlése a tanúsítvány visszavonását vonja vissza. A tanúsítványsal App Service kötések érvénytelenné válnak. A véletlen törlés megelőzése érdekében az Azure zárolja a tanúsítványt. Az új App Service törléséhez először el kell távolítania a tanúsítvány törlési zárolását.

Válassza ki a tanúsítványt App Service [tanúsítványok lapon,](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) majd válassza a **zárolások** lehetőséget a bal oldali navigációs sávon.

Keresse meg a tanúsítvány zárolását Törlés **zárolástípussal.** A jobb oldalon válassza a **Törlés lehetőséget.**

![Tanúsítvány zárolásának App Service törlése](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Most már törölheti a App Service tanúsítványt. A bal oldali navigációs sávon válassza az **Áttekintés**  >  **törlése lehetőséget.** A megerősítő párbeszédpanelen írja be a tanúsítvány nevét, majd kattintson az **OK gombra.**

## <a name="automate-with-scripts&quot;></a>Automatizálás szkriptekkel

### <a name=&quot;azure-cli&quot;></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 &quot;Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>További erőforrások

* [Egyéni DNS-név biztosítása TLS/SSL-kötéssel a Azure App Service](configure-ssl-bindings.md)
* [HTTPS kényszerítése](configure-ssl-bindings.md#enforce-https)
* [A TLS 1.1/1.2 kényszerítése](configure-ssl-bindings.md#enforce-tls-versions)
* [TLS-/SSL-tanúsítvány használata a kódban az Azure App Service-ben](configure-ssl-certificate-in-code.md)
* [Gyakori kérdések: App Service tanúsítványok](./faq-configuration-and-management.md)
