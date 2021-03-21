---
title: Biztonság
description: Ismerje meg, hogyan segít a App Service az alkalmazás biztonságossá tételében, és hogy miként lehet tovább zárolni az alkalmazást a fenyegetésektől.
keywords: Azure app Service, webalkalmazás, mobil alkalmazás, API-alkalmazás, Function app, biztonság, biztonságos, biztonságos, megfelelőség, megfelelő, tanúsítvány, tanúsítványok, HTTPS, FTPS, TLS, megbízhatóság, titkosítás, titkosítás, titkosított, IP-korlátozás, hitelesítés, engedélyezés, authn, autho, MSI, felügyelt szolgáltatás identitása, felügyelt identitás, titok, titok, javítás, javítás, javítás, verzió, elkülönítés, hálózati elkülönítés, DDoS, MitM
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 61bffcfa8b98ed666e450c344023258b752e4880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736105"
---
# <a name="security-in-azure-app-service"></a>Biztonság az Azure App Service-ben

Ez a cikk bemutatja, hogyan segíti a [Azure app Service](overview.md) a webalkalmazások, a Mobile App back end, az API-alkalmazás és a [Function-alkalmazás](../azure-functions/index.yml)biztonságossá tételét. Azt is bemutatja, hogyan lehet tovább biztonságossá tenni az alkalmazást a beépített App Service funkciókkal.

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Az alábbi fejezetek azt mutatják be, hogyan lehet tovább védelemmel ellátni App Service alkalmazást a fenyegetésektől.

## <a name="https-and-certificates"></a>HTTPS és tanúsítványok

