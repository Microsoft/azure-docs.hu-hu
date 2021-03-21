---
title: A Microsoft Threat Modeling Tool kommunikációs biztonsága
titleSuffix: Azure
description: Ismerje meg a Threat Modeling Toolban elérhető kommunikációs biztonsági fenyegetések enyhítését. Tekintse meg a kockázatcsökkentő információkat és a kód megjelenítése példákat.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: d9a4eabf37101622ac69ae05f3bec232fb8d2fe6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517529"
---
# <a name="security-frame-communication-security--mitigations"></a>Biztonsági keret: kommunikációs biztonság | Enyhítését 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Biztonságos kommunikáció az Event hub szolgáltatással SSL/TLS használatával](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[A szolgáltatásfiók-jogosultságok ellenőrzését és annak ellenőrzését, hogy az egyéni szolgáltatások vagy a ASP.NET-lapok figyelembe veszik-e a CRM biztonságát](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Az adatkezelési átjáró használata a helyszíni SQL Server csatlakoztatásával Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Identity Server** | <ul><li>[Győződjön meg arról, hogy az Identity Server felé irányuló összes forgalom HTTPS-kapcsolaton keresztül van](#identity-https)</li></ul> |
| **Webalkalmazás** | <ul><li>[Az SSL-, TLS-és DTLS-kapcsolatok hitelesítéséhez használt X. 509 tanúsítványok ellenőrzése](#x509-ssltls)</li><li>[A TLS/SSL-tanúsítvány konfigurálása az egyéni tartományhoz Azure App Service](#ssl-appservice)</li><li>[A HTTPS-kapcsolaton keresztül Azure App Service összes forgalom kényszerítése](#appservice-https)</li><li>[HTTP-alapú szigorú átviteli biztonság engedélyezése (HSTS)](#http-hsts)</li></ul> |
| **Adatbázis** | <ul><li>[Az SQL Server-kapcsolatok titkosításának és a tanúsítvány ellenőrzésének biztosítása](#sqlserver-validation)</li><li>[Titkosított kommunikáció kényszerítése az SQL Serverrel](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Győződjön meg arról, hogy az Azure Storage-ba irányuló kommunikáció HTTPS-alapú](#comm-storage)</li><li>[MD5-kivonat érvényesítése a blob letöltése után, ha a HTTPS nem engedélyezhető](#md5-https)</li><li>[SMB 3,0-kompatibilis ügyfél használata az Azure-fájlmegosztás továbbításának biztosítása érdekében](#smb-shares)</li></ul> |
| **Mobil ügyfél** | <ul><li>[Tanúsítvány-rögzítés implementálása](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[HTTPS-alapú biztonságos átviteli csatorna engedélyezése](#https-transport)</li><li>[WCF: üzenet biztonsági védelmi szintjének beállítása EncryptAndSign](#message-protection)</li><li>[WCF: a WCF szolgáltatás futtatásához használjon egy legkevésbé Kiemelt jogosultságú fiókot](#least-account-wcf)</li></ul> |
| **Webes API** | <ul><li>[A webes API-khoz érkező összes forgalom kényszerítése HTTPS-kapcsolaton keresztül](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Győződjön meg arról, hogy a Redis-hez készült Azure cache-vel való kommunikáció több mint TLS-](#redis-ssl)</li></ul> |
| **IoT-mező átjárója** | <ul><li>[Az eszköz és a mező közötti átjáró kommunikációjának biztonságossá tétele](#device-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Az eszköz és a felhőalapú átjáró közötti kommunikáció biztonságossá tétele SSL/TLS használatával](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Biztonságos kommunikáció az Event hub szolgáltatással SSL/TLS használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](../../event-hubs/authenticate-shared-access-signature.md) |
| **Lépések** | Biztonságos AMQP vagy HTTP-kapcsolatok az Event hub-hoz SSL/TLS használatával |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>A szolgáltatásfiók-jogosultságok ellenőrzését és annak ellenőrzését, hogy az egyéni szolgáltatások vagy a ASP.NET-lapok figyelembe veszik-e a CRM biztonságát

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | A szolgáltatásfiók-jogosultságok ellenőrzését és annak ellenőrzését, hogy az egyéni szolgáltatások vagy a ASP.NET-lapok figyelembe veszik-e a CRM biztonságát |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Az adatkezelési átjáró használata a helyszíni SQL Server csatlakoztatásával Azure Data Factory

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Data Factory | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Társított szolgáltatások típusai – Azure és helyszíni |
| **Hivatkozások**              |Az [adatáthelyezés a helyszíni és a Azure Data Factory](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-gateway), [adatkezelési átjáró](../../data-factory/v1/data-factory-data-management-gateway.md) között |
| **Lépések** | <p>Az adatkezelés átjáró (DMG) eszköz a Corpnet vagy tűzfal mögött védett adatforrásokhoz való kapcsolódáshoz szükséges.</p><ol><li>A számítógép zárolása elkülöníti a DMG eszközt, és megakadályozza, hogy a hibás programok megsértsék vagy megtörténjenek az adatforrás számítógépén. Pl. telepíteni kell a legújabb frissítéseket, engedélyezni kell a minimálisan szükséges portokat, a vezérelt fiókok üzembe helyezését, a naplózást, engedélyezve van a lemez titkosítása stb.)</li><li>Az adatátjáró kulcsát gyakori időközönként kell elforgatni, vagy amikor a DMG-szolgáltatásfiók jelszava megújítja</li><li>Az adatátvitelt a link Service-en keresztül titkosítani kell</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Győződjön meg arról, hogy az Identity Server felé irányuló összes forgalom HTTPS-kapcsolaton keresztül van

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [IdentityServer3 – kulcsok, aláírások és titkosítás](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 – üzembe helyezés](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Lépések** | Alapértelmezés szerint a IdentityServer megköveteli, hogy minden bejövő kapcsolat HTTPS-alapú legyen. Feltétlenül szükséges, hogy a IdentityServer-mel folytatott kommunikáció csak biztonságos átvitelen keresztül történjen. Vannak bizonyos üzembe helyezési forgatókönyvek, például a TLS-kiürítés, ahol ez a követelmény nyugodt lehet. További információért tekintse meg az Identity Server telepítési lapját a hivatkozásokban. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Az SSL-, TLS-és DTLS-kapcsolatok hitelesítéséhez használt X. 509 tanúsítványok ellenőrzése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Az SSL-, TLS-vagy DTLS használó alkalmazásoknak teljes mértékben ellenőriznie kell a hozzájuk kapcsolódó entitások X. 509 tanúsítványait. Ide tartozik a tanúsítványok ellenőrzése:</p><ul><li>Tartománynév</li><li>Érvényességi dátumok (a kezdő és a lejárati dátumok esetében is)</li><li>Visszavonás állapota</li><li>Használat (például kiszolgáló-hitelesítés kiszolgálók esetében, ügyfél-hitelesítés ügyfelek számára)</li><li>Megbízhatósági lánc. A tanúsítványoknak olyan legfelső szintű hitelesítésszolgáltatónak (CA) kell láncot biztosítaniuk, amelyet a platform megbízhatónak minősít, vagy amelyet a rendszergazda explicit módon konfigurált.</li><li>A tanúsítvány nyilvános kulcsának hosszának >2048 bitenek kell lennie</li><li>A kivonatoló algoritmusnak SHA256 és újabbnak kell lennie |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>A TLS/SSL-tanúsítvány konfigurálása az egyéni tartományhoz Azure App Service

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | [HTTPS engedélyezése alkalmazáshoz Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Lépések** | Alapértelmezés szerint az Azure a *. azurewebsites.net tartományhoz tartozó helyettesítő tanúsítvánnyal minden alkalmazáshoz már engedélyez HTTPS-t. Azonban az összes helyettesítő tartományhoz hasonlóan nem olyan biztonságos, mint a [saját tanúsítvánnyal rendelkező](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)egyéni tartomány használata. Javasoljuk, hogy engedélyezze a TLS-t az egyéni tartomány számára, amelyet a telepített alkalmazás a következővel fog elérni:|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>A HTTPS-kapcsolaton keresztül Azure App Service összes forgalom kényszerítése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | [HTTPS kényszerített Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Lépések** | <p>Habár az Azure már engedélyezi a HTTPS-t az Azure app Serviceshez, és a (*. azurewebsites.net) tartományhoz helyettesítő tanúsítvánnyal rendelkezik, nem kényszeríti ki a HTTPS-t. A látogatók továbbra is hozzáférhetnek az alkalmazáshoz a HTTP használatával, ami veszélyeztetheti az alkalmazás biztonságát, ezért a HTTPS-t explicit módon kell kikényszeríteni. A ASP.NET MVC-alkalmazásoknak a [RequireHttps szűrőt](/dotnet/api/system.web.mvc.requirehttpsattribute) kell használniuk, amely kényszeríti a nem biztonságos http-KÉRÉSek HTTPS protokollon keresztüli újraküldéseit.</p><p>Azt is megteheti, hogy az URL-cím újraírására szolgáló modul, amelyet a Azure App Service tartalmaz, a HTTPS kikényszeríthető. Az URL-átírás modul lehetővé teszi a fejlesztők számára a bejövő kérelmekre alkalmazott szabályok meghatározását, mielőtt a kérések bekerülnek az alkalmazásba. Az URL-átírási szabályok az alkalmazás gyökerében tárolt web.config fájlban vannak meghatározva.</p>|

### <a name="example"></a>Példa
Az alábbi példa egy alapszintű URL-re vonatkozó Újraírási szabályt tartalmaz, amely minden bejövő forgalmat a HTTPS használatára kényszerít
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Ez a szabály úgy működik, hogy egy 301-es HTTP-állapotkódot ad vissza (állandó átirányítás), amikor a felhasználó HTTP-t használó lapot kér. Az 301 átirányítja a kérelmet ugyanarra az URL-címre, mint amelyet a látogató kért, de a kérelem HTTP-részét a HTTPS értékre váltja. A rendszer `HTTP://contoso.com` átirányítja például a következőre: `HTTPS://contoso.com` . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>HTTP-alapú szigorú átviteli biztonság engedélyezése (HSTS)

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [OWASP HTTP-alapú szigorú átviteli biztonsági Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Lépések** | <p>A HTTP Strict Transport Security (HSTS) egy speciális válasz fejléc használatával egy webalkalmazás által meghatározott, a biztonsági fejlesztést engedélyező szolgáltatás. Ha egy támogatott böngésző megkapja ezt a fejlécet, a böngésző megakadályozza, hogy a kommunikáció HTTP-n keresztül történjen a megadott tartományon, és ehelyett a HTTPS protokollon keresztül küldje el az összes kommunikációt. Emellett megakadályozza a HTTPS-kattintást a böngészőkön keresztül.</p><p>A HSTS megvalósításához a következő válasz fejlécét globálisan kell konfigurálni egy webhelyhez a kódban vagy a konfigurációban. Szigorú szállítás – biztonság: max. Age = 300; a altartományok belefoglalása HSTS a következő fenyegetéseket kezeli:</p><ul><li>A felhasználói könyvjelzők vagy a manuális típusok, `https://example.com` amelyekre a rendszer egy ember által a középső támadót használ: a HSTS automatikusan átirányítja a http-kéréseket a CÉLTARTOMÁNY HTTPS-re.</li><li>A tisztán HTTPS-alapú webalkalmazások, amelyek szándékosan HTTP-hivatkozásokat tartalmaznak, vagy HTTP-n keresztül szolgáltatnak tartalmat: a HSTS automatikusan átirányítja a HTTP-kéréseket a céltartomány HTTPS-re.</li><li>Egy ember által a közepén lévő támadó egy érvénytelen tanúsítvánnyal kísérli meg feltartóztatni a forgalmat egy sértett felhasználótól, és reméli, hogy a felhasználó elfogadja a hibás tanúsítványt: a HSTS nem teszi lehetővé, hogy a felhasználó felülbírálja az érvénytelen tanúsítvány-üzenetet.</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Az SQL Server-kapcsolatok titkosításának és a tanúsítvány ellenőrzésének biztosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | SQL Azure  |
| **Attribútumok**              | SQL-verzió – V12 |
| **Hivatkozások**              | [Ajánlott eljárások biztonságos kapcsolatok sztringek írásához SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Lépések** | <p>A SQL Database és az ügyfélalkalmazás közötti összes kommunikációt Transport Layer Security (TLS), korábbi nevén SSL (SSL) használatával titkosítják. A SQL Database nem támogatja a titkosítatlan kapcsolatokat. Ha az alkalmazás kódjával vagy eszközeivel szeretné érvényesíteni a tanúsítványokat, explicit módon igényeljen titkosított kapcsolatot, és ne Bízzon meg a kiszolgálói tanúsítványokban. Ha az alkalmazás kódja vagy eszközei nem igényelnek titkosított kapcsolatot, továbbra is megkapják a titkosított kapcsolatokat</p><p>Előfordulhat azonban, hogy nem érvényesítik a kiszolgálói tanúsítványokat, így azok a "Man in the middle" támadásokban lesznek kitéve. A ADO.NET-kóddal rendelkező tanúsítványok érvényesítéséhez állítsa `Encrypt=True` `TrustServerCertificate=False` a és az adatbázis-kapcsolódási karakterláncot. A tanúsítványok SQL Server Management Studio használatával történő ellenőrzéséhez nyissa meg a Kapcsolódás a kiszolgálóhoz párbeszédpanelt. A kapcsolatok tulajdonságai lapon kattintson a kapcsolatok titkosítása elemre.</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Titkosított kommunikáció kényszerítése az SQL Serverrel

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | OnPrem |
| **Attribútumok**              | SQL-verzió – MsSQL2016, SQL-verzió – MsSQL2012, SQL-verzió – MsSQL2014 |
| **Hivatkozások**              | [Az adatbázismotorhoz titkosított kapcsolattal való csatlakozás engedélyezése](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)  |
| **Lépések** | A TLS-titkosítás engedélyezése növeli a hálózatok között továbbított adatok biztonságát SQL Server és alkalmazások példányai között. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Győződjön meg arról, hogy az Azure Storage-ba irányuló kommunikáció HTTPS-alapú

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Azure Storage Transport-Level titkosítás – HTTPS használatával](../../storage/blobs/security-recommendations.md#networking) |
| **Lépések** | Az Azure Storage-adatok átvitelének biztonsága érdekében mindig a HTTPS protokollt használja a REST API-k meghívásakor vagy a tárolóban lévő objektumok eléréséhez. Emellett a közös hozzáférésű aláírások, amelyek az Azure Storage-objektumokhoz való hozzáférés delegálására használhatók, egy beállítással megadhatja, hogy csak a HTTPS protokollt lehessen használni a közös hozzáférési aláírások használatakor, így biztosíthatja, hogy az SAS-tokenekkel rendelkező bárki a megfelelő protokollt használja.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>MD5-kivonat érvényesítése a blob letöltése után, ha a HTTPS nem engedélyezhető

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType – blob |
| **Hivatkozások**              | [A Windows Azure Blob MD5 áttekintése](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Lépések** | <p>A Windows Azure Blob service olyan mechanizmusokat biztosít, amelyek biztosítják az adatok integritását mind az alkalmazás-, mind a szállítási rétegben. Ha bármilyen okból HTTPS helyett HTTP-t kell használnia, és a blokk Blobokkal dolgozik, az MD5-ellenőrzés segítségével ellenőrizheti az átvitt Blobok integritását.</p><p>Ez segít a hálózati/szállítási rétegbeli hibák elleni védelemben, de nem feltétlenül a közbenső támadásokkal szemben. Ha a HTTPS protokollt használja, amely átviteli szintű biztonságot biztosít, akkor az MD5-ellenőrzés redundáns és szükségtelen.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>SMB 3,0-kompatibilis ügyfél használata az Azure-fájlmegosztás továbbításának biztosítása érdekében

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobil ügyfél | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType-fájl |
| **Hivatkozások**              | [Azure file Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Azure file Storage SMB-támogatás Windows-ügyfelek számára](../../storage/files/storage-dotnet-how-to-use-files.md#understanding-the-net-apis) |
| **Lépések** | Az Azure File Storage támogatja a HTTPS-t a REST API használatakor, de gyakrabban használják egy virtuális géphez csatolt SMB-fájlmegosztásként. Az SMB 2,1 nem támogatja a titkosítást, így a kapcsolatok csak az Azure-beli azonos régión belül engedélyezettek. Az SMB 3,0 azonban támogatja a titkosítást, és a Windows Server 2012 R2, a Windows 8, a Windows 8,1 és a Windows 10 rendszerekkel használható, lehetővé téve a régiók közötti hozzáférést, és akár az asztalon is elérheti a hozzáférést. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Tanúsítvány-rögzítés implementálása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános, Windows Phone-telefon |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Tanúsítványra és nyilvános kulcsra vonatkozó rögzítés](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Lépések** | <p>A tanúsítvány rögzítésével védekezik a MITM-támadások ellen. A rögzítés a gazdagépnek a várt X509-tanúsítvánnyal vagy nyilvános kulccsal való társításának folyamata. Ha egy adott tanúsítvány vagy nyilvános kulcs ismert vagy látható egy gazdagép számára, a tanúsítvány vagy a nyilvános kulcs társítva van, vagy a gazdagéphez van rögzítve. </p><p>Így ha egy támadó megpróbál TLS-MITM támadást végrehajtani, a TLS-kézfogás során a támadó kiszolgálójának kulcsa eltér a rögzített tanúsítvány kulcsaitól, és a rendszer elveti a kérést, így megakadályozza a MITM-tanúsítványok rögzítését a ServicePointManager `ServerCertificateValidationCallback` delegált megvalósításával.</p>|

### <a name="example"></a>Példa
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>HTTPS-alapú biztonságos átviteli csatorna engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [megerősítő Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Lépések** | Az alkalmazás konfigurációjának biztosítania kell, hogy a HTTPS-t használja a bizalmas adatokhoz való összes hozzáféréshez.<ul><li>**Magyarázat:** Ha egy alkalmazás bizalmas adatokat kezel, és nem használ üzenet szintű titkosítást, akkor csak titkosított átviteli csatornán keresztül lehet kommunikálni.</li><li>**Javaslatok:** Győződjön meg arról, hogy a HTTP-átvitel le van tiltva, és engedélyezze a HTTPS-átvitelt. Például cserélje le a `<httpTransport/>` with `<httpsTransport/>` címkét. Ne támaszkodjon hálózati konfigurációra (tűzfal) annak biztosítására, hogy az alkalmazás csak biztonságos csatornán keresztül érhető el. Filozófiai szempontból az alkalmazás nem függ a hálózattól a biztonsága szempontjából.</li></ul><p>Gyakorlati szempontból a hálózat védelméért felelős személyek nem mindig követik az alkalmazás biztonsági követelményeit, ahogy azok fejlődnek.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: üzenet biztonsági védelmi szintjének beállítása EncryptAndSign

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff650862(v=pandp.10)) |
| **Lépések** | <ul><li>**Magyarázat:** Ha a védelmi szint "None" értékre van állítva, akkor letiltja az üzenetek védelmét. A titkosság és az integritás megfelelő szintű beállítással érhető el.</li><li>**JAVASLATOK**<ul><li>Ha `Mode=None` – letiltja az üzenetek védelmét</li><li>Ha `Mode=Sign` a-Signs, de nem titkosítja az üzenetet, akkor kell használni, ha az adatok integritása fontos</li><li>Ha `Mode=EncryptAndSign` – aláírja és titkosítja az üzenetet</li></ul></li></ul><p>Érdemes lehet kikapcsolni a titkosítást, és csak akkor aláírni az üzenetet, ha csak az információ integritását szeretné ellenőrizni a titkossági probléma nélkül. Ez olyan műveletekhez vagy szolgáltatási szerződésekhez lehet hasznos, amelyekben érvényesíteni kell az eredeti feladót, de nem történik bizalmas adatok továbbítása. A védelmi szint csökkentése érdekében ügyeljen arra, hogy az üzenet ne tartalmazzon személyes adatmennyiséget.</p>|

### <a name="example"></a>Példa
A szolgáltatás konfigurálása és a művelet csak az üzenet aláírására szolgál, az alábbi példákban látható. Szolgáltatási szerződés példája `ProtectionLevel.Sign` : az alábbi példa a ProtectionLevel. Sign használatát szemlélteti a szolgáltatási szerződés szintjén: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Példa
Példa a műveleti szerződésre (a részletes `ProtectionLevel.Sign` szabályozáshoz): az alábbi példa a OperationContract szintjén történő használatát szemlélteti `ProtectionLevel.Sign` :

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: a WCF szolgáltatás futtatásához használjon egy legkevésbé Kiemelt jogosultságú fiókot

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff648826(v=pandp.10)) |
| **Lépések** | <ul><li>**Magyarázat:** Ne futtasson WCF-szolgáltatásokat rendszergazdai vagy magas jogosultsági szintű fiókkal. Ha a szolgáltatások megsérülnek, akkor nagy hatással lehet.</li><li>**Javaslatok:** Használjon egy legkevésbé Kiemelt fiókot a WCF szolgáltatás üzemeltetéséhez, mivel az csökkenti az alkalmazás támadási felületét, és csökkenti a lehetséges károkat, ha támadás történik. Ha a szolgáltatásfiók további hozzáférési jogosultságokat igényel az infrastruktúra-erőforrásokhoz, például az MSMQ-hoz, az eseménynaplóhoz, a teljesítményszámlálók és a fájlrendszerhez, megfelelő engedélyeket kell adni ezekhez az erőforrásokhoz, hogy a WCF szolgáltatás sikeresen futtatható legyen.</li></ul><p>Ha a szolgáltatásnak az eredeti hívó nevében hozzá kell férnie bizonyos erőforrásokhoz, a megszemélyesítés és delegálás használatával kell átirányítani a hívó identitását egy alsóbb rétegbeli engedélyezési vizsgálathoz. Fejlesztési forgatókönyv esetén használja a helyi hálózati szolgáltatás fiókját, amely egy speciális beépített fiók, amely csökkentett jogosultságokkal rendelkezik. Éles környezetben hozzon létre egy legkevésbé Kiemelt jogosultságú egyéni tartományi szolgáltatásfiókot.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>A webes API-khoz érkező összes forgalom kényszerítése HTTPS-kapcsolaton keresztül

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az SSL kényszerítése webes API-vezérlőben](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Lépések** | Ha egy alkalmazáshoz HTTPS és HTTP-kötés is tartozik, az ügyfelek továbbra is használhatnak HTTP-t a hely eléréséhez. Ennek megelőzése érdekében használjon műveleti szűrőt annak biztosítására, hogy a védett API-k felé irányuló kérések mindig HTTPS-alapúak legyenek.|

### <a name="example"></a>Példa 
A következő kód egy webes API-hitelesítési szűrőt mutat be, amely a TLS-t ellenőrzi: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Adja hozzá ezt a szűrőt a TLS-t igénylő webes API-műveletekhez: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Győződjön meg arról, hogy a Redis-hez készült Azure cache-vel való kommunikáció több mint TLS-

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Cache for Redis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Azure Redis TLS-támogatás](../../azure-cache-for-redis/cache-faq.md) |
| **Lépések** | A Redis-kiszolgáló nem támogatja a TLS-t a dobozból, de az Azure cache for Redis nem. Ha az Azure cache-hez csatlakozik a Redis-hez, és az ügyfél támogatja a TLS-t, például a StackExchange. Redis, akkor a TLS protokollt kell használnia. Alapértelmezés szerint a nem TLS port le van tiltva az új Azure cache-Redis-példányok esetében. Győződjön meg arról, hogy a biztonságos alapértékek nem változnak, kivéve, ha a Redis-ügyfelek TLS-támogatásának függősége van. |

Vegye figyelembe, hogy a Redis megbízható környezetekben megbízható ügyfelek által elérhetővé kell tenni. Ez azt jelenti, hogy az Redis-példányt általában nem célszerű közvetlenül az internetre közzétenni, vagy általában olyan környezetre, ahol a nem megbízható ügyfelek közvetlenül hozzáférhetnek a Redis TCP-porthoz vagy UNIX-szoftvercsatornához. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Az eszköz és a mező közötti átjáró kommunikációjának biztonságossá tétele

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-mező átjárója | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Az IP-alapú eszközök esetében a kommunikációs protokoll jellemzően egy SSL/TLS-csatornán ágyazható be az átvitel alatt álló adatátvitel védelme érdekében. Az SSL/TLS-t nem támogató protokollok esetében vizsgálja meg, hogy vannak-e olyan biztonságos verziók a protokollon, amelyek a szállítás vagy az üzenet rétegében biztonságot biztosítanak. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Az eszköz és a felhőalapú átjáró közötti kommunikáció biztonságossá tétele SSL/TLS használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Válassza ki a kommunikációs protokollt](../../iot-hub/iot-hub-devguide.md) |
| **Lépések** | Biztonságos HTTP/AMQP vagy MQTT protokollok SSL/TLS használatával. |