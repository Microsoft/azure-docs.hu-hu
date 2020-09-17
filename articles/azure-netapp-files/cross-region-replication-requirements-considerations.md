---
title: A Azure NetApp Files kötetek régiók közötti replikációjának használatára vonatkozó követelmények és megfontolások | Microsoft Docs
description: Ismerteti a Azure NetApp Files kötet régiók közötti replikációs funkciójának használatára vonatkozó követelményeket és szempontokat.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: f2a50872fdb71419a0c3f068712ec67523a098e0
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708787"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>A régiók közötti replikáció használatára vonatkozó követelmények és szempontok 

Vegye figyelembe az alábbi követelményeket és szempontokat a Azure NetApp Files [kötet régiók közötti replikációs funkciójának használatáról](cross-region-replication-create-peering.md) :  

## <a name="requirements-and-considerations"></a>Követelmények és megfontolások 

* A régiók közötti replikációs szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Be kell küldenie egy várólistára vonatkozó kérelmet a szolgáltatás eléréséhez a [Azure NetApp Files régiók közötti replikációs várólista-küldési lapon](https://aka.ms/anfcrrpreviewsignup). Várjon egy hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól a régiók közötti replikációs szolgáltatás használata előtt.
* Azure NetApp Files replikáció csak bizonyos rögzített régiós párokban érhető el. Lásd: [támogatott régió párok](cross-region-replication-introduction.md#supported-region-pairs). 
* Az SMB-kötetek az NFS-kötetekkel együtt támogatottak. Az SMB-kötetek replikálásához Active Directory kapcsolat szükséges a forrás-és a cél NetApp-fiókokban. A cél AD-kapcsolatnak hozzáféréssel kell rendelkeznie a DNS-kiszolgálókhoz, vagy hozzá kell adnia azokat a tartományvezérlőket, amelyek elérhetők a célként megadott régióban lévő delegált alhálózatból. További információ: [Active Directory kapcsolatokra vonatkozó követelmények](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* A célként megadott fióknak a forrás kötet régiójától eltérő régióban kell lennie. Másik régióban is kiválaszthat egy meglévő NetApp-fiókot.  
* Azure NetApp Files replikáció jelenleg nem támogatja több előfizetést; az összes replikációt egyetlen előfizetés alatt kell végrehajtani.
* Régiónként legfeljebb öt kötetet állíthat be egy adott előfizetésen belül. A támogatási jegyet megnyitva kérheti, hogy a rendszer az öt replikációs célként megadott kötet alapértelmezett kvótáját (egy adott régióban előfizetésben) növelje. 
* Akár öt percet is igénybe vehet, amíg a felület egy újonnan hozzáadott pillanatképet tükröz a forrás köteten.  
* A kaszkádolt és a ventilátor be-és kijelentkezési topológiája nem támogatott.
* A kötet replikálásának beállítása pillanatképből létrehozott források esetében jelenleg nem támogatott.
* A régiók közötti replikáció beállítása után a replikációs folyamat *snapmirror-pillanatképeket* hoz létre a forrás kötet és a cél kötete közötti hivatkozások biztosításához. A snapmirror-Pillanatképek automatikusan bekerülnek, amikor egy újat hoznak létre minden növekményes átvitelhez. A snapmirror-Pillanatképek nem törölhetők, amíg a replikációs kapcsolat és a kötet nem törlődik. 
* Ha a replikációs kapcsolat aktív vagy megszakadt, a replikálási kapcsolat törlését követően manuálisan is törölheti a replikálási kapcsolat forrás kötetén található manuális pillanatképeket. A cél kötethez tartozó manuális Pillanatképek nem törölhetők, amíg a replikálási kapcsolat megszakad.

## <a name="next-steps"></a>Következő lépések
* [Replikációs társítás létrehozása](cross-region-replication-create-peering.md)
* [Replikációs kapcsolat állapotának megjelenítése](cross-region-replication-display-health-status.md)
* [Vészhelyreállítás kezelése](cross-region-replication-manage-disaster-recovery.md)
* [Kötet replikációs metrikái](azure-netapp-files-metrics.md#replication)
* [Régiók közötti replikáció – problémamegoldás](troubleshoot-cross-region-replication.md)


