---
title: Red Hat Enterprise Linux az Azure-ban elérhető rendszerképek
description: Ismerkedjen meg Red Hat Enterprise Linux rendszerképekkel Microsoft Azure
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 0a4d2ef5b7f367130151fabda3f1d97b65605931
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676040"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Az Azure-ban elérhető Red Hat Enterprise Linux-(RHEL-) rendszerképek
Az Azure különféle RHEL-rendszerképeket kínál különböző használati esetekhez.

> [!NOTE]
> Minden RHEL-lemezkép elérhető az Azure nyilvános és Azure Government felhőkben. Nem érhetők el az Azure China-felhőkben.

## <a name="list-of-rhel-images"></a>RHEL-lemezképek listája
Ez az Azure-ban elérhető RHEL-lemezképek listája. Ha másként nincs megadva, az összes rendszerkép LVM-particionált, és a normál RHEL-adattárakhoz (nem EUS, nem E4S) van csatolva. Az alábbi rendszerképek jelenleg általános használatra elérhetők:

> [!NOTE]
> A nyers képek már nem jönnek létre az LVM-particionált lemezképek javára. Az LVM számos előnyt biztosít a régebbi nyers (nem LVM) particionálási sémához képest, beleértve a jóval rugalmasabb partíció-átméretezési lehetőségeket.

