---
title: LDAP-szinkronizálás a Ranger és az Apache Ambari az Azure HDInsight
description: Foglalkozzon az LDAP-szinkronizálással a Ranger-és Ambari, és adjon meg általános irányelveket.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933410"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-szinkronizálás a Ranger és az Apache Ambari az Azure HDInsight

A HDInsight Enterprise Security Package (ESP) fürtök a Rangert használják az engedélyezéshez. Az Apache Ambari és a Ranger egymástól függetlenül szinkronizálja a felhasználókat és a csoportokat, és egy kicsit másképp működik. Ebből a cikkből megtudhatja, hogyan kell kezelni az LDAP-szinkronizálást a Rangerben és a Ambari.

## <a name="general-guidelines"></a>Általános irányelvek

* Mindig helyezzen üzembe fürtöket egy vagy több csoporttal.
* Ha több csoportot szeretne használni a fürtben, ellenőrizze, hogy érdemes-e frissíteni a csoporttagságok Azure Active Directoryban (Azure AD).
* Ha módosítani szeretné a fürtözött csoportokat, a szinkronizálási szűrőket a Ambari használatával módosíthatja.
* Az Azure AD-ben az összes csoporttagság változása a fürtben jelenik meg a későbbi szinkronizálásokban. A módosításokat szinkronizálni kell Azure AD Domain Services (Azure AD DS) először, majd a fürtökhöz.
* A HDInsight-fürtök a Samba vagy a winbind használatával tervezik meg a csoporttagságok számára a csoporttagság-projekteket.
* A csoporttagok szinkronizálva vannak a tranzitívnak (az összes alcsoporttal és azok tagjaival), mind a Ambari, mind a Rangersel együtt. 

## <a name="users-are-synced-separately"></a>A felhasználók szinkronizálása külön történik

 * A Ambari és a Ranger nem osztja meg a felhasználói adatbázist, mert két különböző célt szolgálnak. 
   * Ha a felhasználónak a Ambari felhasználói felületét kell használnia, a felhasználónak szinkronizálnia kell a Ambari. 
   * Ha a felhasználó nincs szinkronizálva a Ambari, a Ambari felhasználói felület/API elveti, de a rendszer más részei is működni fognak (a Ranger vagy a Resource Manager által védett, és nem a Ambari).
   * Ha felhasználókat vagy csoportokat szeretne felvenni a Ranger-szabályzatba, a rendszerbiztonsági tagokat explicit módon kell szinkronizálni a Rangerben.

## <a name="ambari-user-sync-and-configuration"></a>Ambari-felhasználó szinkronizálása és konfigurálása

A fő csomópontok egy cron-feladatot `/opt/startup_scripts/start_ambari_ldap_sync.py` futtatnak óránként, hogy ütemezik a felhasználói szinkronizálást. A cron-feladatok meghívja a Ambari REST API-kat a szinkronizálás végrehajtásához. A parancsfájl elküldi a szinkronizálandó felhasználók és csoportok listáját (mivel előfordulhat, hogy a felhasználók nem tartoznak a megadott csoportokhoz, mindkettő külön van megadva). A Ambari a sAMAccountName a Felhasználónév és az összes csoporttagok tranzitívnak szinkronizálja.

