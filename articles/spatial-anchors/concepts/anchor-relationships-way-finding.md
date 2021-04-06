---
title: Horgonyzási kapcsolatok és útkeresés
description: Ismerje meg a horgony kapcsolatai mögötti fogalmi modellt. Megtudhatja, hogyan csatlakoztathatók a horgonyok egy adott térben, és hogyan használhatók a közeli API-k a keresési forgatókönyvek teljesítéséhez.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: ce79fe88b33659241a226af7bbb7a966ede41abb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97696167"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Kapcsolatok és a kitalált módszer – keresés az Azure térbeli Horgonyokban

A horgony kapcsolatainak használatával létrehozhat csatlakoztatott horgonyokat egy térben, majd a következőhöz hasonló kérdéseket tehet fel:

* Vannak a közelben horgonyok?
* Meddig vannak?

## <a name="examples"></a>Példák

A következőhöz hasonló esetekben használhat csatlakoztatott horgonyokat:

* A feldolgozónak olyan feladatot kell végrehajtania, amely egy ipari gyár különböző helyeinek meglátogatását igényli. A gyár térbeli horgonyokkal rendelkezik az egyes helyeken. A HoloLens vagy a Mobile App segítségével az egyik helyről a következőre irányíthatja a feldolgozót. Az alkalmazás először kéri a közeli térbeli horgonyokat, majd a következő helyre irányítja a feldolgozót. Az alkalmazás vizuálisan mutatja az általános irányt és a következő hely távolságát.

* A Múzeum nyilvános kijelzőn hozza létre a térbeli horgonyokat. Ezek a horgonyok együttesen egy órás bemutatót alkotnak a Múzeum alapvető nyilvános megjelenítéséhez. Nyilvános kijelzőn a látogatók megnyitják a Múzeum vegyes valóság alkalmazását a mobileszközön. Ezután rámutatnak a telefonos kamerára, hogy megtekintsék az általános irányt és a távolságot a bemutató többi nyilvános megjelenítésének. Ha a felhasználó egy nyilvános kijelző felé mutat, az alkalmazás frissíti az általános irányt és a távolságot, hogy segítse a felhasználót.

## <a name="set-up-way-finding"></a>Keresési mód beállítása

Egy olyan alkalmazás, amely a vonalak közötti irányt és a horgonyok közötti távolságot használja az útmutatás biztosításához, a *módszert* használja. A megoldási mód eltér a lépésenkénti navigálástól. A lépésenkénti Navigálás során a felhasználók a falakon, az ajtókon és a padlón keresztül vezérlik a felhasználókat. A kereséssel a felhasználó a célhely általános irányára mutató javaslatokat kap. A terület következtetéseit és ismereteit is segítheti a felhasználónak a szerkezetnek a célhelyre való átjárásában.

A felhasználói élmény kiépítéséhez először elő kell készítenie egy helyet a élményhez, és fejlesztenie kell egy alkalmazást, amellyel a felhasználók együttműködnek. Ezek a fogalmi lépések:

