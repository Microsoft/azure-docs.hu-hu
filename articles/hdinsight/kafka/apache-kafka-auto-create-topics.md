---
title: A témakör automatikus létrehozásának engedélyezése Apache Kafkaban – Azure HDInsight
description: Megtudhatja, hogyan konfigurálhat Apache Kafka a HDInsight-ben a témakörök automatikus létrehozásához. A Kafka konfigurálása a `auto.create.topics.enable` Ambari-n keresztül True értékre állítható be. Vagy a fürt létrehozásakor PowerShell vagy Resource Manager-sablonok használatával.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: b416623d6637cfe8e2c1cd795dd62553f8c0aed4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933206"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>A HDInsight Apache Kafka konfigurálása a témakörök automatikus létrehozásához

Alapértelmezés szerint a HDInsight Apache Kafka nem engedélyezi az automatikus témakör létrehozását. Az Apache Ambari használatával engedélyezheti az automatikus témakör létrehozását a meglévő fürtökhöz. Az új Kafka-fürt Azure Resource Manager sablon használatával történő létrehozásakor is engedélyezheti az automatikus témakör létrehozásának lehetőségeit.

## <a name="apache-ambari-web-ui"></a>Apache Ambari webes felhasználói felület

Ha engedélyezni szeretné az automatikus témakör létrehozását egy meglévő fürtön a Ambari webes felhasználói felületén keresztül, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza ki a Kafka-fürtöt.

1. A **fürt irányítópultok** területén válassza a **Ambari Kezdőlap** lehetőséget.

    ![A portál képe, amelyen a fürt irányítópultja ki van választva](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Ha a rendszer kéri, végezzen hitelesítést a fürt bejelentkezési (rendszergazdai) hitelesítő adataival. Ehelyett közvetlenül is csatlakozhat a Amabri, ahonnan `https://CLUSTERNAME.azurehdinsight.net/` a a `CLUSTERNAME` Kafka-fürt neve.

1. Válassza ki a Kafka szolgáltatást a lap bal oldalán található listából.

    ![Apache Ambari-szolgáltatás listája lap](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Válassza a konfigurációk lehetőséget az oldal közepén.

    ![Apache Ambari szolgáltatás – konfigurációk lap](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. A szűrő mezőbe írja be a értékét `auto.create` .

    ![Apache Ambari keresési szűrő mező](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Ezzel a beállítással kiszűrheti a tulajdonságok listáját, és megjelenítheti a `auto.create.topics.enable` beállítást.

1. Módosítsa a értéket a értékre `auto.create.topics.enable` `true` , majd válassza a **Mentés** lehetőséget. Vegyen fel egy megjegyzést, majd válassza a **Mentés** újra lehetőséget.

    ![Az Auto. Create. témák. Enable bejegyzés képe](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Válassza ki a Kafka szolgáltatást, válassza az __Újraindítás__ lehetőséget, majd kattintson az __összes érintett újraindítása__ elemre. Ha a rendszer kéri, válassza __az összes újraindításának megerősítése__ lehetőséget.

    !["Apache Ambari restart all érintett"](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

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
