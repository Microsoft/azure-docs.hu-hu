---
title: A Labs használata képzésekhez – Azure Lab Services
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure DevTest Labs az Azure-beli laborok létrehozásához a betanítási forgatókönyvekhez.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f9eb3d44d470a978e676f6268cd693b283dd2703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433561"
---
# <a name="use-labs-for-trainings"></a>A Labs használata képzésekhez
Az Azure Labs-szolgáltatások lehetővé teszik a pedagógusok (tanárok, oktatók, oktatók vagy oktatási asszisztensek stb.) számára, hogy gyorsan és egyszerűen hozzon létre egy online labort az előre konfigurált oktatási környezetek kiépítéséhez a gyakornokok számára. Az egyes gyakornokok azonos és elszigetelt környezeteket tudnak használni a képzéshez. A szabályzatok alkalmazhatók annak biztosítására, hogy a képzési környezetek csak akkor legyenek elérhetők az egyes gyakornokok számára, ha szükségük van rájuk, és elegendő erőforrást tartalmaznak – például virtuális gépek – szükségesek a betanításhoz. 

![Tantermi labor](./media/classroom-labs-scenarios/classroom.png)

a laborok megfelelnek a következő követelményeknek, amelyek szükségesek a képzéshez bármely virtuális környezetben: 

- A gyakornokok gyorsan üzembe helyezhetik a képzési környezeteket
- Minden betanítási gépnek azonosnak kell lennie
- A gyakornokok nem látják a más gyakornokok által létrehozott virtuális gépeket
- Szabályozhatja a költségeket azáltal, hogy a gyakornokok nem tudnak több virtuális gépet lekérni, mint amennyit a képzéshez és a virtuális gépek leállításához, amikor nem használják őket
- Az oktatási laborok könnyedén megoszthatók az egyes gyakornokokkal
- Újra és újra újra felhasználja a betanítási labort

Ebben a cikkben megismerheti azokat a különböző Azure Lab Services funkciókat, amelyek a korábban leírt képzési követelmények teljesítéséhez használhatók, és részletesen ismertetjük azokat a lépéseket, amelyekkel labort állíthat be a képzéshez.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>A labor-fiók létrehozása Lab-fiók rendszergazdájaként
A Azure Lab Services használatának első lépéseként létre kell hoznia egy Lab-fiókot a Azure Portalban. Miután egy labor-fiók rendszergazdája létrehozta a labor-fiókot, a rendszergazda hozzáadja azokat a felhasználókat, akik a **labor létrehozói** szerepkörhöz szeretnék létrehozni a laborokat. Az oktatók olyan virtuális gépeket hoznak létre a diákoknak, akik a tanulók számára gyakorlatokat készítenek a tanítás során. Részletekért lásd: [labor-fiók létrehozása és kezelése](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-labs"></a>Tesztkörnyezetek létrehozása és kezelése
Egy oktató, aki egy Lab-fiók Lab Creator szerepkörének tagja, létrehozhat egy vagy több labort a labor-fiókban. Az összes szükséges szoftverrel létrehozhatja és konfigurálhatja a sablonhoz tartozó virtuális gépet a tanfolyamon végzett gyakorlatok végrehajtásához. A rendelkezésre álló rendszerképekből kiválaszthatja a tantermi labor létrehozásához szükséges lemezképeket, majd testreszabhatja azt a laborhoz szükséges szoftver telepítésével. Részletekért lásd: [Labs létrehozása és kezelése](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Használati beállítások és szabályzatok konfigurálása
A tesztkörnyezet létrehozója hozzáadhat vagy eltávolíthat felhasználókat a laborhoz, beolvashatja a labor-felhasználók számára küldendő regisztrációs hivatkozást, házirendeket állíthat be, például beállíthatja az egyes kvótákat felhasználónként, frissítheti a laborban elérhető virtuális gépek számát, és így tovább. Részletekért lásd: [használati beállítások és házirendek konfigurálása](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Ütemezések létrehozása és felügyelete
Az ütemtervek lehetővé teszik a tantermi laborok konfigurálását, hogy a laborban lévő virtuális gépek automatikusan elindulnak és leálljanak egy adott időpontban. Meghatározhat egy egyszeri vagy ismétlődő ütemtervet. Részletekért lásd: [a Labs-ütemtervek létrehozása és kezelése](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Sablon virtuális gép beállítása és közzététele
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Állítsa be a sablon virtuális gépet úgy, hogy az pontosan úgy legyen konfigurálva, amit a képzés résztvevőinek szeretne biztosítani. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. Ezután közzéteszi a sablont, hogy a sablonban lévő virtuális gép példányai elérhetővé tegyék a tesztkörnyezet felhasználói számára. Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval. Részletekért lásd: [sablon virtuális gépek beállítása és közzététele](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Virtuális gépek használata az osztályterem laborban
Egy tanuló vagy betanítási résztvevő regisztrálja a labort, és csatlakozik a virtuális géphez, hogy a tanfolyamon gyakorlatokat hajtson végre. Részletekért lásd: a [tantermi labor elérése](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Következő lépések
A labor-fiók létrehozásához a laborban kövesse az alábbi cikket: [oktatóanyag: labor-fiók beállítása Azure Lab Services](tutorial-setup-lab-account.md).