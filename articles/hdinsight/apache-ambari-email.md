---
title: 'Oktatóanyag: az Apache Ambari e-mail értesítéseinek konfigurálása az Azure HDInsight'
description: Ez a cikk azt ismerteti, hogyan használható az SendGrid az Apache Ambari az e-mailes értesítésekhez.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 63d0f87925f14fddf3dbd722fa72326ee8b04f31
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946964"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Oktatóanyag: az Apache Ambari e-mail értesítéseinek konfigurálása az Azure HDInsight

Ebben az oktatóanyagban az Apache Ambari e-mail értesítéseinek konfigurálását az SendGrid használatával végezheti el. Az [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) megkönnyíti a HDInsight-fürtök felügyeletét és figyelését azáltal, hogy könnyen használható webes felhasználói felületet és REST API biztosít. A Ambari a HDInsight-fürtökben található, és a fürt figyelésére és a konfiguráció módosítására szolgál. A [SendGrid](https://sendgrid.com/solutions/) egy ingyenes felhőalapú e-mail-szolgáltatás, amely megbízható tranzakciós e-mail-kézbesítést, skálázhatóságot és valós idejű elemzéseket biztosít, valamint rugalmas API-kat kínál, amelyek egyszerűvé teszik az egyéni integrációt. Az Azure-ügyfelek havonta 25 000 ingyenes e-mailt oldhatnak fel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Sendgrid-Felhasználónév beszerzése
> * Az Apache Ambari e-mail értesítéseinek konfigurálása

## <a name="prerequisites"></a>Előfeltételek

* Egy SendGrid e-mail-fiók. Lásd: az [e-mailek küldése az SendGrid használatával az Azure-](../sendgrid-dotnet-how-to-send-email.md) ban utasításokért.

* An méretű HDInsight-fürt. Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>SendGrid-Felhasználónév beszerzése

1. A [Azure Portal](https://portal.azure.com)navigáljon a SendGrid-erőforráshoz.

1. Az Áttekintés lapon válassza a **kezelés** lehetőséget, hogy a fiókja SendGrid weboldalát lépjen.

    ![A SendGrid áttekintése az Azure Portalon](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. A bal oldali menüben navigáljon a fiók nevére, majd adja meg a **fiók adatait**.

    ![SendGrid-irányítópult navigációja](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. A **fiók részletei** lapon jegyezze fel a **felhasználónevet**.

    ![SendGrid-fiók részletei](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Ambari e-mail értesítések konfigurálása

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts` , ahol a a `CLUSTERNAME` fürt neve.

1. A **műveletek** legördülő listában válassza az **értesítések kezelése** lehetőséget.

1. A **Riasztási értesítések kezelése** ablakban válassza ki az **+** ikont.

    ![A képernyőkép a riasztási értesítések kezelése párbeszédpanelt jeleníti meg.](./media/apache-ambari-email/azure-portal-create-notification.png)

1. A **riasztási értesítés létrehozása** párbeszédpanelen adja meg a következő információkat:

    |Tulajdonság |Leírás |
    |---|---|
    |Név|Adja meg az értesítés nevét.|
    |Csoportok|Konfigurálja a kívánt módon.|
    |Súlyosság|Konfigurálja a kívánt módon.|
    |Description|Választható.|
    |Metódus|Hagyjon **e-mailt**.|
    |E-mail cím|Adja meg az e-maileket az értesítések fogadásához, vesszővel elválasztva.|
    |SMTP-kiszolgáló|`smtp.sendgrid.net`|
    |SMTP-port|25 vagy 587 (titkosítatlan/TLS-kapcsolatokhoz).|
    |E-mail-címe|Adjon meg egy e-mail-címet. A címnek nem kell hitelesnek lennie.|
    |Hitelesítés használata|Jelölje be ezt a jelölőnégyzetet.|
    |Felhasználónév|Adja meg a SendGrid felhasználónevét.|
    |Jelszó|Adja meg a SendGrid-erőforrás Azure-ban való létrehozásakor használt jelszót.|
    |Jelszó megerősítése|Adja meg újra a jelszót.|
    |TLS indítása|Jelölje be ezt a jelölőnégyzetet|

    ![A riasztási értesítés létrehozása párbeszédpanelt a képernyőképen jelenítheti meg.](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Kattintson a **Mentés** gombra. Visszatér a **Riasztási értesítések kezelése** ablakra.

1. A **Riasztási értesítések kezelése** ablakban válassza a **Bezárás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja az Apache Ambari e-mailes értesítéseit a SendGrid használatával. Az Apache Ambari használatával kapcsolatos további tudnivalókért tekintse meg a következőt:

* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](./hdinsight-hadoop-manage-ambari.md)

* [Riasztási értesítés létrehozása](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)