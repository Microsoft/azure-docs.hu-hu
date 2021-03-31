---
title: Az Azure Lab Services bemutatása | Microsoft Docs
description: Megismerheti, hogyan könnyítheti meg a Lab Services a tesztkörnyezetek létrehozását, felügyeletét és védelmét a fejlesztők, tesztelők, oktatók, diákok és mások által használható virtuális gépekkel.
ms.topic: overview
ms.date: 09/16/2020
ms.openlocfilehash: ad17ebb3a803a15d1ac9ef8cb71cf8ca7976243b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333936"
---
# <a name="an-introduction-to-azure-lab-services"></a>Az Azure Lab Services bemutatása
**Azure Lab Services** lehetővé teszi, hogy olyan laborokat hozzon létre, amelyek infrastruktúráját az Azure felügyeli. Jelenleg a tanterem Lab az egyetlen, Azure Lab Services által támogatott felügyelt tesztkörnyezet. A szolgáltatás maga kezeli a felügyelt tesztkörnyezet összes infrastruktúrájának kezelését, a virtuális gépek felgyorsítását a hibák kezelésére és az infrastruktúra skálázására. Azt követően, hogy a rendszergazda létrehoz egy labor-fiókot Azure Lab Servicesban, az oktató gyorsan beállíthatja az osztályhoz tartozó labort, megadhatja az osztályban végzett gyakorlatokhoz szükséges virtuális gépek számát és típusát, valamint felhasználókat adhat hozzá a osztályhoz. Miután a felhasználó regisztrált a osztályba, a felhasználó elérheti a virtuális gépet, hogy az osztályhoz tartozó gyakorlatokat végezzen.  

## <a name="key-capabilities"></a>Főbb képességek
Az Azure Lab Services az alábbi fő képességeket/funkciókat támogatja:

- **A tesztkörnyezetek gyors és rugalmas beállítása**. A tesztkörnyezetek tulajdonosai az Azure Lab Services használatával gyorsan állíthatnak be az igényeinek megfelelő tesztkörnyezetet. A szolgáltatás lehetőséget biztosít az összes Azure-infrastruktúra kezeléséhez a felügyelt labor típusok esetében. A szolgáltatás biztosítja az infrastruktúra beépített méretezését és rugalmasságát azon tesztkörnyezetek esetén, amelyeket a tulajdonos helyett kezel.
- **Egyszerű használat a tesztkörnyezetek felhasználói számára**. A labor felhasználói regisztrálhatnak egy regisztrációs kóddal rendelkező laborba, és bármikor hozzáférhetnek a laborhoz a labor erőforrásainak használatához. 
- **Költségoptimalizálás és elemzés**. A tesztkörnyezet tulajdonosa ütemezéseket állíthat be a tesztkörnyezetben a virtuális gépek automatikus leállításához és elindításához. A labor tulajdonosa beállíthat egy ütemtervet az időpontok megadásához, amikor a labor virtuális gépei elérhetők a felhasználók számára, és felhasználónként vagy laborban használati szabályzatokat állíthatnak be a költségeket optimalizálva. 

Ha azt szeretné, hogy a laborban milyen adatokat kell megadnia, és lehetővé kell tennie a szolgáltatás számára a laborhoz szükséges infrastruktúra beállítását és kezelését, válasszon az egyik **felügyelt labor típus** közül. Jelenleg a **tanterem Lab** az egyetlen felügyelt Lab-típus, amelyet a Azure Lab Services hozhat létre.

Az alábbi szakaszokban részletesen is tárgyaljuk ezeket a tesztkörnyezeteket. 

## <a name="managed-lab-types"></a>Felügyelt tesztkörnyezet-típusok
Az Azure Lab Services segítségével Azure által kezelt infrastruktúrával rendelkező tesztkörnyezeteket hozhat létre. Ez a cikk felügyelt labor típusokként hivatkozik rájuk. A felügyelt labor típusok különböző típusú laborokat kínálnak, amelyek megfelelnek az adott igényeknek. Jelenleg az egyetlen támogatott, felügyelt Lab-típus a **tanterem Lab**. 

A felügyelt labor-típusok lehetővé teszik, hogy azonnal, minimális telepítéssel kezdjen el. Maga a szolgáltatás kezeli a labor infrastruktúrájának összes felügyeletét, a virtuális gépeknek a hibák kezelésére és az infrastruktúra skálázására irányuló felpörgetésével. A felügyelt laborok, például a tantermi laborok létrehozásához először létre kell hoznia egy Lab-fiókot a szervezet számára. A tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének a felügyeletéhez. 

Ha Azure-erőforrásokat hoz létre és használ ezekben a felügyelt labor-típusokban, a szolgáltatás az erőforrásokat belső Microsoft-előfizetésekben hozza létre és kezeli. nem pedig az Ön Azure-előfizetésében. A szolgáltatás belső Microsoft-előfizetésekben követi nyomon ezen erőforrások használatát. A használat számlázása a tesztkörnyezetfiókot tartalmazó Azure-előfizetésben történik.   

Íme néhány példa a **felügyelt labor-típusok használati eseteire**: 

- A pontosan a tanórához szükséges dolgokkal konfigurált virtuális gépekből álló tesztkörnyezet biztosítása a diákok számára. Korlátozott mennyiségű idő biztosítása minden diák számára a virtuális gépek használatához a házi feladatok vagy személyes projektek elvégzéséhez.
- Nagy teljesítményű számítások végrehajtására képes virtuális gépek készletének beállítása nagy számítási vagy grafikai igényű kutatáshoz. A virtuális gépek futtatása igény szerint, majd a gépek törlése, miután végzett. 
- Az iskola fizikai számítógéptermének áthelyezése a felhőbe. A virtuális gépek számának automatikus méretezése a tesztkörnyezetben beállított maximális használatnak és költséghatárnak megfelelően.  
- Virtuális gépekből álló tesztkörnyezet gyors kiépítése Hackathon-verseny megtartásához. A tesztkörnyezet törlése egyetlen kattintással, miután végzett. 

## <a name="next-steps"></a>Következő lépések
A következő oktatóanyagok részletes útmutatást biztosítanak a labor-fiókok létrehozásához, valamint egy osztálytermi labor létrehozásához.

- [Oktatóanyag: labor-fiók beállítása](tutorial-setup-lab-account.md)
- [Oktatóanyag: tantermi labor létrehozása](tutorial-setup-classroom-lab.md)
