---
title: A Premium Preview Azure Firewall üzembe helyezése és konfigurálása
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a Azure Firewall Premiumot.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721786"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>A Premium Preview Azure Firewall üzembe helyezése és konfigurálása

> [!IMPORTANT]
> A Azure Firewall Premium szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 A Azure Firewall Premium Preview egy új generációs tűzfal, amely a szigorúan bizalmas és szabályozott környezetekhez szükséges képességekkel rendelkezik. A következő funkciókat tartalmazza:

- **TLS-vizsgálat** – visszafejti a kimenő forgalmat, feldolgozza az adatokat, majd titkosítja az adatokat, és elküldi azt a célhelyre.
- **IDP** – a hálózati behatolás-észlelési és-megelőzési rendszer (IDP) lehetővé teszi a kártékony tevékenységek hálózati tevékenységének figyelését, a tevékenység adatainak naplózását, a jelentés megadását, és opcionálisan megkísérli a blokkolását.
- **URL-szűrés** – kiterjeszti Azure Firewall FQDN-szűrési képességét egy teljes URL-cím megfontolni. Például a `www.contoso.com/a/c` helyett `www.contoso.com` .
- **Webes kategóriák** – a rendszergazdák engedélyezhetik vagy megtagadhatják a webhely-kategóriákhoz való hozzáférést, például a szerencsejáték-webhelyeket, a közösségi média webhelyeket és egyebeket.

További információ: [Azure Firewall Premium-funkciók](premium-features.md).

A sablon használatával olyan tesztkörnyezet helyezhető üzembe, amely központi VNet (10.0.0.0/16) rendelkezik három alhálózattal:
- egy feldolgozói alhálózat (10.0.10.0/24)
- egy Azure-beli megerősített alhálózat (10.0.20.0/24)
- egy tűzfal alhálózata (10.0.100.0/24)

Ebben a tesztkörnyezetben az egyszerűség kedvéért egyetlen központi VNet használunk. Éles környezetben olyan [sugaras és küllős topológiát](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) virtuális hálózatok, amely egyenrangú.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Központi VNet-topológia":::

A feldolgozó virtuális gép olyan ügyfél, amely HTTP/S kérelmeket küld a tűzfalon keresztül.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="deploy-the-infrastructure"></a>Az infrastruktúra üzembe helyezése

A sablon üzembe helyez egy teljes körű tesztelési környezetet Azure Firewall prémium szintű IDP, TLS-ellenőrzés, URL-szűrés és webes kategóriák használatával:

- egy új Azure Firewall Premium-és tűzfalszabályok előre meghatározott beállításokkal, amelyek lehetővé teszik az alapvető képességek (IDP, TLS-vizsgálat, URL-szűrés és webes kategóriák) egyszerű érvényesítését.
- központilag telepíti az összes függőséget, beleértve a Key Vault és a felügyelt identitást is. Éles környezetben ezek az erőforrások már létrehozhatók, és nem szükségesek ugyanabban a sablonban.
- önaláírt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT hoz létre, és üzembe helyezi a generált Key Vault
-  származtatott közbenső HITELESÍTÉSSZOLGÁLTATÓ generálása és üzembe helyezése Windows-teszt virtuális gépen (WorkerVM)
- egy megerősített gazdagép (BastionHost) is üzembe helyezhető, és használható a Windows tesztelési géphez való kapcsolódásra (WorkerVM)



