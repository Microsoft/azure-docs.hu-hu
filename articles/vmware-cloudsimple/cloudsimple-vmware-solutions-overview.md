---
title: Azure VMware-megoldás a CloudSimple által – áttekintés
description: Ismerje meg az Azure-beli VMware-megoldások funkcióit, forgatókönyveit és előnyeit a CloudSimple szolgáltatásban.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7b5017cdeade14e53778b58154456412507c5ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "77024874"
---
# <a name="what-is-azure-vmware-solution-by-cloudsimple"></a>Mi az Azure VMware-megoldás a CloudSimple

A **CloudSimple által nyújtott Azure VMware-megoldás** egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a VMware platform futtatását az Azure-ban. Ez a megoldás magában foglalja az vSphere, a vCenter, a vSAN, a NSX-T és a megfelelő eszközöket. A VMware-környezet natív módon fut az Azure-beli operációs rendszer nélküli infrastruktúrában az Azure felhőalapú helyein. A szolgáltatás minden olyan funkciót magában foglal, amely a VMware-platformok hatékony és biztonságos felhasználásához szükséges.

![VMware-megoldás az Azure-ban a CloudSimple áttekintése](media/azure-vmware-solution-by-cloudsimple.png)

## <a name="features"></a>Funkciók

* VMware felhőalapú környezetek igény szerinti, önkiszolgáló üzembe helyezése. Kapacitás igény szerinti hozzáadásának és eltávolításának lehetősége.
* VMware platform üzembe helyezése, frissítés, felügyeleti sík biztonsági mentése, állapot/kapacitás figyelése, riasztások, hibaelhárítás és szervizelés.
* A VMware engedélyezéséhez szükséges hálózati szolgáltatások, beleértve az L2/L3-szolgáltatásokat és a tűzfalszabályok kezelését.
* Edge típusú hálózati szolgáltatások, beleértve a VPN-t, a nyilvános IP-címet és az internetes átjárókat. Ezek a szolgáltatások az Azure-ban futnak, és az Azure biztonsági és DDoS-védelmét végzik.
* Kapacitás foglalása alacsonyabb költségek mellett.
* Nagy sebességű, kis késleltetésű kapcsolat az Azure-ban és a helyszínen.
* Megoldási architektúrák az ügyfelek számára, hogy integrált módon használják az Azure-szolgáltatásokat, és kihasználják ezt az egyedi "VMware Cloud in a Public Cloud" architektúrát. Az Azure-szolgáltatások közé tartozik az Azure AD, a Storage, az Application Gateway és egyebek.
* Az Ön számára teljes mértékben dedikált infrastruktúra, amely a többi ügyfél infrastruktúrájában fizikailag el van különítve.
* Felügyeleti funkciók, például a tevékenységek kezelése, a használat, a számlázás/mérés és a felhasználói felügyelet.
* nonstop ügyfélszolgálata.

## <a name="benefits"></a>Előnyök

* **Működési folytonosság**. A CloudSimple natív hozzáférést biztosít a VMware platformokhoz. A CloudSimple architektúra kompatibilis a meglévővel:
    * Alkalmazások
    * Üzemeltetés
    * Biztonság
    * Backup
    * Vészhelyreállítás
    * Naplózás
    * Megfelelőségi eszközök
    * Folyamatok
* **Nincs újraképzés**. A VMware platform Compatibility lehetővé teszi a meglévő képességek és ismeretek használatát.
* **Infrastruktúra-agilitás**. Már nem kell előre megjósolnia az összes kapacitási igényt, és végül az elpazarolt kapacitással vagy az infrastruktúra hiányával kell foglalkoznia. A CloudSimple felhőalapú szolgáltatásként érhető el, és bármikor felveheti vagy csökkentheti a kapacitást.
* **Biztonság**. Az Azure-on keresztül elérhető CloudSimple-környezet beépített DDoS-védelmet és biztonsági monitorozást biztosít.
* **Alacsonyabb díj**. A CloudSimple platform nagy mértékben lett tervezve, és magas szintű automatizálást, működési hatékonyságot és méretgazdaságosságot biztosít. A CloudSimple emellett olyan megoldási architektúrákat tesz közzé, amelyek kihasználják a VMware nyilvános felhőben való jelenlétét a költségek csökkentése érdekében. Ilyenek például az Azure AD, az Azure Storage-ba való biztonsági mentés, az Application Gateway, a Load Balancer és egyebek.
* **Egy új hibrid platform**. A szolgáltatás lehetővé teszi a nagy sebességű, kis késésű hozzáférést az Azure többi részénél. Emellett a CloudSimple-kezelés lehetővé teszi a VMware virtuális gépek és az Azure többi részének egységes kezelését ugyanazzal a KEZELŐFELÜLETtel és API-val. A fejlesztői csapatok integrált, egységes módon vehetik igénybe a nyilvános és a privát platformokat is.
* **Infrastruktúra-figyelés, hibaelhárítás és támogatás**. A CloudSimple szolgáltatásként működteti a mögöttes infrastruktúrát. A meghiúsult hardver automatikusan lecserélve. Koncentrálhat a felhasználásra, miközben a CloudSimple biztosítja, hogy a környezet zökkenőmentesen fusson.
* **Házirend-kompatibilitás**. Megtarthatja VMware-alapú eszközeit, biztonsági eljárásait, auditálási gyakorlatait és megfelelőségi tanúsítványait.