A App Service segítségével biztonságossá teheti az alkalmazásokat a [https](https://wikipedia.org/wiki/HTTPS)használatával. Az alkalmazás létrehozásakor az alapértelmezett tartományneve ( \<app_name> . azurewebsites.net) már elérhető a HTTPS protokoll használatával. Ha [Egyéni tartományt állít be az alkalmazáshoz](app-service-web-tutorial-custom-domain.md), akkor azt is biztonságossá kell tennie [egy TLS-/SSL-tanúsítvánnyal](configure-ssl-bindings.md) , hogy az ügyféloldali böngészők biztonságos HTTPS-kapcsolatot tegyenek lehetővé az egyéni tartományhoz. A App Service számos különböző típusú tanúsítványt támogat:

- Felügyelt tanúsítvány Ingyenes App Service
- Tanúsítvány App Service
- Külső gyártótól származó tanúsítvány
- Azure Key Vaultból importált tanúsítvány

További információ: [TLS/SSL-tanúsítvány hozzáadása Azure app Serviceban](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Nem biztonságos protokollok (HTTP, TLS 1,0, FTP)

Ha az alkalmazást az összes titkosítatlan (HTTP) kapcsolaton biztonságossá szeretné tenni, App Service egy kattintással konfigurálhatja a HTTPS-t. A nem biztonságos kérések még az alkalmazás kódjának elérése előtt el vannak kapcsolva. További információ: [https kikényszerítés](configure-ssl-bindings.md#enforce-https).

A [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1,0 már nem tekinthető biztonságosnak az iparági szabványok, például a [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service lehetővé teszi az elavult protokollok letiltását a [TLS 1.1/1.2 kényszerítésével](configure-ssl-bindings.md#enforce-tls-versions).

A App Service az FTP-t és a FTPS is támogatja a fájlok telepítéséhez. A FTPS-et azonban FTP helyett kell használni, ha lehetséges. Ha az egyik vagy mindkét protokoll nincs használatban, [Tiltsa le őket](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statikus IP-korlátozások

Alapértelmezés szerint a App Service alkalmazás az internetről érkező összes IP-címről fogad kérelmeket, de az IP-címek kis részhalmazára korlátozhatja a hozzáférést. A Windows App Service lehetővé teszi az alkalmazás elérésére jogosult IP-címek listájának megadását. Az engedélyezett lista tartalmazhat egyedi IP-címeket vagy egy alhálózati maszk által meghatározott IP-címtartományt. További információ: [Azure app Service statikus IP-korlátozások](app-service-ip-restrictions.md).

A Windows App Service esetén a _web.config_ konfigurálásával dinamikusan is korlátozhatja az IP-címeket. További információ: [dinamikus IP-biztonság \<dynamicIpSecurity>](/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Ügyfél-hitelesítés és engedélyezés

Azure App Service a felhasználók vagy ügyfélalkalmazások kulcsrakész hitelesítését és engedélyezését teszi lehetővé. Ha ez a beállítás engedélyezve van, a felhasználók és az ügyfélalkalmazások csak kis vagy egyetlen alkalmazási kóddal jelentkezhetnek be. Saját hitelesítési és engedélyezési megoldást is alkalmazhat, vagy engedélyezheti a App Service számára, hogy az Ön számára kezelje azt. A hitelesítési és engedélyezési modul a webes kérelmeket az alkalmazás kódjába való átadás előtt kezeli, és megtagadja a jogosulatlan kérelmeket, mielőtt azok elérnék a kódot.

App Service a hitelesítés és az engedélyezés több hitelesítésszolgáltató használatát támogatja, beleértve a Azure Active Directory, a Microsoft-fiókokat, a Facebook, a Google és a Twitter szolgáltatást. További információkért lásd: [Hitelesítés és engedélyezés az Azure App Service-ben](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés

A háttérrendszer hitelesítése során a App Service két különböző mechanizmust biztosít a szükségestől függően:

- **Szolgáltatás identitása** – jelentkezzen be a távoli erőforrásba az alkalmazás identitásával. App Service lehetővé teszi, hogy könnyen hozzon létre egy [felügyelt identitást](overview-managed-identity.md), amelyet más szolgáltatásokkal való hitelesítéshez használhat, például [Azure SQL Database](/azure/sql-database/) vagy [Azure Key Vault](../key-vault/index.yml). Ennek a megközelítésnek a teljes körű oktatóanyagát lásd: [biztonságos Azure SQL Database a app Service-kapcsolatot felügyelt identitás használatával](app-service-web-tutorial-connect-msi.md).
- **Bejelentkezett (OBO)** – a felhasználó nevében delegált hozzáférést biztosít a távoli erőforrásokhoz. A Azure Active Directory hitelesítési szolgáltatóként a App Service alkalmazás delegált bejelentkezést hajthat végre egy távoli szolgáltatáshoz, például [Microsoft Graph API](../active-directory/develop/microsoft-graph-intro.md) -hoz vagy egy távoli API-alkalmazáshoz app Service. Ennek a módszernek a teljes körű oktatóanyagát lásd: a [felhasználók hitelesítése és engedélyezése a Azure app Service](tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Távoli erőforrásokhoz való kapcsolódás

Az alkalmazásnak háromféle távoli erőforrással kell rendelkeznie a következők eléréséhez: 

- [Azure-erőforrások](#azure-resources)
- [Azure-Virtual Network belüli erőforrások](#resources-inside-an-azure-virtual-network)
- [Helyszíni erőforrások](#on-premises-resources)

Ezekben az esetekben a App Service lehetővé teszi a biztonságos kapcsolatok biztosítását, de továbbra is érdemes megfigyelni az ajánlott biztonsági eljárásokat. Például mindig titkosított kapcsolatokat használjon, még akkor is, ha a háttér-erőforrás engedélyezi a titkosítatlan kapcsolatokat. Továbbá győződjön meg arról, hogy a háttérbeli Azure-szolgáltatás lehetővé teszi az IP-címek minimális készletének használatát. Az alkalmazás kimenő IP-címeit a [bejövő és kimenő IP-címek Azure app Serviceban](overview-inbound-outbound-ips.md)találhatja meg.

### <a name="azure-resources"></a>Azure-erőforrások

Ha az alkalmazás az Azure-erőforrásokhoz, például a [SQL Databasehoz](https://azure.microsoft.com/services/sql-database/) és az [Azure Storage](../storage/index.yml)-hoz csatlakozik, a kapcsolat az Azure-ban marad, és nem keresztez semmilyen hálózati határt. A kapcsolat azonban a megosztott hálózatkezelésen keresztül zajlik az Azure-ban, ezért mindig ellenőrizze, hogy a kapcsolat titkosítva van-e. 

Ha az alkalmazás egy [app Service környezetben](environment/intro.md)üzemel, akkor [Virtual Network szolgáltatás-végpontok használatával kell csatlakoznia a támogatott Azure-szolgáltatásokhoz](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Azure-Virtual Network belüli erőforrások

Az alkalmazás [Virtual Network-integráción](web-sites-integrate-with-vnet.md)keresztül férhet hozzá az [Azure-Virtual Network](../virtual-network/index.yml) erőforrásaihoz. Az integráció egy Virtual Network pont – hely típusú VPN-kapcsolattal van létrehozva. Az alkalmazás ezután hozzáférhet a Virtual Network lévő erőforrásokhoz saját IP-címeik használatával. A pont – hely kapcsolat azonban továbbra is a megosztott hálózatokon halad át az Azure-ban. 

Ha az erőforrás-kapcsolatot teljesen el szeretné különíteni az Azure-beli megosztott hálózatokból, hozza létre az alkalmazást [app Service környezetben](environment/intro.md). Mivel egy App Service környezet mindig dedikált Virtual Network van telepítve, az alkalmazás és a Virtual Network belüli erőforrások közötti kapcsolat teljesen elkülönített. App Service környezetben a hálózati biztonság egyéb szempontjaival kapcsolatban lásd: [hálózati elkülönítés](#network-isolation).

### <a name="on-premises-resources"></a>Helyszíni erőforrások

A helyszíni erőforrásokhoz (például adatbázisokhoz) való biztonságos hozzáférés háromféleképpen lehetséges: 

- [Hibrid kapcsolatok](app-service-hybrid-connections.md) – pont-pont típusú kapcsolatot létesít a távoli erőforrással egy TCP-alagúton keresztül. A TCP-alagút a TLS 1,2 közös hozzáférés-aláírási (SAS-) kulcsokkal van létrehozva.
- Virtual Network a helyek közötti VPN-sel való [integrációt](web-sites-integrate-with-vnet.md) – az [Azure-Virtual Network belüli erőforrásokban](#resources-inside-an-azure-virtual-network)leírtak szerint, de a Virtual Network a [helyek közötti VPN](../vpn-gateway/tutorial-site-to-site-portal.md)-kapcsolaton keresztül csatlakozhat a helyszíni hálózathoz. Ebben a hálózati topológiában az alkalmazás olyan helyszíni erőforrásokhoz is csatlakozhat, mint például a Virtual Network egyéb erőforrásai.
- [App Service környezet](environment/intro.md) helyek közötti VPN-vel – az [Azure-Virtual Network belüli erőforrásokban](#resources-inside-an-azure-virtual-network)leírtak szerint, de a Virtual Network a [helyek közötti VPN](../vpn-gateway/tutorial-site-to-site-portal.md)-kapcsolaton keresztül csatlakozhat a helyszíni hálózathoz. Ebben a hálózati topológiában az alkalmazás olyan helyszíni erőforrásokhoz is csatlakozhat, mint például a Virtual Network egyéb erőforrásai.

## <a name="application-secrets"></a>Alkalmazás-titkok

Ne tárolja az alkalmazás-titkokat, például az adatbázis hitelesítő adatait, az API-jogkivonatokat és a titkos kulcsokat a kódban vagy a konfigurációs fájlokban. Az általánosan elfogadott módszer az, hogy az Ön által választott nyelven a standard minta alapján [környezeti változókként](https://wikipedia.org/wiki/Environment_variable) férjen hozzájuk. App Service a környezeti változók definiálásának módja az [Alkalmazásbeállítások](configure-common.md#configure-app-settings) (és különösen a .NET-alkalmazások, a [kapcsolódási karakterláncok](configure-common.md#configure-connection-strings)) használatával történik. Az Alkalmazásbeállítások és a kapcsolódási karakterláncok az Azure-ban vannak titkosítva, és csak akkor lesznek visszafejtve, ha az alkalmazás elindul az alkalmazás folyamatának memóriájában. A titkosítási kulcsok elforgatása rendszeresen történik.

Azt is megteheti, hogy a App Service alkalmazást a speciális titkok kezeléséhez [Azure Key Vault](../key-vault/index.yml) segítségével integrálja. Ha [a Key Vault felügyelt identitással fér hozzá](../key-vault/general/tutorial-net-create-vault-azure-web-app.md), a app Service alkalmazás biztonságosan hozzáférhet a szükséges titkokhoz.

## <a name="network-isolation"></a>Hálózatelkülönítés

Az **elkülönített** díjszabási szinten kívül minden csomag a app Service megosztott hálózati infrastruktúráján futtatja az alkalmazásokat. A nyilvános IP-címek és az előtér-terheléselosztó például más Bérlővel is megoszthatók. Az **elkülönített** csomag lehetővé teszi a hálózati elkülönítést az alkalmazások dedikált [app Service környezetben](environment/intro.md)való futtatásával. App Service környezet az [Azure Virtual Network](../virtual-network/index.yml)saját példányában fut. A következőkre van lehetőség: 

- Az alkalmazásokat dedikált nyilvános végponton keresztül, dedikált kezelőfelülettel tálalhatja.
- Belső alkalmazás kiszolgálása belső terheléselosztó (ILB) használatával, amely csak az Azure-Virtual Networkon belülről engedélyezi a hozzáférést. A ILB IP-címmel rendelkezik a privát alhálózatból, amely az alkalmazások teljes elkülönítését biztosítja az internetről.
- [Használjon egy ILB a webalkalmazási tűzfal mögött (WAF)](environment/integrate-with-application-gateway.md). A WAF nagyvállalati szintű védelmet kínál a nyilvános alkalmazásokhoz, például a DDoS-védelemhez, az URI-szűréshez és az SQL-injektálások megelőzéséhez.

További információ: [bevezetés Azure app Service környezetekben](environment/intro.md).