Ajánlat| Termékváltozat | Particionálás | Kiépítés | Jegyzetek
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | NYERS    | Linux-ügynök | Meghosszabbított életciklus-támogatás érhető el december 1-től. [További részletek.](redhat-extended-lifecycle-support.md)
|             | 6.8      | NYERS    | Linux-ügynök | Meghosszabbított életciklus-támogatás érhető el december 1-től. [További részletek.](redhat-extended-lifecycle-support.md)
|             | 6.9      | NYERS    | Linux-ügynök | Meghosszabbított életciklus-támogatás érhető el december 1-től. [További részletek.](redhat-extended-lifecycle-support.md)
|             | 6,10     | NYERS    | Linux-ügynök | Meghosszabbított életciklus-támogatás érhető el december 1-től. [További részletek.](redhat-extended-lifecycle-support.md)
|             | 7 – NYERS    | NYERS    | Linux-ügynök | RHEL 7. x rendszerképekből álló család. <br> Alapértelmezés szerint a normál adattárakhoz van csatolva (nem EUS).
|             | 7 – LVM    | LVM    | Linux-ügynök | RHEL 7. x rendszerképekből álló család. <br> Alapértelmezés szerint a normál adattárakhoz van csatolva (nem EUS). Ha az üzembe helyezéshez szabványos RHEL lemezképet keres, használja ezt a lemezképet és/vagy annak 2. generációjának megfelelőjét.
|             | 7lvm – Gen2| LVM    | Linux-ügynök | 2. generációs, RHEL 7. x rendszerképek. <br> Alapértelmezés szerint a normál adattárakhoz van csatolva (nem EUS). Ha az üzembe helyezéshez szabványos RHEL lemezképet keres, használja ezt a lemezképet és/vagy annak 1. generációs megfelelőjét.
|             | 7-RAW-CI | NYERS – CI | Cloud-init  | RHEL 7. x rendszerképekből álló család. <br> Alapértelmezés szerint a normál adattárakhoz van csatolva (nem EUS).
|             | 7.2      | NYERS    | Linux-ügynök |
|             | 7.3      | NYERS    | Linux-ügynök |
|             | 7,4      | NYERS    | Linux-ügynök | A EUS-adattárakhoz van csatolva, alapértelmezés szerint április 2019.
|             | 74 – Gen2  | NYERS    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 7,5      | NYERS    | Linux-ügynök | A EUS-adattárakhoz a 2019-as számú alapértelmezett módon van csatolva.
|             | 75 – Gen2  | NYERS    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 7.6      | NYERS    | Linux-ügynök | A EUS-adattárakhoz a 2019-as számú alapértelmezett módon csatolva.
|             | 76 – Gen2  | NYERS    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 7.7      | LVM    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 77 – Gen2  | LVM    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 7,8      | LVM    | Linux-ügynök | Csatolva a normál adattárakhoz (a RHEL 7,8 EUS nem érhető el)
|             | 78 – Gen2  | LVM    | Linux-ügynök | Csatolva a normál adattárakhoz (a RHEL 7,8 EUS nem érhető el)
|             | 7.9      | LVM    | Linux-ügynök | Csatolva a normál adattárakhoz (a RHEL 7,9 EUS nem érhető el)
|             | 79 – Gen2  | LVM    | Linux-ügynök | Csatolva a normál adattárakhoz (a RHEL 7,9 EUS nem érhető el)
|             | 8 – LVM    | LVM    | Linux-ügynök | RHEL 8. x rendszerképekből álló család. Normál adattárakhoz csatolva.
|             | 8 – LVM – Gen2| LVM    | Linux-ügynök | Hyper-V Generation 2 – RHEL 8. x rendszerképek. Normál adattárakhoz csatolva.
|             | 8        | LVM    | Linux-ügynök | RHEL 8,0 lemezképek.
|             | 8 – Gen2   | LVM    | Linux-ügynök | Hyper-V generációs 2 – RHEL 8,0 lemezképek.
|             | 8.1      | LVM    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 81gen2   | LVM    | Linux-ügynök | Hyper-V 2. generációs, EUS-adattárakhoz csatolva november 2020.
|             | 8,1 – CI   | LVM    | Linux-ügynök | A EUS-adattárakhoz van csatolva november 2020.
|             | 81 – CI-Gen2| LVM    | Linux-ügynök | Hyper-V 2. generációs, EUS-adattárakhoz csatolva november 2020.
|             | 8.2      | LVM    | Linux-ügynök | A EUS-adattárakhoz van csatolva november 2020.
|             | 82gen2   | LVM    | Linux-ügynök | Hyper-V 2. generációs, EUS-adattárakhoz csatolva november 2020.
|             | 8.3   | LVM    | Linux-ügynök |  Csatolva a normál adattárakhoz (a RHEL 8,3 EUS nem érhető el)
|             | 83 – Gen2   | LVM    | Linux-ügynök |Hyper-V 2. generációs – normál adattárakhoz csatolva (a EUS nem érhető el a RHEL 8,3 esetében)
RHEL – SAP      | 7,4      | LVM    | Linux-ügynök | RHEL 7,4 SAP HANA és üzleti alkalmazásokhoz. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 74sap – Gen2| LVM    | Linux-ügynök | RHEL 7,4 SAP HANA és üzleti alkalmazásokhoz. 2. generációs rendszerkép. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 7,5       | LVM    | Linux-ügynök | RHEL 7,5 SAP HANA és üzleti alkalmazásokhoz. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 75sap – Gen2| LVM    | Linux-ügynök | RHEL 7,5 SAP HANA és üzleti alkalmazásokhoz. 2. generációs rendszerkép. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 7.6       | LVM    | Linux-ügynök | RHEL 7,6 SAP HANA és üzleti alkalmazásokhoz. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 76sap – Gen2| LVM    | Linux-ügynök | RHEL 7,6 SAP HANA és üzleti alkalmazásokhoz. 2. generációs rendszerkép. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 7.7       | LVM    | Linux-ügynök | RHEL 7,7 SAP HANA és üzleti alkalmazásokhoz. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
RHEL-SAP-HANA (el kell távolítani a 2020 novemberében) | 6.7       | NYERS    | Linux-ügynök | SAP HANA RHEL 6,7. Elavult a RHEL-SAP-lemezképek mellett. Ez a rendszerkép 2020 novemberében lesz eltávolítva. A Red Hat SAP felhőalapú ajánlatával kapcsolatos további részletek [itt](https://access.redhat.com/articles/3751271)érhetők el.
|             | 7.2       | LVM    | Linux-ügynök | SAP HANA RHEL 7,2. Elavult a RHEL-SAP-lemezképek mellett. Ez a rendszerkép 2020 novemberében lesz eltávolítva. A Red Hat SAP felhőalapú ajánlatával kapcsolatos további részletek [itt](https://access.redhat.com/articles/3751271)érhetők el.
|             | 7.3       | LVM    | Linux-ügynök | SAP HANA RHEL 7,3. Elavult a RHEL-SAP-lemezképek mellett. Ez a rendszerkép 2020 novemberében lesz eltávolítva. A Red Hat SAP felhőalapú ajánlatával kapcsolatos további részletek [itt](https://access.redhat.com/articles/3751271)érhetők el.
RHEL – SAP-APPS | 6.8       | NYERS    | Linux-ügynök | RHEL 6,8 az SAP Business Applicationshoz. Elavult a RHEL-SAP-lemezképek mellett.
|             | 7.3       | LVM    | Linux-ügynök | RHEL 7,3 az SAP Business Applicationshoz. Elavult a RHEL-SAP-lemezképek mellett.
|             | 7,4       | LVM    | Linux-ügynök | RHEL 7,4 az SAP Business Applicationshoz.
|             | 7.6       | LVM    | Linux-ügynök | RHEL 7,6 az SAP Business Applicationshoz.
|             | 7.7       | LVM    | Linux-ügynök | RHEL 7,7 az SAP Business Applicationshoz.
|             | 77 – Gen2       | LVM    | Linux-ügynök | RHEL 7,7 az SAP Business Applicationshoz. 2. generációs rendszerkép
|             | 8.1       | LVM    | Linux-ügynök | RHEL 8,1 az SAP Business Applicationshoz.
|             | 81 – Gen2      | LVM    | Linux-ügynök | RHEL 8,1 az SAP Business Applicationshoz. 2. generációs rendszerkép.
|             | 8.2       | LVM    | Linux-ügynök | RHEL 8,2 az SAP Business Applicationshoz.
|             | 82 – Gen2      | LVM    | Linux-ügynök | RHEL 8,2 az SAP Business Applicationshoz. 2. generációs rendszerkép.
RHEL – HA       | 7,4       | LVM    | Linux-ügynök | RHEL 7,4, HA a bővítmény be van kapcsolva. Díjat számítunk fel, és a RHEL az alap számítási díj alapján kell fizetni. Elavult a RHEL-SAP-HA lemezképek mellett.
|             | 7,5       | LVM    | Linux-ügynök | RHEL 7,5, HA a bővítmény be van kapcsolva. Díjat számítunk fel, és a RHEL az alap számítási díj alapján kell fizetni. Elavult a RHEL-SAP-HA lemezképek mellett.
|             | 7.6       | LVM    | Linux-ügynök | RHEL 7,6, HA a bővítmény be van kapcsolva. Díjat számítunk fel, és a RHEL az alap számítási díj alapján kell fizetni. Elavult a RHEL-SAP-HA lemezképek mellett.
RHEL – SAP-HA   | 7,4          | LVM    | Linux-ügynök | RHEL 7,4 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 74sapha – Gen2 | LVM    | Linux-ügynök | RHEL 7,4 for SAP az HA és a Update Services szolgáltatással. 2. generációs rendszerkép. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 7,5          | LVM    | Linux-ügynök | RHEL 7,5 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 7.6          | LVM    | Linux-ügynök | RHEL 7,6 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 76sapha – Gen2 | LVM    | Linux-ügynök | RHEL 7,6 for SAP az HA és a Update Services szolgáltatással. 2. generációs rendszerkép. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 7.7          | LVM    | Linux-ügynök | RHEL 7,7 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 77sapha – Gen2 | LVM    | Linux-ügynök | RHEL 7,7 for SAP az HA és a Update Services szolgáltatással. 2. generációs rendszerkép. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 8.1          | LVM    | Linux-ügynök | RHEL 8,1 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 81sapha – Gen2          | LVM    | Linux-ügynök | RHEL 8,1 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolt 2. generációs lemezképek. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 8.2          | LVM    | Linux-ügynök | RHEL 8,2 for SAP az HA és a Update Services szolgáltatással. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 82sapha – Gen2          | LVM    | Linux-ügynök | RHEL 8,2 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolt 2. generációs lemezképek. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
RHEL – BYOS     |RHEL – lvm74| LVM    | Linux-ügynök | A RHEL 7,4 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm75| LVM    | Linux-ügynök | A RHEL 7,5 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm76| LVM    | Linux-ügynök | A RHEL 7,6 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL-lvm76-Gen2| LVM    | Linux-ügynök | A RHEL 7,6 2. generációs BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyik forrásához, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm77| LVM    | Linux-ügynök | A RHEL 7,7 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL-lvm77-Gen2| LVM    | Linux-ügynök | A RHEL 7,7 2. generációs BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyik forrásához, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm78| LVM    | Linux-ügynök | A RHEL 7,8 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL-lvm78-Gen2| LVM    | Linux-ügynök | A RHEL 7,8 2. generációs BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyik forrásához, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm8 | LVM    | Linux-ügynök | A RHEL 8,0 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL-lvm8-Gen2 | LVM    | Linux-ügynök | A RHEL 8,0 2. generációs BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyik forrásához, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm81 | LVM    | Linux-ügynök | A RHEL 8,1 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL-lvm81-Gen2 | LVM    | Linux-ügynök | A RHEL 8,1 2. generációs BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyik forrásához, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm82 | LVM    | Linux-ügynök | A RHEL 8,2 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL-lvm82-Gen2 | LVM    | Linux-ügynök | A RHEL 8,2 2. generációs BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyik forrásához, nem számítanak fel RHEL prémium díjat.

> [!NOTE]
> A RHEL-SAP-HANA-termék ajánlata a Red Hat által életbe lépésnek minősül. A meglévő telepítések továbbra is rendesen működnek, de a Red Hat azt ajánlja, hogy az ügyfelek a RHEL-SAP-HANA rendszerképekről a RHEL-SAP-if lemezképekre telepítsenek, beleértve a SAP HANA-tárházat és a HA-bővítményt. A Red Hat SAP felhőalapú ajánlatával kapcsolatos további részletek [itt](https://access.redhat.com/articles/3751271)érhetők el.

## <a name="next-steps"></a>Következő lépések
* További információ az [Azure-beli Red Hat-lemezképekről](./redhat-images.md).
* További információ a [Red Hat frissítési infrastruktúráról](./redhat-rhui.md).
* További információ a [RHEL BYOS ajánlatáról](./byos.md).
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.
