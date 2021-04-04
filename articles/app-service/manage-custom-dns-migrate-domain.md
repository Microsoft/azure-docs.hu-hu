---
title: Aktív DNS-név átmigrálása
description: Megtudhatja, hogyan telepíthet át egy élő webhelyhez már hozzárendelt egyéni DNS-tartománynevet állásidő nélkül Azure App Service.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89484298"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Aktív DNS-név átmigrálása Azure App Service

Ez a cikk bemutatja, hogyan telepíthet át egy aktív DNS-nevet [Azure app Service](../app-service/overview.md) leállás nélkül.

Ha élő helyet és DNS-tartománynevet App Servicere telepít át, akkor a DNS-név már élő forgalmat fog szolgálni. Az áttelepítés során elkerülheti az állásidőt a DNS-feloldás során, ha az aktív DNS-nevet a App Service alkalmazás megelőző jelleggel köti.

Ha nem aggódik a DNS-feloldási állásidővel kapcsolatban, tekintse [meg a meglévő egyéni DNS-név leképezése Azure app Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Előfeltételek

A útmutató elvégzéséhez:

- Győződjön [meg arról, hogy a app Service alkalmazás nem ingyenes szintű](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Megelőző jelleggel tartománynév kötése

Egyéni tartomány megelőző jelleggel kötése esetén a következő műveleteket hajthatja végre a meglévő DNS-rekordok módosítása előtt:

- A tartomány tulajdonjogának igazolása
- Az alkalmazás tartománynevének engedélyezése

Ha végül áttelepíti az egyéni DNS-nevet a régi helyről a App Service alkalmazásba, a DNS-feloldás nem lesz leállás.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>Tartomány-ellenőrzési azonosító lekérése

Az alkalmazáshoz tartozó tartomány-ellenőrzési azonosító lekéréséhez kövesse a [tartomány-ellenőrzési azonosító beolvasása](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)című témakör lépéseit.

### <a name="create-domain-verification-record"></a>Tartomány-ellenőrzési rekord létrehozása

A tartomány tulajdonjogának ellenőrzéséhez adjon hozzá egy TXT-rekordot a tartomány ellenőrzéséhez. A TXT rekord állomásneve a leképezni kívánt DNS-bejegyzéstípus típusától függ. Tekintse meg a következő táblázatot ( `@` általában a legfelső szintű tartományt jelenti):

| Példa DNS-rekordra | TXT-gazdagép | TXT-érték |
| - | - | - |
| \@ legfelső szintű | _asuid_ | [Az alkalmazáshoz tartozó tartomány-ellenőrzési azonosító](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| www (Sub) | _asuid. www_ | [Az alkalmazáshoz tartozó tartomány-ellenőrzési azonosító](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* helyettesítő | _asuid_ | [Az alkalmazáshoz tartozó tartomány-ellenőrzési azonosító](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

A DNS-rekordok oldalon jegyezze fel az áttelepíteni kívánt DNS-név bejegyzéstípusát. A App Service támogatja a CNAME és A rekordok leképezését.

> [!NOTE]
> `*`A helyettesítő rekordok nem ellenőrzik az altartományokat egy meglévő CNAME rekordkal. Előfordulhat, hogy explicit módon létre kell hoznia egy TXT-rekordot mindegyik altartományhoz.

### <a name="enable-the-domain-for-your-app"></a>Az alkalmazás tartományának engedélyezése

1. A [Azure Portal](https://portal.azure.com)az alkalmazás lap bal oldali navigációs sávján válassza az **Egyéni tartományok** elemet. 

    ![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Az **Egyéni tartományok** lapon válassza az **egyéni tartomány hozzáadása** elemet.

    ![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írja be az áttelepíteni kívánt teljes tartománynevet, amely megfelel a létrehozott TXT-rekordnak (például,, `contoso.com` `www.contoso.com` vagy `*.contoso.com` ). Válassza az **Érvényesítés** lehetőséget.

    Az **egyéni tartomány hozzáadása** gomb aktiválva van. 

1. Győződjön meg arról, hogy az **állomásnév bejegyzéstípus** az áttelepíteni kívánt DNS-bejegyzéstípusra van beállítva. Válassza a **Gazdagépnév hozzáadása** lehetőséget.

    ![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Eltarthat egy ideig, amíg az új gazdanév megjelenik az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

    ![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    Az egyéni DNS-név mostantól engedélyezve van az Azure-alkalmazásban. 

## <a name="remap-the-active-dns-name"></a>Az aktív DNS-név újratársítása

Az egyetlen teendő, hogy átirányítja az aktív DNS-rekordot, hogy App Service mutasson. Most még mindig a régi helyre mutat.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Az alkalmazás IP-címének másolása (csak rekord)

Ha egy CNAME rekordot újra hozzárendel, ugorja át ezt a szakaszt. 

Egy rekord átadásához szüksége lesz a App Service alkalmazás külső IP-címére, amely az **Egyéni tartományok** lapon látható.

Az **Egyéni tartományok** oldalon másolja az alkalmazás IP-címét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>A DNS-rekord frissítése

A tartományi szolgáltató DNS-rekordok lapján válassza ki a felvenni kívánt DNS-rekordot.

A `contoso.com` legfelső szintű tartományhoz például a következő táblázatban szereplő példákhoz hasonló módon kell felvennie az a vagy a CNAME rekordot: 

| Példa FQDN-re | Rekordtípus | Gazdagép | Érték |
| - | - | - | - |
| contoso.com (root) | A | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
| www \. -contoso.com (Sub) | CNAME | `www` | _&lt;AppName>. azurewebsites.net_ |
| \*. contoso.com (helyettesítő karakter) | CNAME | _\*_ | _&lt;AppName>. azurewebsites.net_ |

Mentse a beállításokat.

A DNS-lekérdezések a DNS-propagálás megkezdése után azonnal megoldják a App Service alkalmazás feloldását.

## <a name="migrate-domain-from-another-app"></a>Tartomány migrálása egy másik alkalmazásból

Áttelepítheti az aktív egyéni tartományt az Azure-ban, előfizetések között vagy ugyanazon az előfizetésen belül. A leállás nélküli áttelepítés esetében azonban a forrásoldali alkalmazásnak és a célként megadott alkalmazásnak egy adott időpontban ugyanahhoz az egyéni tartományhoz kell rendelnie. Ezért győződjön meg arról, hogy a két alkalmazás nincs telepítve ugyanarra a központi telepítési egységre (belső nevén webtárhely). A tartománynév csak egy alkalmazáshoz rendelhető hozzá minden egyes telepítési egységben.

Az alkalmazás központi telepítési egységét az FTP/S URL-cím tartománynevének megtekintésével érheti el `<deployment-unit>.ftp.azurewebsites.windows.net` . Ellenőrizze, hogy a telepítési egység különbözik-e a forrásoldali alkalmazás és a cél alkalmazás között. Az alkalmazás központi telepítési egységét az [app Service-csomag](overview-hosting-plans.md) határozza meg. Az Azure véletlenszerűen választja ki a csomag létrehozásakor, és nem módosítható. Az Azure-ban csak két csomag van ugyanabban a telepítési egységben, amikor [ugyanabban az erőforráscsoportban *és* ugyanabban a régióban hozza létre őket](app-service-plan-manage.md#create-an-app-service-plan), de nem rendelkezik logikai értékkel, hogy a csomagok különböző üzembe helyezési egységekben legyenek. Az egyetlen módja, ha egy másik üzembe helyezési egységben is létrehoz egy csomagot, hogy egy új erőforráscsoport vagy régió számára hozzon létre egy csomagot, amíg egy másik üzembe helyezési egységet nem kap.

## <a name="next-steps"></a>Következő lépések

Útmutató egyéni TLS/SSL-tanúsítvány kötéséhez App Serviceához.

> [!div class="nextstepaction"]
> [Egyéni DNS-név biztonságossá tétele TLS-kötéssel Azure App Service](configure-ssl-bindings.md)
