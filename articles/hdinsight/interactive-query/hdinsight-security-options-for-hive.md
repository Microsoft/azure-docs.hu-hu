---
title: Biztonsági beállítások a Kaptárhoz az Azure HDInsight
description: Biztonsági beállítások a Kaptárhoz a standard és az ESP fürtökben.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: a608c34225641a3c7764d6c7dd3872c5f61fe3c8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869718"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Biztonsági beállítások a Kaptárhoz az Azure HDInsight

Ez a dokumentum ismerteti a kaptár ajánlott biztonsági beállításait a HDInsight-ben. Ezek a beállítások az Ambari használatával konfigurálhatók.

:::image type="content" source="./media/hdinsight-security-options-for-hive/security-options-hive.png " alt-text="&quot;Biztonsági beállítások a Kaptárhoz&quot;" border="true":::

## <a name="hiveserver2-authentication"></a>HiveServer2-hitelesítés

Standard szintű fürtök esetén a HiveServer2-hitelesítés javasolt beállítása az alapértelmezett érték, amely nincs. A hitelesítés engedélyezéséhez javasoljuk, hogy frissítsen egy [ESP](../domain-joined/hdinsight-security-overview.md) (Enterprise Security Package) fürtre. 

ESP-fürtök esetén a [Kerberos](https://web.mit.edu/Kerberos/) -hitelesítés alapértelmezés szerint engedélyezve van. A csatlakoztatható hitelesítési modulok (PAM) és az egyéni hitelesítési sémák nem támogatottak.

## <a name="hiveserver2-authorization"></a>HiveServer2-hitelesítés

Standard szintű fürtök esetében az alapértelmezett beállítás nincs. A [SqlStdAuth (SQL-szabványokon alapuló hitelesítés)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) engedélyezve lehet. A standard fürtök esetében nem támogatott az [Apache rangern](https://ranger.apache.org/) keresztüli engedélyezés. Javasoljuk, hogy a Ranger-hitelesítéshez egy ESP-fürtöt frissítsen. 

Az ESP-fürtök esetében a Ranger engedélyezése alapértelmezés szerint engedélyezve van. 


## <a name="ssl-encryption-for-hiveserver2"></a>SSL-titkosítás a HiveServer2

A Hiveserver2 SSL engedélyezése standard vagy ESP fürtök esetén nem ajánlott. Az SSL engedélyezve van az átjárón. Az [átvitel során](../domain-joined/encryption-in-transit.md) engedélyezhető a fürt csomópontjai közötti kommunikáció titkosítása [Internet Protocol biztonság (IPSec)](https://en.wikipedia.org/wiki/IPsec)használatával.


## <a name="next-steps"></a>Következő lépések
* [Az HiveServer2-hitelesítés áttekintése](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Az HiveServer2-engedélyezés áttekintése](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [SQL-szabványon alapuló struktúra engedélyezésének engedélyezése](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger a kaptárral](../domain-joined/apache-domain-joined-run-hive.md)
