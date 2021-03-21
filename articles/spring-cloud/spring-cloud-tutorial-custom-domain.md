---
title: Oktatóanyag – meglévő egyéni tartomány leképezése az Azure Spring Cloud-ra
description: Meglévő egyéni Distributed Name Service-(DNS-) név leképezése az Azure Spring Cloud-ra
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6b2134ffd414aed3ed6561fb34544094eb52baa0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452475"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Meglévő egyéni tartomány leképezése az Azure Spring Cloud-ra

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

A tartománynév-szolgáltatás (DNS) a hálózati csomópontok nevének hálózaton belüli tárolására szolgáló módszer. Ez az oktatóanyag egy tartományt, például a www.contoso.com-t egy CNAME-rekord használatával képezi le. Az egyéni tartományt egy tanúsítvánnyal védi, és bemutatja, hogyan lehet kényszeríteni a Transport Layer Security (TLS), más néven SSL (SSL) használatát. 

A tanúsítványok titkosítják a webes forgalmat. Ezek a TLS/SSL-tanúsítványok a Azure Key Vault tárolhatók. 

## <a name="prerequisites"></a>Előfeltételek
* Azure Spring Cloud üzembe helyezett alkalmazás (lásd: gyors útmutató [: meglévő Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](spring-cloud-quickstart.md), vagy meglévő alkalmazás használata).
* Egy tartománynév, amely hozzáféréssel rendelkezik a tartományi szolgáltató DNS-beállításjegyzékéhez, például a GoDaddyhez.
* Privát tanúsítvány (azaz önaláírt tanúsítvány) egy harmadik féltől származó szolgáltatótól. A tanúsítványnak meg kell egyeznie a tartománnyal.
* [Azure Key Vault](../key-vault/general/overview.md) üzembe helyezett példánya

