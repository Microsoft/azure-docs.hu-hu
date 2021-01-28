---
title: Azure Active Directory-felhasználók szinkronizálása a HDInsight-fürttel
description: Hitelesített felhasználók szinkronizálása Azure Active Directoryról egy HDInsight-fürtre.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: a3307c5d677028c17717c0d591790f0373f131d6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931631"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory-felhasználók HDInsight-fürttel való szinkronizálása

[Enterprise Security Package (ESP) HDInsight-fürtök](./domain-joined/hdinsight-security-overview.md) erős hitelesítést használhatnak Azure Active Directory (Azure ad) felhasználókkal, valamint az *Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)* házirendjeit. Amikor felhasználókat és csoportokat AD hozzá az Azure AD-hez, szinkronizálhatja azokat a felhasználókat, akiknek a fürthöz hozzáféréssel kell rendelkezniük.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem tette meg, [hozzon létre egy HDInsight-fürtöt Enterprise Security Package](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="add-new-azure-ad-users"></a>Új Azure AD-felhasználók hozzáadása

A gazdagépek megtekintéséhez nyissa meg a Ambari webes felhasználói felületét. Az egyes csomópontok új felügyelet nélküli frissítési beállításokkal lesznek frissítve.

1. A [Azure Portal](https://portal.azure.com)navigáljon az ESP-fürthöz társított Azure ad-címtárhoz.

2. Válassza a **minden felhasználó** lehetőséget a bal oldali menüben, majd válassza az **új felhasználó** lehetőséget.

    ![Az összes felhasználó és csoport Azure Portal](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Fejezze be az új felhasználói űrlapot. Válassza ki a fürtön alapuló engedélyek hozzárendeléséhez létrehozott csoportokat. Ebben a példában hozzon létre egy "HiveUsers" nevű csoportot, amelyhez új felhasználókat rendelhet hozzá. Az ESP-fürtök létrehozására [vonatkozó példa](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) a következő két csoport hozzáadását tartalmazza: `HiveUsers` és `AAD DC Administrators` .

    ![Azure Portal felhasználói ablaktábla csoportok kiválasztása](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Válassza a **Létrehozás** lehetőséget.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>A felhasználók szinkronizálása az Apache Ambari REST API használatával

Ekkor történik a fürt létrehozása során meghatározott felhasználói csoportok szinkronizálása. A felhasználók szinkronizálása óránként egyszer automatikusan megtörténik. A felhasználók azonnali szinkronizálásához, vagy a fürt létrehozásakor megadott csoportokon kívüli csoportok szinkronizálásához használja a Ambari REST API.

A következő metódus a POST és a Ambari REST API használja. További információ: HDInsight- [fürtök kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md).

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot úgy, hogy lecseréli a `CLUSTERNAME` fürt nevét, majd beírja a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A hitelesítés után írja be a következő parancsot:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    A következő választ kell kapnia:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. A szinkronizálás állapotának megtekintéséhez hajtson végre egy új `curl` parancsot:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    A következő választ kell kapnia:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Ez az eredmény azt mutatja, hogy az állapot **elkészült**, egy új felhasználó lett létrehozva, és a felhasználó hozzá lett rendelve egy tagsághoz. Ebben a példában a felhasználó hozzá van rendelve az "HiveUsers" szinkronizált LDAP-csoporthoz, mivel a felhasználó hozzá lett adva ugyanahhoz a csoporthoz az Azure AD-ben.

    > [!NOTE]  
    > Az előző metódus csak a tartományi beállítások **hozzáférés felhasználói csoport** tulajdonságában megadott Azure ad-csoportokat szinkronizálja a fürt létrehozásakor. További információt a HDInsight-  [fürt létrehozása](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)című témakörben talál.

## <a name="verify-the-newly-added-azure-ad-user"></a>Az újonnan hozzáadott Azure AD-felhasználó ellenőrzése

Nyissa meg az [Apache Ambari webes felületét](hdinsight-hadoop-manage-ambari.md) annak ellenőrzéséhez, hogy az új Azure ad-felhasználó hozzá lett-e adva. A Ambari webes felhasználói felületének eléréséhez tallózással keresse meg a következőt: **`https://CLUSTERNAME.azurehdinsight.net`** . Adja meg a fürt rendszergazdai felhasználónevét és jelszavát.

1. A Ambari Irányítópultján kattintson a **felügyelet Ambari** elemre a **rendszergazda** menüben.

    ![Apache Ambari-irányítópult Ambari kezelése](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. A lap bal oldalán, a **felhasználó + csoport kezelése** menü csoportjában válassza a **felhasználók** lehetőséget.

    ![HDInsight-felhasználók és-csoportok menü](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Az új felhasználónak szerepelnie kell a felhasználók táblában. A típus nem értékre van állítva `LDAP`  `Local` .

    ![HDInsight HRE-felhasználók lap – áttekintés](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Jelentkezzen be a Ambari új felhasználóként

Amikor az új felhasználó (vagy bármely más tartományi felhasználó) bejelentkezik a Ambari-ba, a teljes Azure AD-felhasználónevet és tartományi hitelesítő adatokat használják.  A Ambari megjelenít egy felhasználói aliast, amely a felhasználó megjelenítendő neve az Azure AD-ben.
Az új példában szereplő felhasználó neve `hiveuser3@contoso.com` . A Ambari-ben ez az új felhasználó úgy jelenik meg, ahogy `hiveuser3` a felhasználó bejelentkezik a Ambari-ba `hiveuser3@contoso.com` .

## <a name="see-also"></a>Lásd még

* [Apache Hive házirendek konfigurálása a HDInsight-ben ESP-vel](./domain-joined/apache-domain-joined-run-hive.md)
* [HDInsight-fürtök az ESP-vel való kezelése](./domain-joined/apache-domain-joined-manage.md)
* [Felhasználók engedélyezése az Apache Ambari](hdinsight-authorize-users-to-ambari.md)