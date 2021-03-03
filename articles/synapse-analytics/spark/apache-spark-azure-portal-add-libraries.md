---
title: Függvénytár-kezelés
description: Megtudhatja, hogyan veheti fel és kezelheti a Apache Spark által használt könyvtárakat az Azure szinapszis Analyticsben.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 955d7f8c2d2ce5ea126d4cce67b0e4e55152ac72
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695090"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Apache Spark kódtárainak kezelése az Azure szinapszis Analyticsben
A tárak újrafelhasználható kódot biztosítanak, amelyet a programok vagy projektek számára érdemes felvenni. 

Előfordulhat, hogy különböző okokból frissítenie kell a kiszolgáló nélküli Apache Spark Pool-környezetet. Előfordulhat például, hogy a következőket találja:
- a legfontosabb függőségei egy új verziót adtak ki.
- További csomagra van szükség a gépi tanulási modell betanításához vagy az adatelőkészítéshez.
- jobb csomagot talált, és már nincs szüksége a régebbi csomagra.
- a csapata létrehozott egy egyéni csomagot, amelyre szüksége lesz a Apache Spark készletében.

Ahhoz, hogy a harmadik féltől származó vagy helyileg létrehozott kódot elérhetővé tegye az alkalmazásai számára, telepíthet egy függvénytárat az egyik kiszolgáló nélküli Apache Spark készletére vagy notebook-munkamenetére.
  
## <a name="default-installation"></a>Alapértelmezett telepítés
Apache Spark az Azure szinapszis Analyticsben a teljes anacondas telepítése plusz további könyvtárakat tartalmaz. A teljes kódtárak listája a következő címen érhető el: [Apache Spark Version support](apache-spark-version-support.md). 

A Spark-példány indításakor ezek a kódtárak automatikusan szerepelni fognak. További csomagokat a Spark-készlet szintjén vagy a munkamenet szintjén adhat hozzá.

## <a name="workspace-packages"></a>Munkaterület-csomagok
Egyéni alkalmazások vagy modellek fejlesztésekor a csapata különböző kódrészleteket fejleszthet, például a Wheel vagy jar fájlokat a kód csomagolásához. 

A Szinapszisban a munkaterület-csomagok egyéni, illetve saját kerek vagy JAR-fájlok is lehetnek. Ezeket a csomagokat feltöltheti a munkaterületre, majd később hozzárendelheti őket egy adott Spark-készlethez. Hozzárendelés után a rendszer automatikusan telepíti ezeket a munkaterület-csomagokat az összes Spark Pool-munkamenetre.

A munkaterület-kódtárak kezelésével kapcsolatos további információkért tekintse meg a következő útmutatókat:
- [Python-munkaterület csomagjai: ](./apache-spark-manage-python-packages.md#Install-wheel-files) Töltse fel a Python Wheel-fájlokat munkaterület-csomagként, majd később adja hozzá ezeket a csomagokat adott kiszolgáló nélküli Apache Spark készletekhez.
- [Scala/Java Workspace-csomagok (előzetes verzió): ](./apache-spark-manage-scala-packages.md#Workspace-packages) Töltse fel a Scala és a java jar-fájlokat munkaterület-csomagként, majd később adja hozzá ezeket a csomagokat adott kiszolgáló nélküli Apache Spark készletekhez.

## <a name="pool-management"></a>Készlet kezelése
Bizonyos esetekben érdemes lehet szabványosítani az adott Apache Spark-készletben használt csomagok készletét. Ez a szabványosítás akkor lehet hasznos, ha ugyanazokat a csomagokat általában több személy is telepíti a csapaton belül. 

Az Azure szinapszis Analytics-készlet felügyeleti képességeinek használatával konfigurálhatja az adott kiszolgáló nélküli Apache Spark készletre telepített könyvtárak alapértelmezett készletét. Ezek a kódtárak az [alapszintű futtatókörnyezetre](./apache-spark-version-support.md)vannak telepítve. 

Jelenleg a készlet kezelése csak a Python esetében támogatott. A Python esetében a szinapszis Spark-készletek a Conda használatával telepítik és kezelik a Python-csomagok függőségeit. A készlet szintű kódtárak megadásakor már megadhat egy requirements.txt vagy egy Environment. YML. Ez a környezeti konfigurációs fájl minden alkalommal használatos, amikor egy Spark-példányt hoznak létre az adott Spark-készletből. 

Ha többet szeretne megtudni ezekről a képességekről, tekintse meg a [Python-készlet felügyeletének](./apache-spark-manage-python-packages.md#Pool-libraries)dokumentációját.

> [!IMPORTANT]
> - Ha a telepített csomag nagy méretű, vagy hosszú ideig tart a telepítés, ez hatással van a Spark-példány indítási idejére.
> - A PySpark, a Python, a Scala/Java, a .NET vagy a Spark verzió módosítása nem támogatott.
> - A csomagok telepítése a PyPI-ből nem támogatott a DEP-kompatibilis munkaterületeken.

## <a name="session-scoped-packages"></a>Munkamenet-hatókörű csomagok
Az interaktív adatelemzés vagy a gépi tanulás során előfordulhat, hogy az újabb csomagokat szeretné kipróbálni, vagy előfordulhat, hogy olyan csomagokra van szüksége, amelyek még nem érhetők el a Apache Spark-készletben. A készlet konfigurációjának frissítése helyett a felhasználók mostantól munkamenet-hatókörű csomagokat is használhatnak a munkamenet-függőségek hozzáadásához, kezeléséhez és frissítéséhez.

A munkamenet-hatókörrel rendelkező csomagok lehetővé teszik a felhasználók számára, hogy a munkamenetük kezdetén definiálják a csomagok függőségeit. Munkamenet-hatókörű csomag telepítésekor csak az aktuális munkamenet fér hozzá a megadott csomagokhoz. Ennek eredményeképpen ezek a munkamenet-hatókörű csomagok nem érintik a többi munkamenetet vagy feladatokat ugyanazzal a Apache Spark készlettel. Emellett ezek a kódtárak az alapszintű futtatókörnyezetre és a készlet szintű csomagokra is telepítve vannak. 

A munkamenet-hatókörű csomagok kezelésével kapcsolatos további információkért tekintse meg a következő útmutatókat:
- [Python-munkamenet csomagjai (előzetes verzió):](./apache-spark-manage-python-packages.md#Session-scoped-libraries-(preview)) A munkamenet elején adjon meg egy Conda- *környezetet. a YML* további Python-csomagokat telepíthet a népszerű adattárakból. 
- [Scala/Java-munkamenetek csomagjai: ](./apache-spark-manage-scala-packages.md#Workspace-packages) A munkamenet elején adja meg a használatával telepítendő JAR-fájlok listáját ```%%configure``` .

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett könyvtárak megtekintése: [Apache Spark verzió támogatása](apache-spark-version-support.md)
