---
title: Tanúsítvány elforgatása Azure Database for MySQL
description: További információ a főtanúsítványok változásairól, amelyek hatással lesznek a Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4b218a9481fdd5970fd6fc8fa6a1d071161e5b58
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313364"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ változásának megértése Azure Database for MySQL egyetlen kiszolgálón

Azure Database for MySQL egy kiszolgáló sikeresen befejezte a főtanúsítvány változását **február 15-én, 2021 (02/15/2021)** a szokásos karbantartási és biztonsági eljárások részeként. Ez a cikk további részleteket tartalmaz a változásokról, az érintett erőforrásokról és azokról a lépésekről, amelyekkel biztosítható, hogy az alkalmazás fenntartsa a kapcsolatot az adatbázis-kiszolgálóval.

> [!NOTE]
> Ez a cikk csak [Azure Database for MySQL-egyetlen kiszolgálóra](single-server-overview.md) vonatkozik. [Azure Database for MySQL rugalmas kiszolgáló](flexible-server/overview.md)esetén az SSL protokollon keresztüli kommunikációhoz szükséges tanúsítvány [DigiCert globális LEGfelső szintű hitelesítésszolgáltató](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)
>
> Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.
>

#### <a name="why-is-a-root-certificate-update-required"></a>Miért van szükség a főtanúsítvány frissítésére?

