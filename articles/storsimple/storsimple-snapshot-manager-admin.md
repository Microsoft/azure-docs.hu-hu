---
title: StorSimple Snapshot Manager felügyelet | Microsoft Docs
description: Áttekintést nyújt, és hivatkozásokat tartalmaz a StorSimple Snapshot Manager megoldás-felügyeleti feladatokkal és munkafolyamatokkal kapcsolatos további információkra.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: alkohli
ms.openlocfilehash: 16d782c6a25d8d2d8e1323b7cb683f41d8c99649
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054976"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>A StorSimple-megoldás felügyeletéhez használja a StorSimple Snapshot Manager

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amely leegyszerűsíti az adatvédelmet és a biztonsági mentési felügyeletet egy Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Manager segítségével egyetlen integrált tárolási megoldásként kezelheti az adatközpontban és a felhőben tárolt Microsoft Azure StorSimple-adatmennyiségeket, így egyszerűsítve a biztonsági mentési folyamatokat, és csökkentheti a költségeket.

A StorSimple Snapshot Manager központi felügyeleti konzol lehetővé teszi a helyi és a Felhőbeli adatmennyiségek konzisztens, időponthoz kötött biztonsági másolatának létrehozását. A konzollal például a következőket végezheti el:

* Kötetek konfigurálása, biztonsági mentése és törlése.
* Konfigurálja a mennyiségi csoportokat annak biztosítására, hogy a biztonsági másolatok az alkalmazások konzisztensek legyenek.
* Biztonsági mentési házirendek kezelése, hogy az adatbiztonsági mentés előre meghatározott ütemterv szerint történjen.
* A felhőben tárolható és a vész-helyreállítási művelethez használt független adatmásolatok létrehozása.

Ez a cikk olyan oktatóanyagokra mutató hivatkozásokat tartalmaz, amelyek leírják a StorSimple Snapshot Manager, valamint azt, hogyan használható a rendszerfelügyeleti feladatok és munkafolyamatok végrehajtásához.

* További információ a StorSimple Snapshot Manager összetevőiről és architektúráról: [Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* A StorSimple Snapshot Manager letöltéséhez nyissa meg [a StorSimple Snapshot Manager letöltési lapját](https://www.microsoft.com/download/details.aspx?id=44220).
* A StorSimple Snapshot Manager üzembe helyezési eljárásaihoz keresse fel a [StorSimple Snapshot Manager üzembe helyezése című témakört](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> A StorSimple Snapshot Manager nem használhatók Microsoft Azure StorSimple virtuális tömbök (más néven StorSimple-alapú virtuális eszközök) kezelésére.


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager feladatok és munkafolyamatok
A StorSimple Snapshot Manager segítségével figyelheti és kezelheti a jelenlegi, ütemezett és befejezett biztonsági mentési feladatokat. Emellett a StorSimple Snapshot Manager katalógusa akár 64 befejezett biztonsági mentést is biztosít. A katalógus használatával kötetek vagy egyedi fájlok kereshetők és állíthatók vissza. 

| HA EZT SZERETNÉ TENNI... | AZ OKTATÓANYAG HASZNÁLATA... |
|:--- |:--- |
| További információ a StorSimple Snapshot Manager |[Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| A StorSimple telepítése Snapshot Manager<br>StorSimple újratelepítése Snapshot Manager<br>StorSimple eltávolítása Snapshot Manager |[StorSimple Snapshot Manager üzembe helyezése](storsimple-snapshot-manager-deployment.md) |
| StorSimple Snapshot Manager menük és szolgáltatások használata:<ul><li>Menüsáv</li><li>Eszköztár</li><li>Hatókör panel</li><li>Eredmények ablaktábla</li><li>Műveletek ablaktábla</li><li>Billentyűparancsok és billentyűparancsok</li></ul> |[StorSimple Snapshot Manager felhasználói felület](storsimple-use-snapshot-manager.md) |
| Használja az StorSimple Snapshot Managerban található általános MMC-funkciókat:<ul><li>Nézet</li><li>Új ablak innen</li><li>Frissítés</li><li>Exportálási lista</li><li>Súgó</li></ul> |[Használja az MMC menü műveleteit a StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Eszköz hozzáadása vagy cseréje<br>Eszköz csatlakoztatása<br>Importált mennyiségi csoportok ellenőrzése<br>Csatlakoztatott eszközök frissítése<br>Eszköz hitelesítése<br>Eszközadatok megtekintése<br>Eszköz konfigurációjának törlése<br>Eszköz jelszavának módosítása<br>Sikertelen eszköz cseréje<br> |[A StorSimple Snapshot Manager használata a StorSimple-eszközök csatlakoztatásához és kezeléséhez](storsimple-snapshot-manager-manage-devices.md) |
| Kötetek csatlakoztatása<br>Kötetek adatainak megtekintése<br>Kötet törlése<br>Kötetek újraellenőrzése<br>Alapszintű kötet konfigurálása és biztonsági mentése<br>Dinamikus tükrözött kötet konfigurálása és biztonsági mentése |[Kötetek megtekintése és kezelése StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-manage-volumes.md) |
| Mennyiségi csoportok megtekintése<br>Kötet csoport létrehozása<br>Mennyiségi csoport biztonsági mentése<br>Mennyiségi csoport szerkesztése<br>Mennyiségi csoport törlése |[StorSimple-Snapshot Manager használata a kötetek létrehozásához és kezeléséhez](storsimple-snapshot-manager-manage-volume-groups.md) |
| Biztonsági mentési szabályzat létrehozása <br>Biztonsági mentési szabályzat szerkesztése<br>Biztonsági mentési szabályzat törlése |[Biztonsági mentési szabályzatok létrehozása és kezelése StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-manage-backup-policies.md) |
| Ütemezett biztonsági mentési feladatok megtekintése és kezelése<br>Legutóbbi biztonsági mentési feladatok megtekintése és kezelése<br>Jelenleg futó biztonsági mentési feladatok megtekintése és kezelése |[A StorSimple Snapshot Manager használata a biztonsági mentési feladatok megtekintéséhez és kezeléséhez](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Kötet visszaállítása<br>Kötet vagy számítógépcsoport klónozása<br>Biztonsági másolat törlése<br>Fájl helyreállítása<br>A StorSimple Snapshot Manager adatbázisának visszaállítása |[A StorSimple Snapshot Manager használata a biztonsági mentési katalógus kezeléséhez](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Következő lépések
[Töltse le a StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

