---
title: Azure Stack Edge Pro GPU megosztások kezelése | Microsoft Docs
description: Ismerteti, hogyan kezelheti a Azure Portal az Azure Stack Edge Pro GPU-ban lévő megosztások kezeléséhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b79cb1b195d35c1e25dd750476c0dacb296f8010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102638309"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>A Azure Portal használata az Azure Stack Edge Pro-beli megosztások kezeléséhez

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk bemutatja, hogyan kezelheti a megosztásokat a Azure Stack Edge Pro-ban. A Azure Stack Edge Pro-t a Azure Portal vagy a helyi webes felületen keresztül kezelheti. A megosztásokhoz társított Storage-fiókhoz használja a Azure Portal a megosztások hozzáadásához, törléséhez, frissítéséhez vagy a Storage-kulcs szinkronizálásához. Ez a cikk Azure Stack Edge Pro GPU-ra, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkozik.

## <a name="about-shares"></a>Információk a megosztásokról

Az adatok Azure-ba történő átviteléhez megosztásokat kell létrehoznia a Azure Stack Edge Pro-ban. Az Azure Stack Edge Pro-eszközön hozzáadott megosztások lehetnek helyi megosztások vagy megosztások, amelyek leküldik az adatforgalomot a felhőbe.

 - **Helyi megosztások**: akkor használja ezeket a megosztásokat, amikor azt szeretné, hogy az eszközön helyileg dolgozzák fel az adatfeldolgozást.
 - **Megosztások**: akkor használja ezeket a megosztásokat, ha azt szeretné, hogy az eszköz adatai automatikusan a felhőben legyenek leküldve a Storage-fiókba. Az összes Felhőbeli funkció, például a **frissítési** és **szinkronizálási tárolási kulcsok** a megosztásokra vonatkoznak.


## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre a következő lépéseket az Azure Portalon.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, majd lépjen a **Cloud Storage gateway > megosztások** pontra. Válassza a **+ megosztás hozzáadása** parancsot a parancssorban.

    ![Válassza a megosztás hozzáadása elemet.](media/azure-stack-edge-gpu-manage-shares/add-share-1.png)

2. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak.
    
    A megosztások nevei csak számokat, kisbetűket és kötőjelet tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.

3. Válasszon egy **típust** a megosztáshoz. A típus lehet **SMB** vagy **NFS**, és az SMB az alapértelmezett. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

4. Adja meg azt a **tárfiókot**, ahol a megosztás található. A rendszer a tárfiókban létrehoz egy tárolót a megosztás nevével, ha még nem létezik ilyen. Ha létezik, akkor a rendszer a meglévő tárolót használja.

5. A legördülő listából válassza ki a **Storage szolgáltatást** a blob, az oldal blob vagy a Files blokkban. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Ebben az esetben például azt szeretnénk, hogy az adat az Azure-ban blokkolja a blobokat, ezért a **blob blokkolása** lehetőséget választjuk. Az **oldal blobjának** kiválasztásakor győződjön meg arról, hogy az adatai 512 bájtra vannak igazítva. Az **oldal blobjának** használata VHD-k vagy VHDX esetén, amelyek mindig 512 bájtra vannak igazítva.

6. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre.
    - **SMB-megosztás létrehozásakor** – A **Teljes körű jogosultsággal rendelkező helyi felhasználó** mezőben válasszon az **Új létrehozása** és a **Meglévő használata** lehetőségek közül. Ha új helyi felhasználót hoz létre, adja meg a **felhasználónevet** és a **jelszót**, majd erősítse meg a jelszót. Ezzel hozzárendeli az engedélyeket a helyi felhasználóhoz. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.

        ![SMB-megosztás hozzáadása](media/azure-stack-edge-gpu-manage-shares/add-smb-share.png)

        Ha a megosztott adatokhoz bejelöli a Csak az olvasási műveletek engedélyezése lehetőséget, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.
    - **NFS-megosztás létrehozásakor** – Meg kell adnia azon **engedélyezett ügyfelek IP-címeit**, akik hozzáférhetnek a megosztáshoz.

        ![NFS-megosztás hozzáadása](media/azure-stack-edge-gpu-manage-shares/add-nfs-share.png)