1. **A terület megtervezése**: döntse el, hogy a területen belül mely helyek lesznek a megoldási tapasztalatok. Ebben az esetben a gyári felügyelő vagy a múzeumi bemutató koordinátora dönthet úgy, hogy milyen helyszíneket kíván felvenni a gyakorlati tapasztalatok megkeresésére.
2. **Horgonyok összekötése**: keresse meg a kiválasztott helyszíneket a térbeli horgonyok létrehozásához. Ezt a végfelhasználói alkalmazás rendszergazdai módjában vagy egy másik alkalmazásban teheti meg. Az egyes horgonyokat összekapcsolhatja a többivel, vagy kapcsolódhat hozzájuk. A szolgáltatás karbantartja ezeket a kapcsolatokat.
3. **A végfelhasználói élmény elindítása**: a felhasználók az alkalmazás futtatásával megkereshetik a horgonyt, amely bármelyik kiválasztott helyen lehet. A teljes kialakításnak meg kell határoznia azokat a helyszíneket, ahol a felhasználók megadhatják a felhasználói élményt.
4. **Közeli horgonyok keresése**: miután a felhasználó talál egy horgonyt, az alkalmazás kérheti a közeli horgonyokat. Ez az eljárás az eszköz és a horgonyok közötti értéket adja vissza.
5. **Útmutató a felhasználó** számára: az alkalmazás a következőket használhatja az egyes horgonyokhoz, hogy útmutatást nyújtson a felhasználó általános irányáról és távolságáról. Előfordulhat például, hogy az alkalmazásban található kamera-hírcsatorna egy ikont és egy nyilat jelenít meg az egyes lehetséges célhelyek megjelenítéséhez, ahogy az alábbi képen látható.
6. **Pontosítsa az útmutatót**: ahogy a felhasználó besétál, az alkalmazás rendszeres időközönként kiszámíthatja az eszköz és a cél horgonya közötti új problémát. Az alkalmazás továbbra is pontosítja azokat az útmutatókat, amelyek segítenek a felhasználónak megérkezni a célhelyen.

    ![Példa arra, hogy az alkalmazás hogyan jeleníti meg a módot – útmutatás keresése](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Horgonyok összekötése

A módszer megtalálásához először a kiválasztott helyekre kell helyeznie a horgonyokat. Ebben a szakaszban feltételezzük, hogy az alkalmazás rendszergazdája már befejezte ezt a munkát.

### <a name="connect-anchors-in-a-single-session"></a>Horgonyok összekötése egyetlen munkamenetben

A horgonyok összekapcsolásához:

1. Sétáljon az első helyre, és hozzon létre egy horgonyt egy CloudSpatialAnchorSession használatával.
2. A második helyre mutat. A mögöttes MR/AR platform nyomon követi a mozgást.
3. Hozzon létre B horgonyt ugyanazzal a CloudSpatialAnchorSession. Az A és A B horgony már csatlakoztatva van. A térbeli horgonyok szolgáltatás megtartja ezt a kapcsolatot.
4. Folytassa a fennmaradó horgonyok eljárását.

### <a name="connect-anchors-in-multiple-sessions"></a>Horgonyok összekötése több munkamenetben

A térbeli horgonyokat több munkamenetben is összekapcsolhatjuk. Ezzel a módszerrel egyszerre létrehozhat és összekapcsolhat néhány horgonyt, majd később létrehozhat és összekapcsolhat további horgonyokat.

Horgonyok összekötése több munkamenetben:

1. Az alkalmazás létrehoz néhány horgonyt (A és B horgonyt) egy CloudSpatialAnchorSession.
2. Az alkalmazás egy másik időpontban megkeresi az egyik ilyen horgonyt (például A horgonyt) egy új CloudSpatialAnchorSession használatával.
3. Új hely bejárása. A mögöttes vegyes valóság és a kibővített valóság platform nyomon követi a mozgást.
4. Hozzon létre egy C horgonyt ugyanazzal a CloudSpatialAnchorSession. Az A, B és C horgonyok már csatlakoztatva vannak. A térbeli horgonyok szolgáltatás megtartja ezt a kapcsolatot.

Ezt az eljárást továbbra is használhatja több horgonyra és több munkamenetre az idő múlásával.

### <a name="verify-anchor-connections"></a>Horgony kapcsolatok ellenőrzése

Az alkalmazás képes ellenőrizni, hogy a két horgony csatlakoztatva van-e egy, a közeli horgonyokra vonatkozó lekérdezés kiadásával, amelyet a on a-on kell beállítania `NearAnchorCriteria` `CloudSpatialAnchorWatcher` . Ha a lekérdezés eredménye tartalmazza a cél horgonyt, a rendszer ellenőrzi a horgony kapcsolatát. Ha a horgonyok nincsenek csatlakoztatva, az alkalmazás megpróbálhatja újra csatlakoztatni őket.

Íme néhány ok, amiért a horgonyok sikertelenek lehetnek a kapcsolódáshoz:

* A mögöttes kevert valóság vagy kibővített valóság platform a horgonyok összekapcsolásának folyamata során elvesztette a követést.
* Hálózati hiba miatt a térbeli horgonyok szolgáltatással folytatott kommunikáció során a rendszer nem tudta megőrizni a horgony kapcsolatát.

### <a name="find-sample-code"></a>Mintakód keresése

A horgonyok és a közeli lekérdezések összekapcsolását bemutató mintakód a következő témakörben található: [térbeli horgonyok minta alkalmazások](https://github.com/Azure/azure-spatial-anchors-samples).
