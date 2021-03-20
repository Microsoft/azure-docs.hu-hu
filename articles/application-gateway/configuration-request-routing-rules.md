---
title: Azure Application Gateway kérelmek útválasztási szabályainak konfigurálása
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni az Azure Application Gateway kérelmek útválasztási szabályait.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397535"
---
# <a name="application-gateway-request-routing-rules"></a>Application Gateway kérelmek útválasztási szabályai

Amikor az Azure Portal használatával hoz létre Application Gateway-t, létrehoz egy alapértelmezett szabályt (*rule1*). Ez a szabály az alapértelmezett háttér-készlettel (*appGatewayBackendPool*) és az alapértelmezett HÁTTÉRbeli http-beállításokkal (*appGatewayBackendHttpSettings*) köti össze az alapértelmezett figyelőt (*appGatewayHttpListener*). Az átjáró létrehozása után szerkesztheti az alapértelmezett szabály beállításait, vagy létrehozhat új szabályokat is.

## <a name="rule-type"></a>Szabály típusa

Szabály létrehozásakor az [ *alapszintű* és az *elérési út alapján*](./application-gateway-components.md#request-routing-rules)kell választania.

- Válassza az alapszintű lehetőséget, ha szeretné továbbítani az összes kérelmet a társított figyelőn (például: *blog <i></i> . contoso.com/ \* )* egyetlen háttér-készletre.
- Válassza az elérésiút-alapú lehetőséget, ha az adott URL-címről érkező kérelmeket adott háttérbeli készletekre szeretné irányítani. Az elérésiút-minta csak az URL elérési útjára lesz alkalmazva, nem pedig a lekérdezési paraméterekre.

### <a name="order-of-processing-rules"></a>Feldolgozási szabályok sorrendje

A v1-es és v2-es SKU esetében a bejövő kérések egyeztetését abban a sorrendben dolgozza fel a rendszer, hogy az elérési utak megjelennek az elérésiút-alapú szabály URL-címének elérési útja alatt. Ha egy kérelem megegyezik az elérési út két vagy több elérési útjában szereplő mintázattal, az elsőként megjelenő elérési út illeszkedik. A rendszer továbbítja a kérést az elérési úthoz társított háttér-végponthoz.

## <a name="associated-listener"></a>Társított figyelő

Rendeljen egy figyelőt a szabályhoz, hogy a figyelőhöz társított *kérelem-útválasztási szabály* kiértékelése megtörténjen, hogy a rendszer a kérést átirányítsa a háttér-készletbe.

## <a name="associated-back-end-pool"></a>Társított háttérrendszer-készlet

Rendeljen hozzá egy olyan háttér-készletet, amely tartalmazza a figyelő által fogadott kérelmeket kézbesítő háttérbeli célokat.

 - Alapszintű szabályok esetében csak egy háttérbeli készlet engedélyezett. A társított figyelő összes kérelmét továbbítja a rendszer a háttér-készletnek.

 - Elérésiút-alapú szabály esetén adjon hozzá több háttér-készletet, amelyek megfelelnek az egyes URL-címek elérési útjának. A megadott URL-címhez tartozó kérelmek továbbítva lesznek a megfelelő háttér-készletbe. Emellett adjon hozzá egy alapértelmezett háttér-készletet. Azok a kérelmek, amelyek nem egyeznek meg a szabály URL-címével, továbbítva lesznek erre a készletre.

## <a name="associated-back-end-http-setting"></a>Társított háttérrendszer HTTP-beállítása

Adjon hozzá egy háttérbeli HTTP-beállítást az egyes szabályokhoz. A rendszer a kérelmeket az Application Gateway-ből a háttérbeli célokhoz irányítja a beállításban megadott portszám, protokoll és egyéb információk használatával.

Alapszintű szabályok esetében csak egy háttérbeli HTTP-beállítás engedélyezett. Ezzel a HTTP-beállítással a társított figyelő összes kérelmét továbbítja a rendszer a megfelelő háttérbeli tárolókra.

Elérésiút-alapú szabály esetén adjon hozzá több háttérbeli HTTP-beállítást, amelyek megfelelnek az egyes URL-címek elérési útjának. Az ebben a beállításban szereplő URL elérési úttal egyező kérelmeket a rendszer az egyes URL-címekhez tartozó HTTP-beállításokkal továbbítja a megfelelő háttérbeli célokhoz. Emellett adjon hozzá egy alapértelmezett HTTP-beállítást. Azok a kérelmek, amelyek nem felelnek meg a szabály URL-címének, az alapértelmezett HTTP-beállítással továbbítódnak az alapértelmezett háttér-készletbe.

## <a name="redirection-setting"></a>Átirányítás beállítása

Ha az átirányítás alapszintű szabályhoz van konfigurálva, a rendszer a társított figyelő összes kérelmét átirányítja a célhelyre. Ez *globális* átirányítás. Ha az átirányítás egy elérésiút-alapú szabályhoz van konfigurálva, akkor a rendszer csak egy adott hely területén lévő kérelmeket irányítja át. Ilyenek például a */cart/ \** által jegyzett bevásárlókosár-területek. Ez az *elérésiút-alapú* átirányítás.

További információ az átirányításokról: [Application Gateway átirányítások áttekintése](redirect-overview.md).

### <a name="redirection-type"></a>Átirányítás típusa

Válassza ki a szükséges átirányítás típusát: *Permanent (301)*, *ideiglenes (307*), *Found (302)*, vagy *más (303)*.

### <a name="redirection-target"></a>Átirányítás célja

Válasszon egy másik figyelőt vagy egy külső helyet az átirányítás céljaként.

#### <a name="listener"></a>Figyelő

Válassza a figyelő lehetőséget, hogy átirányítsa a forgalmat az egyik figyelőről egy másikra az átjárón. Erre a beállításra akkor van szükség, ha engedélyezni szeretné a HTTP – HTTPS átirányítást. Átirányítja a forgalmat a forrás-figyelőtől, amely ellenőrzi a bejövő HTTP-kéréseket a cél figyelőnek, amely ellenőrzi a bejövő HTTPS-kéréseket. Azt is megteheti, hogy a lekérdezési karakterláncot és az elérési utat az eredeti kérelemből is felveszi az átirányítási célra továbbított kérelembe.

![Összetevők Application Gateway párbeszédpanel](./media/configuration-overview/configure-redirection.png)

További információ a HTTP – HTTPS átirányítással kapcsolatban:
- [HTTP – HTTPS átirányítás a Azure Portal használatával](redirect-http-to-https-portal.md)
- [HTTP – HTTPS átirányítás a PowerShell használatával](redirect-http-to-https-powershell.md)
- [HTTP – HTTPS átirányítása az Azure CLI használatával](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>Külső hely

Válassza a külső hely lehetőséget, ha át szeretné irányítani a szabályhoz társított figyelő forgalmát egy külső helyre. Dönthet úgy, hogy a lekérdezési karakterláncot az eredeti kérelemből adja meg az átirányítási célra továbbított kérelemben. Az elérési út nem továbbítható az eredeti kérelemben szereplő külső helyhez.

További információ az átirányítással kapcsolatban:
- [Forgalom átirányítása egy külső helyre a PowerShell használatával](redirect-external-site-powershell.md)
- [Forgalom átirányítása egy külső helyre a parancssori felület használatával](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>HTTP-fejlécek és URL átírása

Az Újraírási szabályok használatával a HTTP (S) kérések és válaszok fejléceit, valamint az URL-cím és a lekérdezési karakterlánc paramétereit is hozzáadhatja, eltávolíthatja vagy frissítheti, mivel a kérelmek és válaszok csomagjai az Application Gateway segítségével az ügyfél és a háttérbeli készletek között mozognak.

A fejlécek és URL-paraméterek statikus értékekre vagy más fejlécekre és kiszolgálói változókra állíthatók be. Ez segíti a fontos használati eseteket, például az ügyfél IP-címeinek kinyerését, a háttér bizalmas adatainak eltávolítását, a nagyobb biztonság hozzáadását stb.
További információkért lásd:

 - [HTTP-fejlécek és URL-címek átírása – áttekintés](rewrite-http-headers-url.md)
 - [HTTP-fejléc újraírásának konfigurálása](rewrite-http-headers-portal.md)
 - [URL-cím újraírásának konfigurálása](rewrite-url-portal.md)

## <a name="next-steps"></a>Következő lépések

- [További tudnivalók a HTTP-beállításokról](configuration-http-settings.md)