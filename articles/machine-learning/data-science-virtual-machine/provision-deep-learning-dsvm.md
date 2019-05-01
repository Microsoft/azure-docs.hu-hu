---
title: Deep Learning-adatelemzési virtuális gép létrehozása
titleSuffix: Azure
description: Konfigurálja és a egy Deep Learning-adatelemzési virtuális gép létrehozása az Azure-ban Analytics és a gépi tanulás.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 1fc4a4024893c29c35b4369d13227830cfbbca84
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943922"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Deep Learning-Azure-beli virtuális gép kiépítése 

A Deep Learning virtuális gép (DLVM) egy speciálisan konfigurált változata a népszerű [adatelemző virtuális gép](https://aka.ms/dsvm) , hogy könnyebben használható GPU-alapú virtuális gép (DSVM)-példányok gyorsan a deep learning-modellek betanításához. Az alap, a Windows 2016 vagy az Ubuntu DSVM támogatott. A DLVM megosztja az ugyanazon alapvető Virtuálisgép-rendszerképek és ezért a gazdag eszközkészletet, amely elérhető a dsvm-hez. 

A DLVM számos olyan eszközt tartalmaz, a mesterséges Intelligencia, ideértve például a Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; számos népszerű deep learning keretrendszerek GPU kiadásai eszközök beszerzésére és előfeldolgozási kép, szöveges adatok, eszközök adatelemzési modellezéshez és fejlesztési tevékenységekhez például a Microsoft R Server Developer Edition, Anaconda Python, a Jupyter notebooks, a Python és az R, Python és az SQL, R ide-ket adatbázisokat és sok más adatelemzési és gépi Tanulási eszközökkel. 

## <a name="create-your-deep-learning-virtual-machine"></a>A Mélytanulási virtuális gép létrehozása
Hozzon létre egy példányt, a Deep Learning virtuális gép a lépései a következők: 

1. Keresse meg a virtuális gépet, az ajánlati [az Azure portal](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Válassza ki a **létrehozás** gombra az alsó kell figyelembe venni a varázslót.![ Hozzon létre dlvm](./media/dlvm-provision-wizard.PNG)
3. A varázsló a DLVM létrehozásához használt szükséges **bemenetek** minden a **négy lépést** jobb oldalán Ez az ábra számba. Az alábbiakban a bemeneti adatok konfigurálása az egyes lépéseket:

   <a name="basics"></a>   
   1. **Alapvető beállítások**
      
      1. **Név**: Neve az adatelemzési kiszolgálót hoz létre.
      2. **Válassza ki a Deep Learning virtuális gép operációs rendszerének típusa**: Válassza ki a Windows vagy Linux (a Windows 2016 és Ubuntu Linux alap dsvm-hez)
      2. **Felhasználónév**: Rendszergazdai fiók bejelentkezési azonosítója.
      3. **Jelszó**: Rendszergazdai fiók jelszava.
      4. **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás.
      5. **Erőforráscsoport**: Létrehozhat egy újat, vagy használjon egy **üres** meglévő Azure-erőforráscsoport az előfizetésben.
      6. **Hely**: Válassza ki az Adatközpont leginkább megfelelő. Általában az adatközpont, amely tartalmazza a legtöbb az adatokat, vagy a leggyorsabb hálózati hozzáféréshez a fizikai helyéhez legközelebbi. 
      
      > [!NOTE]
      > A DLVM támogatja az összes hálózati vezérlő és ND sorozatú GPU VM-példányokon. A DLVM kiépítésekor választania kell a helyek gpu-kkal rendelkező Azure-ban. Ellenőrizze a [régió lap által az Azure-termékekkel](https://azure.microsoft.com/regions/services/) lapon az elérhető helyeket, és keressen **NC sorozat**, **NCv2 sorozat**, **az NCv3 sorozatú** , vagy **ND sorozat** alatt **számítási**. 

   1. **Beállítások**: Válasszon ki egy az NC sorozat (NC, NCv2, az NCv3) vagy az ND sorozat virtuálisgép-méretek GPU, amely megfelel a funkcionális és a költségek megkötések. Hozzon létre egy tárfiókot a virtuális géphez.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   1. **Összefoglalás**: Győződjön meg arról, hogy minden, a megadott adatok helyesek.

   1. **Vásároljon**: Kattintson a **vásárlása** a kiépítésének megkezdéséhez. A tranzakció feltételeiben szerepel egy hivatkozás. A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretét a számítási túl további díjakat a **mérete** . lépés. 

> [!NOTE]
> A kiépítés körülbelül 10 – 20 percet vesz igénybe. A kiépítési állapota jelenik meg az Azure Portalon.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>A Deep Learning virtuális gép elérése

### <a name="windows-edition"></a>Windows Edition
A virtuális gép létrehozása után azokat a rendszergazdai fiók hitelesítő adatait, amely az előző konfigurálta a távoli asztal is **alapjai** szakaszban. 

### <a name="linux-edition"></a>Linux Edition

Ha a virtuális gép létrejött, bejelentkezhet hozzá SSH használatával. A fiók hitelesítő adataival, amelyet a [ **alapjai** ](#basics) szakasz 3. lépésben a szöveg shell felületén. Azure virtuális gépekre SSH-kapcsolatok kapcsolatos további információkért lásd: [telepítése és konfigurálása az Azure-beli Linuxos virtuális gép kapcsolódni a távoli asztal](/azure/virtual-machines/linux/use-remote-desktop). Egy Windows-ügyfélen, töltse le egy SSH-ügyfél eszköz, például [Putty](http://www.putty.org). Ha inkább egy grafikus desktop (X Windows rendszer), használhatja a Putty-továbbítás X11 vagy X2Go ügyfél telepítése. 

> [!NOTE]
> A X2Go ügyfél jobban teljesített, mint a tesztelés továbbítás X11. Azt javasoljuk, hogy az asztali grafikus felület X2Go ügyfél használatával.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Telepítésével és konfigurálásával X2Go ügyfél
A Linux DLVM már kiépített X2Go kiszolgálóval, és készen áll kapcsolatok fogadására. Ha csatlakozni szeretne a Linuxos virtuális gép grafikus asztali, az alábbi eljárással az ügyfélen:

1. Töltse le és telepítse a saját ügyfélplatformjára X2Go ügyfél [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Futtassa a X2Go ügyfél, és válassza a **új munkamenet**. Több lap egy konfigurációs ablaka nyílik. Adja meg az alábbi konfigurációs paramétereket:
   * **Munkamenet lapon**:
     * **Gazdagép**: A gazdagép neve vagy a Linux rendszerű adatelemző virtuális gép IP-címét.
     * **Bejelentkezési**: A felhasználónév, a Linux rendszerű virtuális gépen.
     * **SSH Port**: Meghagyhatja az alapértelmezett érték 22.
     * **Munkamenet típusa**: Módosítsa az értéket **XFCE**. A Linux-DSVM jelenleg csak XFCE desktop támogatja.
   * **Az adathordozó lapon**: Kikapcsolhatja eredményes támogatása és az ügyfél nyomtatási, ha nem szeretné használni őket.
   * **A megosztott mappák**: Ha az ügyfél gépek csatlakoztatása Linux rendszerű virtuális gépen szeretné címtárak, adja hozzá az ügyfél gép könyvtárakat, hogy meg szeretné osztani az ezen a lapon a virtuális géppel.

Bejelentkezés után a virtuális géphez SSH-ügyfél vagy a grafikus asztali XFCE X2Go ügyfélszámítógépen keresztül, készen áll az eszközöket, amelyek telepítése és konfigurálása történik meg a virtuális gép használatának megkezdéséhez. A XFCE látható alkalmazások parancsikonjai és asztali ikonok a számos olyan eszközzel.

A virtuális gép létrehozása és üzembe helyezett, után készen áll az eszközöket, amelyek telepítése és konfigurálása történik rá használatának megkezdéséhez. Start menü csempék és számos olyan eszközzel asztali ikonok vannak. 
