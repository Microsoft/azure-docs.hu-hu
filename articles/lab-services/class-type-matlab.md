---
title: Tesztkörnyezet beállítása a MATLAB tanításához Azure Lab Services használatával | Microsoft Docs
description: Megtudhatja, hogyan állíthat be egy labort a MATLAB Azure Lab Services használatával történő tanításához.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444998"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Tesztkörnyezet beállítása a MATLAB tanításához

A mátrix-laboratóriumok számára készült [MATLAB](https://www.mathworks.com/products/matlab.html)a [MathWorks](https://www.mathworks.com/)programozási platformja.  A számítási teljesítményt és vizualizációt ötvözve a matematika, a mérnöki tudományok, a fizika és a kémia terén is népszerűvé teszi a számítástechnikai eszközöket.

Ha a teljes [campuson alapuló licencet](https://www.mathworks.com/academia/tah-support-program/administrators.html)használ, tekintse meg a [MATLAB telepítési fájljainak letöltése](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) című témakör útmutatását, hogy letöltse a MATLAB telepítőjének fájljait a sablon számítógépén.  

Ebben a cikkben bemutatjuk, hogyan állíthat be egy olyan osztályt, amely MATLAB-ügyfélszoftvert használ egy licenckiszolgálóra.

## <a name="license-server"></a>Licenckiszolgáló

A tesztkörnyezet sablonjának módosítása előtt be kell állítania a kiszolgálót a [hálózati licenc-kezelő](https://www.mathworks.com/help/install/administer-network-licenses.html) szoftver futtatásához.  Ezek az utasítások csak olyan intézmények esetében alkalmazhatók, amelyek a MATLAB hálózatkezelési licencelési lehetőségét választják, ami lehetővé teszi a felhasználók számára, hogy megosszák a licenckulcs készletét.  Később is mentenie kell a licencfájl és a fájl telepítési kulcsát.  A licencfájl letöltésével kapcsolatos részletes utasításokért tekintse meg a [hálózati licenc-kezelő telepítése internetkapcsolattal](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html) című cikk első lépését.

A licencelési kiszolgáló telepítésére vonatkozó részletes utasítások a [Network License Manager telepítése internetkapcsolattal](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)című cikkben találhatók.  A hitelfelvétel engedélyezéséhez tekintse meg a [kölcsön licencét](https://www.mathworks.com/help/install/license/borrow-licenses.html) ismertető cikket.

Ha a licenckiszolgáló helyi hálózaton vagy egy Azure-beli magánhálózaton található, ne felejtse el, hogy [a virtuális hálózatot](how-to-connect-peer-virtual-network.md) a [labor-fiókjához](tutorial-setup-lab-account.md)adja.  A hálózati társítást a tesztkörnyezet létrehozása előtt kell elvégezni, hogy a tesztkörnyezet virtuális gépei hozzáférhessenek a licenckiszolgálóhoz.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

A tesztkörnyezet beállításához Azure-előfizetésre van szükség a kezdéshez.  Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services, vagy használhat meglévő fiókot is.  Új Labor-fiók létrehozásához tekintse meg a [labor-fiók beállítása című oktatóanyagot](tutorial-setup-lab-account.md).

Új Labor létrehozásához kövesse [az osztályterem Lab-oktatóanyag beállítása](tutorial-setup-classroom-lab.md)című témakört.  Alkalmazza a következő beállításokat:

| Virtuális gép mérete | Kép |
| -------------------- | ----- |
| Közepes | Windows 10 |

A MATLAB több operációs rendszeren is támogatott.  A részletekért lásd a [MATLAB rendszerkövetelményeit](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) .

> [!WARNING]
> Ne felejtse el [, hogy a](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) tesztkörnyezet létrehozása előtt a tesztkörnyezet virtuális hálózatát a virtuális hálózatra kell felvennie.

## <a name="template-machine"></a>Sablon számítógép

A sablon létrehozása után indítsa el a gépet, és kapcsolódjon hozzá a következő jelentősebb feladatok végrehajtásához.

1. Töltse le a MATLAB ügyfélszoftver telepítési fájljait.
2. Telepítse a MATLAB-t a fájl telepítési kulcsával.

A MATLAB telepítése több részből álló folyamat lesz.  Az első rész letölti a MATLAB és a telepíteni kívánt termékek fájljait.  A fájl telepítési kulcsának használatához a telepítendő termékek összes telepítési fájlját előre le kell tölteni.  A második rész telepíti a MATLAB szoftvert a sablon virtuális gépre, és aktiválja a szoftvert.  Ha a sablon virtuális gépe úgy van konfigurálva, hogy a licenckiszolgáló használatával aktiválja az aktiválást, akkor a tanuló virtuális gépek ugyanezt hajtják végre.

### <a name="download-installation-files"></a>Telepítési fájlok letöltése

A telepítési fájlok letöltéséhez, valamint a licencfájl és a fájl telepítési kulcsának beszerzéséhez licenc-rendszergazdának kell lennie.  A telepítési fájlok letöltésének lépései a következők:

1. Jelentkezzen be a fiókjába [https://www.mathworks.com](https://www.mathworks.com) .
2. Válassza **a saját fiók** lehetőséget.
3. A fiók lap **saját szoftverek** szakasza alatt kattintson a laborhoz tartozó Network License Manager-beállításhoz csatolt licencre.
4. A licenc részletei lapon kattintson a **termékek letöltése** elemre.
5. Várjon, amíg a telepítő önállóan kinyeri.
6. Indítsa el a telepítőt.  
7. A **Bejelentkezés a MathWorks-fiókba** lapon adja meg a MathWorks-fiókját.
8. A **MathWorks-licencszerződés** lapon fogadja el a kifejezést, és kattintson a **tovább** gombra.
9. Kattintson a **Speciális beállítások** legördülő menüre, és válassza a **telepítés nélkül szeretnék letölteni** lehetőséget.
10. A **célmappa kijelölése** lapon kattintson a **tovább** gombra.
11. Válassza a **Windows** lehetőséget annak a számítógépnek a platformja, amelyre a MATLAB-t telepíteni fogja.
12. A **termék kiválasztása** lapon győződjön meg róla, hogy a MATLAB lehetőség van kiválasztva a telepíteni kívánt többi MathWorks-termékkel együtt.
13. A **kiválasztás és a letöltés megerősítése** lapon kattintson a **Letöltés megkezdése** elemre.  
14. Várja meg a kiválasztott termékek letöltését.  Kattintson a **Finish** (Befejezés) gombra.

Az ISO-rendszerképet a MathWorks webhelyről is letöltheti.

1. Jelentkezzen be a fiókjába [https://www.mathworks.com](https://www.mathworks.com) .
2. Ugrás a következőre: [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads) .
3. Válassza ki a telepíteni kívánt MATLAB-verziót.
4. Kattintson a "Letöltés {Version}. ISO-rendszerkép" hivatkozásra a kapcsolódó hivatkozások alatt, ahol a (z) {Version} hasonló, mint a R2020a.
5. Kattintson a kék **letöltési kiadás** hivatkozásra a Windows rendszerhez.

### <a name="run-installer"></a>Telepítő futtatása

A fájlok letöltése után a második lépés a telepítő futtatása. A lépés végrehajtásához licenc-rendszergazdának kell lennie.  Csak a licencelési rendszergazdák telepíthetik a MATLAB-t a fájl telepítési kulcsával.

1. Ellenőrizze a letöltött licencfájl listáját, és ellenőrizze, hogy a FÁJLKISZOLGÁLÓ helyesen listázza-e a licenckiszolgálót.  A licencfájl formázásával kapcsolatos információkért lásd: a [hálózati licenc frissítése](https://www.mathworks.com/help/install/ug/network-license-files.html), a [licencek hitelfelvétele](https://www.mathworks.com/help/install/license/borrow-licenses.html)és a [gazdagép-azonosító cikkek keresése](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) .
2. Indítsa el a MATLAB telepítőjét.
3. A **Bejelentkezés a MathWorks-fiókba** lapon adja meg a MathWorks-fiókját.
4. A **MathWorks-licencszerződés** lapon fogadja el a kifejezést, és kattintson a **tovább** gombra.
5. Kattintson a **Speciális beállítások** legördülő menüre, és válassza a **fájl telepítési kulcsa** lehetőséget.
6. A **telepítés a telepítőfájl használatával** lapon adja meg a licenckiszolgáló fájljának telepítési kulcsát.   Kattintson a **Tovább** gombra.
7. A **licencfájl kiválasztása** lapon navigáljon a korábban a telepítési fájlok letöltésekor mentett licencfájl eléréséhez.
8. A **célmappa kiválasztása** lapon kattintson a **tovább** gombra.
9. A **termékek kiválasztása** lapon kattintson a **tovább** gombra.
10. A **beállítások kiválasztása** lapon kattintson a **tovább** gombra.
11. A **kijelölések és telepítések megerősítése** lapon kattintson a **telepítés megkezdése** elemre.
12. A **telepítés kész** lapon ellenőrizze, hogy be van-e JELÖLVE a **MATLAB aktiválása** jelölőnégyzet.  Kattintson a **Finish** (Befejezés) gombra.

## <a name="cost-estimate"></a>Költségbecslés

Az osztályra vonatkozó lehetséges költségbecslés.  Ez a becslés nem tartalmazza a licenckiszolgáló futtatásának költségeit.  25 tanulós osztályt fogunk használni.  20 órányi ütemezett idő van.  Emellett minden tanuló 10 órás kvótát kap a házi feladat vagy az ütemezett osztályon kívüli hozzárendelések számára.  A kiválasztott virtuálisgép-méret közepes, ami 55 labor egység.

Az alábbi példa egy lehetséges költségbecslés erre az osztályra vonatkozóan:

25 tanuló \* (20 ütemezett óra + 10 kvóta óra) \* 55 labor egység \*  0,01 USD/óra = 412,50 USD

>[!IMPORTANT]
> A költségbecslés csak példaként szolgál.  A díjszabással kapcsolatos aktuális információk: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása, kezelése és közzététele](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)
