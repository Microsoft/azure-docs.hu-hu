---
title: Az Sqoop importálási/exportálási parancsa meghiúsul az ESP-fürtökben lévő egyes felhasználók esetében – Azure HDInsight
description: 'Az Apache Sqoop importálási/exportálási parancsa meghiúsul az "import failed: Java. IO. IOException: az átmeneti könyvtár/User/yourusername/.staging nem a várt módon" hibaüzenet az Azure HDInsight ESP-fürt egyes felhasználói számára.'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387360"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Forgatókönyv: az Sqoop importálási/exportálási parancsa meghiúsul az Azure HDInsight ESP-fürtökben 20 karakternél nagyobb felhasználónevek esetén

Ez a cikk egy ismert problémát és megkerülő megoldást ismertet, ha az Azure HDInsight ESP (Enterprise Security Pack) engedélyezte a fürtöket a ADLS Gen2 (ABFS) Storage-fiók használatával.

## <a name="issue"></a>Probléma

A sqoop import/export parancs futtatásakor az alábbi hibaüzenet jelenik meg egyes felhasználók esetén:

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

A fenti példában a `/user/yourlongdomainuserna/.staging` felhasználónévhez tartozó 20 karakterből álló felhasználónevet jeleníti meg `yourlongdomainusername` .

## <a name="cause"></a>Ok

A Felhasználónév hossza hosszabb 20 karakternél. 

További részletekért tekintse meg, [Hogyan szinkronizálja az objektumokat és a hitelesítő adatokat egy Azure Active Directory Domain Services felügyelt tartományban](../active-directory-domain-services/synchronization.md) .

## <a name="workaround"></a>Áthidaló megoldás

Használjon 20 karakternél rövidebb vagy egyenlő felhasználónevet.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
