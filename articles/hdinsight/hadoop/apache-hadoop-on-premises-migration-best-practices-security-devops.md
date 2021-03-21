---
title: 'Biztonság: helyszíni Apache Hadoop migrálása az Azure HDInsight'
description: Ismerje meg a biztonsági és DevOps ajánlott eljárásokat a helyszíni Hadoop-fürtök Azure-HDInsight való áttelepítéséhez.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: fa6a4a8686fe5a33a6f240a8e972a687e872732a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939740"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Helyszíni Apache Hadoop-fürtök migrálása az Azure HDInsight – biztonsági és DevOps – ajánlott eljárások

Ez a cikk az Azure HDInsight Systems biztonsági és DevOps kapcsolatos javaslatait ismerteti. Egy sorozat része, amely ajánlott eljárásokat biztosít a helyszíni Apache Hadoop rendszerek Azure HDInsight való áttelepítésének segítésére.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Fürt biztonságossá tétele és szabályozása Enterprise Security Package

A Enterprise Security Package (ESP) támogatja a Active Directory-alapú hitelesítést, a többfelhasználós támogatást és a szerepköralapú hozzáférés-vezérlést. Ha a kiválasztott ESP lehetőséget választja, a HDInsight-fürt csatlakozik a Active Directory tartományhoz, és a vállalati rendszergazda szerepköralapú hozzáférés-vezérlést (RBAC) konfigurálhat Apache Hive biztonsághoz az Apache Ranger használatával. A rendszergazda is naplózhatja az alkalmazottak és a hozzáférés-vezérlési szabályzatok által végzett módosításokat.

Az ESP a következő típusú fürtökön érhető el: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka és interaktív lekérdezés (kaptár LLAP).

A tartományhoz csatlakoztatott HDInsight-fürt üzembe helyezéséhez kövesse az alábbi lépéseket:

- Azure Active Directory (HRE) üzembe helyezése a tartománynév átadásával.
- Azure Active Directory Domain Services üzembe helyezése (HRE DS).
- Hozza létre a szükséges Virtual Network és alhálózatot.
- Helyezzen üzembe egy virtuális gépet a Virtual Network a HRE DS felügyeletéhez.
- Csatlakoztassa a virtuális gépet a tartományhoz.
- Telepítse az AD és a DNS-eszközöket.
- A HRE DS rendszergazdája hozzon létre egy szervezeti egységet (OU).
- Engedélyezze az LDAPs szolgáltatást a HRE DS-hez.
- Hozzon létre egy Azure Active Directory-beli szolgáltatásfiókot a meghatalmazott olvasási & írási rendszergazdai engedéllyel a szervezeti egységhez, hogy az képes legyen. Ez a szolgáltatásfiók ezután csatlakoztathatja a gépeket a tartományhoz, és elhelyezheti a számítógépeket a szervezeti egységen belül. Emellett a fürt létrehozása során megadott szervezeti egységen belül is létrehozhat egyszerű szolgáltatásokat.

    > [!Note]
    > A szolgáltatásfiók nem feltétlenül AD tartományi rendszergazdai fióknak kell lennie.

- A következő paraméterek megadásával telepítse a HDInsight ESP-fürtöt:

    |Paraméter |Leírás |
    |---|---|
    |Tartománynév|Az Azure AD DShoz társított tartománynév.|
    |Tartományi Felhasználónév|Az előző szakaszban létrehozott Azure AD DS DC által felügyelt tartományhoz tartozó szolgáltatásfiók, például: `hdiadmin@contoso.onmicrosoft.com` . Ez a tartományi felhasználó lesz a HDInsight-fürt rendszergazdája.|
    |Tartományi jelszó|A szolgáltatásfiók jelszava.|
    |Szervezeti egység|A HDInsight-fürthöz használni kívánt szervezeti egység megkülönböztető neve, például: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com` . Ha ez a szervezeti egység nem létezik, a HDInsight-fürt megpróbálja létrehozni a szervezeti egységet a szolgáltatásfiók jogosultságainak használatával.|
    |LDAPS URL-CÍM|például: `ldaps://contoso.onmicrosoft.com:636` .|
    |Felhasználói csoport elérése|Azok a biztonsági csoportok, amelyek felhasználóit szinkronizálni szeretné a fürttel, például: `HiveUsers` . Ha több felhasználói csoportot szeretne megadni, pontosvesszővel válassza el őket egymástól. Az ESP-fürt létrehozása előtt a csoport (ok) nak léteznie kell a címtárban.|

További információért tekintse át a következő cikkeket:

