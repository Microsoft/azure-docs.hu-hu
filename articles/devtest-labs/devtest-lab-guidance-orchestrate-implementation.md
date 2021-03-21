---
title: Azure DevTest Labs megvalósításának koordinálása
description: Ez a cikk útmutatást nyújt a Azure DevTest Labs szervezeten belüli megvalósításának előkészítéséhez.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1958e818f014b7419a1a33e9453fbad460dfc159
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330614"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>A Azure DevTest Labs megvalósításának összehangolása
Ez a cikk a Azure DevTest Labs gyors üzembe helyezésének és megvalósításának ajánlott módszerét ismerteti. Az alábbi képen az általános folyamat részletes útmutatást nyújt a különböző iparági követelmények és forgatókönyvek támogatásának rugalmasságával kapcsolatban.

![A Azure DevTest Labs megvalósításának lépései](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Feltételezések
Ez a cikk azt feltételezi, hogy a következő elemek vannak érvényben a DevTest Labs-próba megvalósítása előtt:

- **Azure-előfizetés**: a kísérleti csapat hozzáfér az erőforrások Azure-előfizetéshez való üzembe helyezéséhez. Ha a munkaterhelések csak fejlesztési és tesztelési célokra használhatók, a további elérhető rendszerképekhez és a Windows rendszerű virtuális gépek alacsonyabb díjszabásához ajánlott kiválasztani a vállalati DevTest ajánlatot.
- Helyszíni **hozzáférés**: ha szükséges, a helyszíni hozzáférés már konfigurálva van. A helyszíni hozzáférés hely-hely típusú VPN-kapcsolaton keresztül vagy expressz útvonalon keresztül valósítható meg. Az expressz útvonalon keresztüli kapcsolódás általában több hétig is eltarthat, ezért azt javasoljuk, hogy a projekt megkezdése előtt legyen elérhető az expressz útvonal.
- **Kísérleti csapatok**: az DevTest Labs-t használó kezdeti fejlesztői projektcsapat a megfelelő fejlesztési vagy tesztelési tevékenységekkel, valamint az adott csapatokra vonatkozó követelmények/célok/célkitűzések kialakításával lett azonosítva.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>1. mérföldkő: kezdeti hálózati topológia és kialakítás létrehozása
Azure DevTest Labs megoldás telepítésekor a fókusz első területe a virtuális gépek tervezett kapcsolatának kialakítása. A következő lépések ismertetik a szükséges eljárásokat:

1. Adja meg az Azure-beli DevTest Labs-előfizetéshez hozzárendelt **kezdeti IP-címtartományt** . Ehhez a lépéshez a virtuális gépek várható kihasználtságának előrejelzését kell megadnia, így nagy mennyiségű blokkot biztosíthat a jövőbeli bővítéshez.
2. Azonosítsa a DevTest Labs **kívánt hozzáférésének módszereit** (például külső/belső hozzáférés). Ennek a lépésnek a lényege annak megállapítása, hogy a virtuális gépek rendelkeznek-e nyilvános IP-címmel (azaz közvetlenül az internetről elérhető).
3. Azonosítsa és hozza létre a **kapcsolatot** a többi Azure-beli felhőalapú környezettel és a helyszínen. Ha a kényszerített útválasztás engedélyezve van az expressz útvonalon, akkor valószínű, hogy a virtuális gépeknek megfelelő proxybeállítások szükségesek a vállalati tűzfal bejárásához.
4. Ha a virtuális gépeknek **tartományhoz** kell csatlakozniuk, állapítsa meg, hogy csatlakoznak-e felhőalapú tartományhoz (például HRE) vagy egy helyszíni tartományhoz. A helyszíni környezetben határozza meg, hogy a virtuális gépek milyen szervezeti egységben (OU-n) vannak összekapcsolva az Active Directoryban. Továbbá győződjön meg arról, hogy a felhasználók hozzáférhetnek a csatlakozáshoz (vagy hozzon létre egy szolgáltatásfiókot, amely képes a számítógép-rekordok létrehozására a tartományban)

## <a name="milestone-2-deploy-the-pilot-lab"></a>2. mérföldkő: a kísérleti labor üzembe helyezése
A hálózati topológia bevezetését követően az első/kísérleti labor a következő lépésekkel hozható létre:

1. Hozzon létre egy kezdeti DevTest Labs-környezetet.
2. Határozza meg az engedélyezett virtuálisgép-rendszerképeket és-méreteket a laborban való használathoz. Döntse el, hogy az egyéni lemezképeket fel lehet-e tölteni az Azure-ba a DevTest Labs szolgáltatással való használatra.
3. Biztonságos hozzáférés a laborhoz kezdeti Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) létrehozásával a laborhoz (labor owners and labor Users). Javasoljuk, hogy a DevTest Labs szolgáltatással rendelkező, szinkronizált Active Directory-fiókokat használjon Azure Active Directory.
4. Konfigurálja a DevTest Labs szolgáltatást olyan házirendek használatára, mint az ütemtervek, a Cost Management, a igényelhető virtuális gépek, az egyéni lemezképek vagy a képletek.
5. Hozzon létre egy online tárházat, például az Azure Repos/git programot.
6. Döntse el, hogy a nyilvános vagy privát adattárakat vagy a kettő kombinációját használja-e. JSON-sablonok rendszerezése és hosszú távú fenntartása.
7. Ha szükséges, hozzon létre egyéni összetevőket. Ez a lépés nem kötelező. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>3. mérföldkő: dokumentáció, támogatás, megismerés és fejlesztés
A kezdeti kísérleti csapatok részletesebb támogatást igényelhetnek az első lépésekhez. Tapasztalataikat felhasználva biztosíthatja a megfelelő dokumentációt és támogatást a Azure DevTest Labs további bevezetéséhez.

1. A kísérleti csapatok bevezetése az új DevTest Labs-erőforrásokra (bemutatók, dokumentáció)
2. A kísérleti csapatok tapasztalatai alapján tervezze meg és adja meg a szükséges dokumentációt
3. Formalizálása folyamat új csapatok bevezetéséhez (laborok létrehozása és konfigurálása, hozzáférés biztosítása stb.)
4. A kezdeti felvétel alapján ellenőrizze, hogy az IP-címtartomány eredeti előrejelzése továbbra is ésszerű és pontos
5. Győződjön meg arról, hogy a megfelelő megfelelőségi és biztonsági felülvizsgálatok befejeződtek

## <a name="next-steps"></a>Következő lépések
Tekintse meg a sorozat következő cikkét: [Azure DevTest Labs infrastruktúra irányítása](devtest-lab-guidance-governance-resources.md)
