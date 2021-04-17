---
title: 'Oktatóanyag: Meglévő egyéni tartomány leképezés Azure Spring Cloud'
description: Meglévő egyéni elosztott névszolgáltatás (DNS) nevének leképezése a Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 493752a3857b80b43668b6bf1b20480604442955
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567995"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>Oktatóanyag: Meglévő egyéni tartomány leképezés Azure Spring Cloud

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

A tartománynév-szolgáltatás (DNS) a hálózati csomópontok nevének a hálózaton való tárolására szolgáló technika. Ez az oktatóanyag leképez egy tartományt, például www.contoso.com CNAME rekord használatával. Tanúsítvánnyal védi az egyéni tartományt, és bemutatja, hogyan kényszeríthető Transport Layer Security (TLS), más néven SSL (SSL). 

A tanúsítványok titkosítják a webes forgalmat. Ezek a TLS-/SSL-tanúsítványok tárolhatók a Azure Key Vault. 

## <a name="prerequisites"></a>Előfeltételek
* A Azure Spring Cloud üzembe helyezett alkalmazás (lásd: Rövid [útmutató:](spring-cloud-quickstart.md)Meglévő Azure Spring Cloud indítása a Azure Portal használatával, vagy egy meglévő alkalmazás használata).
* Egy tartománynév, amely hozzáféréssel rendelkezik a tartományszolgáltató (például a GoDaddy) DNS-beállításjegyzékéhez.
* Egy külső szolgáltatótól származó privát tanúsítvány (azaz az Ön önaírt tanúsítványa). A tanúsítványnak egyeznie kell a tartománnyal.
* A virtuális gép üzembe [helyezett Azure Key Vault](../key-vault/general/overview.md)

## <a name="keyvault-private-link-considerations"></a>A Keyvault Private Link szempontjai

A Azure Spring Cloud felügyeleti IP-k még nem részei az Azure Trusted Microsoft-szolgáltatások. Ezért ha engedélyezni Azure Spring Cloud, hogy a Key Vault privát végponti kapcsolatokkal védett Key Vault, a következő IP-eket kell hozzáadnia a Azure Key Vault tűzfalhoz:

```
20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243
```

