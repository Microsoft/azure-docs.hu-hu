---
title: Az átjáróval kapcsolatos problémák elhárítása adatkezelés
description: Tippeket nyújt adatkezelés átjáróval kapcsolatos problémák elhárításához.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 284486c5db248ced8ada6e7194c7bc5a9be5689f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100388345"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Az adatkezelési átjáró használata közben felmerülő hibák elhárítása
Ez a cikk a adatkezelés átjáró használatával kapcsolatos hibaelhárítási problémákkal kapcsolatos információkat tartalmaz.

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Data Factory saját üzemeltetésű integrációs modulját](../create-self-hosted-integration-runtime.md).

Az átjáróval kapcsolatos részletes információkért tekintse meg az [adatkezelés átjáróval](data-factory-data-management-gateway.md) foglalkozó cikket. Az adatok [áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) című cikkből megtudhatja, hogyan helyezhet át adatáthelyezést egy SQL Server-adatbázisból az átjáró használatával Microsoft Azure Blob Storage-ba.

## <a name="failed-to-install-or-register-gateway"></a>Nem sikerült az átjáró telepítése vagy regisztrálása
### <a name="1-problem"></a>1. probléma
Ez a hibaüzenet akkor jelenik meg, amikor egy átjárót telepít és regisztrál, különösen az átjáró telepítési fájljának letöltése közben.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Ok
A gép, amelyre telepíteni kívánja az átjárót, hálózati probléma miatt nem tudta letölteni a legújabb átjáró telepítési fájlját a letöltőközpontból.