## <a name="import-certificate"></a>Tanúsítvány importálása
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>A tanúsítványfájl előkészítése PFX-formátumban (nem kötelező)
Azure Key Vault támogatja a privát tanúsítvány importálását PEM és PFX formátumban. Ha a hitelesítésszolgáltatótól beszerzett PEM-fájl nem működik az alábbi szakaszban: a [tanúsítvány mentése a Key Vaultban](#save-certificate-in-key-vault), kövesse az itt leírt lépéseket a pfx létrehozásához Azure Key Vaulthoz.

#### <a name="merge-intermediate-certificates"></a>Köztes tanúsítványok egyesítése

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

#### <a name="export-certificate-to-pfx"></a>Tanúsítvány exportálása PFX-fájlba

Exportálja az egyesített TLS/SSL-tanúsítványt annak a titkos kulcsnak a használatával, amelyhez a tanúsítványkérelem létrejött.

Ha OpenSSL használatával hozta létre a tanúsítványkérést, akkor létrehozott egy titkoskulcsfájlt. A tanúsítvány PFX-fájlba exportáláshoz futtassa az alábbi parancsot. Cserélje le a helyőrzők _&lt; titkos kulcs-fájl>_ és az _&lt; egyesített-Certificate-file>_ a titkos kulcs és az egyesített tanúsítványfájl elérési útjaira.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Amikor a rendszer megkéri, adjon meg egy exportálási jelszót. Ezt a jelszót fogja használni a TLS/SSL-tanúsítvány későbbi Azure Key Vault való feltöltésekor.

Ha az IIS vagy a _Certreq.exe_ használatával hozta létre a tanúsítványkérést, telepítse a tanúsítványt a helyi számítógépre, majd [exportálja a tanúsítványt PFX-fájlba](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Tanúsítvány mentése Key Vault
A tanúsítvány importálására vonatkozó eljáráshoz a PEM-vagy PFX-kódolású fájlnak lemezen kell lennie, és rendelkeznie kell a titkos kulccsal. 
#### <a name="portal"></a>[Portál](#tab/Azure-portal)
A tanúsítvány feltöltése a Key vaultba:
1. Nyissa meg a Key Vault-példányt.
1. A bal oldali navigációs ablaktáblán kattintson a **tanúsítványok** elemre.
1. A felső menüben kattintson a **készítés/importálás** elemre.
1. A **tanúsítvány létrehozása** párbeszédpanel **tanúsítvány létrehozása módjában** válassza a lehetőséget `Import` .
1. A **tanúsítványfájl feltöltése** területen navigáljon a tanúsítvány helyére, és jelölje ki.
1. A **jelszó** területen adja meg a tanúsítvány titkos kulcsát.
1. Kattintson a **Létrehozás** lehetőségre.

    ![1. tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Azure Spring Cloud-hozzáférés biztosítása a Key vaulthoz

A tanúsítvány importálása előtt Azure Spring Cloud-hozzáférést kell biztosítania a kulcstartóhoz:
#### <a name="portal"></a>[Portál](#tab/Azure-portal)
1. Nyissa meg a Key Vault-példányt.
1. A bal oldali navigációs panelen kattintson a **hozzáférési rendőr** elemre.
1. A felső menüben kattintson a **hozzáférési szabályzat hozzáadása** elemre.
1. Töltse ki az adatokat, és kattintson a **Hozzáadás** gombra, majd **mentse** a hozzáférési rendőrséget.

| Titkos engedély | Tanúsítvány engedélye | Rendszerbiztonsági tag kiválasztása |
|--|--|--|
| Lekérés, Listázás | Lekérés, Listázás | Azure Spring Cloud Domain-Management |

![2. tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

Adja meg az Azure Spring Cloud olvasási hozzáférését a Key vaulthoz, és cserélje le a `<key vault resource group>` és a `<key vault name>` parancsot a következő parancsra.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Tanúsítvány importálása az Azure Spring Cloud-ba
#### <a name="portal"></a>[Portál](#tab/Azure-portal)
1. Lépjen a szolgáltatási példányra. 
1. Az alkalmazás bal oldali navigációs paneljén válassza a **TLS/SSL-beállítások** lehetőséget.
1. Ezután kattintson **Key Vault tanúsítvány importálása** elemre.

    ![Tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate.png)

1. Miután sikeresen importálta a tanúsítványt, megjelenik a **titkos kulcsokra vonatkozó tanúsítványok** listájában.

    ![Titkos kulcs tanúsítványa](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

Az importált tanúsítványok listájának megjelenítése:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> Ha egy egyéni tartományt ezzel a tanúsítvánnyal kíván biztonságossá tenni, akkor továbbra is egy adott tartományhoz kell kötnie a tanúsítványt. Kövesse az ebben a szakaszban található lépéseket: [SSL-kötés hozzáadása](#add-ssl-binding).

## <a name="add-custom-domain"></a>Egyéni tartomány hozzáadása
CNAME rekord használatával képezhető le egyéni DNS-nevet az Azure Spring Cloud-ra. 

> [!NOTE] 
> Az A rekord nem támogatott. 

### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása
Nyissa meg a DNS-szolgáltatót, és adjon hozzá egy CNAME-rekordot a tartománynak a <service_name>. azuremicroservices.io való leképezéséhez. Itt <service_name> az Azure Spring Cloud-példány neve. A helyettesítő karakteres tartományt és altartományt is támogatjuk. A CNAME hozzáadása után a DNS-rekordok lap a következő példához hasonló lesz: 

![DNS-rekordok oldala](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Egyéni tartomány leképezése az Azure Spring Cloud-alkalmazásra
Ha nem rendelkezik alkalmazással az Azure Spring Cloud-ban, kövesse a rövid útmutató [: meglévő Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)című témakör utasításait.

#### <a name="portal"></a>[Portál](#tab/Azure-portal)
Ugrás az alkalmazás oldalra.

1. Válassza az **egyéni tartomány** lehetőséget.
2. Ezután **adja hozzá az egyéni tartományt**. 

    ![Egyéni tartomány](./media/custom-dns-tutorial/custom-domain.png)

3. Írja be a teljes tartománynevet, amelyhez hozzáadott egy CNAME rekordot (például www.contoso.com). Győződjön meg arról, hogy az állomásnév bejegyzéstípusa CNAME értékre van beállítva (<service_name>. azuremicroservices.io)
4. Az **Érvényesítés** gombra kattintva engedélyezheti a **Hozzáadás** gombot.
5. Kattintson a **Hozzáadás** parancsra.

    ![Egyéni tartomány hozzáadása](./media/custom-dns-tutorial/add-custom-domain.png)

Egy alkalmazás több tartománnyal is rendelkezhet, de egy tartomány csak egyetlen alkalmazáshoz képezhető le. Ha sikeresen leképezte az egyéni tartományt az alkalmazáshoz, az egyéni tartomány táblán fog megjelenni.

![Egyéni tartomány tábla](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

Az egyéni tartományok listájának megjelenítése:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nincs SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba érkező HTTPS-kérések hibaüzenetet vagy figyelmeztetést kapnak.

## <a name="add-ssl-binding"></a>SSL-kötés hozzáadása

#### <a name="portal"></a>[Portál](#tab/Azure-portal)
Az egyéni tartomány táblában válassza az **SSL-kötés hozzáadása** lehetőséget az előző ábrán látható módon.  
1. Válassza ki a **tanúsítványt** , vagy importálja.
1. Kattintson a **Mentés** gombra.

    ![SSL-kötés hozzáadása 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

Az SSL-kötés sikeres hozzáadása után a tartomány állapota biztonságos lesz: **kifogástalan**. 

![SSL-kötés hozzáadása 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS kényszerítése
Alapértelmezés szerint bárki megtekintheti az alkalmazást HTTP-n keresztül, de az összes HTTP-kérelmet átirányíthatja a HTTPS-portra.
#### <a name="portal"></a>[Portál](#tab/Azure-portal)
Az alkalmazás lapjának bal oldali navigációs sávján válassza az **egyéni tartomány** lehetőséget. Ezt követően **csak a https**-t állítsa *igaz* értékre.

![3. SSL-kötés hozzáadása](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
Ha a művelet befejeződött, navigáljon az alkalmazására mutató HTTPS URL-címek bármelyikéhez. Vegye figyelembe, hogy a HTTP-URL-címek nem működnek.

## <a name="see-also"></a>Lásd még
* [Mi az Azure Key Vault?](../key-vault/general/overview.md)
* [Tanúsítvány importálása](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [A Spring Cloud-alkalmazás elindítása az Azure CLI használatával](./spring-cloud-quickstart.md)
