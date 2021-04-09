---
title: Az üzletmenet folytonosságának áttekintése
titleSuffix: Microsoft Genomics
description: Ez az Áttekintés ismerteti azokat a képességeket, amelyeket a Microsoft Genomics az üzletmenet folytonosságát és a vész-helyreállítást biztosítja.
keywords: Üzletmenet-folytonosság, vész-helyreállítás
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 3097041c96f70dda384f0b116bbbc9bc9130be4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93395750"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Az üzletmenet folytonosságának áttekintése Microsoft Genomics
Ez az Áttekintés ismerteti azokat a képességeket, amelyeket a Microsoft Genomics az üzletmenet folytonosságát és a vész-helyreállítást biztosítja. Ismerje meg, hogy milyen lehetőségek állnak rendelkezésre a zavaró események, például az Azure-régiók meghibásodása esetén, ami adatvesztést okozhat. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Az üzletmenet folytonosságát támogató szolgáltatások Microsoft Genomics 
Bár ritkán fordul elő, az Azure-adatközpontok meghibásodást okozhatnak, ami akár néhány percet is igénybe vehet, ami néhány percen belül megszakadhat. Leállás esetén az adatközpontban jelenleg futó összes feladat sikertelen lesz, és a Microsoft Genomics szolgáltatás nem küldi el automatikusan a feladatokat egy másodlagos régióba. 

* Az egyik lehetőség az, hogy megvárja, amíg az adatközpont ismét online állapotba kerül, amikor az adatközpont-kimaradás meghalad. Ha a leállás rövid, akkor a Microsoft Genomics szolgáltatás automatikusan felismeri a sikertelen feladatokat, és a munkafolyamat automatikusan újraindul.

* Egy másik lehetőség, hogy proaktív módon küldje el a munkafolyamatot egy másik adatterületen. Microsoft Genomics különböző [Azure-régiókban](https://azure.microsoft.com/regions/services/)helyez üzembe példányokat, és mindegyik régióra jellemző példány független. Ha az Microsoft Genomics-példányok egyike a régió helyi meghibásodását tapasztalja, akkor a Microsoft Genomics példányait futtató más régiók továbbra is feldolgozzák a feladatokat. A másik régióba való átvitel a felhasználó felügyelete alá esik, és bármikor elérhető.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Microsoft Genomics munkafolyamatok manuális feladatátvétele egy másik régióba
Ha regionális adatközpont-kimaradás történik, dönthet úgy, hogy az egyes adatszuverenitási és üzletmenet-folytonossági követelmények alapján elküld Microsoft Genomics munkafolyamatokat egy másodlagos régióban. A Microsoft Genomics-munkafolyamatok manuális feladatátvételéhez más régió-specifikust kellene használnia. A genomikai fiók és a megfelelő régió-specifikus genomikai és Storage-fiók hitelesítő adataival küldje el a feladatot.

A következőkre lesz szüksége:
* Hozzon létre egy genomikai fiókot a másodlagos régióban a Azure Portal használatával. 
* A bemeneti adatokat áttelepítheti egy Storage-fiókba a másodlagos régióban, és beállíthat egy kimeneti mappát a másodlagos régióban.
* A munkafolyamat elküldése a másodlagos régióban.

Az eredeti régió visszaállításakor a Microsoft Genomics szolgáltatás nem telepíti át az adatait a másodlagos régióból az eredeti régióba. Dönthet úgy, hogy áthelyezi a bemeneti és kimeneti fájlokat a másodlagos régióból az eredeti régióba.  Ha úgy dönt, hogy áthelyezi az adatait, ez a genomikai szolgáltatáson kívül esik, és az adatáthelyezéssel kapcsolatos összes díj az Ön felelőssége. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Felkészülés egy lehetséges régió-specifikus kimaradásra
Ha egy adatközpont-kimaradás esetén a gyorsabb helyreállításról van szó, néhány lépésből megtudhatja, hogy milyen időt vesz igénybe a Microsoft Genomics munkafolyamatainak manuális újraküldése egy másodlagos régióba:

* Azonosítsa a megfelelő másodlagos régiót, és proaktív módon hozzon létre egy genomikai fiókot az adott régióban
* Duplikálja az adatait az elsődleges és a másodlagos régióban, hogy az adatai azonnal elérhetők legyenek a másodlagos régióban. Ezt manuálisan is megteheti, vagy az Azure Storage-ban elérhető [geo-redundáns tárolási](../storage/common/storage-redundancy.md) funkciót használhatja. 

## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtudhatta, hogyan használhatja az üzletmenet folytonosságát és a vész-helyreállítási lehetőségeket a Microsoft Genomics szolgáltatás használatakor. Az üzletmenet folytonosságával és az Azure-beli vész-helyreállítással kapcsolatos további információkért lásd: az [Azure rugalmasságának technikai útmutatója.](/azure/architecture/resiliency/recovery-loss-azure-region)