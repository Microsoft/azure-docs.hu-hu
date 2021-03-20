---
title: A Azure Data Box Gateway általánosan elérhető kiadási megjegyzései | Microsoft Docs
description: Az általános rendelkezésre állási kiadást futtató Azure Data Box Gateway kritikusan nyitott problémáit és megoldásait ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582320"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway általános elérhetőségi kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat, valamint a Azure Data Box Edge és Azure Data Box Gateway általánosan elérhető kiadásával kapcsolatos feloldott problémákat. 

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A Data Box Edge/Data Box Gateway üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

A GA-kiadás a szoftverek verziószámának felel meg:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Újdonságok

- **Új virtuális lemezes rendszerképek** – az új VHDX és VMDK mostantól elérhetők a Azure Portal. Töltse le ezeket a lemezképeket új Data Box Gateway GA-eszközök kiépítéséhez, konfigurálásához és üzembe helyezéséhez. A korábbi előzetes verziókban létrehozott Data Box Gateway-eszközök nem frissíthetők erre a verzióra. További információ: [felkészülés a Azure Data Box Gateway üzembe helyezésére](data-box-gateway-deploy-prep.md).
- **NFS-támogatás** – az NFS-támogatás jelenleg előzetes verzióban érhető el, és elérhető a v 3.0 és a v 4.1 rendszerű ügyfeleknek, amelyek hozzáférnek a Data Box Edge és Data Box Gateway eszközökhöz.
- **Tárolási rugalmasság** – a Data Box Edge eszköz képes ellenállni egy adatlemez meghibásodásának a tárolási rugalmassági funkcióval. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. A tárterület rugalmasságának engedélyezéséhez válassza a **rugalmas** lehetőséget a helyi webes felhasználói felület **tárolási beállításai** között.


## <a name="known-issues-in-ga-release"></a>Ismert problémák a GA kiadásban

A következő táblázat összefoglalja az Data Box Gateway futó kiadásának ismert problémáit.

| Nem. | Szolgáltatás | Probléma | Áthidaló megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Fájltípusok | A következő fájltípusok nem támogatottak: karakterek, fájlok, szoftvercsatornák, csövek és szimbolikus hivatkozások.  |Ezeknek a fájloknak a másolása az NFS-megosztáson létrehozott, 0 hosszúságú fájlokat eredményez. Ezek a fájlok hibás állapotban maradnak, és *error.xmlban* is szerepelnek. <br> A címtárakra mutató szimbolikus hivatkozások azt eredményezik, hogy a címtárak soha nem jelennek meg offline állapotban. Ennek eredményeképpen előfordulhat, hogy nem látja a szürke keresztet azon könyvtárakon, amelyek jelzik, hogy a címtárak offline állapotban vannak, és az összes kapcsolódó tartalmat teljesen feltöltötte az Azure-ba. |
| **2.** |Törlés | A jelen kiadás hibája miatt, ha egy NFS-megosztást törölnek, akkor előfordulhat, hogy nem törli a megosztást. A megosztás állapota a *törlést* fogja megjeleníteni.  |Ez csak akkor fordul elő, ha a megosztás nem támogatott fájlnevet használ. |
| **3.** |Másolás | Az adatmásolási hiba a következő hibával meghiúsul: a kért műveletet nem lehetett befejezni a fájlrendszer korlátozása miatt.  |A 128 KB-nál nagyobb fájlmérethez társított alternatív adatfolyam (ADS) nem támogatott.   |


## <a name="next-steps"></a>Következő lépések

- [Felkészülés a Azure Data Box Gateway üzembe helyezésére](data-box-gateway-deploy-prep.md).
- [Felkészülés a Azure Data Box Edge üzembe helyezésére](../databox-online/azure-stack-edge-deploy-prep.md).