[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most már tesztelheti a IDP, a TLS-vizsgálatot, a webes szűrést és a webes kategóriákat.

### <a name="add-firewall-diagnostics-settings"></a>Tűzfal-diagnosztika beállításainak hozzáadása

A tűzfalszabályok összegyűjtéséhez diagnosztikai beállításokat kell hozzáadnia a tűzfal naplófájljainak összegyűjtéséhez.

1. Válassza ki a **DemoFirewall** , és a **figyelés** területen válassza a **diagnosztikai beállítások** elemet.
2. Válassza a **Diagnosztikai beállítások megadása** lehetőséget.
3. A **diagnosztikai beállítások neve** mezőbe írja be az *FW-diag* nevet.
4. A **napló** területen válassza a **AzureFirewallApplicationRule** és a **AzureFirewallNetworkRule** elemet.
5. A **célhely részletei** területen válassza **a Küldés log Analytics munkaterületre** lehetőséget.
6. Kattintson a **Mentés** gombra.

### <a name="idps-tests"></a>IDP tesztek

A IDP teszteléséhez telepítenie kell a saját belső webkiszolgálóját egy megfelelő kiszolgálótanúsítvány használatával. A prémium szintű előzetes tanúsítványokra vonatkozó követelményekkel kapcsolatos további Azure Firewall információkért lásd: [Azure Firewall Premium Preview-tanúsítványok](premium-certificates.md).

`curl`A használatával különböző HTTP-fejléceket adhat meg, és szimulálhatja a rosszindulatú forgalmat.

#### <a name="to-test-idps-for-http-traffic"></a>A HTTP-forgalom IDP tesztelése:

1. A WorkerVM virtuális gépen nyisson meg egy rendszergazdai parancssorablakot.
2. Írja be a következő parancsot a parancssorba:

   `curl -A "BlackSun" <your web server address>`
3. Ekkor megjelenik a webkiszolgáló válasza.
4. Nyissa meg a tűzfal hálózati szabályának naplóit a Azure Portalon, és keresse meg a következő üzenethez hasonló riasztást:

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Riasztási üzenet":::

   > [!NOTE]
   > Eltarthat egy ideig, amíg az adatgyűjtés meg nem jelenik a naplókban. Adjon meg legalább 20 percet, hogy a naplók megkezdhesse az adatmegjelenítést.
5. Aláírási szabály hozzáadása a 2008983 aláíráshoz:

   1. Válassza ki a **DemoFirewallPolicy** , és a **Beállítások** területen válassza a **IDP (előzetes verzió)** lehetőséget.
   1. Válassza az **aláírási szabályok** lapot.
   1. Az **aláírás azonosítója** alatt a Megnyitás szövegmezőbe írja be a *2008983* típust.
   1. A **mód** területen válassza a **Megtagadás** lehetőséget.
   1. Kattintson a **Mentés** gombra.
   1. Várjon, amíg a telepítés befejeződik a továbblépés előtt.



6. A WorkerVM futtassa újra a `curl` parancsot:

   `curl -A "BlackSun" <your web server address>`

   Mivel a tűzfal mostantól blokkolja a HTTP-kérelmet, a kapcsolat időtúllépése után a következő kimenet jelenik meg:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Nyissa meg a Azure Portal figyelő naplóit, és keresse meg a letiltott kérelem üzenetét.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>A HTTPS-forgalom IDP tesztelése

Ismételje meg ezeket a curl-teszteket HTTP helyett HTTPS használatával. Például:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

Ugyanazokat az eredményeket kell látnia, mint a HTTP-tesztek.

### <a name="tls-inspection-with-url-filtering"></a>TLS-ellenőrzés URL-szűréssel

A következő lépések végrehajtásával tesztelheti a TLS-vizsgálatot az URL-szűrés használatával.

1. Szerkessze a tűzfalszabályok alkalmazási szabályait, és adjon hozzá egy nevű új szabályt `AllowURL` a `AllowWeb` szabály gyűjteményhez. Konfigurálja a cél URL-címet `www.nytimes.com/section/world` , a forrás IP-címét **\* *_, a célhely _* URL-címét (előzetes verzió)**, válassza a TLS- **vizsgálat (előzetes verzió)** és a protokollok **http, HTTPS** lehetőséget.

3. Amikor az üzembe helyezés befejeződött, nyisson meg egy böngészőt a WorkerVM, és `https://www.nytimes.com/section/world` ellenőrizze, hogy a HTML-válasz a várt módon jelenjen-e meg a böngészőben.
4. A Azure Portal a teljes URL-címet megtekintheti az alkalmazás-szabály figyelési naplóiban:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Az URL-címet bemutató riasztási üzenet":::

Előfordulhat, hogy egyes HTML-lapok hiányosak, mert más, megtagadott URL-címekre hivatkoznak. A probléma megoldásához a következő megközelítéssel lehet eljárni:

- Ha a HTML-lap más tartományokra mutató hivatkozásokat is tartalmaz, ezeket a tartományokat hozzáadhatja egy új alkalmazás-szabályhoz, amely engedélyezi a hozzáférést ezekhez a teljes tartománynevek eléréséhez.
- Ha a HTML-lap alurl-címekre mutató hivatkozásokat tartalmaz, akkor módosíthatja a szabályt, és hozzáadhat egy csillagot az URL-címhez. Például: `targetURLs=www.nytimes.com/section/world*`

   Másik lehetőségként új URL-címet is hozzáadhat a szabályhoz. Például: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Webes kategóriák tesztelése

Hozzon létre egy szabályt, amely engedélyezi a sport webhelyeinek elérését.
1. A portálon nyissa meg az erőforráscsoportot, és válassza a **DemoFirewallPolicy** lehetőséget.
2. Válassza ki az **alkalmazás szabályait**, majd **vegyen fel egy szabálygyűjtemény-gyűjteményt**.
3. A **Name (név**) mezőbe írja be a következőt: *GeneralWeb*, **priority** *103*, **szabály gyűjtési csoport** **DefaultApplicationRuleCollectionGroup** kiválasztása.
4. A *AllowSports*,  **forrás**  *\** , **protokoll** *http, HTTPS*, Select **TLS-vizsgálat**, **cél típus** kiválasztása *webes kategóriák (előzetes verzió)*, **cél** kiválasztása *sport* elemre.
5. Válassza a **Hozzáadás** lehetőséget.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="A sport webes kategóriája":::
6. Ha az üzembe helyezés befejeződött, nyissa meg a  **WorkerVM** , és nyisson meg egy webböngészőt, és keresse meg a következőt: `https://www.nfl.com` .

   Ekkor meg kell jelennie az NFL-weblapon, és az alkalmazási szabály naplója azt mutatja, hogy egy **webes Kategória: a sport** szabálya egyeztetve lett, és a kérés engedélyezett.

## <a name="next-steps"></a>Következő lépések

- [Azure Firewall Premium Preview a Azure Portal](premium-portal.md)