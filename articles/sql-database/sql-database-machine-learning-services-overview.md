---
title: Az Azure SQL Database Machine Learning-szolgáltatások (előzetes verzió) R – áttekintés
description: Ez a cikk ismerteti az Azure SQL Database Machine Learning-szolgáltatások (az r nyelv), és leírja annak működését.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 172bf201e2327f5ae8db0ac3c82f2f07e3f0ffc9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997478"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Az Azure SQL Database Machine Learning-szolgáltatások (előzetes verzió) R-rel

Machine Learning-szolgáltatások az Azure SQL Database-adatbázis-R-parancsfájlok futtatásához használt egy funkciója. A szolgáltatás a Microsoft R-csomagokat a nagy teljesítményű prediktív elemzési és gépi tanulási tartalmazza. Tárolt eljárások, a T-SQL parancsfájlt tartalmazó R utasítások vagy az R-kód, amely tartalmazza a T-SQL R-szkriptek a relációs adatok is használható.

> [!IMPORTANT]
> Az Azure SQL Database Machine Learning-szolgáltatások (az r nyelv) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A nyilvános előzetes verzióban érhető el az önálló adatbázisok és rugalmas készletek a Virtuálismag-alapú vásárlási modell a használatával a **általános célú** és **üzletileg kritikus** szolgáltatáshoz. A kezdeti nyilvános előzetes verzióban érhető el a **nagy kapacitású** szolgáltatási szint és a **felügyelt példány** rendszerbe állítási beállításának használata nem támogatott. Jelenleg az R az egyetlen támogatott nyelv. A Python jelenleg nem támogatott.
>
> Jelenleg az előzetes verzió érhető el a következő régiókban: Nyugat-Európa, Észak-Európa, USA nyugati RÉGIÓJA 2, USA keleti RÉGIÓJA, USA déli középső RÉGIÓJA, USA északi középső RÉGIÓJA, közép-Kanada, Délkelet-Ázsia, Dél-India és Délkelet-Ausztrália.
>
> [Regisztráljon az előzetes verzió](#signup) alatt.

## <a name="what-you-can-do-with-r"></a>Teendők, R-rel

Használja az R nyelvvel, hogy a fejlett elemzési és gépi tanulási adatbázison belüli. Ez a képesség számos lehetőséget kínál, számítások és feldolgozása, ahol az adatok találhatók, így nincs szükség az adatok a hálózaton keresztül. Emellett vállalati R-csomagok hatékonyságát, hogy a fejlett analitikai ipari méretekben használhatja.

Machine Learning-szolgáltatások, R, a Microsoft nagyvállalati R-csomagok az átfedett alap terjesztési tartalmazza. A Microsoft R-függvények és algoritmusokat a méretezés és a prediktív elemzés, statisztikai modellezési, adatvizualizációk és élvonalbeli gépi tanulási algoritmusok segédprogram fejthetők vissza.

### <a name="r-packages"></a>R-csomagok

Leggyakrabban használt nyílt forráskódú R-csomagok olyan előre telepített, a Machine Learning-szolgáltatások. A következő, a Microsoft R-csomagokat is szerepelnek:

| R-csomag | Leírás|
|-|-|
| [A Microsoft R Open](https://mran.microsoft.com/rro) | A Microsoft R Open a továbbfejlesztett terjesztési verziója a Microsoft R. Ez az statisztikai elemzés és az adattudomány teljes nyílt forráskódú platform. Ez be és 100 %-kal kompatibilis az R-alapú, és javítja a teljesítményt és megismételhetőség további képességeket tartalmazza. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR az elsődleges tár, ebben a könyvtárban méretezhető R. funkciók a következők közül a legszélesebb körben használt. Ezek a kódtárak adatátalakítások és adatmanipulációs, statisztikai összegzési, vizualizációs és modellezési és elemzések sok formáját találhatók. Ezenkívül ezek a kódtárak függvényei automatikusan elosztják a munkaterhelést párhuzamos feldolgozás, lehetővé teszi az dolgoznak adattömböket koordinált és a számítási motor által kezelt adatok elérhető magok száma. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML hozzáadja a gépi tanulási algoritmusok, szövegelemzés, képelemzés és hangulatelemzés egyéni modellek létrehozásának eljárásait. |

<!-- Add this back when the new package-related article is written
In addition to the pre-installed packages, you can [install additional packages](sql-database-quickstart-r-create-script.md#add-a-package).
-->

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Feliratkozás az előzetes verzióra

Ha szeretne regisztrálni a nyilvános előzetes verzió, kövesse az alábbi lépéseket:

1. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

2. E-mail küldése a Microsoftnak, [ sqldbml@microsoft.com ](mailto:sqldbml@microsoft.com) regisztrálhat a nyilvános előzetes verziója. A Machine Learning Services (with R) nyilvános előzetes verziójának használata az SQL Database-ben alapértelmezés szerint nem engedélyezett.

Ha Ön regisztrálva van a programban, a Microsoft is megjelenik majd, a nyilvános előzetes verziója és az R engedélyezése a meglévő vagy új adatbázis.

Machine Learning-szolgáltatások az r nyelv nem ajánlott az éles számítási feladatok a nyilvános előzetes verzió ideje alatt.

## <a name="next-steps"></a>További lépések

- Tekintse meg a [kulcs különbségek az SQL Server Machine Learning-szolgáltatások](sql-database-machine-learning-services-differences.md)
- Az Azure SQL Database használata a Machine Learning-szolgáltatások (az r nyelv) című témakörben talál [a rövid útmutató](sql-database-connect-query-r.md).
- További tudnivalók a [SQL Server R nyelv oktatóanyagok](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)