- [Bevezetés a Apache Hadoop biztonságba a tartományhoz csatlakoztatott HDInsight-fürtökkel](../domain-joined/hdinsight-security-overview.md)
- [Azure-tartományhoz csatlakoztatott Apache Hadoop-fürtök tervezése a HDInsight-ben](../domain-joined/apache-domain-joined-architecture.md)
- [Tartományhoz csatlakoztatott HDInsight-fürt konfigurálása Azure Active Directory Domain Services használatával](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Azure Active Directory-felhasználók HDInsight-fürttel való szinkronizálása](../hdinsight-sync-aad-users-to-cluster.md)
- [Apache Hive házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight](../domain-joined/apache-domain-joined-run-hive.md)
- [Apache Oozie futtatása tartományhoz csatlakoztatott HDInsight Hadoop-fürtökben](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Végpontok közötti vállalati biztonság megvalósítása

A végpontok közötti vállalati biztonság a következő vezérlők használatával érhető el:

**Magán-és védett adatfolyamatok (peremhálózat-szintű biztonság)**
    - A szegélyhálózati biztonság az Azure Virtual Networks, a hálózati biztonsági csoportok és az átjáró szolgáltatás használatával érhető el.

**Az adathozzáférés hitelesítése és engedélyezése**
    - Tartományhoz csatlakoztatott HDInsight-fürt létrehozása Azure Active Directory Domain Services használatával. (Enterprise Security Package).
    - A Ambari használatával szerepköralapú hozzáférést biztosíthat a fürt erőforrásaihoz az AD-felhasználók számára.
    - Az Apache Ranger használatával beállíthatja a struktúra hozzáférés-vezérlési házirendjeit a tábla/oszlop/sor szintjén.
    - A fürthöz való SSH-hozzáférés csak a rendszergazdára korlátozható.

**Naplózás**
    - Megtekintheti és bejelentheti a HDInsight-fürt erőforrásaihoz és az adatforrásokhoz való hozzáférést.
    - Megtekintheti és bejelentheti a hozzáférés-vezérlési szabályzatok összes módosítását.

**Titkosítás**
    - Transzparens Server-Side titkosítás a Microsoft által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal.
    - Az átvitel titkosítása Client-Side titkosítás, HTTPS és TLS használatával.

További információért tekintse át a következő cikkeket:

- [Azure Virtual Networks – áttekintés](../../virtual-network/virtual-networks-overview.md)
- [Azure hálózati biztonsági csoportok – áttekintés](../../virtual-network/network-security-groups-overview.md)
- [Azure-beli virtuális hálózati társviszony](../../virtual-network/virtual-network-peering-overview.md)
- [Azure Storage – biztonsági útmutató](../../storage/blobs/security-recommendations.md)
- [Azure Storage Service Encryption a nyugalmi állapotban](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Figyelési & riasztások használata

További információt a következő cikkben talál:

[Azure Monitor – áttekintés](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Fürtök frissítése

Rendszeresen frissítsen a legújabb HDInsight-verzióra, hogy kihasználhassa a legújabb funkciókat. A következő lépéseket követve frissítheti a fürtöt a legújabb verzióra:

1. Hozzon létre egy új teszt HDInsight-fürtöt a legújabb elérhető HDInsight-verzió használatával.
1. Tesztelje az új fürtöt, és győződjön meg arról, hogy a feladatok és a munkaterhelések a várt módon működnek.
1. Szükség szerint módosítsa a feladatokat vagy alkalmazásokat vagy munkaterheléseket.
1. A fürt csomópontjain helyileg tárolt átmeneti adattárolás biztonsági mentése.
1. Törölje a meglévő fürtöt.
1. Hozzon létre egy fürtöt a legújabb HDInsight-verzióban ugyanabban a virtuális hálózati alhálózatban, ugyanazzal az alapértelmezett adattal és meta-tárolóval, mint az előző fürttel.
1. Importálja a biztonsági mentés alatt álló átmeneti adatfájlokat.
1. Feladatok indítása/a feldolgozás folytatása az új fürt használatával.

További információt a [HDInsight-fürt frissítése új verzióra](../hdinsight-upgrade-cluster.md)című cikkben talál.

## <a name="patch-cluster-operating-systems"></a>Fürt operációs rendszereinek javítása

További információkért tekintse meg a [HDInsight operációsrendszer-javítását](../hdinsight-os-patching.md)ismertető cikket.

## <a name="post-migration"></a>Áttelepítés utáni

1. **Alkalmazások szervizelése** – a iteratív végezze el a feladatok, a folyamatok és a parancsfájlok szükséges módosításait.
2. **Tesztek végrehajtása** – a iteratív funkcionális és teljesítményteszteket futtathat.
3. **Optimalizálás** – javítsa ki a teljesítménnyel kapcsolatos problémákat a fenti tesztek eredményei alapján, majd ellenőrizze a teljesítmény fejlesztését.

## <a name="next-steps"></a>Következő lépések

További információ a [HDInsight 4,0](./apache-hadoop-introduction.md)-ről.