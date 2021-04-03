---
title: Jogcím-képességek használata a Azure DevTest Labsban | Microsoft Docs
description: Ismerkedjen meg a Azure DevTest Labs jogcímek/jogcímek funkcióinak használatára vonatkozó különböző forgatókönyvekkel
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 73ed3c0b94a66f5d17b5c8e2561c65bb48579aa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476529"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Jogcím-képességek használata Azure DevTest Labs
A Azure DevTest Labs szolgáltatás javítja a fejlesztők és tesztelők hatékonyságát és hatékonyságát. Ez a cikk a virtuális gépek Azure DevTest Labs-ban való igénylésének vagy visszaigénylésének képességét ismerteti. Emellett felsorolja a funkció által a felhasználói élmény javításához szükséges különféle módszereket is. A funkció használatának megkezdése előtt tekintse át, **hogy mi a helyzet, és** hogyan működik.

## <a name="claimable-machines"></a>Igényelhető gépek
A igényelhető gép egy olyan virtuális gép (VM), amely tulajdonos nélkül, laborban lett létrehozva. Ha a gépet igényelte, a felhasználó teljes körű lehetőségekkel rendelkezik az adott virtuális géphez. Ha egy felhasználó egy gépet követel meg, néhány módosítást hajt végre. A virtuális gépet a rendszer áthelyezi a **lekérhető virtuális gépek** listájából a Azure Portal **virtuális gépek** listájára. 

A felhasználó csatlakozhat a virtuális géphez, testreszabhatja az összetevőket, újraindíthatja, leállíthatja vagy lekérheti a gépet. A virtuális gép igénylése több módon is elvégezhető:

- Hozzon létre egy gépet, és ne igényelje azt úgy, hogy az átkerüljön a igényelhető készletbe. 
- Hozzon létre egy virtuális gépet, és helyezze a megosztott készletbe [Speciális beállítások](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/)használatával.

A jogcímek vagy az ENSZ-jogcím funkciói nem használhatók hatékonyan. Az első eset több előrelátást és tervezést igényel, hogy megfelelően legyen kialakítva és végrehajtva. A második pedig a helyzet. Az alábbiakban néhány példát láthat a különböző esetekre.

## <a name="designed-use-of-claimable-machines"></a>A igényelhető gépek tervezett használata

- **Szoftverfejlesztés/tesztelés:** Lehetővé teheti a fejlesztők és a tesztelők számára, hogy a konfigurált gépek készen állnak, és nem igényelt állapotban legyenek. A különböző konfigurációkkal, a szükséges eszközökkel és a legújabb kóddal rendelkező virtuális gépek készlete lehetővé teszi, hogy a felhasználók egy virtuális gépet igényeljenek, és a munka megkezdése nélkül kelljen időt fordítani a gép beállítására. A virtuális gépek igénybe állítása előtt a gépek üzembe helyezése megtörténik, de a leállításuk minimalizálja a kevésbé gyakran használt gépek költségeit. Ha a virtuális gépekre szükség van, a felhasználó egyszerűen bekéri a virtuális gépet, amely elindítja a számítógépet. Az unjogcím beállítás nem annyira hasznos ebben az esetben, mert az új virtuális gép létrehozása gyakran egyszerűbb és olcsóbb.
- **Osztályterem/Labs:** A virtuális gépeket előre konfigurálták egy osztályhoz vagy egy laborhoz, hogy a tanulók azonnal csatlakozhassanak a géphez a Azure Portal használatával.  Ha egy tanuló egy virtuális gépet igényel, a labor gondoskodik arról, hogy senki sem igényelhet ugyanazt a gépet. A folyamat automatizálása biztosítja, hogy a megadott környezettel rendelkező gépek száma elérhető legyen. Ha a tanulók nem jelennek meg, vagy későn futnak, a nem igényelt gépeket csak akkor tarthatják elérhetővé, ha a munkamenet nem haladja meg a minimális költségeket. Ebben az esetben az unjogcím beállítás nem érvényes, mert a virtuális gép ismeretlen állapotban van, amikor az előző felhasználó elkészült.
- **Bemutatók:** Használjon olyan gépeket a bemutatóhoz, ahol a laborban lévő gépek meghatározott környezetekben vannak beállítva. Ez a funkció akkor hasznos, ha több személy is ad egy bemutatót egyszerre vagy véletlenszerű időpontokban, például egy konferencián. Az unjogcím beállítás hasznos lehet ebben az esetben, mivel a bemutató nem változtatja meg a gép állapotát, így a felhasználók visszatérhetnek a virtuális géphez a következő bemutatóhoz tartozó, igényelhető készletbe. Ha a nem igényelt gépet kiosztották, és a minimális költségek merülnek fel, a virtuális gépek hosszabb időszakokra is maradhatnak a laborban.
- **Ideiglenes/szerződéses feldolgozók:** Számítógép használatának engedélyezése a felhasználók számára. Ha elhagyja őket, az adatvesztés nélkül visszaadják a virtuális gépet a igényelhető készletbe. Ha a virtuális gép nem igényelt, egy másik felhasználó igénybe veheti a virtuális gépet, és további információkat is megtekintheti a gépen.
- **Általánosságban:** A virtuális gépek egy adott időpontra való automatikus konfigurálása és üzembe helyezése egyetlen forrásból is lehetséges, és számos különböző helyzetben hasznos lehet. Több különböző helyzet áll fenn, ha a jogcím/visszavonási funkció segít a felhasználóknak hatékonyabbá tételében azáltal, hogy automatizált eljárással hozza létre a virtuális gépeket egy meghatározott konfigurációval rendelkező nem jogcímes állapotba. A konfiguráció (k) különböző operációs rendszereket, nyelveket, lemezeket vagy [más szoftvereket (összetevőket)](devtest-lab-artifact-author.md) tartalmazhatnak az igényeitől függően. Egy virtuális gép a laborból való igénylésének lehetősége lehetővé teszi, hogy a labor felhasználó megfelelő módon konfigurált rendszer beszerzése nélkül, a gép konfigurálásához szükséges idő vagy erőfeszítés elköltésével. A labor Manager a virtuális gépek állítólagos állapotát felhasználva növelheti a generált számítógépek számát és a gépek karbantartását, és meghatározhatja a konfigurációk prioritását. A [rendszerkép-előállító](image-factory-create.md) jó példa arra, hogyan hozhat létre virtuális gépeket és lemezképeket több Labs számára. A parancsfájlok módosíthatók úgy, hogy a következő helyzetek bármelyikét végrehajtják a megfelelő módosításokkal, vagy az egyéni rendszer létrehozására szolgáló hivatkozásként használják őket.

