---
title: Egyéni tartomány beállítása az Azure statikus Web Apps
description: Útmutató egyéni tartomány leképezéséhez az Azure statikus Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 578860883a108bba4b4bcd8cd04e8c08f484d474
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92173688"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Egyéni tartomány beállítása az Azure Static Web Apps előzetes verziójában

Alapértelmezés szerint az Azure statikus Web Apps egy automatikusan generált tartománynevet biztosít. Ez a cikk bemutatja, hogyan képezhető le egyéni tartománynevet egy Azure statikus Web Apps alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

- Egy megvásárolt tartománynév
- Hozzáférés a tartomány DNS-konfigurációs tulajdonságaihoz

Tartománynevek konfigurálásakor A "A" rekordok használatával képezhetők le a legfelső szintű tartományok (például `example.com` ) egy IP-címhez. A legfelső szintű tartományokat közvetlenül egy IP-címhez kell hozzárendelni, mert a DNS-specifikáció nem teszi lehetővé egyik tartomány leképezését egy másikra.

## <a name="dns-configuration-options"></a>DNS-konfigurációs beállítások

Az alkalmazásokhoz számos különböző típusú DNS-konfiguráció érhető el.

| Ha szeretné | Majd |
|--|--|
| Támogatás `www.example.com` vagy `blog.example.net` | [CNAME rekord hozzárendelése](#map-a-cname-record) |
| Támogatja `example.com` | [Legfelső szintű tartomány konfigurálása](#configure-a-root-domain) |
| Az összes altartományra mutat `www.example.com` | [Helyettesítő karakter hozzárendelése](#map-a-wildcard-domain) |

## <a name="map-a-cname-record"></a>CNAME rekord hozzárendelése

Egy CNAME rekord egy tartományt képez le egy másikra. CNAME rekord használatával képezhető le `www.example.com` , `blog.example.com` vagy bármely más altartomány az Azure statikus Web Apps által biztosított, automatikusan létrehozott tartományba.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

1. **Statikus Web Apps** keresése és kiválasztása

1. A _statikus Web Apps_ lapon válassza ki az alkalmazás nevét.

1. Kattintson a menü **Egyéni tartományok** elemére.

1. Kattintson a **Hozzáadás** gombra

1. Az _Egyéni tartományok_ ablakban másolja az URL-címet az **érték** mezőbe.

### <a name="configure-dns-provider"></a>DNS-szolgáltató konfigurálása

1. Jelentkezzen be a tartományszolgáltatója webhelyére.

2. Keresse meg a DNS-rekordok kezelésére szolgáló oldalt. Minden tartományszolgáltató saját felülettel rendelkezik a DNS-rekordok kezelésére, ezért tekintse meg a szolgáltatói dokumentációt. A webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** címkével ellátott területeit keresse.

3. A DNS-rekordok oldala gyakran a fiókadatok megtekintésekor jelenik meg, majd itt keresse meg például a **Saját tartományok** hivatkozást. Nyissa meg a lapot, majd keresse meg a **zónafájl**, a **DNS-rekordok** vagy a **Speciális konfiguráció** nevű hivatkozást.

    A következő képernyőkép egy DNS-rekordokat tartalmazó oldalra mutat példát:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Minta DNS-szolgáltató konfigurációja":::

4. Hozzon létre egy új **CNAME** -rekordot a következő értékekkel...

    | Beállítás             | Érték                     |
    | ------------------- | ------------------------- |
    | Típus                | CNAME                     |
    | Gazdagép                | www                       |
    | Érték               | Beillesztés a vágólapról |
    | TTL (ha van ilyen) | Kilépés alapértelmezett értékként    |

5. Mentse a módosításokat a DNS-szolgáltatóval.

### <a name="validate-cname"></a>CNAME ellenőrzése

1. Térjen vissza a Azure Portal _Egyéni tartományok_ ablakához.

1. Adja meg a tartományt, beleértve az `www` _egyéni tartomány ellenőrzése_ szakasz részét.

1. Kattintson az **Érvényesítés** gombra.

Most, hogy az egyéni tartomány konfigurálva van, több óráig is eltarthat, amíg a DNS-szolgáltató propagálni kívánja a módosításokat világszerte. A propagálás állapotát a [dnspropagation.net](https://dnspropagation.net)címen tekintheti meg. Adja meg az egyéni tartományt, beleértve a `www` elemet, válassza ki a CNAME elemet a legördülő menüből, és válassza az **Indítás** lehetőséget.

Ha a DNS-módosítások feltöltve lettek, a webhely a statikus webalkalmazás automatikusan generált URL-címét (például _Random-Name-123456789c.azurestaticapps.net_) adja vissza.

## <a name="configure-a-root-domain"></a>Legfelső szintű tartomány konfigurálása

A gyökértartomány tartománya mínusz bármely altartomány, beleértve a-t is `www` . Például a a gyökértartomány tartománya `www.example.com` `example.com` . Ezt "APEX" tartománynak is nevezzük.

Noha az előzetes verzióban nem érhető el a gyökértartomány támogatása funkció, a blogbejegyzések [konfigurálása az Azure statikus web Appsban](https://burkeholland.github.io/posts/static-app-root-domain) című cikkből megtudhatja, hogyan konfigurálhatja a gyökértartomány támogatását egy statikus webalkalmazással.

## <a name="map-a-wildcard-domain"></a>Helyettesítő karaktert tartalmazó tartomány hozzárendelése

Néha azt szeretné, hogy az altartományba eljuttatott összes forgalom egy másik tartományba legyen irányítva. Gyakori példa az összes altartomány-forgalom leképezése `www.example.com` . Így, még akkor is, ha valaki típust `w.example.com` használ `www.example.com` , a rendszer elküldi a kérést `www.example.com` .

### <a name="configure-dns-provider"></a>DNS-szolgáltató konfigurálása

1. Jelentkezzen be a tartományszolgáltatója webhelyére.

2. Keresse meg a DNS-rekordok kezelésére szolgáló oldalt. Minden tartományszolgáltató saját felülettel rendelkezik a DNS-rekordok kezelésére, ezért tekintse meg a szolgáltatói dokumentációt. A webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** címkével ellátott területeit keresse.

3. A DNS-rekordok oldala gyakran a fiókadatok megtekintésekor jelenik meg, majd itt keresse meg például a **Saját tartományok** hivatkozást. Nyissa meg a lapot, majd keressen egy hasonló nevű hivatkozást a **zónafájl**, a **DNS-rekordok** vagy a **Speciális konfiguráció** elemre.

    A következő képernyőkép egy DNS-rekordokat tartalmazó oldalra mutat példát:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Minta DNS-szolgáltató konfigurációja":::

4. Hozzon létre egy új **CNAME** -rekordot a következő értékekkel, és cserélje `www.example.com` le az egyéni tartománynevére.

    | Beállítás | Érték                  |
    | ------- | ---------------------- |
    | Típus    | CNAME                  |
    | Gazdagép    | \*                     |
    | Érték   | www.example.com        |
    | TTL     | Kilépés alapértelmezett értékként |

5. Mentse a módosításokat a DNS-szolgáltatóval.

Most, hogy a helyettesítő karakteres tartomány konfigurálva van, több óráig is eltarthat, amíg a módosítások a világ bármely részén elterjednek. A propagálás állapotát a [dnspropagation.net](https://dnspropagation.net)címen tekintheti meg. Adja meg a tartomány egyéni tartományát bármely altartománnyal (kivéve `www` ), válassza ki a CNAME elemet a legördülő menüből, és válassza az **Indítás** lehetőséget.

Ha a DNS-módosítások feltöltése megtörtént, a webhely visszaadja a statikus webalkalmazáshoz konfigurált egyéni tartományt (például: `www.example.com` ).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásbeállítások konfigurálása](application-settings.md)
