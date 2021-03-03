---
title: A Kerberos teljesítményére gyakorolt hatás Azure NetApp Files NFSv 4.1-es kötetek esetében | Microsoft Docs
description: Ismerteti a rendelkezésre álló biztonsági beállításokat, a tesztelt teljesítmény-vektorokat, valamint a Kerberos Azure NetApp Files NFSv 4.1-es köteteken várható teljesítménybeli hatását.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746089"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>A Kerberos teljesítményére gyakorolt hatás Azure NetApp Files NFSv 4.1-es köteteken

Azure NetApp Files támogatja az [NFS-ügyfél titkosítását Kerberos](configure-kerberos-encryption.md) -módban (krb5, krb5i és KRB5P) AES-256 titkosítással. Ez a cikk a Kerberos teljesítményre gyakorolt hatását mutatja be a NFSv 4.1 köteteken. 

## <a name="available-security-options"></a>Elérhető biztonsági beállítások 

A NFSv 4.1-kötetek jelenleg elérhető biztonsági beállítások a következők: 

* a **SEC = sys** helyi UNIX UID és GID használ az NFS-műveletek hitelesítéséhez AUTH_SYS használatával.
* **mp = a krb5** a Kerberos V5-et használja a helyi UNIX-UID és a GID helyett a felhasználók hitelesítéséhez.
* **SEC = a Krb5i** Kerberos V5 protokollt használ a felhasználók hitelesítéséhez, és az adatok illetéktelen módosításának megakadályozásához biztonságos ellenőrzőösszegek használatával végzi az NFS-műveletek integritásának ellenőrzését.
* **mp = a Krb5p** Kerberos V5 protokollt használ a felhasználók hitelesítéséhez és az integritás ellenőrzéséhez. Titkosítja az NFS-forgalmat, hogy megakadályozza a forgalom elemzését. Ez a lehetőség a legbiztonságosabb beállítás, de a legnagyobb teljesítménnyel is jár.

## <a name="performance-vectors-tested"></a>Tesztelt teljesítmény-vektorok

Ez a szakasz a különböző beállítások egyetlen ügyféloldali teljesítményére gyakorolt hatását ismerteti `sec=*` .

* A teljesítményre gyakorolt hatás két szinten lett tesztelve: alacsony Egyidejűség (alacsony terhelés) és magas Egyidejűség (az I/O és az átviteli sebesség felső határértéke).  
* A munkaterhelések három típusát tesztelték:  
    * Kis művelet véletlenszerű olvasási/írási (FIO használatával)
    * Nagyméretű művelet szekvenciális olvasási/írási (FIO használatával)
    * Metaadatok nagy mennyiségű számítási feladattal, például a git-vel generált alkalmazások

## <a name="expected-performance-impact"></a>Várható teljesítmény-hatás 

A fókusz két területből áll: a kis-és nagybetűkből álló terheléssel. Az alábbi listában a teljesítményre gyakorolt hatás biztonsági beállításai láthatók a biztonsági beállítások és forgatókönyvek szerint. Minden összehasonlítás a `sec=sys` biztonsági paraméterrel történik. A teszt egyetlen köteten, egyetlen ügyfél használatával lett végrehajtva. 

A krb5 teljesítményére gyakorolt hatás:

* Alacsony Egyidejűség (r/w):
    * A szekvenciális késés megnövelte a 0,3 MS-ot.
    * A véletlenszerű I/O-késés nagyobb, mint 0,2 MS.
    * A metaadatok I/O-késése nagyobb, mint 0,2 MS.
* Magas Egyidejűség (r/w): 
    * A krb5 nem befolyásolta a maximális szekvenciális átviteli sebességet.
    * A teljes véletlenszerű I/O-érték 30%-kal csökkent a tiszta olvasási munkaterhelések esetében, mivel a számítási feladatok a tiszta írásra váltanak. 
    * A metaadatok maximális munkaterhelése 30%-kal csökkent.

A krb5i teljesítményére gyakorolt hatás: 

* Alacsony Egyidejűség (r/w):
    * A szekvenciális késés megnövelte a 0,5 MS-ot.
    * A véletlenszerű I/O-késés nagyobb, mint 0,2 MS.
    * A metaadatok I/O-késése nagyobb, mint 0,2 MS.
* Magas Egyidejűség (r/w): 
    * A maximális soros átviteli sebesség 70%-kal csökkent, a munkaterhelés-elegytől függetlenül.
    * A maximális véletlenszerű I/O-érték 50%-kal csökkent a tiszta olvasási munkaterhelések esetében, és az általános hatás 25%-ra csökken, mivel a munkaterhelés a tiszta írásra vált. 
    * A metaadatok maximális munkaterhelése 30%-kal csökkent.

A krb5p teljesítményére gyakorolt hatás:

* Alacsony Egyidejűség (r/w):
    * A szekvenciális késés megnövelte a 0,8 MS-ot.
    * A véletlenszerű I/O-késés nagyobb, mint 0,2 MS.
    * A metaadatok I/O-késése nagyobb, mint 0,2 MS.
* Magas Egyidejűség (r/w): 
    * A maximális soros átviteli sebesség 85%-kal csökkent, a munkaterhelés-elegytől függetlenül. 
    * A teljes véletlenszerű I/O-érték 65%-kal csökkent a tiszta olvasási munkaterhelések esetében, és az általános hatás 43%-ra csökken, mivel a munkaterhelés a tiszta írásra változik. 
    * A metaadatok maximális munkaterhelése 30%-kal csökkent.

## <a name="next-steps"></a>Következő lépések  

* [Az NFSv 4.1 Kerberos-titkosításának konfigurálása az Azure NetApp Fileshoz](configure-kerberos-encryption.md) 