## <a name="scenarios"></a>Forgatókönyvek

* **Adatközpont-nyugdíjazás vagy áttelepítés**. Ha eléri a meglévő adatközpont korlátait, vagy frissíti a hardvert, további kapacitást érhet el. Könnyedén felveheti a szükséges kapacitást a felhőbe, és nem távolítja el a hardveres frissítések kezelésének fejfájását. Csökkentse a felhőbe való Migrálás kockázatát és költségeit az időigényes átalakítások vagy az újraarchitektúrák összehasonlításával. A felhőbe történő Migrálás felgyorsításához használja a jól ismert VMware-eszközöket és-képességeket. A felhőben az Azure-szolgáltatások segítségével modernizálhatja alkalmazásait a tempóban.
* **Kibontás igény szerint**. Bontsa ki a felhőt a nem várt igények kielégítéséhez, például az új fejlesztési környezetekhez vagy a szezonális kapacitások kitöréséhez. Igény szerint egyszerűen hozhat létre új kapacitást, és megtarthatja azt, amíg szüksége van rá. Csökkentse a kezdeti befektetéseket, gyorsítsa fel a kiépítés sebességét, és csökkentse a bonyolultságot ugyanazzal az architektúrával és házirenddel mind a helyszínen, mind a felhőben.
* Vész- **helyreállítási és virtuális asztalok az Azure-felhőben**. Távoli hozzáférés létrehozása az Azure-felhőben tárolt adatelemekhez, alkalmazásokhoz és asztali gépekhez. A nagy sávszélességű kapcsolatokkal gyorsan feltöltheti és letöltheti az adatokat az incidensből való helyreállításhoz. Az alacsony késleltetésű hálózatok gyors válaszidőt biztosítanak a felhasználók számára egy asztali alkalmazástól. A CloudSimple segítségével egyszerűen replikálhatja az összes házirendjét és hálózatát a felhőben a CloudSimple-portál és az ismerős VMware-eszközök használatával. A könnyű helyreállítás és a replikálás nagy mértékben csökkenti a DR-és VDI-implementációk létrehozásának és kezelésének erőfeszítéseit és kockázatait.
* **Nagy teljesítményű alkalmazások és adatbázisok**. A CloudSimple egy hiperkonvergens-architektúrát biztosít, amely a legigényesebb VMware-alapú számítási feladatok futtatására szolgál. Az Oracle, a Microsoft SQL Server, a köztes rendszerek és a nagy teljesítményű, nem SQL-adatbázisok futtatása. A felhőt saját adatközpontként használhatja nagy sebességű, 25 GB/s-os hálózati kapcsolatokkal, amelyek lehetővé teszik a helyszíni, VMware-alapú és Azure-beli privát számítási feladatok futtatását a teljesítmény veszélyeztetése nélkül.
* **Valódi hibrid**. Egyesítse a DevOps a VMware és az Azure között. Optimalizálja a VMware-felügyeletet az Azure-szolgáltatásokhoz és-megoldásokhoz, amelyek az összes számítási feladatra alkalmazhatók. A nyilvános felhőalapú szolgáltatásokat anélkül érheti el, hogy kibővíti az adatközpontot, vagy újratervezi az alkalmazásokat. Az Azure-beli VMware-alkalmazások identitásának, hozzáférés-vezérlési házirendjeinek, naplózásának és figyelésének központosítása.

![Forgatókönyvek](media/cloudsimple-scenarios.png)

## <a name="next-steps"></a>Következő lépések

* [CloudSimple szolgáltatás létrehozása](quickstart-create-cloudsimple-service.md)
* [Privát felhő létrehozása](quickstart-create-private-cloud.md)