#### <a name="resolution"></a>Feloldás
Ellenőrizze a tűzfal proxykiszolgáló-beállításait, hogy a beállítások letiltják-e a számítógép hálózati csatlakozását a [letöltőközpontból](https://download.microsoft.com/), és ennek megfelelően frissíti a beállításokat.

Azt is megteheti, hogy [a letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717) letölti a legújabb átjáró telepítési fájlját más olyan gépeken, amelyek hozzáférnek a letöltőközpontból. Ezután a telepítőfájlt átmásolhatja az átjárót futtató számítógépre, és manuálisan futtathatja az átjáró telepítéséhez és frissítéséhez.

### <a name="2-problem"></a>2. probléma
Ez a hiba akkor jelenik meg, ha egy átjáró telepítésére van szüksége, ha a Azure Portal a **számítógépen a telepítés közvetlenül** lehetőségre kattint.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Ok
Már telepítve van egy átjáró a gépen.

#### <a name="resolution"></a>Feloldás
Távolítsa el a gépen a meglévő átjárót, majd kattintson a **telepítés közvetlenül erre a számítógépre** hivatkozásra.

### <a name="3-problem"></a>3. probléma
Ez a hiba akkor fordulhat elő, amikor új átjárót regisztrál.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Ok
Ez az üzenet a következő okok egyike miatt jelenhet meg:

* Az átjáró kulcsának formátuma érvénytelen.
* Az átjáró kulcsa érvénytelenítve.
* Az átjáró kulcsa újra lett generált a portálról.  

#### <a name="resolution"></a>Feloldás
Ellenőrizze, hogy a megfelelő átjáró-kulcsot használja-e a portálon. Ha szükséges, állítson be egy kulcsot, és használja a kulcsot az átjáró regisztrálásához.

### <a name="4-problem"></a>4. probléma
Az átjáró regisztrálásakor a következő hibaüzenet jelenhet meg:

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![A kulcs tartalma vagy formátuma érvénytelen](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Ok
A bemeneti átjáró kulcsának tartalma vagy formátuma helytelen. Ennek egyik oka az lehet, hogy csak a kulcs egy részét másolta a portálról, vagy érvénytelen kulcsot használ.

#### <a name="resolution"></a>Feloldás
Egy átjáró kulcsának létrehozása a portálon, és a másolás gombbal másolja a teljes kulcsot. Ezután illessze be ebbe az ablakba az átjáró regisztrálásához.

### <a name="5-problem"></a>5. probléma
Az átjáró regisztrálásakor a következő hibaüzenet jelenhet meg:

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Képernyőkép, amely kiemeli, hogy az átjáró kulcsát jelző hibaüzenet érvénytelen vagy üres.](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Ok
Az átjáró kulcsa újra lett előkészítve, vagy az átjáró törlődött a Azure Portalban. Akkor is előfordulhat, ha a adatkezelés-átjáró telepítése nem legújabb.

#### <a name="resolution"></a>Feloldás
Ellenőrizze, hogy a legújabb verzió-e a adatkezelés-átjáró telepítése, a legújabb verziót találja a Microsoft [letöltőközpontból](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Ha a telepítés folyamatban van/a legfrissebb, és az átjáró továbbra is létezik a portálon, akkor a Azure Portalban adja újra az átjáró kulcsát, és a másolás gombbal másolja a teljes kulcsot, majd illessze be az ablakba az átjáró regisztrálásához. Ellenkező esetben hozza létre újra az átjárót, és kezdje újra.

### <a name="6-problem"></a>6. probléma
Az átjáró regisztrálásakor a következő hibaüzenet jelenhet meg:

`Error: Gateway has been online for a while, then shows "Gateway is not registered" with the status "Gateway key is invalid"`

![Az átjáró kulcsa érvénytelen vagy üres](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Ok
Ez a hiba akkor fordulhat elő, ha az átjárót törölték, vagy a társított átjáró kulcsát újragenerálták.

#### <a name="resolution"></a>Feloldás
Ha az átjárót törölték, hozza létre újra az átjárót a portálról, kattintson a **regisztráció** elemre, másolja a kulcsot a portálról, illessze be, majd próbálja meg regisztrálni az átjárót.

Ha az átjáró még létezik, de a kulcsa újra lett generált, akkor az új kulccsal regisztrálja az átjárót. Ha nem rendelkezik a kulccsal, újra létrehozza a kulcsot a portálról.

### <a name="7-problem"></a>7. probléma
Amikor regisztrál egy átjárót, lehet, hogy meg kell adnia egy tanúsítvány elérési útját és jelszavát.

![Képernyőfelvétel: Itt adhatja meg a tanúsítvány elérési útját és jelszavát.](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Ok
Az átjáró korábban már regisztrálva van más gépeken. Egy átjáró kezdeti regisztrálása során egy titkosítási tanúsítvány van társítva az átjáróhoz. A tanúsítványt az átjáró saját maga hozhatja létre, vagy a felhasználó által biztosíthatja.  Ez a tanúsítvány az adattár (társított szolgáltatás) hitelesítő adatainak titkosítására szolgál.  

![Tanúsítvány exportálása](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Ha egy másik gazdagépen állítja vissza az átjárót, a regisztrációs varázsló arra kéri a tanúsítványt, hogy fejtse vissza a tanúsítvánnyal korábban titkosított hitelesítő adatokat.  A tanúsítvány nélkül az új átjáró nem tudja visszafejteni a hitelesítő adatokat, és az új átjáróhoz társított későbbi másolási tevékenységek végrehajtása sikertelen lesz.  

#### <a name="resolution"></a>Feloldás
Ha a hitelesítő adatokat az eredeti átjárót tartalmazó gépről exportálta a adatkezelés Gateway Configuration Manager **Beállítások** lapján az **Exportálás** gombra kattintva, használja a tanúsítványt.

Az átjáró helyreállításakor ezt a szakaszt nem lehet kihagyni. Ha a tanúsítvány hiányzik, törölnie kell az átjárót a portálról, és újra létre kell hoznia egy új átjárót.  Továbbá frissítse az átjáróhoz kapcsolódó összes társított szolgáltatást a hitelesítő adataik újbóli megadásával.

### <a name="8-problem"></a>8. probléma
Előfordulhat, hogy a következő hibaüzenet jelenik meg.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Ok
Ez a hiba akkor fordul elő, ha az átjáró olyan környezetben van, amelyhez HTTP-proxy szükséges az internetes erőforrásokhoz való hozzáféréshez, vagy a proxy hitelesítési jelszava megváltozik, de az átjáróban nem frissül.

#### <a name="resolution"></a>Feloldás
Kövesse a jelen cikk proxykiszolgáló-megfontolások című szakaszának utasításait, és konfigurálja a proxybeállításokat adatkezelés átjáró Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Az átjáró korlátozott funkcionalitással van online állapotban
### <a name="1-problem"></a>1. probléma
Az átjáró állapota online állapotú, korlátozott funkcionalitással jelenik meg.

#### <a name="cause"></a>Ok
A következő okok egyike miatt az átjáró állapota online állapotú, korlátozott funkcionalitással jelenik meg:

* Az átjáró nem tud kapcsolódni a Cloud Service-hez Azure Service Buson keresztül.
* A Cloud Service nem tud csatlakozni az átjáróhoz Service Buson keresztül.

Ha az átjáró korlátozott funkcionalitású online állapotban van, előfordulhat, hogy nem tudja használni a Data Factory másolás varázslót, hogy adatfolyamatokat hozzon létre a helyszíni adattárakba vagy azokból történő adatmásoláshoz. Megkerülő megoldásként használhatja Data Factory szerkesztőt a portálon, a Visual Studióban vagy a Azure PowerShellban.

#### <a name="resolution"></a>Feloldás
A probléma megoldása (korlátozott funkcionalitással rendelkező online) attól függ, hogy az átjáró nem tud-e kapcsolódni a Cloud Service-hez vagy más módszerhez. A következő szakaszokban ismertetjük ezeket a megoldásokat.

### <a name="2-problem"></a>2. probléma
A következő hiba jelenik meg.

`Error: Gateway cannot connect to cloud service through service bus`

![Az átjáró nem tud csatlakozni a felhőalapú szolgáltatáshoz](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Ok
Az átjáró nem tud csatlakozni a felhőalapú szolgáltatáshoz Service Buson keresztül.

#### <a name="resolution"></a>Feloldás
Kövesse az alábbi lépéseket az átjáró újbóli letöltéséhez:

1. Engedélyezze az IP-címek kimenő szabályait az átjárót és a vállalati tűzfalat. Az IP-címek a Windows-eseménynaplóból (azonosító = = 401) találhatók: kísérlet történt egy szoftvercsatorna elérésére olyan módon, hogy az a hozzáférési engedélyeivel (XX. XX. XX. XX: 9350) tiltott.
1. Konfigurálja a proxybeállításokat az átjárón. A részletekért tekintse meg a proxykiszolgáló szempontjait ismertető szakaszt.
1. Engedélyezze a 5671-es és a 9350-9354-as kimenő portokat az átjárót és a vállalati tűzfalat is a Windows tűzfalon. A részletekért tekintse meg a portok és a tűzfal szakaszt. Ez a lépés nem kötelező, de javasoljuk, hogy a teljesítmény szempontjából megfontolandó legyen.

### <a name="3-problem"></a>3. probléma
A következő hiba jelenik meg.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Ok
Átmeneti hiba történt a hálózati kapcsolatban.

#### <a name="resolution"></a>Feloldás
Kövesse az alábbi lépéseket az átjáró újbóli letöltéséhez:

1. Várjon néhány percet, és a rendszer automatikusan helyreállítja a kapcsolatot, ha a hiba megszűnik.
1. Ha a hiba továbbra is fennáll, indítsa újra az átjáró szolgáltatást.

## <a name="failed-to-author-linked-service"></a>Nem sikerült létrehozni a társított szolgáltatást
### <a name="problem"></a>Probléma
Ez a hiba akkor fordulhat elő, ha az új társított szolgáltatás hitelesítő adatainak megadására, vagy egy meglévő társított szolgáltatás hitelesítő adatainak frissítésére próbál használni a hitelesítőadat-kezelőt a portálon.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Ha ezt a hibaüzenetet látja, a adatkezelés-Configuration Manager átjáró beállítások lapja az alábbi képernyőképhez hasonlóan jelenhet meg.

![Az adatbázis nem érhető el](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Ok
Lehetséges, hogy a TLS/SSL-tanúsítvány megszakadt az átjáró gépen. Az átjáró-számítógép nem tudja betölteni a TLS-titkosításhoz jelenleg használt tanúsítványt. Előfordulhat, hogy az eseménynaplóban egy hibaüzenet jelenik meg, amely a következő üzenethez hasonló.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Feloldás
A probléma megoldásához kövesse az alábbi lépéseket:

1. Adatkezelés átjáró Configuration Manager elindítása.
2. Váltson a **Beállítások** lapra.  
3. A TLS/SSL-tanúsítvány módosításához kattintson a **módosítás** gombra.

   ![Tanúsítvány módosítása gomb](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Válasszon ki egy új tanúsítványt a TLS/SSL-tanúsítványként. Az Ön vagy bármely szervezet által létrehozott TLS/SSL-tanúsítványokat is használhat.

   ![Tanúsítvány meghatározása](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>A másolási tevékenység meghiúsul
### <a name="problem"></a>Probléma
A folyamatnak a portálon való beállítása után a következő "UserErrorFailedToConnectToSqlserver" hibaüzenet jelenhet meg.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Ok
Ez különböző okok miatt fordulhat elő, és ennek megfelelően változik a mérséklés.

#### <a name="resolution"></a>Feloldás
Az SQL-adatbázishoz való csatlakozás előtt engedélyezze a kimenő TCP-kapcsolatok TCP/1433-kapcsolaton keresztüli portját a adatkezelés átjáró-ügyfél oldalán.

Ha a céladatbázis Azure SQL Databaseban található, ellenőrizze az Azure-beli tűzfal-beállítások SQL Serverét is.

A helyszíni adattárakkal létesített kapcsolatok teszteléséhez tekintse meg a következő szakaszt.

## <a name="data-store-connection-or-driver-related-errors"></a>Adattár-kapcsolat vagy illesztőprogrammal kapcsolatos hibák
Ha az adattár-kapcsolat vagy az illesztőprogrammal kapcsolatos hiba jelenik meg, hajtsa végre a következő lépéseket:

1. Indítsa el adatkezelés átjáró Configuration Manager az átjárót a gépen.
2. Váltson a **diagnosztika** lapra.
3. A **tesztelési kapcsolatban** adja hozzá az átjáró-csoportok értékeit.
4. Kattintson a **tesztelés** elemre, és ellenőrizze, hogy tud-e csatlakozni a helyszíni adatforráshoz az átjárót tartalmazó számítógépről a kapcsolati információk és a hitelesítő adatok használatával. Amennyiben a kapcsolat tesztelése az illesztő telepítése után is sikertelen, indítsa újra az átjárót, hogy az érvényesítse a legutóbbi módosítást.

![A kapcsolatok tesztelése a diagnosztika lapon](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Átjárónaplók
### <a name="send-gateway-logs-to-microsoft"></a>Átjáró-naplók küldése a Microsoftnak
Ha az átjáróval kapcsolatos problémák elhárításához segítségre van szüksége a Microsoft ügyfélszolgálatahoz, előfordulhat, hogy meg kell adnia az átjáró naplófájljait. Az átjáró kiadásával megoszthatja a szükséges átjárói naplókat két kattintással a adatkezelés átjáró Configuration Manager.    

1. Váltson a **diagnosztika** lapra adatkezelés átjáró Configuration Manager.

    ![adatkezelés átjáró diagnosztika lapja](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Kattintson a **naplók küldése** elemre a következő párbeszédpanel megjelenítéséhez.

    ![Naplók elküldése adatkezelés átjáróknak](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. Választható Kattintson a **naplók megtekintése** elemre az eseménynaplóban található naplók áttekintéséhez.
4. Választható A Microsoft webszolgáltatások adatvédelmi nyilatkozatának áttekintéséhez kattintson az **Adatvédelem** elemre.
5. Ha meggyőződött arról, hogy mire készül a feltöltés, kattintson a **naplók küldése** gombra, hogy az elmúlt hét napban küldje el a naplókat a Microsoftnak a hibaelhárításhoz. A küldési naplók művelet állapotát a következő képernyőképen látható módon kell megtekinteni.

    ![A küldési naplók művelet állapotának megtekintését bemutató képernyőkép.](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. A művelet befejezése után megjelenik egy párbeszédpanel, ahogy az alábbi képernyőképen is látható.

    ![Átjárók küldésének adatkezelési állapota](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Mentse a **jelentés azonosítóját** , és ossza meg Microsoft ügyfélszolgálataával. A jelentés azonosítója a hibaelhárításhoz feltöltött átjárók megkeresésére szolgál.  A rendszer az eseménynaplóban is menti a jelentés AZONOSÍTÓját.  A "25" AZONOSÍTÓJÚ esemény megtekintésével megkeresheti a dátumot és az időt.

    ![adatkezelés átjáró küldési naplójának azonosítója](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archivált átjárók naplói az átjáró gazdagépén
Vannak olyan helyzetek, amikor átjáróval kapcsolatos problémák léptek fel, és nem oszthatók meg közvetlenül az átjáró naplói:

* Manuálisan telepítheti az átjárót, és regisztrálja az átjárót.
* Megpróbálja regisztrálni az átjárót egy újragenerált kulccsal adatkezelés átjáró Configuration Manager.
* Megpróbál elküldeni naplókat, és az átjáró-gazdagép szolgáltatás nem csatlakoztatható.

Ezekben az esetekben az átjáró-naplókat zip-fájlként mentheti, és megoszthatja a Microsoft ügyfélszolgálatával. Ha például az átjáró regisztrálásakor hibaüzenet jelenik meg, az alábbi képernyőképen látható módon.   

![adatkezelés átjáró regisztrálási hibája](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Kattintson az **archív átjáró naplói** hivatkozásra az archiváláshoz és a naplók mentéséhez, majd ossza meg a zip-fájlt a Microsoft ügyfélszolgálatával.

![adatkezelés átjáró archiválási naplói](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Átjáró naplófájljainak megkeresése
Az átjárókkal kapcsolatos részletes információkat a Windows-eseménynaplókban találja.

1. Indítsa el a Windows **Eseménynapló**.
2. Keresse meg a naplókat az **alkalmazás és szolgáltatások naplók**  >  **adatkezelés átjáró** mappájában.

   Az átjárókkal kapcsolatos problémák elhárításakor keresse meg a hiba szintű eseményeket az eseménynaplóban.

![Átjáró-naplók adatkezelés az eseménynaplóban](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