## <a name="import-certificate"></a>Tanúsítvány importálása
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>A tanúsítványfájl előkészítése PFX formátumban (nem kötelező)
Azure Key Vault a privát tanúsítvány PEM és PFX formátumban történő importálását. Ha a tanúsítványszolgáltatótól kapott PEM-fájl nem működik az [alábbi,](#save-certificate-in-key-vault)Tanúsítvány mentése a Key Vault-ban című szakaszban, kövesse az itt található lépéseket egy PFX létrehozásához a Azure Key Vault.

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

Exportálja az egyesített TLS-/SSL-tanúsítványt azzal a titkos kulccsal, amelyből a tanúsítványkérelem létre lett hozva.

Ha OpenSSL használatával hozta létre a tanúsítványkérést, akkor létrehozott egy titkoskulcsfájlt. A tanúsítvány PFX-fájlba exportáláshoz futtassa az alábbi parancsot. Cserélje le a _&lt; private-key-file>_ és _&lt; merged-certificate-file>_ a titkos kulcs és az egyesített tanúsítványfájl elérési útjára.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Amikor a rendszer megkéri, adjon meg egy exportálási jelszót. Ezt a jelszót a TLS-/SSL-tanúsítvány későbbi feltöltése Azure Key Vault használni.

Ha az IIS vagy a _Certreq.exe_ használatával hozta létre a tanúsítványkérést, telepítse a tanúsítványt a helyi számítógépre, majd [exportálja a tanúsítványt PFX-fájlba](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Tanúsítvány mentése a Key Vault
A tanúsítvány importálásának eljárásához a PEM- vagy PFX-kódolású fájlnak lemezen kell lennie, és a titkos kulccsal kell rendelkezik. 
#### <a name="portal"></a>[Portál](#tab/Azure-portal)
A tanúsítvány feltöltése a Key Vaultba:
1. Ugrás a Key Vault-példányra.
1. A bal oldali navigációs panelen kattintson a Tanúsítványok **elemre.**
1. A felső menüben kattintson a **Generate/import (Generálás/importálás) elemre.**
1. A Tanúsítvány **létrehozása párbeszédpanelen, a** **Tanúsítvány létrehozásának módszere alatt válassza** a `Import` lehetőséget.
1. A **Tanúsítványfájl feltöltése alatt** keresse meg és jelölje ki a tanúsítvány helyét.
1. A **Jelszó alatt** adja meg a tanúsítvány titkos kulcsát.
1. Kattintson a **Létrehozás** lehetőségre.

    ![1. tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Hozzáférés Azure Spring Cloud kulcstartóhoz

A tanúsítvány importálása előtt Azure Spring Cloud hozzáférést a kulcstartóhoz:
#### <a name="portal"></a>[Portál](#tab/Azure-portal)
1. Ugrás a Key Vault-példányra.
1. A bal oldali navigációs panelen kattintson a **Hozzáférésirend-vezérlő elemre.**
1. A felső menüben kattintson a **Hozzáférési szabályzat hozzáadása elemre.**
1. Töltse ki az adatokat, majd kattintson a **Hozzáadás gombra,** majd a **Hozzáférésirend** mentése gombra.

| Titkos engedély | Tanúsítványengedély | Rendszerbiztonsági tag kiválasztása |
|--|--|--|
| Lekérés, Listázás | Lekérés, Listázás | Azure Spring Cloud Domain-Management |

![2. tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

Adjon olvasási Azure Spring Cloud kulcstartóhoz, és cserélje le a és a helyére `<key vault resource group>` `<key vault name>` a következő parancsban.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Tanúsítvány importálása a Azure Spring Cloud
#### <a name="portal"></a>[Portál](#tab/Azure-portal)
1. Ugrás a szolgáltatáspéldányra. 
1. Az alkalmazás bal oldali navigációs panelen válassza a **TLS/SSL-beállítások lehetőséget.**
1. Ezután kattintson a **Tanúsítvány importálása Key Vault elemre.**

    ![Tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate.png)

1. Miután sikeresen importálta a tanúsítványt, megjelenik a titkoskulcs-tanúsítványok **listájában.**

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
> Ahhoz, hogy ezzel a tanúsítvánnyal egy egyéni tartományt biztosítsunk, a tanúsítványt egy adott tartományhoz kell kötni. Kövesse a következő szakaszban található lépéseket: [SSL-kötés hozzáadása.](#add-ssl-binding)

## <a name="add-custom-domain"></a>Új Custom Domain
Egy CNAME rekord használatával leképezhet egy egyéni DNS-nevet a Azure Spring Cloud. 

> [!NOTE] 
> Az A rekord nem támogatott. 

### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása
A DNS-szolgáltatónál adjon hozzá egy CNAME rekordot, amely leképezi a tartományt <service_name>.azuremicroservices.io. Itt <service_name> a saját Azure Spring Cloud neve. A helyettesítő karakterek tartománya és altartománya támogatott. A CNAME hozzáadása után a DNS-rekordok oldala az alábbi példához hasonló lesz: 

![DNS-rekordok oldala](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Egyéni tartomány leképezés Azure Spring Cloud alkalmazásra
Ha nem található alkalmazás a Azure Spring Cloud, kövesse a következő rövid útmutatóban található utasításokat: Meglévő Azure Spring Cloud indítása a [Azure Portal.](./spring-cloud-quickstart.md)

#### <a name="portal"></a>[Portál](#tab/Azure-portal)
Ugrás az alkalmazás oldalára.

1. Válassza a **Custom Domain** lehetőséget.
2. Ezután **adja hozzá a Custom Domain.** 

    ![Egyéni tartomány](./media/custom-dns-tutorial/custom-domain.png)

3. Írja be azt a teljes tartománynevet, amelyhez CNAME rekordot adott hozzá, például www.contoso.com. Győződjön meg arról, hogy a Gazdagépnév rekordtípus beállítása CNAME (<service_name>.azuremicroservices.io)
4. Kattintson **az Érvényesítés** gombra a **Hozzáadás gomb engedélyezéséhez.**
5. Kattintson a **Hozzáadás** parancsra.

    ![Egyéni tartomány hozzáadása](./media/custom-dns-tutorial/add-custom-domain.png)

Egy alkalmazás több tartománnyal is rendelkezik, de egy tartomány csak egy alkalmazáshoz lehet leképezni. Miután sikeresen leképezte az egyéni tartományt az alkalmazásra, az az egyéni tartománytáblában fog látni.

![Egyéni tartománytábla](./media/custom-dns-tutorial/custom-domain-table.png)

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
> Az **egyéni tartomány Nem biztonságos** címkéje azt jelenti, hogy még nincs SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba való HTTPS-kérések hibaüzenetet vagy figyelmeztetést kapnak.

## <a name="add-ssl-binding"></a>SSL-kötés hozzáadása

#### <a name="portal"></a>[Portál](#tab/Azure-portal)
Az egyéni tartománytáblában válassza az **Ssl-kötés hozzáadása lehetőséget** az előző ábrán látható módon.  
1. Válassza ki **a tanúsítványt,** vagy importálja.
1. Kattintson a **Mentés** gombra.

    ![SSL-kötés hozzáadása – 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

Az SSL-kötés sikeres hozzáadása után a tartomány állapota biztonságos lesz: **Kifogástalan.** 

![SSL-kötés hozzáadása – 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS kényszerítése
Alapértelmezés szerint az alkalmazáshoz továbbra is bárki hozzáférhet HTTP-protokollon keresztül, de az összes HTTP-kérést átirányíthatja a HTTPS-portra.
#### <a name="portal"></a>[Portál](#tab/Azure-portal)
Az alkalmazás oldalán a bal oldali navigációs sávon válassza **a** Custom Domain. Ezután állítsa a **Csak HTTPS beállítását** True *(Igaz) értékre.*

![SSL-kötés hozzáadása – 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
Ha a művelet befejeződött, keresse meg az alkalmazásra mutató HTTPS URL-címek bármelyikét. Vegye figyelembe, hogy a HTTP URL-címek nem működnek.

## <a name="see-also"></a>Lásd még
* [Mi az Azure Key Vault?](../key-vault/general/overview.md)
* [Tanúsítvány importálása](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Saját Spring Cloud indítása az Azure CLI használatával](./spring-cloud-quickstart.md)
