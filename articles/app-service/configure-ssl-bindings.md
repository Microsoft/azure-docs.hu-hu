---
title: Egyéni DNS biztonságossá tétele TLS/SSL-kötéssel
description: Biztonságos HTTPS-hozzáférés az egyéni tartományhoz egy tanúsítványhoz tartozó TLS/SSL-kötés létrehozásával. Javítsa a webhely biztonságát a HTTPS vagy a TLS 1,2 betartatásával.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 04/30/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: a713d869a754f17f4030c531999c822a2cbb76ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039801"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure App Service

Ez a cikk bemutatja, hogyan védheti meg az [Egyéni tartományt](app-service-web-tutorial-custom-domain.md) a [app Service alkalmazásban](./index.yml) vagy a [Function alkalmazásban](../azure-functions/index.yml) egy tanúsítvány kötésének létrehozásával. Ha elkészült, elérheti a App Service alkalmazást a `https://` végpontban az egyéni DNS-név (például `https://www.contoso.com` ) számára. 

![Egyéni TLS/SSL-tanúsítvánnyal rendelkező webalkalmazás](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Az [Egyéni tartományok](app-service-web-tutorial-custom-domain.md) tanúsítványokkal történő biztonságossá tétele két lépésből áll:

- [Adjon hozzá egy privát tanúsítványt app Servicehoz](configure-ssl-certificate.md) , amely megfelel az összes [privát tanúsítványra vonatkozó követelménynek](configure-ssl-certificate.md#private-certificate-requirements).
-  Hozzon létre egy TLS-kötést a megfelelő egyéni tartományhoz. Ez a második lépés a cikk hatálya alá esik.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás tarifacsomagjának bővítése.
> * Egyéni tartomány biztonságossá tétele tanúsítvánnyal
> * HTTPS kényszerítése
> * A TLS 1.1/1.2 kényszerítése
> * A TLS kezelésének automatizálása szkriptekkel

## <a name="prerequisites"></a>Előfeltételek

A következő útmutató követése:

- [Létre kell hoznia egy App Service-alkalmazást.](./index.yml)
- [Tartománynév hozzárendelése az alkalmazáshoz](app-service-web-tutorial-custom-domain.md) , illetve [Az Azure-beli vásárlás és konfigurálás](manage-custom-dns-buy-domain.md)
- [Privát tanúsítvány hozzáadása az alkalmazáshoz](configure-ssl-certificate.md)

> [!NOTE]
> Privát tanúsítvány hozzáadásának legegyszerűbb módja egy [ingyenes app Service felügyelt tanúsítvány](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (előzetes verzió) létrehozása.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Egyéni tartomány biztonságossá tétele

Hajtsa végre a következő lépéseket:

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>bal oldali menüjében válassza a app Services lehetőséget   >  **\<app-name>** .

Az alkalmazás bal oldali navigációs sávján indítsa el a **TLS/SSL kötési** párbeszédpanelt a következő lépésekkel:

- **Egyéni tartományok** kijelölése  >  **kötés hozzáadása**
- **TLS/SSL-beállítások** kiválasztása  >  **TLS/SSL-kötés hozzáadása**

![Kötés hozzáadása a tartományhoz](./media/configure-ssl-bindings/secure-domain-launch.png)

Az **egyéni tartomány** területen válassza ki azt az egyéni tartományt, amelyhez hozzá szeretné adni a kötést.

Ha az alkalmazás már rendelkezik tanúsítvánnyal a kiválasztott egyéni tartományhoz, lépjen a [kötés közvetlen létrehozása](#create-binding) elemre. Ellenkező esetben folytassa a munkát.

### <a name="add-a-certificate-for-custom-domain"></a>Tanúsítvány hozzáadása egyéni tartományhoz

Ha az alkalmazás nem rendelkezik tanúsítványokkal a kiválasztott egyéni tartományhoz, akkor két lehetőség közül választhat:

- **Pfx-tanúsítvány feltöltése** – kövesse a munkafolyamatot a [privát tanúsítvány feltöltése](configure-ssl-certificate.md#upload-a-private-certificate)lapon, majd válassza ezt a lehetőséget.
- **Importálás app Service-tanúsítvány** – kövesse a munkafolyamatot [app Service tanúsítvány importálása](configure-ssl-certificate.md#import-an-app-service-certificate)lapon, majd válassza ezt a lehetőséget.

> [!NOTE]
> [Létrehozhat egy ingyenes tanúsítványt](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (előzetes verzió) is, vagy [importálhat egy Key Vault tanúsítványt](configure-ssl-certificate.md#import-a-certificate-from-key-vault), de ezt külön kell elvégeznie, majd vissza kell térnie a **TLS/SSL kötési** párbeszédpanelre.

### <a name="create-binding"></a>Kötés létrehozása

A következő táblázat segítségével konfigurálhatja a TLS-kötést a **TLS/SSL-kötés** párbeszédpanelen, majd kattintson a **kötés hozzáadása** elemre.

| Beállítás | Leírás |
|-|-|
| Egyéni tartomány | Az a tartománynév, amelyhez hozzá kell adni a TLS/SSL-kötést. |
| Privát Tanúsítvány ujjlenyomata | A kötni kívánt tanúsítvány. |
| TLS/SSL-típus | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** – több SNI SSL kötés adható hozzá. Ez a beállítás lehetővé teszi, hogy több TLS/SSL-tanúsítvány biztosítson több tartományt ugyanazon az IP-címen. A legtöbb modern böngésző (például az Internet Explorer, a Chrome, a Firefox és az Opera) támogatja a SNI (További információ: [kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP SSL** – csak egy IP SSL kötés adható hozzá. Ez a beállítás csak egy TLS/SSL-tanúsítványt engedélyez egy dedikált nyilvános IP-cím biztonságossá tételéhez. Miután konfigurálta a kötést, kövesse a következő témakörben ismertetett lépéseket: [IP SSL rekordok](#remap-records-for-ip-ssl)visszavonása.<br/>IP SSL csak **standard** vagy újabb verzióban támogatott. </li></ul> |

A művelet befejezése után az egyéni tartomány TLS/SSL-állapota **biztonságosra** változik.

![A TLS/SSL-kötés sikerült](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Az **Egyéni tartományokban** található **biztonságos** állapot azt jelenti, hogy a tanúsítvány védett, de app Service nem vizsgálja, hogy a tanúsítvány önaláírt vagy lejárt, például hogy a böngészők hibát vagy figyelmeztetést jelenítenek-e meg.

## <a name="remap-records-for-ip-ssl"></a>IP SSL rekordok ismételt társítása

Ha nem használja a IP SSL alkalmazást az alkalmazásban, ugorjon a [https tesztelése az egyéni tartományhoz](#test-https)lehetőségre.

Két módosítást kell végeznie, potenciálisan:

- Alapértelmezés szerint az alkalmazás egy megosztott nyilvános IP-címet használ. Ha IP SSLrel rendelkező tanúsítványt köt össze, App Service létrehoz egy új, dedikált IP-címet az alkalmazáshoz. Ha hozzárendelt egy rekordot az alkalmazáshoz, frissítse a tartomány beállításjegyzékét ezzel az új, dedikált IP-címmel.

    Az alkalmazás **egyéni tartomány** lapja az új, dedikált IP-címmel frissül. [Másolja ezt az IP-címet](app-service-web-tutorial-custom-domain.md#info), majd [képezze le újra az A rekordot](app-service-web-tutorial-custom-domain.md#map-an-a-record) erre az új IP-címre.

- Ha SNI SSL kötése van a szolgáltatáshoz `<app-name>.azurewebsites.net` , akkor a [CNAME-hozzárendelést](app-service-web-tutorial-custom-domain.md#map-a-cname-record) újra fel kell vennie, hogy `sni.<app-name>.azurewebsites.net` a rendszer az `sni` előtagot adja meg

## <a name="test-https"></a>HTTPS tesztelése

A különböző böngészőkben keresse meg az alkalmazást, és ellenőrizze, hogy az alkalmazás elérhető-e `https://<your.custom.domain>` .

:::image type="content" source="./media/configure-ssl-bindings/app-with-custom-ssl.png" alt-text="Képernyőkép: a contoso.com URL-cím kiemelésével megjeleníthető példa az egyéni tartományra való tallózásra.":::

Az alkalmazás kódja a "x-appservice-proto" fejlécen keresztül ellenőrizheti a protokollt. A fejléc értéke `http` vagy `https` . 

> [!NOTE]
> Ha az alkalmazás tanúsítvány-ellenőrzési hibákat ad, valószínűleg önaláírt tanúsítványt használ.
>
> Ha nem így van, előfordulhat, hogy kihagyott néhány köztes tanúsítványt, amikor a tanúsítványt a PFX-fájlba exportálta.

## <a name="prevent-ip-changes"></a>IP-változások megakadályozása

A bejövő IP-cím akkor is megváltozhat, ha törli a kötést, még akkor is, ha a kötés IP SSL. Ez különösen akkor fontos, ha olyan tanúsítványt újít meg, amely már egy IP SSL-kötésben van. Annak érdekében, hogy az alkalmazás IP-címe ne változzon, kövesse sorrendben az alábbi lépéseket:

1. Töltse fel az új tanúsítványt.
2. Kösse az új tanúsítványt a kívánt egyéni tartományhoz anélkül, hogy törölné a régit. Ez a művelet lecserélni a kötést ahelyett, hogy eltávolítaná a régit.
3. Törölje a régi tanúsítványt. 

## <a name="enforce-https"></a>HTTPS kényszerítése

Alapértelmezés szerint bárki elérheti az alkalmazást HTTP használatával. A HTTP-kéréseket átirányíthatja a HTTPS-portra.

Az alkalmazás lapjának bal oldali navigációs sávján válassza az **SSL-beállítások** elemet. Ezután a **HTTPS Only** (Csak HTTPS) területen válassza az **On** (Be) elemet.

![HTTPS kényszerítése](./media/configure-ssl-bindings/enforce-https.png)

Ha a művelet befejeződött, nyissa meg az alkalmazásra mutató HTTP URL-címek valamelyikét. Például:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS-verziók kényszerítése

Az alkalmazása alapértelmezés szerint a [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2-t engedélyezi, amely az iparági szabványok, például a [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) szerint ajánlott TLS-szint. A TLS más verzióinak kényszerítéséhez kövesse az alábbi lépéseket:

Az alkalmazás lapjának bal oldali navigációs sávján válassza az **SSL-beállítások** elemet. Ezután a **TLS version** (TLS-verzió) szakaszban válassza ki a kívánt TLS minimális verzióját. Ez a beállítás csak a bejövő hívásokat szabályozza. 

![A TLS 1.1 vagy 1.2 kényszerítése](./media/configure-ssl-bindings/enforce-tls1-2.png)

A művelet befejezése után az alkalmazás elutasítja a korábbi TLS-verziójú kapcsolatokat.

## <a name="handle-tls-termination"></a>TLS-megszakítás kezelése

App Service a [TLS-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében fordul elő, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécet.

A nyelvspecifikus konfigurációs útmutatók, például a [Linux Node.js konfigurációs](configure-language-nodejs.md#detect-https-session) útmutatója bemutatja, hogyan ÉSZLELHETŐ egy https-munkamenet az alkalmazás kódjában.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>További erőforrások

* [TLS-/SSL-tanúsítvány használata a kódban az Azure App Service-ben](configure-ssl-certificate-in-code.md)
* [Gyakori kérdések: App Service tanúsítványok](./faq-configuration-and-management.md)