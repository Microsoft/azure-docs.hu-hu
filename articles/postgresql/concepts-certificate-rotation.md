---
title: Tanúsítvány elforgatása Azure Database for PostgreSQL egyetlen kiszolgálóhoz
description: Ismerje meg a főtanúsítványok változásainak a Azure Database for PostgreSQL egyetlen kiszolgálót érintő közelgő változásait
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 6dc687879eb646b4abd081b40bce292d20ff3186
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123989"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ változásának megértése Azure Database for PostgreSQL egyetlen kiszolgálón

Azure Database for PostgreSQL egy kiszolgáló sikeresen befejezte a főtanúsítvány változását **február 15-én, 2021 (02/15/2021)** a szokásos karbantartási és biztonsági eljárások részeként. Ez a cikk további részleteket tartalmaz a változásokról, az érintett erőforrásokról és azokról a lépésekről, amelyekkel biztosítható, hogy az alkalmazás fenntartsa a kapcsolatot az adatbázis-kiszolgálóval.

## <a name="why-root-certificate-update-is-required"></a>Miért van szükség a főtanúsítvány frissítésére?

A PostgreSQL-hez készült Azure Database-felhasználók csak az előre definiált tanúsítványt használhatják a PostgreSQL-kiszolgálóhoz való kapcsolódáshoz, amely [itt](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)található. Azonban a hitelesítésszolgáltató [(CA) böngésző fóruma](https://cabforum.org/)   nemrég közzétette a CA-gyártók által kiállított több tanúsítvány jelentéseit, amelyek nem megfelelőek.

Az iparág megfelelőségi követelményeinek megfelelően a CA-szállítók megkezdték a CA-tanúsítványok visszavonását a nem megfelelő hitelesítésszolgáltatók számára, és a kiszolgálókat a megfelelő hitelesítésszolgáltatók által kiadott tanúsítványok használatára kötelezik, valamint a megfelelő hitelesítésszolgáltatóktól származó HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok aláírásával. Mivel Azure Database for MySQL használta ezeket a nem megfelelő tanúsítványokat, a tanúsítványt a megfelelő verzióra kell elforgatni, hogy csökkentse a MySQL-kiszolgálók potenciális fenyegetését.

Az új tanúsítvány be van vezetve, és a 2021-es (02/15/2021-as) március 15. után lép érvénybe. 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Mi történt a változás a 2021-es február 15-én (02/15/2021)?

2021. február 15-én a [BaltimoreCyberTrustRoot legfelső szintű tanúsítványát](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) az azonos [BaltimoreCyberTrustRoot főtanúsítvány](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **megfelelő verziójára** cserélték annak biztosítása érdekében, hogy a meglévő ügyfeleknek ne kelljen semmit módosítaniuk, és nincs hatással a kiszolgálóval létesített kapcsolatokra. A módosítás során a [BaltimoreCyberTrustRoot főtanúsítványa](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **nem lett lecserélve** a [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) -re, és a módosítás késleltetve lett, hogy több idő legyen az ügyfelek számára a módosításra.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Kell-e módosítani az ügyfelem számára a kapcsolat fenntartásához?

Az ügyfél oldalán nincs szükség módosításra. Ha követte az alábbi korábbi javaslatot, továbbra is csatlakozhat mindaddig, amíg a **BaltimoreCyberTrustRoot-tanúsítvány nem törlődik** a kombinált hitelesítésszolgáltatói tanúsítványból. **Javasoljuk, hogy a kapcsolat fenntartása érdekében ne távolítsa el a BaltimoreCyberTrustRoot a kombinált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványból.**

### <a name="previous-recommendation"></a>Előző javaslat

*   Töltse le a BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 legfelső szintű HITELESÍTÉSSZOLGÁLTATÓját az alábbi hivatkozások közül:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Kombinált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványtároló létrehozása a **BaltimoreCyberTrustRoot** -és **DigiCertGlobalRootG2** -tanúsítványokkal együtt.
    *   A Java (PostgreSQL JDBC) felhasználók számára a DefaultJavaSSLFactory használatával futtassa a következőt:

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Ezután cserélje le az eredeti alaptároló fájlját az új létrehozott-re:
        *   System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. net. SSL. trustStorePassword", "password");
        
    *   Windows rendszerű .NET-(Npgsql-) felhasználók esetén győződjön meg arról, hogy a Windows-tanúsítványtárolóban, a megbízható legfelső szintű hitelesítésszolgáltatóknál a **Baltimore CyberTrust root** és a **DigiCert globális root G2** is létezik. Ha valamelyik tanúsítvány nem létezik, importálja a hiányzó tanúsítványt.

        ![.Net-tanúsítvány Azure Database for PostgreSQL](media/overview/netconnecter-cert.png)

    *   A .NET-(Npgsql-) felhasználók számára a SSL_CERT_DIR használatával Linux rendszeren a **BaltimoreCyberTrustRoot** és a **DigiCertGlobalRootG2** egyaránt létezik a SSL_CERT_DIR által jelzett könyvtárban. Ha valamelyik tanúsítvány nem létezik, hozza létre a hiányzó tanúsítványfájl-fájlt.

    *   A PostgreSQL-ügyfél többi felhasználója számára egyesítheti az alábbi formátumhoz hasonló két HITELESÍTÉSSZOLGÁLTATÓI tanúsítványfájl-fájlt

        </br>-----A TANÚSÍTVÁNY MEGKEZDÉSE-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. PEM)
 </br>-----ZÁRÓ TANÚSÍTVÁNY-----
 </br>-----A TANÚSÍTVÁNY MEGKEZDÉSE-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. PEM)
 </br>-----ZÁRÓ TANÚSÍTVÁNY-----

*   Cserélje le az eredeti legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI PEM-fájlt a kombinált legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI fájlra, és indítsa újra az alkalmazást/ügyfelet.
*    A jövőben a kiszolgálói oldalon üzembe helyezett új tanúsítvány után a HITELESÍTÉSSZOLGÁLTATÓ PEM-fájlját a DigiCertGlobalRootG2. CRT. PEM értékre módosíthatja.

> [!NOTE]
> Ne dobja el vagy ne változtassa meg a **Baltimore-tanúsítványt** , amíg meg nem történik a tanúsítvány módosítása. A módosítások elvégzése után a rendszer elküld egy kommunikációt, amely után a Baltimore-tanúsítvány eldobása biztonságos. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Miért nem váltotta fel a BaltimoreCyberTrustRoot-tanúsítvány a DigiCertGlobalRootG2 a változás során a 2021-es február 15-én?

Kiértékelte az ügyfél készültségét ehhez a változáshoz, és felismerte, hogy sok ügyfelünk a változás kezeléséhez további érdeklődői időt keresett. Annak érdekében, hogy az ügyfelek számára még több időt biztosítson a felkészülésre, úgy döntöttünk, hogy legalább egy évig késleltetjük a DigiCertGlobalRootG2, hogy elegendő idő álljon rendelkezésre az ügyfelek és a végfelhasználók számára. 

Javaslataink a felhasználók számára: a fenti lépésekkel hozzon létre egy egyesített tanúsítványt, és kapcsolódjon a kiszolgálóhoz, de ne távolítsa el a BaltimoreCyberTrustRoot-tanúsítványt, amíg el nem küld egy kommunikációt az eltávolításához. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Mi a teendő, ha eltávolította a BaltimoreCyberTrustRoot-tanúsítványt?

Kapcsolódási hibák lépnek fel a Azure Database for PostgreSQL-kiszolgálóhoz való csatlakozáskor. A kapcsolat fenntartása érdekében újra be kell állítania az SSL-t a [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -tanúsítvánnyal.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Ha nem használok SSL/TLS-t, továbbra is frissíteni kell a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT?
Nincs szükség műveletre, ha nem SSL/TLS protokollt használ. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. ha SSL/TLS protokollt használok, a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ frissítéséhez újra kell indítani az adatbázis-kiszolgálót?
Nem, nem kell újraindítani az adatbázis-kiszolgálót az új tanúsítvány használatának megkezdéséhez. Ez egy ügyféloldali változás, és a bejövő ügyfélkapcsolatoknak az új tanúsítvánnyal kell rendelkezniük ahhoz, hogy az adatbázis-kiszolgálóhoz csatlakozzanak.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Hogyan tudni, hogy az SSL/TLS-t használom-e a főtanúsítvány ellenőrzése?

A kapcsolati karakterlánc áttekintésével megtekintheti, hogy a kapcsolatok ellenőrzik-e a főtanúsítványt.
-    Ha a kapcsolódási sztring tartalmaz `sslmode=verify-ca` vagy `sslmode=verify-full` , frissítenie kell a tanúsítványt.
-    Ha a kapcsolódási karakterlánc magában foglalja a, a, a `sslmode=disable` `sslmode=allow` vagy a `sslmode=prefer` `sslmode=require` , nem kell frissítenie a tanúsítványokat. 
-    Ha a kapcsolódási karakterlánc nem ad meg sslmode, nem szükséges frissítenie a tanúsítványokat.

Ha olyan ügyfelet használ, amely el tudja olvasni a kapcsolódási karakterláncot, tekintse át az ügyfél dokumentációját, és Ismerje meg, hogy igazolja-e a tanúsítványokat. A PostgreSQL sslmode megismeréséhez tekintse át az [SSL-mód leírásait](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) a PostgreSQL dokumentációjában.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. milyen hatással van a App Service és a Azure Database for PostgreSQL használata?
Az Azure app Services esetében a Azure Database for PostgreSQLhoz való csatlakozás két lehetséges forgatókönyvet tartalmazhat, amelyek attól függnek, hogy miként használja az SSL-t az alkalmazással.
*   Ez az új tanúsítvány App Service platform szinten lett hozzáadva. Ha az alkalmazásban App Service platformon található SSL-tanúsítványokat használja, nincs szükség beavatkozásra.
*   Ha explicit módon az SSL-tanúsítvány elérési útját is tartalmazza a kódban, akkor le kell töltenie az új tanúsítványt, és frissítenie kell a kódot az új tanúsítvány használatára. Jó példa erre a forgatókönyvre, ha egyéni tárolókat használ a App Service a [app Service dokumentációjában](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress) megosztva.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. milyen hatással van az Azure Kubernetes Services (ak) használata a Azure Database for PostgreSQL?
Ha az Azure Kubernetes Services (ak) használatával próbál csatlakozni a Azure Database for PostgreSQLhoz, hasonló a dedikált ügyfelek gazdagép-környezetéhez való hozzáféréshez. Tekintse át a lépéseket [itt](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. milyen hatással van a Azure Data Factory használata a Azure Database for PostgreSQLhoz való kapcsolódásra?
A Azure Integration Runtimet használó összekötők esetében az összekötő kihasználja a Windows tanúsítványtárolóban található tanúsítványokat az Azure által üzemeltetett környezetben. Ezek a tanúsítványok már kompatibilisek az újonnan alkalmazott tanúsítványokkal, ezért nincs szükség beavatkozásra.

A saját üzemeltetésű Integration Runtime használó összekötő esetében, ha explicit módon felveszi az SSL-tanúsítvány elérési útját a kapcsolati karakterláncba, le kell töltenie az [új tanúsítványt](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) , és frissítenie kell a kapcsolati karakterláncot a használatára.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. meg kell tervezni egy adatbázis-kiszolgáló karbantartási állásidőt ehhez a változáshoz?
Nem. Mivel a változás csak az ügyféloldali oldalon csatlakozik az adatbázis-kiszolgálóhoz, nincs szükség karbantartási állásidőre az adatbázis-kiszolgáló számára ehhez a változáshoz.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. Ha új kiszolgálót hoz létre a 2021 (02/15/2021) február 15. után, a rendszer hatással lesz rá?
A 02/15/2021 2021. február 15. után létrehozott kiszolgálók esetében továbbra is a [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) fogja használni az alkalmazásokhoz az SSL használatával történő kapcsolódáshoz.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. milyen gyakran frissíti a Microsoft a tanúsítványait, vagy mi a lejárati szabályzat?
Az Azure Database for PostgreSQL által használt tanúsítványokat a megbízható hitelesítésszolgáltatók (CA) biztosítják. Így a tanúsítványok támogatása a CA által támogatott tanúsítványok támogatásához van kötve. A [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -tanúsítvány a 2025-as lejáratra van ütemezve, ezért a Microsoftnak a lejárat előtt végre kell hajtania a tanúsítvány megváltoztatását. Abban az esetben is, ha előre nem látható hibák jelennek meg az előre meghatározott tanúsítványokban, a Microsoftnak a 2021. február 15-én végrehajtott módosításhoz legkorábbi módon kell elvégeznie a tanúsítvány elforgatását, hogy biztosítsa a szolgáltatás biztonságos és megfelelő megfelelőségét.

### <a name="10-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>10. ha olvasási replikákat használok, ezt a frissítést csak az elsődleges kiszolgálón vagy az olvasási replikán kell elvégezni?
Mivel ez a frissítés ügyféloldali módosítás, ha az ügyfél a másodpéldány-kiszolgálóról olvassa az adatok olvasását, akkor a módosításokat is alkalmaznia kell az ügyfeleken. 

### <a name="11-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>11. van-e kiszolgálóoldali lekérdezés annak ellenőrzéséhez, hogy az SSL használatban van-e?
Annak ellenőrzéséhez, hogy SSL-kapcsolatot használ-e a kiszolgálóhoz való kapcsolódáshoz, tekintse meg az [SSL-ellenőrzést](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity).

### <a name="12-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>12. van szükség beavatkozásra, ha már van a DigiCertGlobalRootG2 a saját tanúsítványfájl?
Nem. Nincs szükség beavatkozásra, ha a tanúsítványfájl már rendelkezik a **DigiCertGlobalRootG2**.

### <a name="13-what-if-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>13. Mi a teendő, ha a Microsoft által biztosított PgBouncer oldalkocsi Docker-rendszerképét használja?
[Itt](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar) jelennek meg az alábbi, a [**Baltimore**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -t és a [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) támogató új Docker-rendszerkép (legújabb címke). Az új rendszerkép lekérésével elkerülheti, hogy a kapcsolat megszakítása 2021. február 15-én kezdődik. 

### <a name="14-what-if-i-have-further-questions"></a>14. Mi a teendő, ha további kérdéseim vannak?
Ha kérdése van, választ kaphat a [Microsoft Q&a](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)közösségi szakértőitől. Ha támogatási csomaggal rendelkezik, és technikai segítségre van szüksége,  [vegye fel velünk a kapcsolatot](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)
