---
title: Feladatok beosztása
description: Feladatok kezelése feladatütemezés használatával.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 4661c9fc22868870af147998467c9f355f30580e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219686"
---
# <a name="schedule-jobs-for-efficiency"></a>Feladatok ütemezése a hatékony végrehajtás érdekében

A Batch-feladatok ütemezése lehetővé teszi, hogy rangsorolja azokat a feladatokat, amelyeket először szeretne futtatni, miközben a más feladatokra függőségekkel rendelkező feladatokat is figyelembe veszi. A feladatok ütemezésével gondoskodhat arról, hogy a lehető legkevesebb erőforrást használja. Ha nincs rá szükség, a csomópontok levonhatók, és a munkafolyamatok optimalizálására szolgáló feladatokat csak időben használják fel. Egyszerre csak egy feladatot futtat. Egy új nem indul el, amíg az előző be nem fejeződik. A feladatot beállíthatja automatikus kiegészítésre. 

## <a name="benefit-of-job-scheduling"></a>A feladatütemezés előnyei

A feladatok ütemezésének előnye, hogy megadhat egy ütemezést a feladatok létrehozásához. A Feladatkezelő feladat használatával ütemezett feladatok egy feladathoz vannak társítva. A Feladatkezelő feladat feladatokat hoz létre a feladathoz. Ehhez a Feladatkezelő feladatnak hitelesítenie kell magát a Batch-fiókkal. Használja a AZ_BATCH_AUTHENTICATION_TOKEN hozzáférési tokent. A jogkivonat a feladatokhoz való hozzáférést is lehetővé teszi. 

## <a name="use-the-portal-to-schedule-a-job"></a>Feladatok beütemezés a portál használatával

   1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/).

   2. Válassza ki azt a Batch-fiókot, amelyben a feladatokat ütemezni szeretné.

   3. A **Hozzáadás** gombra kattintva hozzon létre egy új feladatütemezés, és fejezze be az **alapszintű űrlapot**.



![Feladatok ütemezve][1]

**FELADATÜTEMEZÉS azonosítója**: a feladatütemezés egyedi azonosítója.

**Megjelenítendő név**: a feladatokhoz tartozó megjelenítendő névnek nem kell egyedinek lennie, de legfeljebb 1024 karakter hosszú lehet.

Ne **fusson, amíg**: megadja a művelet legkorábbi futási idejét. Ha ezt nem teszi meg, az ütemterv azonnal készen áll a feladatok futtatására.

Ne **futtassa azt követően**: az itt beállított idő után nem futnak feladatok. Ha nem ad meg időpontot, akkor ismétlődő feladatütemezés jön létre, amely mindaddig aktív marad, amíg explicit módon meg nem szünteti.

**Ismétlődési időköz**: megadhatja a feladatok közötti időtartamot. Ütemezés szerint egyszerre csak egy feladattal rendelkezhet, így ha új feladatot szeretne létrehozni egy feladatütemezés alatt, de az előző művelet még fut, a Batch szolgáltatás nem hozza létre az új feladatot addig, amíg az előző feladatot be nem fejeződik.  

**Kezdési ablak**: Itt adhatja meg azt az időintervallumot, amelytől kezdve az ütemezés azt jelzi, hogy melyik feladatot kell létrehozni, amíg be nem fejeződik. Ha az aktuális feladattípus nem fejeződött be az ablakában, a következő feladatot nem fogja elindítani.

Az alapszintű űrlap alján adja meg azt a készletet, amelyen futtatni szeretné a feladatot. A készlet AZONOSÍTÓjának adatainak megkereséséhez válassza a **frissítés** lehetőséget. 

![Készlet meghatározása][2]


**Készlet azonosítója**: azonosítsa azt a készletet, amelyen a feladatot futtatni fogja.

**Feladat-konfigurálási feladat**: válassza a **frissítés** lehetőséget a Feladatkezelő feladat elnevezéséhez, valamint a feladat-előkészítési és-kiadási feladatokhoz, ha azokat használja.

**Prioritás**: adjon prioritást a feladatnak.

**Maximális falióra-idő**: állítsa be azt a maximális időtartamot, ameddig a feladatot futtatni lehet. Ha az idő keretén belül nem fejeződik be, a Batch leállítja a feladatot. Ha ezt nem teszi meg, akkor a feladatokhoz nincs időkorlát.

**Feladat-újrapróbálkozások maximális** száma: Itt adhatja meg, hogy hány alkalommal próbálkozzon újra egy tevékenység legfeljebb négyszer. Ez nem ugyanaz, mint az API-hívások újrapróbálkozások száma.

**Ha az összes feladat befejeződött**: az alapértelmezett érték nincs művelet.

**Ha egy feladat meghiúsul**: az alapértelmezett érték nincs művelet. Egy feladat meghiúsul, ha az újrapróbálkozások száma kimerült, vagy hiba történt a feladat indításakor. 

Ha a **Mentés** gombra kattint, ha a bal oldali navigációs sávon a **feladatok ütemterve** lehetőségre kattint, a **végrehajtási adatok** lehetőség választásával nyomon követheti a feladatok végrehajtását.


## <a name="for-more-information"></a>További tudnivalók

Ha az Azure CLI használatával szeretné kezelni a feladatokat, tekintse meg az [az batch Job-Schedule](/cli/azure/batch/job-schedule)című témakört.

## <a name="next-steps"></a>Következő lépések

[Feladat függőségeinek létrehozása olyan feladatok futtatásához, amelyek más feladatoktól függenek](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add-job-schedule-02.png
[2]: ./media/batch-job-schedule/add-job-schedule-03.png