Azure Database for MySQL a felhasználók csak az előre definiált tanúsítványt használhatják az [itt](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)található MySQL-kiszolgálóhoz való kapcsolódáshoz. Azonban a hitelesítésszolgáltató [(CA) böngésző fóruma](https://cabforum.org/)   nemrég közzétette a CA-gyártók által kiállított több tanúsítvány jelentéseit, amelyek nem megfelelőek.

Az iparág megfelelőségi követelményei szerint a CA-szállítók megkezdték a CA-tanúsítványok visszavonását a nem megfelelő hitelesítésszolgáltatók számára, és a megfelelő hitelesítésszolgáltatók által kiadott tanúsítványok használatát igénylik a kiszolgálók számára, valamint a megfelelő hitelesítésszolgáltatóktól származó HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok aláírásával. Mivel Azure Database for MySQL használta ezeket a nem megfelelő tanúsítványokat, a tanúsítványt a megfelelő verzióra kell elforgatni, hogy csökkentse a MySQL-kiszolgálók potenciális fenyegetését.

Az új tanúsítvány az 2021-as (02/15/2021-as) február 15-től érvényes.

#### <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Mi történt a változás a 2021-es február 15-én (02/15/2021)?

2021. február 15-én a [BaltimoreCyberTrustRoot legfelső szintű tanúsítványát](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) az azonos [BaltimoreCyberTrustRoot főtanúsítvány](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **megfelelő verziójára** cserélték annak biztosítása érdekében, hogy a meglévő ügyfeleknek ne kelljen semmit módosítaniuk, és nincs hatással a kiszolgáló kapcsolataira. A módosítás során a [BaltimoreCyberTrustRoot főtanúsítványa](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **nem lett lecserélve** a [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) -re, és a módosítás késleltetve lett, hogy több idő legyen az ügyfelek számára a módosításra.

#### <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Kell-e módosítani az ügyfelem számára a kapcsolat fenntartásához?

Az ügyfél oldalán nincs szükség módosításra. Ha követte az alábbi korábbi javaslatot, továbbra is csatlakozhat, amíg a **BaltimoreCyberTrustRoot-tanúsítvány nem törlődik** a kombinált hitelesítésszolgáltatói tanúsítványból. **A kapcsolat fenntartása érdekében javasoljuk, hogy a BaltimoreCyberTrustRoot a kombinált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványban őrizze meg további értesítésig.**

###### <a name="previous-recommendation"></a>Előző javaslat

Az alábbi lépéseket követve megakadályozhatja, hogy az alkalmazás rendelkezésre állása a tanúsítványok váratlan visszavonása vagy a visszavont tanúsítványok frissítése miatt ne legyen megszakítva. Az ötlet egy új *. PEM* -fájl létrehozása, amely egyesíti az aktuális tanúsítványt és az újat, és az SSL-tanúsítvány ellenőrzése során az egyik megengedett értéket fogja használni. Tekintse át a következő lépéseket:

1. Töltse le a BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 legfelső szintű HITELESÍTÉSSZOLGÁLTATÓját a következő hivatkozásokból:

    * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
    * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

2. Kombinált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványtároló létrehozása a **BaltimoreCyberTrustRoot** -és **DigiCertGlobalRootG2** -tanúsítványokkal együtt.

    * A Java (MySQL Connector/J) felhasználók számára futtassa a következőt:

      ```console
      keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
      ```

      ```console
      keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
      ```

      Ezután cserélje le az eredeti alaptároló fájlját az új létrehozott-re:

      * System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file");
      * System. setProperty ("javax. net. SSL. trustStorePassword", "password");

    * .NET (MySQL Connector/NET, MySQLConnector) felhasználók esetén győződjön meg arról, hogy a **BaltimoreCyberTrustRoot** és a **DigiCertGlobalRootG2** egyaránt létezik a Windows tanúsítványtárolóban, a megbízható legfelső szintű hitelesítésszolgáltatókban. Ha valamelyik tanúsítvány nem létezik, importálja a hiányzó tanúsítványt.

      :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Azure Database for MySQL .NET-tanúsítvány diagramja":::

    * A Linux rendszerű .NET-felhasználók SSL_CERT_DIRon való használata esetén győződjön meg arról, hogy a **BaltimoreCyberTrustRoot** és a **DigiCertGlobalRootG2** egyaránt létezik a SSL_CERT_DIR által jelzett könyvtárban. Ha valamelyik tanúsítvány nem létezik, hozza létre a hiányzó tanúsítványfájl-fájlt.

    * Más (MySQL Client/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) felhasználók esetén a következő formátumban egyesítheti két HITELESÍTÉSSZOLGÁLTATÓI tanúsítványfájl-fájlt:

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

3. Cserélje le az eredeti legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI PEM-fájlt a kombinált legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI fájlra, és indítsa újra az alkalmazást/ügyfelet.

   A jövőben, miután az új tanúsítvány telepítve van a kiszolgálóoldali oldalon, módosíthatja a HITELESÍTÉSSZOLGÁLTATÓI PEM-fájlt a DigiCertGlobalRootG2. CRT. PEM fájlba.

> [!NOTE]
> A tanúsítvány módosítása után ne dobja el vagy ne változtassa meg a **Baltimore-tanúsítványt** . A módosítások elvégzése után a rendszer elküld egy értesítést a **Baltimore-tanúsítvány** eldobása után.

#### <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Miért nem váltotta fel a BaltimoreCyberTrustRoot-tanúsítvány a DigiCertGlobalRootG2 a változás során a 2021-es február 15-én?

Kiértékelte az ügyfél készültségét ehhez a változáshoz, és rájött, hogy sok ügyfél kereste a változás kezeléséhez szükséges időt. Úgy döntöttünk, hogy az ügyfelek számára még több időt biztosítanak a felkészülésre, ezért úgy döntöttünk, hogy a DigiCertGlobalRootG2 legalább egy évig elhalasztják, és elegendő időt biztosítanak az ügyfelek és a végfelhasználók számára.

Javasoljuk, hogy a fenti lépésekkel hozzon létre egy egyesített tanúsítványt, és kapcsolódjon a kiszolgálóhoz, de ne távolítsa el a BaltimoreCyberTrustRoot-tanúsítványt, amíg nem küldünk egy kommunikációt az eltávolításhoz.

#### <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Mi a teendő, ha eltávolította a BaltimoreCyberTrustRoot-tanúsítványt?

A Azure Database for MySQL-kiszolgálóhoz való kapcsolódás során elkezdi a kapcsolódási hibákat. A kapcsolat fenntartása érdekében újra [be kell állítania az SSL](howto-configure-ssl.md) -t a [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -tanúsítvánnyal.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

#### <a name="if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Ha nem használok SSL/TLS-t, továbbra is frissíteni kell a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT?

  Nincs szükség műveletre, ha nem használ SSL/TLS-t.

#### <a name="if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>Ha SSL/TLS protokollt használok, a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ frissítéséhez újra kell indítani az adatbázis-kiszolgálót?

Nem, nem kell újraindítani az adatbázis-kiszolgálót az új tanúsítvány használatának megkezdéséhez. Ez a főtanúsítvány egy ügyféloldali változás, és a bejövő ügyfélkapcsolatoknak az új tanúsítvánnyal kell rendelkezniük ahhoz, hogy az adatbázis-kiszolgálóhoz csatlakozzanak.

#### <a name="how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>Hogyan tudni, hogy az SSL/TLS-t használom-e a főtanúsítvány ellenőrzésekor?

A kapcsolati karakterlánc áttekintésével megtekintheti, hogy a kapcsolatok ellenőrzik-e a főtanúsítványt.

* Ha a kapcsolódási sztring tartalmaz `sslmode=verify-ca` vagy `sslmode=verify-identity` , frissítenie kell a tanúsítványt.
* Ha a kapcsolódási karakterlánc magában foglalja a,, `sslmode=disable` `sslmode=allow` `sslmode=prefer` vagy `sslmode=require` , nem kell frissítenie a tanúsítványokat.
* Ha a kapcsolódási karakterlánc nem ad meg sslmode, nem szükséges frissítenie a tanúsítványokat.

Ha olyan ügyfelet használ, amely el tudja olvasni a kapcsolódási karakterláncot, tekintse át az ügyfél dokumentációját, és Ismerje meg, hogy igazolja-e a tanúsítványokat.

#### <a name="what-is-the-impact-of-using-app-service-with-azure-database-for-mysql"></a>Milyen hatással van a App Service használatára a Azure Database for MySQL?

Az Azure Database for MySQL-hoz csatlakozó Azure app Services esetében két lehetséges forgatókönyv van attól függően, hogy hogyan használja az SSL-t az alkalmazással.

* Ez az új tanúsítvány App Service platform szinten lett hozzáadva. Ha az alkalmazásban App Service platformon található SSL-tanúsítványokat használja, nincs szükség beavatkozásra. Ez a leggyakoribb forgatókönyv.
* Ha kifejezetten az SSL-tanúsítvány elérési útját használja a kódban, akkor le kell töltenie az új tanúsítványt, és létre kell hoznia egy, a fentiekben említett egyesített tanúsítványt, és a tanúsítványt kell használnia. Jó példa erre a forgatókönyvre, ha egyéni tárolókat használ a App Service az [app Service dokumentációjában](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)megosztottként. Ez egy nem gyakori forgatókönyv, de a felhasználók ezt használják.

#### <a name="what-is-the-impact-of-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>Milyen hatással van az Azure Kubernetes Services (ak) használatának a Azure Database for MySQL?

Ha az Azure Kubernetes Services (ak) használatával próbál csatlakozni a Azure Database for MySQLhoz, hasonló a dedikált ügyfelek gazdagép-környezetéhez való hozzáféréshez. Tekintse át a lépéseket [itt](../aks/ingress-own-tls.md).

#### <a name="what-is-the-impact-of-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>Milyen hatással van a Azure Data Factory használatára a Azure Database for MySQLhoz való kapcsolódáshoz?

Azure Integration Runtime használó összekötőhöz az összekötő a Windows tanúsítványtárolóban található tanúsítványokat használja az Azure által üzemeltetett környezetben. Ezek a tanúsítványok már kompatibilisek az újonnan alkalmazott tanúsítványokkal, ezért nincs szükség beavatkozásra.

A saját üzemeltetésű Integration Runtime használó összekötők esetében, ahol explicit módon felveszi az SSL-tanúsítvány elérési útját a kapcsolati karakterláncba, le kell töltenie az [új tanúsítványt](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) , és frissítenie kell a kapcsolati karakterláncot a használatára.

#### <a name="do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>Meg kell tervezni egy adatbázis-kiszolgáló karbantartási állásidőt ehhez a változáshoz?

Nem. Mivel a változás csak az ügyféloldali oldalon csatlakozik az adatbázis-kiszolgálóhoz, nincs szükség karbantartási állásidőre az adatbázis-kiszolgáló számára ehhez a változáshoz.

#### <a name="if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>Ha az új kiszolgálót a 2021 (02/15/2021) február 15. után hozzuk létre, hatással leszek rá?

A 02/15/2021 2021. február 15. után létrehozott kiszolgálók esetében továbbra is a [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) fogja használni az alkalmazásokhoz az SSL használatával történő kapcsolódáshoz.

#### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Milyen gyakran frissíti a Microsoft a tanúsítványokat, vagy mi a lejárati szabályzat?

Az Azure Database for MySQL által használt tanúsítványokat a megbízható hitelesítésszolgáltatók (CA) biztosítják. Így a tanúsítványok támogatása a CA által támogatott tanúsítványok támogatásához van kötve. A [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -tanúsítvány a 2025-as lejáratra van ütemezve, ezért a Microsoftnak a lejárat előtt végre kell hajtania a tanúsítvány megváltoztatását. Abban az esetben is, ha előre nem látható hibák jelennek meg az előre meghatározott tanúsítványokban, a Microsoftnak a 2021. február 15-én végrehajtott módosításhoz legkorábbi módon kell elvégeznie a tanúsítvány elforgatását, hogy biztosítsa a szolgáltatás biztonságos és megfelelő megfelelőségét.

#### <a name="if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>Ha olvasási replikákat használok, ezt a frissítést csak a forráskiszolgálón vagy az olvasási replikán kell elvégezni?

Mivel ez a frissítés ügyféloldali módosítás, ha az ügyfél a másodpéldány-kiszolgálóról olvassa az adatok olvasását, akkor ezeket az ügyfeleken is alkalmaznia kell.

#### <a name="if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>Ha adatreplikálást használok, végre kell hajtani valamilyen műveletet?

Ha [adatreplikálást](concepts-data-in-replication.md) használ a Azure Database for MySQLhoz való kapcsolódáshoz, két szempontot kell figyelembe venni:

* Ha az adatok replikálása virtuális gépről (helyszíni vagy Azure-beli virtuális gépre) történik a Azure Database for MySQL, akkor ellenőriznie kell, hogy az SSL-t használja-e a replika létrehozásához. Futtassa a **Slave-állapot megjelenítése** parancsot, és tekintse meg a következő beállítást.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Ha látja, hogy a tanúsítvány a CA_file, SSL_Cert és SSL_Key számára van megadva, akkor az [új tanúsítvány](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) hozzáadásával és egy egyesített tanúsítványfájl létrehozásával frissítenie kell a fájlt.

* Ha az adatreplikálás két Azure Database for MySQL kiszolgáló között van, akkor a másodpéldányt alaphelyzetbe kell állítania a **hívási MySQL.az_replication_change_master** végrehajtásával, és az új kettős főtanúsítványt kell megadnia az utolsó paraméterként [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication).

#### <a name="is-there-a-server-side-query-to-determine-whether-ssl-is-being-used"></a>Van olyan kiszolgálóoldali lekérdezés, amely meghatározza, hogy az SSL használatban van-e?

Annak ellenőrzéséhez, hogy SSL-kapcsolatot használ-e a kiszolgálóhoz való kapcsolódáshoz, tekintse meg az [SSL-ellenőrzést](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

#### <a name="is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>Van szükség beavatkozásra, ha már van a DigiCertGlobalRootG2 a saját tanúsítványfájl?

Nem. Nincs szükség beavatkozásra, ha a tanúsítványfájl már rendelkezik a **DigiCertGlobalRootG2**.

#### <a name="what-if-i-have-further-questions"></a>Mi a teendő, ha további kérdéseim vannak?

Kérdéseire választ kaphat a [Microsoft Q&a](mailto:AzureDatabaseforMySQL@service.microsoft.com)közösségi szakértőitől. Ha támogatási csomaggal rendelkezik, és technikai segítségre van szüksége, [vegye fel velünk a kapcsolatot](mailto:AzureDatabaseforMySQL@service.microsoft.com).
