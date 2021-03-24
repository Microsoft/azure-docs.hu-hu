---
title: 'Oktatóanyag: az Apache Ambari e-mail értesítéseinek konfigurálása az Azure HDInsight'
description: Ez a cikk azt ismerteti, hogyan használható az SendGrid az Apache Ambari az e-mailes értesítésekhez.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 5b344c0c4b1db9159d0223c861e5d371cb225f5a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867202"
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

    :::image type="content" source="./media/apache-ambari-email/azure-portal-sendgrid-manage.png" alt-text="A SendGrid áttekintése az Azure Portalon":::

1. A bal oldali menüben navigáljon a fiók nevére, majd adja meg a **fiók adatait**.

    :::image type="content" source="./media/apache-ambari-email/sendgrid-dashboard-navigation.png" alt-text="SendGrid-irányítópult navigációja":::

1. A **fiók részletei** lapon jegyezze fel a **felhasználónevet**.

    :::image type="content" source="./media/apache-ambari-email/sendgrid-account-details.png" alt-text="SendGrid-fiók részletei":::

## <a name="configure-ambari-e-mail-notification"></a>Ambari e-mail értesítések konfigurálása

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts` , ahol a a `CLUSTERNAME` fürt neve.

1. A **műveletek** legördülő listában válassza az **értesítések kezelése** lehetőséget.

1. A **Riasztási értesítések kezelése** ablakban válassza ki az **+** ikont.

    :::image type="content" source="./media/apache-ambari-email/azure-portal-create-notification.png" alt-text="A képernyőkép a riasztási értesítések kezelése párbeszédpanelt jeleníti meg.":::

1. A **riasztási értesítés létrehozása** párbeszédpanelen adja meg a következő információkat:

    |Tulajdonság |Leírás |
    |---|---|
    |Név|Adja meg az értesítés nevét.|
    |Csoportok|Konfigurálja a kívánt módon.|
    |Súlyosság|Konfigurálja a kívánt módon.|
    |Leírás|Választható.|
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

    :::image type="content" source="./media/apache-ambari-email/ambari-create-alert-notification.png" alt-text="A riasztási értesítés létrehozása párbeszédpanelt a képernyőképen jelenítheti meg.":::

    Kattintson a **Mentés** gombra. Visszatér a **Riasztási értesítések kezelése** ablakra.

1. A **Riasztási értesítések kezelése** ablakban válassza a **Bezárás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja az Apache Ambari e-mailes értesítéseit a SendGrid használatával. Az Apache Ambari használatával kapcsolatos további tudnivalókért tekintse meg a következőt:

* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](./hdinsight-hadoop-manage-ambari.md)

* [Riasztási értesítés létrehozása](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)