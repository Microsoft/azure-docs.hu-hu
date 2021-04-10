---
title: A témakör automatikus létrehozásának engedélyezése Apache Kafkaban – Azure HDInsight
description: Megtudhatja, hogyan konfigurálhat Apache Kafka a HDInsight-ben a témakörök automatikus létrehozásához. A Kafka konfigurálása a `auto.create.topics.enable` Ambari-n keresztül True értékre állítható be. Vagy a fürt létrehozásakor PowerShell vagy Resource Manager-sablonok használatával.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 3766d41959383d802e50aafbf59b9841d1c8d74e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870687"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>A HDInsight Apache Kafka konfigurálása a témakörök automatikus létrehozásához

Alapértelmezés szerint a HDInsight Apache Kafka nem engedélyezi az automatikus témakör létrehozását. Az Apache Ambari használatával engedélyezheti az automatikus témakör létrehozását a meglévő fürtökhöz. Az új Kafka-fürt Azure Resource Manager sablon használatával történő létrehozásakor is engedélyezheti az automatikus témakör létrehozásának lehetőségeit.

## <a name="apache-ambari-web-ui"></a>Apache Ambari webes felhasználói felület

Ha engedélyezni szeretné az automatikus témakör létrehozását egy meglévő fürtön a Ambari webes felhasználói felületén keresztül, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza ki a Kafka-fürtöt.

1. A **fürt irányítópultok** területén válassza a **Ambari Kezdőlap** lehetőséget.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png" alt-text="A portál képe, amelyen a fürt irányítópultja ki van választva" border="true":::

    Ha a rendszer kéri, végezzen hitelesítést a fürt bejelentkezési (rendszergazdai) hitelesítő adataival. Ehelyett közvetlenül is csatlakozhat a Amabri, ahonnan `https://CLUSTERNAME.azurehdinsight.net/` a a `CLUSTERNAME` Kafka-fürt neve.

1. Válassza ki a Kafka szolgáltatást a lap bal oldalán található listából.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-list.png" alt-text="Apache Ambari-szolgáltatás listája lap" border="true":::

1. Válassza a konfigurációk lehetőséget az oldal közepén.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-config.png" alt-text="Apache Ambari szolgáltatás – konfigurációk lap" border="true":::

1. A szűrő mezőbe írja be a értékét `auto.create` .

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png" alt-text="Apache Ambari keresési szűrő mező" border="true":::

    Ezzel a beállítással kiszűrheti a tulajdonságok listáját, és megjelenítheti a `auto.create.topics.enable` beállítást.

1. Módosítsa a értéket a értékre `auto.create.topics.enable` `true` , majd válassza a **Mentés** lehetőséget. Vegyen fel egy megjegyzést, majd válassza a **Mentés** újra lehetőséget.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png" alt-text="Az Auto. Create. témák. Enable bejegyzés képe" border="true":::

1. Válassza ki a Kafka szolgáltatást, válassza az __Újraindítás__ lehetőséget, majd kattintson az __összes érintett újraindítása__ elemre. Ha a rendszer kéri, válassza __az összes újraindításának megerősítése__ lehetőséget.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/restart-all-affected.png" alt-text="&quot;Apache Ambari restart all érintett&quot;" border="true":::

> [!NOTE]  
> A Ambari értékeket a Ambari REST API is megadhatja. Ez általában nehezebb, mivel több REST-hívást kell megszereznie a jelenlegi konfiguráció beolvasásához, a módosítást stb. További információ: [HDInsight-fürtök kezelése az Apache Ambari REST API dokumentum használatával](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

Amikor egy Azure Resource Manager sablonnal hoz létre egy Kafka-fürtöt, közvetlenül is beállíthatja azt a alkalmazásban `auto.create.topics.enable` `kafka-broker` . A következő JSON-kódrészlet bemutatja, hogyan állíthatja be ezt az értéket `true` :

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan engedélyezheti az automatikus témakör létrehozását a HDInsight-Apache Kafka. Ha többet szeretne megtudni a Kafka használatáról, tekintse meg az alábbi hivatkozásokat:

* [Apache Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Az Apache Kafka-fürtök közötti adatreplikálás](apache-kafka-mirroring.md)
