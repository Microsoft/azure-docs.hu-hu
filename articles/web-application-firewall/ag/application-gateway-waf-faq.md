---
title: Azure webalkalmazási tűzfal Application Gatewayban – gyakori kérdések
description: Ez a cikk a webalkalmazási tűzfallal kapcsolatos gyakori kérdésekre ad választ Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 890688dba70a7fa654e97652b3e474b919f9a077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585383"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Gyakori kérdések az Azure webalkalmazási tűzfallal kapcsolatban Application Gateway

Ez a cikk az Azure webalkalmazási tűzfallal (WAF) kapcsolatos gyakori kérdésekre ad választ a Application Gateway szolgáltatásaival és funkcióival kapcsolatban. 

## <a name="what-is-azure-waf"></a>Mi az Azure WAF?

Az Azure WAF egy webalkalmazási tűzfal, amely segít megvédeni webalkalmazásait az olyan gyakori fenyegetésektől, mint az SQL-injektálás, a helyek közötti parancsfájlkezelés és más webszolgáltatások használata. A webalkalmazásokhoz való hozzáférés szabályozásához egyéni és felügyelt szabályok kombinációjából álló WAF-szabályzatot is meghatározhat.

Az Azure-WAF szabályzatok alkalmazhatók Application Gateway vagy Azure-beli előtérben futó webalkalmazásokra is.

## <a name="what-features-does-the-waf-sku-support"></a>Milyen funkciókat támogat a WAF SKU?

A WAF SKU a standard SKU-ban elérhető összes funkciót támogatja.

## <a name="how-do-i-monitor-waf"></a>Hogyan monitor WAF?

WAF figyelése diagnosztikai naplózással. További információ: [diagnosztikai naplózás és mérőszámok Application Gatewayhoz](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>Blokkolja az észlelési mód a forgalmat?

Nem. Az észlelési mód csak a WAF-szabályt kiváltó forgalmat naplózza.

## <a name="can-i-customize-waf-rules"></a>Testreszabhatók a WAF-szabályok?

Igen. További információ: [WAF és-szabályok testreszabása](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Jelenleg milyen szabályok érhetők el a WAF?

A WAF jelenleg a CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), a [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)és a [3,1](application-gateway-crs-rulegroups-rules.md#owasp31)-es verziója támogatja. Ezek a szabályok alapkonfigurációt biztosítanak a Web Application Security Project (OWASP) által azonosított legtöbb 10 legfontosabb biztonsági rés ellen: 

* SQL-injektálás elleni védelem
* Helyek közötti parancsfájlok elleni védelem
* Védelem a gyakori webes támadásokkal szemben, például a parancsok befecskendezése, a HTTP-kérések csempészete, a HTTP-válaszok felosztása és a távoli fájl-integrációs támadás ellen
* HTTP protokoll megsértése elleni védelem
* HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem
* Robotprogramok, webbejárók és képolvasók elleni védelem
* Az alkalmazások gyakori konfigurációs beállításainak észlelése (azaz Apache, IIS stb.)

További információ: [OWASP Top-10 biztonsági rések](https://owasp.org/www-project-top-ten/).

## <a name="what-content-types-does-waf-support"></a>Milyen tartalomtípusokat támogat a WAF?

Application Gateway WAF a következő tartalomtípusokat támogatja a felügyelt szabályokhoz:

* application/json
* alkalmazás/XML
* Application/x-www-Form-urlencoded
* multipart/űrlap – adattípusok

És egyéni szabályok esetén:

* Application/x-www-Form-urlencoded
* multipart/űrlap – adattípusok

## <a name="does-waf-support-ddos-protection"></a>Támogatja a WAF a DDoS Protectiont?

Igen. Engedélyezheti a DDoS Protection szolgáltatást azon a virtuális hálózaton, amelyen az Application Gateway telepítve van. Ezzel a beállítással biztosíthatja, hogy a Azure DDoS Protection szolgáltatás az Application Gateway virtuális IP-címét (VIP) is védi.

### <a name="does-waf-store-customer-data"></a>Az WAF tárolja az ügyféladatokat?

Nem, a WAF nem tárolja az ügyféladatokat.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az [Azure webalkalmazási tűzfalat](../overview.md).
- További információ az [Azure bejáratáról](../../frontdoor/front-door-overview.md).
