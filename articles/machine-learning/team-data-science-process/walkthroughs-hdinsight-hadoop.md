---
title: Elemzések Azure HDInsight Hadoop a kaptár-Team adatelemzési folyamat használatával
description: Példák a csoportos adatelemzési folyamatra, amely a Kaptárnak a Azure HDInsight Hadoopon való használatára mutat a prediktív elemzések elvégzéséhez.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "75864162"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>HDInsight Hadoop adatelemzési útmutató az Azure-beli struktúra használatával 

Ezek a forgatókönyvek a kaptárt egy HDInsight Hadoop-fürttel használják a prediktív elemzések elvégzéséhez. Ezek követik a csoportos adatelemzési folyamat lépéseit. A csoportos adatelemzési folyamat áttekintését lásd: [adatelemzési folyamat](overview.md). Az Azure HDInsight bemutatása: [Bevezetés az Azure HDInsight, a Hadoop Technology stackbe és a Hadoop-fürtökbe](../../hdinsight/hadoop/apache-hadoop-introduction.md).

A csoportos adatelemzési folyamatokat végrehajtó további adatelemzési útmutatókat az általuk használt **platform** szerint csoportosítjuk. A példák részletezés tekintse meg [a csoportos adatelemzési folyamat végrehajtásához szükséges útmutatókat](walkthroughs.md) .


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>A HDInsight Hadoop-val rendelkező kaptár használatával előre jelezheti a taxi-tippeket

A [HDInsight Hadoop-fürtök használata](hive-walkthrough.md) útmutató a New York-i taxik adatait használja a következő előrejelzéshez: 

- Azt jelzi, hogy díjköteles-e a tipp 
- A tip-összegek eloszlása

A forgatókönyv egy [Azure HDInsight Hadoop fürttel](https://azure.microsoft.com/services/hdinsight/)van megvalósítva a kaptár használatával. Megtudhatja, hogyan tárolhatja, vizsgálhatja és tekintheti meg a nyilvánosan elérhető NYC taxi Trip és fare adatkészletből származó mérnöki adatokat. A modellek létrehozásához és üzembe helyezéséhez Azure Machine Learning is használhatja.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Hirdetmények előrejelzése a kaptár használatával a HDInsight Hadoop

A [Azure HDInsight Hadoop-fürtök használata 1 TB-os adatkészlet](hive-criteo-walkthrough.md) -útmutatóban nyilvánosan elérhető [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) -t használ az adatkészlet elemre kattintva előre jelezheti, hogy a tipp fizetős-e, és a várt mennyiségek. A forgatókönyv a kaptár használatával valósul meg egy [Azure HDInsight Hadoop-fürtön](https://azure.microsoft.com/services/hdinsight/) , amely a mintaadatok tárolására, feltárására, kifejlesztésére és leállására szolgál. A Azure Machine Learning használatával készíti elő, betanítja és szerzi be a bináris besorolási modellt, amely azt jelzi, hogy a felhasználó egy hirdetésre kattint-e. A bemutató azt mutatja be, hogyan teheti közzé ezen modellek egyikét webszolgáltatásként.


## <a name="next-steps"></a>Következő lépések

A csoportos adatelemzési folyamatot alkotó főbb összetevőkről a [csoportos adatelemzési folyamat áttekintésében](overview.md)talál további információt.

A csoportos adatelemzési folyamat életciklusának megvitatására, amelyet az adatelemzési projektek felépítéséhez használhat, lásd: [csoportos adatelemzési folyamat életciklusa](lifecycle.md). Az életciklus az elejétől a végéig terjedő lépéseket ismerteti, amelyeket a projektek általában a végrehajtásuk után követnek. 

