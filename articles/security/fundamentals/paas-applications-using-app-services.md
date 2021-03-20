---
title: A Pásti web & Mobile-alkalmazások biztonságossá tétele
titleSuffix: Azure App Service
description: 'Ismerkedjen meg Azure App Service biztonsági bevált gyakorlattal a Pásti web-és Mobile-alkalmazások biztonságossá tételéhez. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: 849743dd756eff27ec0670cc39d2419c60538183
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94701014"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Ajánlott eljárások a Pásti webes és mobil alkalmazások biztonságossá tételéhez Azure App Service használatával

Ebben a cikkben [Azure app Service](../../app-service/overview.md) biztonsági bevált eljárások gyűjteményét tárgyaljuk a Pásti web-és Mobile-alkalmazások biztonságossá tételéhez. Ezek az ajánlott eljárások az Azure tapasztalataiból és az ügyfelek, például saját tapasztalataiból származnak.

A Azure App Service egy szolgáltatásként nyújtott platform (Pásti), amely lehetővé teszi webes és mobil alkalmazások létrehozását bármilyen platformra vagy eszközre, és bárhol, a felhőben vagy a helyszínen csatlakozik az adatforrásokhoz. A App Service tartalmazza azokat a webes és mobil képességeket, amelyeket korábban az Azure websites és az Azure Mobile Services külön szállítottak el. Ezenkívül új lehetőségek is elérhetők az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként a App Service számos lehetőséget kínál webes, mobil-és integrációs forgatókönyvek kialakítására.

## <a name="authenticate-through-azure-active-directory-ad"></a>Hitelesítés Azure Active Directory (AD) használatával
A App Service OAuth 2,0 szolgáltatást biztosít az identitás-szolgáltató számára. A OAuth 2,0 a webalkalmazások, asztali alkalmazások és mobiltelefonok speciális engedélyezési folyamatainak biztosítása mellett az ügyfelek fejlesztői egyszerűségére koncentrál. Az Azure AD OAuth 2,0-et használ a mobil-és webalkalmazásokhoz való hozzáférés engedélyezéséhez. További információ: [hitelesítés és engedélyezés Azure app Serviceban](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Hozzáférés korlátozása a szerepkör alapján
A hozzáférés korlátozása elengedhetetlen azon szervezetek számára, akik biztonsági házirendeket kívánnak kikényszeríteni az adateléréshez. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával engedélyeket rendelhet hozzá a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy bizonyos hatókörben, például a szükséges ismeretek és a minimális jogosultságok biztonsági alapelveit. Ha többet szeretne megtudni arról, hogy a felhasználók hogyan férhetnek hozzá az alkalmazásokhoz, tekintse meg a [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)című témakört.

## <a name="protect-your-keys"></a>A kulcsok megóvása
Nem számít, hogy mennyire jó a biztonsága, ha elveszíti az előfizetési kulcsokat. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Key Vault segítségével titkosíthatja a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a Storage-fiók kulcsait, az adattitkosítási kulcsokat). PFX-fájlok és jelszavak) a hardveres biztonsági modulok (HSM-EK) által védett kulcsok használatával. A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban. A TLS-tanúsítványok automatikus megújítással történő kezeléséhez Key Vault is használhatja. További információ: [Mi a Azure Key Vault](../../key-vault/general/overview.md) .

## <a name="restrict-incoming-source-ip-addresses"></a>Bejövő forrás IP-címeinek korlátozása
[App Service környezetek](../../app-service/environment/intro.md) virtuális hálózati integrációs funkciója segítségével a bejövő forrás IP-címeket hálózati biztonsági csoportokkal (NSG) korlátozhatja. Ha nem ismeri az Azure Virtual Networks (virtuális hálózatok) szolgáltatást, ez egy olyan képesség, amely lehetővé teszi, hogy számos Azure-erőforrást egy nem internetes, átirányítható hálózaton helyezzen el, amely a hozzáférését szabályozza. További információ: [az alkalmazás integrálása Azure-Virtual Networkokkal](../../app-service/web-sites-integrate-with-vnet.md).

A Windows App Service esetén a web.config konfigurálásával dinamikusan is korlátozhatja az IP-címeket. További információ: [dinamikus IP-biztonság](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Következő lépések
Ez a cikk App Service biztonsági bevált eljárások gyűjteményét mutatta be a Pásti web-és Mobile-alkalmazások biztonságossá tételéhez. További információ a Pásti-telepítések biztonságossá tételéről:

- [PaaS-környezetek védelme](paas-deployments.md)
- [PaaS-adatbázisok védelme az Azure-ban](paas-applications-using-sql.md)