7. Az Edge számítási modulok megosztásának egyszerű eléréséhez használja a helyi csatlakoztatási pontot. Válassza **a megosztás Edge-számítással** lehetőséget, hogy a megosztás automatikusan a létrehozás után legyen csatlakoztatva. Ha ez a beállítás be van jelölve, a peremhálózati modul a helyi csatlakoztatási ponttal is használhatja a számítást.

8. A megosztás létrehozásához kattintson a **Létrehozás** gombra. A rendszer megjelenít egy értesítést arról, hogy a megosztás létrehozása folyamatban van. Miután létrehozta a megosztást a megadott beállításokkal, a **megosztások** panel frissül, hogy tükrözze az új megosztást.

## <a name="add-a-local-share"></a>Helyi megosztás hozzáadása

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, majd lépjen a **Cloud Storage gateway > megosztások** pontra. Válassza a **+ megosztás hozzáadása** parancsot a parancssorban.

    ![Válassza a 2. megosztás hozzáadása lehetőséget](media/azure-stack-edge-gpu-manage-shares/add-local-share-1.png)

2. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak.
    
    A megosztások nevei csak számokat, kisbetűket és nagybetűket és kötőjeleket tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.

3. Válasszon egy **típust** a megosztáshoz. A típus lehet **SMB** vagy **NFS**, és az SMB az alapértelmezett. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a használt Azure Storage-fiók nem rendelkezik módosíthatatlansági-házirendekkel, ha Azure Stack Edge Pro vagy Data Box Gateway eszközzel használja. További információ: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](../storage/blobs/storage-blob-immutability-policies-manage.md).

4. Az Edge számítási modulok megosztásának egyszerű eléréséhez használja a helyi csatlakoztatási pontot. Válassza **a megosztás Edge-számítással** lehetőséget, hogy a peremhálózati modul a helyi csatlakoztatási ponttal tudja használni a számítást.

5. Válassza **a konfigurálás helyi megosztásként** lehetőséget. A helyi megosztásokban lévő információk helyileg maradnak az eszközön. Ezeket az adatfolyamatokat helyileg is feldolgozhatja.

6. A **minden jogosultság helyi felhasználó** mezőben válassza az **új létrehozása** lehetőséget, vagy **használja a meglévőt**.

