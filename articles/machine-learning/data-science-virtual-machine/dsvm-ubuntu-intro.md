---
title: 'Gyors útmutató: Ubuntu-Data Science Virtual Machine létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: A Linux (Ubuntu) Data Science Virtual Machine konfigurálása és létrehozása az elemzéshez és a gépi tanuláshoz.
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: quickstart
ms.date: 03/10/2020
ms.openlocfilehash: b25d9846f3bb678c182fa22d824f7b4171d2d803
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515631"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Gyors útmutató: a Linux Data Science Virtual Machine beállítása (Ubuntu)

Az Ubuntu 18,04 Data Science Virtual Machine használatba veheti a szolgáltatást.

## <a name="prerequisites"></a>Előfeltételek

Ubuntu 18,04 Data Science Virtual Machine létrehozásához Azure-előfizetéssel kell rendelkeznie. [Próbálja ki ingyenesen az Azure](https://azure.com/free)-t.

>[!NOTE]
>Az ingyenes Azure-fiókok nem támogatják a GPU-t használó virtuális gépekhez tartozó SKU-ket.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>A Linux-Data Science Virtual Machine létrehozása

Az alábbi lépéseket követve hozhatja létre a Data Science Virtual Machine Ubuntu 18,04-példányát:

1. Nyissa meg az [Azure Portal](https://portal.azure.com). Előfordulhat, hogy a rendszer arra kéri, hogy jelentkezzen be az Azure-fiókjába, ha még nincs bejelentkezve.
1. Keresse meg a virtuális gép listáját az "adatelemzési virtuális gép" beírásával, és válassza a "Data Science Virtual Machine-Ubuntu 18,04" lehetőséget.

1. A következő ablakban válassza a **Létrehozás** lehetőséget.

1. A rendszer átirányítja a virtuális gép létrehozása panelre.
   
1. Adja meg a következő adatokat a varázsló egyes lépéseinek konfigurálásához:

    1. **Alapismeretek**:
    
       * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki azt a számítógépet, amelyet a gép létre fog hozni és számláz. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
       * **Erőforráscsoport**: hozzon létre egy új csoportot, vagy használjon egy meglévőt.
       * **Virtuális gép neve**: adja meg a virtuális gép nevét. Ezt a nevet fogja használni a Azure Portal.
       * **Régió**: válassza ki a legmegfelelőbb adatközpontot. A leggyorsabb hálózati hozzáféréshez ez az adatközpont, amely a legtöbb adattal rendelkezik, vagy a legközelebb áll a fizikai helyhez. További információ az [Azure-régiókról](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Rendszerkép**: hagyja meg az alapértelmezett értéket.
       * **Méret**: Ez a beállítás az általános számítási feladatokhoz megfelelő mérettel automatikusan feltölthető. További információ a [LINUXOS virtuális gépek méretéről az Azure-ban](../../virtual-machines/sizes.md).
       * **Hitelesítés típusa**: a gyorsabb telepítéshez válassza a "jelszó" lehetőséget. 
         
         > [!NOTE]
         > Ha a JupyterHub-t szeretné használni, ügyeljen arra, hogy válassza a "jelszó" lehetőséget, mivel a JupyterHub *nem* az SSH nyilvános kulcsok használatára van konfigurálva.

       * **Felhasználónév**: adja meg a rendszergazda felhasználónevét. Ezt a felhasználónevet fogja használni a virtuális gépre való bejelentkezéshez. A felhasználónévnek nem kell megegyeznie az Azure-felhasználónévvel. Ne *használjon* nagybetűs betűket.
         
         > [!IMPORTANT]
         > Ha nagybetűs betűket használ a felhasználónévben, a JupyterHub nem fog működni, és 500 belső kiszolgálóhiba lép fel.

       * **Password (jelszó**): adja meg a virtuális gépre való bejelentkezéshez használni kívánt jelszót.    
    
   1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   1. **Felülvizsgálat + létrehozás**
      * Győződjön meg arról, hogy a megadott összes adat helyes. 
      * Válassza a **Létrehozás** lehetőséget.
    
    A kiépítés körülbelül 5 percet vesz igénybe. Az állapot megjelenik a Azure Portalban.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Az Ubuntu Data Science Virtual Machine elérése

Az Ubuntu DSVM háromféle módon érheti el:

  * SSH terminál-munkamenetekhez
  * X2Go grafikus munkamenetekhez
  * JupyterHub és JupyterLab Jupyter-notebookokhoz

### <a name="ssh"></a>SSH

Ha a virtuális gépet SSH-hitelesítéssel konfigurálta, a 3. lépés **alapjai** című részében létrehozott fiók hitelesítő adataival jelentkezhet be a szöveges rendszerhéj felületéhez. Windows rendszeren egy SSH-ügyfél eszközt (például a [Putty](https://www.putty.org)-t) tölthet le. Ha a grafikus asztali számítógép (X rendszer) használatát részesíti előnyben, használhat X11-továbbítást a PuTTY-on.

> [!NOTE]
> A X2Go-ügyfél jobban teljesített, mint az X11 továbbítása a tesztelés során. Azt javasoljuk, hogy a X2Go-ügyfelet egy grafikus asztali felületen használja.

### <a name="x2go"></a>X2Go

A Linux rendszerű virtuális gép már ki van építve a X2Go-kiszolgálóval, és készen áll az ügyfélkapcsolatok fogadására. A linuxos virtuális gép grafikus asztalához az ügyfélen végrehajtott alábbi eljárással csatlakozhat:

1. Töltse le és telepítse az X2Go-ügyfelet az ügyfélplatformjára az [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) webhelyéről.
1. Jegyezze fel a virtuális gép nyilvános IP-címét, amelyet a Azure Portal a létrehozott virtuális gép megnyitásával talál.

   ![Ubuntu gép IP-címe](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Futtassa az X2Go-ügyfelet. Ha az "új munkamenet" ablak nem jelenik meg automatikusan, ugorjon a munkamenet-> új munkamenet elemre.

1. A megjelenő konfigurációs ablakban adja meg a következő konfiguráció-paramétereket:
   * **Munkamenet lap**:
     * **Állomásnév**: Adja meg a virtuális gép IP-címét, amelyet korábban feljegyzett.
     * **Bejelentkezési név**: Adja meg a linuxos virtuális gépen érvényes felhasználónevét.
     * **SSH-port**: Hagyja meg az alapértelmezett 22 értéket.
     * **Munkamenet típusa**: Állítsa át az **XFCE** értékre. A linuxos virtuális gép jelenleg csak az XFCE-asztalt támogatja.
   * **Multimédia lap**: Kikapcsolhatja a hangtámogatást és az ügyfélről indított nyomtatást, ha nincs rájuk szüksége.
   * **Megosztott mappák**: ezen a lapon adhatja hozzá a virtuális gépen csatlakoztatni kívánt ügyfélszámítógép-könyvtárat. 

   ![X2Go-konfiguráció](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Kattintson az **OK** gombra.
1. A X2Go ablak jobb oldali paneljén kattintson a jelölőnégyzetre a virtuális gép bejelentkezési képernyőjének megjelenítéséhez.
1. Adja meg a virtuális gépen érvényes jelszavát.
1. Kattintson az **OK** gombra.
1. A csatlakozás befejezéséhez esetleg engedélyt kell adnia az X2Go-nak a tűzfal megkerülésére.
1. Ekkor megjelenik az Ubuntu-DSVM grafikus felülete. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub és JupyterLab

Az Ubuntu DSVM egy többfelhasználós Jupyter-kiszolgálót futtat [JupyterHub](https://github.com/jupyterhub/jupyterhub). A kapcsolódáshoz hajtsa végre a következő lépéseket:

   1. Jegyezze fel a virtuális gép nyilvános IP-címét úgy, hogy megkeresi és kijelöli a virtuális gépet a Azure Portalban.
      ![Ubuntu gép IP-címe](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. A helyi gépen nyisson meg egy webböngészőt, és navigáljon a https: \/ /Your-VM-IP: 8000 fájlhoz, és cserélje le a "Your-VM-IP" kifejezést a korábban jegyzett IP-címre.
   1. A böngésző valószínűleg nem fogja tudni megnyitni a lapot közvetlenül, és közli, hogy hiba történt a tanúsítványban. A DSVM egy önaláírt tanúsítványon keresztül biztosítja a biztonságot. A legtöbb böngésző lehetővé teszi, hogy a figyelmeztetést követően kattintson ide. Számos böngésző továbbra is biztosít valamilyen vizuális figyelmeztetést a tanúsítványról a webes munkamenet során.

      >[!NOTE]
      > Ha a böngészőben megjelenik a `ERR_EMPTY_RESPONSE` hibaüzenet, győződjön meg arról, hogy a *https* protokoll használatával explicit módon fér hozzá a géphez, nem pedig *http* vagy csak a webcím használatával. Ha a címnek a címben való megadása nélkül írja be a webcímet `https://` , a legtöbb böngésző alapértelmezett értékre kerül `http` , és ezt a hibaüzenetet fogja látni.

   1. Adja meg a virtuális gép létrehozásához használt felhasználónevet és jelszót, majd jelentkezzen be. 

      ![Adja meg a Jupyter-bejelentkezést](./media/dsvm-ubuntu-intro/jupyter-login.png)

      >[!NOTE]
      > Ha ebben a fázisban 500 hibát kap, akkor valószínű, hogy a felhasználónévben tőkésített leveleket használt. Ez az Jupyter hub és az általa használt PAMAuthenticator közötti ismert interakció. Ha "nem lehet elérni ezt a lapot" hibaüzenet jelenik meg, akkor valószínű, hogy módosítani kell a hálózati biztonsági csoport engedélyeit. A Azure Portal keresse meg a hálózati biztonsági csoport erőforrását az erőforráscsoport területen. A JupyterHub nyilvános internetről való eléréséhez meg kell nyitni a 8000-es portot. (A rendszerkép azt mutatja, hogy ez a virtuális gép az igény szerinti hozzáféréshez van konfigurálva, de kifejezetten ajánlott. Lásd: [a felügyeleti portok biztonságossá tétele az](../../security-center/security-center-just-in-time.md)igény szerinti hozzáféréssel.) ![A hálózati biztonsági csoport konfigurációja](./media/dsvm-ubuntu-intro/nsg-permissions.png)

   1. Böngésszen a rendelkezésre álló számos jegyzetfüzetben.

JupyterLab, a Jupyter-jegyzetfüzetek és-JupyterHub következő generációja is elérhető. A hozzáféréshez jelentkezzen be a JupyterHub, majd keresse meg a https: \/ /Your-VM-IP: 8000/User/your-username/Lab URL-címet, és cserélje le a "your-username" kifejezést a virtuális gép konfigurálásakor kiválasztott felhasználónévre. Előfordulhat, hogy a rendszer eredetileg nem fér hozzá a helyhez a tanúsítvány hibája miatt.

A JupyterLab az alapértelmezett jegyzetfüzet-kiszolgálóként is beállíthatja, ha hozzáadja ezt a sort a következőhöz `/etc/jupyterhub/jupyterhub_config.py` :

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Következő lépések

Így folytathatja a tanulást és a kutatást:

* A [Linux rendszerhez készült Data Science Virtual Machine adatelemzés](linux-dsvm-walkthrough.md) azt mutatja be, hogyan végezhető el számos általános adatelemzési feladat az itt kiépített Linux-DSVM. 
* A jelen cikkben ismertetett eszközök kipróbálásával megismerheti a DSVM különböző adatelemzési eszközeit. A `dsvm-more-info` virtuális gépen található rendszerhéjon is futtathatja az alapszintű bevezetést és a mutatókat, hogy további információkhoz lehessen jutni a virtuális gépre telepített eszközökről.  
* Ismerje meg, hogyan hozhat létre szisztematikusan analitikai megoldásokat a [csoportos adatelemzési folyamat](../team-data-science-process/index.yml)használatával.
* Látogasson el az Azure AI-szolgáltatásokat használó gépi tanulási és adatelemzési minták [Azure AI Gallery](https://gallery.azure.ai/) .
* Olvassa el a virtuális gép megfelelő [dokumentációját](./reference-ubuntu-vm.md) .