## <a name="situational-use-of-claimable-machines"></a>A igényelhető gépek szituációs használata

- A jogcím/az ENSZ-jogcím funkció használata, amely lehetővé teszi a felhasználók számára, hogy az egyikről a másikra irányítsák át a gépeket, és nem kell pontosan tudnia, hogy ki fogja felvenni a gépet.
- Olyan forgatókönyvek fejlesztése, tesztelése és hibakeresése, amelyekben egy adott gép konfigurációja képes reprodukálni egy hibát, a gép nem engedélyezhető, így egy másik fejlesztő igényelhetheti a gépet, és folytathatja a munkát. Ez a funkció különösen hasznos, mivel egyre több ember dolgozik távolról a világ különböző területein. 
- A csapattagok egyetlen környezettel működhetnek. Létrehozhat például egy olyan összetett környezetet, amely nem automatizálható, vagy olyan erőforrásokat hozhat létre, amelyek csak egyetlen bemeneti, például rendszerképek módosításait kezelhetik. A múltban ezt a problémát egy dedikált gép üzembe helyezésével kezeltük. A lekérhető funkció a manuális folyamat továbbfejlesztése a beépített felhasználói hozzáférés-vezérléssel és a vizualizációk azonosításával, ha elérhető. Ha nem igényelt, a virtuális gép kiépítve csökkenti a költségeket.
- Rendelkeznie kell egy virtuális géphez csatolt adatlemezzel. Az 1 TB-ig terjedő összes lemez lehetővé teszi, hogy a nagy mennyiségű adat átadása az adat másolása vagy duplikálása nélkül történjen. A virtuális gép kezdetben egy csatlakoztatott lemezzel lett létrehozva, amely nagy mennyiségű adattal rendelkezik.  A felhasználók ezután igényelhetik a gépet, és hozzáférhetnek az adatkezeléshez. Ha elkészült, a virtuális gép letiltásával más felhasználókat is engedélyezhet a gépre.

Vannak bizonyos kikötések a felhasználható gépek használatára, ami általában a gép elérését igényli. Ha a számítógép tartományhoz csatlakozik, akkor a gépet igénylő felhasználónak már hozzáférésre van szüksége, általában pedig hozzáférést biztosít egy olyan csoporthoz, amely magában foglalja a laborban lévő összes felhasználót a virtuális gép létrehozásakor. Ha a gép nincs tartományhoz csatlakoztatva, a **virtuális gép jelszó alaphelyzetbe állítása** lehetőséget kell futtatni a nyilvános tárházban, hogy a felhasználót rendszergazdaként adja hozzá.  Az összetevők a gép elindítása vagy azt követően is alkalmazhatók.

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikket: [igényelhető virtuális gépek létrehozása és kezelése Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