7. Válassza a **Létrehozás** lehetőséget. 

    ![Helyi megosztás létrehozása](media/azure-stack-edge-gpu-manage-shares/add-local-share-2.png)

    Megjelenik egy értesítés arról, hogy a megosztás létrehozása folyamatban van. Miután létrehozta a megosztást a megadott beállításokkal, a **megosztások** panel frissül, hogy tükrözze az új megosztást.

    ![Frissítések megosztásának megtekintése panel](media/azure-stack-edge-gpu-manage-shares/add-local-share-3.png)
    
    Válassza ki a megosztást a megosztás peremhálózati számítási moduljainak helyi csatlakoztatási pont megtekintéséhez.

    ![Helyi megosztás részleteinek megtekintése](media/azure-stack-edge-gpu-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Megosztás csatlakoztatása

Ha létrehozott egy megosztást, mielőtt beállította a számítást a Azure Stack Edge Pro-eszközön, csatlakoztatnia kell a megosztást. A megosztás csatlakoztatásához hajtsa végre az alábbi lépéseket.


1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, majd lépjen a **Cloud Storage gateway > megosztások** pontra. A megosztások listájában válassza ki a csatlakoztatni kívánt megosztást. A **számítási** oszlophoz használt érték a kiválasztott megosztásnál **letiltott** állapotot jeleníti meg.

    ![Jelölje ki a megosztást](media/azure-stack-edge-gpu-manage-shares/mount-share-1.png)

2. Válassza a **Csatlakoztatás** lehetőséget.

    ![Csatlakoztatás kiválasztása](media/azure-stack-edge-gpu-manage-shares/mount-share-2.png)

3. Ha a rendszer megerősítést kér, válassza az **Igen** lehetőséget. Ekkor a rendszer csatlakoztatja a megosztást.

    ![Csatlakoztatás megerősítése](media/azure-stack-edge-gpu-manage-shares/mount-share-3.png)

4. A megosztás csatlakoztatása után lépjen a megosztások listájára. Láthatja, hogy a **számítási** oszlophoz használt érték azt jelzi, hogy a megosztás állapota **engedélyezett**.

    ![Csatlakoztatott megosztás](media/azure-stack-edge-gpu-manage-shares/mount-share-4.png)

5. A megosztás helyi csatlakoztatási pont megtekintéséhez válassza újra a megosztást. Az Edge számítási modul ezt a helyi csatlakoztatási pont használja a megosztáshoz.

    ![A megosztás helyi csatlakoztatási pont](media/azure-stack-edge-gpu-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Megosztás leválasztása

A megosztás leválasztásához hajtsa végre az alábbi lépéseket a Azure Portalben.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, majd lépjen a **Cloud Storage gateway > megosztások** pontra. A megosztások listájában válassza ki a leválasztani kívánt megosztást. Meg szeretné győződni arról, hogy a leválasztott megosztást egyetlen modul sem használja. Ha a megosztást egy modul használja, akkor a rendszer a megfelelő modullal kapcsolatos problémákat fogja látni.

    ![2. megosztás kiválasztása](media/azure-stack-edge-gpu-manage-shares/unmount-share-1.png)

2.  Válassza a **Leválasztás** lehetőséget.

    ![Válassza a leválasztás lehetőséget](media/azure-stack-edge-gpu-manage-shares/unmount-share-2.png)

3. Ha a rendszer megerősítést kér, válassza az **Igen** lehetőséget. Ezzel leválasztja a megosztást.

    ![Leválasztás megerősítése](media/azure-stack-edge-gpu-manage-shares/unmount-share-3.png)

4. A megosztás leválasztása után lépjen a megosztások listájára. Láthatja, hogy a számítási oszlophoz **használt** érték a megosztás állapotát **letiltottként** jeleníti meg.

    ![Megosztás leválasztva](media/azure-stack-edge-gpu-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Megosztás törlése

Megosztás törléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. A megosztások listájában jelölje ki a törölni kívánt megosztást, majd kattintson rá.

    ![3. megosztás kiválasztása](media/azure-stack-edge-gpu-manage-shares/delete-share-1.png)

2. Kattintson a **Törlés** gombra.

    ![Kattintson a Törlés gombra](media/azure-stack-edge-gpu-manage-shares/delete-share-2.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

    ![Törlés megerősítése](media/azure-stack-edge-gpu-manage-shares/delete-share-3.png)

A megosztások listájának frissítése, hogy tükrözze a törlést.

## <a name="refresh-shares"></a>Megosztások frissítése

A frissítési funkció lehetővé teszi egy megosztás tartalmának frissítését. A megosztások frissítésekor a rendszer keresést indít a legutóbbi frissítés óta a felhőbe feltöltött Azure-objektumok (például blobok és fájlok) azonosítására. A rendszer ezután letölti ezeket a további fájlokat az eszközön található megosztás tartalmának frissítéséhez.

> [!IMPORTANT]
> - A helyi megosztások nem frissíthetők.
> - Az engedélyek és hozzáférés-vezérlési listák (ACL-ek) nem őrződnek meg a frissítési művelet során. 

Megosztás frissítéséhez hajtsa végre a következő lépéseket az Azure Portalon.

1.  Az Azure Portalon lépjen a **Megosztások** területre. Jelölje ki a frissíteni kívánt megosztást, majd kattintson rá.

    ![4. megosztás kiválasztása](media/azure-stack-edge-gpu-manage-shares/refresh-share-1.png)

2.  Kattintson a **Frissítés** gombra. 

    ![Kattintson a Frissítés gombra](media/azure-stack-edge-gpu-manage-shares/refresh-share-2.png)
 
3.  Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. A rendszer ekkor egy feladatot indít a helyszíni megosztás tartalmának frissítésére.

    ![Frissítés megerősítése](media/azure-stack-edge-gpu-manage-shares/refresh-share-3.png)
 
4.   Amíg folyamatban van a frissítés, a Frissítés lehetőség kiszürkítve jelenik meg a helyi menüben. A frissítési feladat állapotát a feladatértesítésre kattintva tekintheti meg.

5.   A frissítés végrehajtásához szükséges idő az Azure-tárolón és az eszközön található fájlok számától függ. A frissítés sikeres befejezése után frissül a megosztás időbélyege. Ha a frissítés csak részben sikeres, maga a művelet akkor is sikeresnek tekintendő, és az időbélyeg frissülni fog. A frissítési naplók frissítése is megtörtént.

![Frissített időbélyeg](media/azure-stack-edge-gpu-manage-shares/refresh-share-4.png)
 
Hiba esetén a rendszer riasztást ad ki. A riasztás részletei között szerepel a hiba oka és a hibaelhárítási javaslat. A riasztás továbbá tartalmaz egy fájlra mutató hivatkozást is, amelyre rákattintva megtekintheti a teljes hibajegyzéket, azon fájlok listájával együtt, amelyek esetében a frissítési vagy törlési művelet meghiúsult.

## <a name="sync-pinned-files"></a>Rögzített fájlok szinkronizálása 

A rögzített fájlok automatikus szinkronizálásához hajtsa végre a következő lépéseket a Azure Portalban:
 
1. Válasszon ki egy meglévő Azure Storage-fiókot. 

2. A tárolók létrehozásához nyissa **meg a tárolókat, és válassza** a **+ tároló** elemet. Nevezze el ezt a tárolót *newcontainer*. A **nyilvános hozzáférési szint** beállítása tárolóra.

    ![Rögzített fájlok automatikus szinkronizálása 1](media/azure-stack-edge-gpu-manage-shares/image-1.png)

3. Válassza ki a tároló nevét, és állítsa be a következő metaadatokat:  

    - Név = "rögzített" 
    - Value = "true" 

    ![A rögzített fájlok automatikus szinkronizálása 2](media/azure-stack-edge-gpu-manage-shares/image-2.png)
 
4. Hozzon létre egy új megosztást az eszközön. Rendelje hozzá a rögzített tárolóhoz a meglévő tároló lehetőség kiválasztásával. A megosztás megjelölése csak olvashatóként. Hozzon létre egy új felhasználót, és adja meg a megosztáshoz tartozó felhasználónevet és jelszót.  

    ![Rögzített fájlok automatikus szinkronizálása 3](media/azure-stack-edge-gpu-manage-shares/image-3.png)
 
5. A Azure Portal keresse meg a létrehozott tárolót. Töltse fel a fájlt, amelyet rögzíteni szeretne a newcontainer, amely a metaadatokat rögzített értékre állítja. 

6. Válassza az **adatok frissítése** Azure Portal lehetőséget, hogy az eszköz letöltse az adott Azure Storage-tárolóra vonatkozó rögzítési szabályzatot.  

    ![A rögzített fájlok automatikus szinkronizálása 4](media/azure-stack-edge-gpu-manage-shares/image-4.png)
 
7. Nyissa meg az eszközön létrehozott új megosztást. A Storage-fiókba feltöltött fájl most már le van töltve a helyi megosztásra. 

    Az eszköz leválasztása vagy újracsatlakoztatása után a rendszer elindítja a frissítést. A frissítés csak azokat a fájlokat fogja leállítani, amelyek módosultak. 


## <a name="sync-storage-keys"></a>Tárkulcsok szinkronizálása

Ha a tárfiók kulcsai le lettek cserélve, akkor szinkronizálni kell a tárelérési kulcsokat. A szinkronizálással biztosítható, hogy az eszköz tárfiókkulcsai naprakészek legyenek.

A tárelérési kulcsok szinkronizálásához hajtsa végre a következő lépéseket az Azure Portalon.

1. Lépjen az erőforrás **Áttekintés** területére. A megosztások listájában válassza ki a szinkronizálni kívánt tárfiókhoz tartozó megosztást, majd kattintson rá.

    ![Megosztás kiválasztása a megfelelő Storage-fiókkal](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-1.png)

2. Kattintson a **Tárkulcsok szinkronizálása** elemre. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

     ![Válassza ki a Storage-kulcs szinkronizálása lehetőséget](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-2.png)

3. A szinkronizálást követően zárja be a párbeszédpanelt.

>[!NOTE]
> Ezt a műveletet csak egyszer kell elvégezni az egyes tárfiókok esetében, nem kell megismételni minden egyes, ugyanazon tárfiókhoz tartozó megosztásnál.


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [kezelheti a felhasználókat az Azure Portalon keresztül](azure-stack-edge-gpu-manage-users.md).