A naplóknak a-ben kell lenniük `/var/log/ambari-server/ambari-server.log` . További információ: a [Ambari naplózási szintjének konfigurálása](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

Data Lake-fürtökön a felhasználó létrehozás utáni hookja a szinkronizált felhasználók otthoni mappáinak létrehozására szolgál, amelyek a Kezdőlap mappák tulajdonosaként vannak beállítva. Ha a felhasználó nincs megfelelően szinkronizálva a Ambari, akkor előfordulhat, hogy a felhasználó nem tudja megbirkózni a futó feladatok meghibásodásával, mert a kezdőmappa nem megfelelően van beállítva.

## <a name="ranger-user-sync-and-configuration"></a>A Ranger felhasználói szinkronizálása és konfigurálása

A Ranger egy beépített szinkronizálási motorral rendelkezik, amely óránként fut a felhasználók szinkronizálásához. Nem osztja meg a felhasználói adatbázist a Ambari. A HDInsight úgy konfigurálja a keresési szűrőt, hogy szinkronizálja a rendszergazda felhasználót, a watchdog felhasználót és a csoportnak a fürt létrehozásakor megadott tagjait. A csoporttagok szinkronizálva lesznek a tranzitívnak:

1. A növekményes szinkronizálás letiltása.
1. A felhasználói csoport szinkronizálási térképének engedélyezése.
1. Adja meg a keresési szűrőt a tranzitív csoporttagok belefoglalásához.
1. Szinkronizálja a sAMAccountName attribútumot a felhasználók és a csoportok Name attribútuma számára.

### <a name="group-or-incremental-sync"></a>Csoport-vagy növekményes szinkronizálás

A Ranger támogatja a csoportos szinkronizálási lehetőséget, de a felhasználói szűrővel való összekapcsolással, nem pedig a csoporttagságok és a felhasználói szűrő közötti együttműködéssel működik. A Ranger csoport-szinkronizálási szűrő tipikus használati esete a-Group Filter: (DN = clusteradmingroup), felhasználói szűrő: (város = Seattle).

A növekményes szinkronizálás csak azokra a felhasználókra működik, akik már szinkronizálva vannak (az első alkalommal). A növekményes szinkronizálás nem szinkronizálja a csoportokhoz hozzáadott új felhasználókat a kezdeti szinkronizálás után.

### <a name="update-ranger-sync-filter"></a>A Ranger szinkronizálási szűrő frissítése

Az LDAP-szűrő a Ambari felhasználói FELÜLETén, a Ranger felhasználó – szinkronizálás konfigurálása szakaszban található. A meglévő szűrő az űrlapon fog megjelenni `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Győződjön meg arról, hogy a predikátumot a végén adja hozzá, majd tesztelje a szűrőt a `net ads` Search paranccsal vagy ldp.exe vagy valamilyen hasonló módon.

## <a name="ranger-user-sync-logs"></a>A Ranger felhasználói szinkronizálási naplói

A Ranger felhasználói szinkronizálása a átjárócsomópontokkal közül bármelyikből kiléphet. A naplók a-ben találhatók `/var/log/ranger/usersync/usersync.log` . A naplók részletességének növeléséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Ambari.
1. Lépjen a Ranger-konfiguráció szakaszra.
1. Lépjen a speciális **usersync-log4j** szakaszra.
1. Módosítsa a `log4j.rootLogger` `DEBUG` szintet. (A módosítás után a következőhöz hasonlóan kell kinéznie: `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Mentse a konfigurációt, és indítsa újra a Rangert.

## <a name="known-issues-with-ranger-user-sync"></a>Ismert problémák a Ranger felhasználói szinkronizálásával
* Ha a csoportnév Unicode karaktereket tartalmaz, a Ranger szinkronizálása nem tudja szinkronizálni az objektumot. Ha egy felhasználó olyan csoporthoz tartozik, amely nemzetközi karaktereket tartalmaz, a Ranger a részleges csoporttagság szinkronizálását végzi.
* A felhasználónévnek (sAMAccountName) és a csoport nevének (név) legalább 20 karakterből kell lennie. Ha a csoport neve továbbra is fennáll, akkor a rendszer úgy kezeli a felhasználót, mintha nem a csoporthoz tartoznak, az engedélyek kiszámításakor.

## <a name="next-steps"></a>Következő lépések

* [Hitelesítési problémák az Azure HDInsight](./domain-joined-authentication-issues.md)
* [Azure AD-felhasználók szinkronizálása HDInsight-fürttel](../hdinsight-sync-aad-users-to-cluster.md)
