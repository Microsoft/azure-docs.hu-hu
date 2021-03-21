---
title: Tesztkörnyezet létrehozása az adatelemzéshez Python-és Jupyter-jegyzetfüzetekkel | Microsoft Docs
description: Ismerje meg, hogyan állíthat be olyan labort, amely a Python és a Jupyter notebookok használatával tanítja az adatelemzést.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94648021"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Tesztkörnyezet létrehozása az adatelemzéshez Python és Jupyter notebookokkal
Ez a cikk azt ismerteti, hogyan állítható be a sablon virtuális gép (VM) a labor Servicesben a diákoknak a [Jupyter-jegyzetfüzetek](http://jupyter-notebook.readthedocs.io/)használatának megtanításához szükséges eszközökkel, valamint arról, hogy a tanulók hogyan kapcsolódhatnak a notebookokhoz a virtuális gépeken (VM-EK).

A Jupyter notebookok egy nyílt forráskódú projekt, amely lehetővé teszi, hogy könnyedén kombinálja a Rich Text és a végrehajtható Python-forráskódot egyetlen, jegyzetfüzet nevű vásznon. A jegyzetfüzetek futtatása a bemenetek és kimenetek lineáris rekordját eredményezi. Ezek a kimenetek tartalmazhatnak szöveget, táblákat, valamint elszórt ábrákat és egyéb adatokat.

## <a name="set-up-the-lab"></a>A labor beállítása

### <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja
A tesztkörnyezet beállításához hozzá kell férnie egy Azure-előfizetéshez és egy labor-fiókhoz. A szervezet rendszergazdájával megtudhatja, hogy tud-e hozzáférni egy meglévő Azure-előfizetéshez. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

Ha rendelkezik Azure-előfizetéssel, hozzon létre egy új Labor-fiókot Azure Lab Services az oktatóanyag: [labor-fiók beállítása](tutorial-setup-lab-account.md)című témakörben leírtak szerint. Használhat meglévő labor-fiókot is.

### <a name="lab-account-settings"></a>Tesztkörnyezet-Fiókbeállítások
Engedélyezze az alábbi táblázatban ismertetett beállításokat a labor-fiókhoz. A Piactéri lemezképek engedélyezésével kapcsolatos további információkért lásd: a [Piactéri lemezképek elérhetővé tétele a labor-készítők](specify-marketplace-images.md)számára.

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ------------------- | ------------ |
| Marketplace-beli rendszerkép | A labor-fiókjában az operációs rendszer igényei alapján engedélyezze az Azure Marketplace-lemezképek egyikét: <br/><ul><li>Data Science Virtual Machine – Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18,04</li></ul> |

> [!NOTE]
> Ez a cikk az Azure Marketplace-en elérhető adatelemzési virtuálisgép-rendszerképeket használja, mivel azok előre vannak konfigurálva a Jupyter Notebook használatával. Ezek a lemezképek azonban számos más fejlesztési és modellező eszközt is tartalmaznak az adatelemzéshez. Ha nem szeretné, hogy ezek a további eszközök, és csak Jupyter notebookokkal rendelkező egyszerű telepítést szeretne, hozzon létre egy egyéni virtuálisgép-rendszerképet. Példa a [JupyperHub telepítésére az Azure](http://tljh.jupyter.org/en/latest/install/azure.html)-ban. Miután létrehozta az egyéni rendszerképet, feltöltheti egy megosztott képkatalógusba, hogy Azure Lab Serviceson belül használhassa a rendszerképet. További információ a [megosztott képkatalógus használatáról Azure Lab Servicesban](how-to-attach-detach-shared-image-gallery.md). 

### <a name="lab-settings"></a>Tesztkörnyezet beállításai
A **virtuális gépek méretének** és a **virtuálisgép-rendszerkép** beállításainak konfigurálása az alábbi táblázatban látható módon, az osztályterem labor beállításakor. A tantermi labor létrehozásával kapcsolatos utasításokért tekintse meg [a tantermi labor beállítása](tutorial-setup-classroom-lab.md)című témakört.

| Tesztkörnyezet beállításai | Érték/utasítások |
| ------------ | ------------------ | 
| Virtuális gép mérete | <p>Az itt kiválasztott méret a futtatni kívánt munkaterheléstől függ:</p><ul><li>Kis vagy közepes – jó a Jupyter-jegyzetfüzetek elérésének alapszintű beállításához</li><li>Kisméretű GPU (számítás) – a legjobb megoldás a nagy számítási igényű és hálózati igényű alkalmazásokhoz, mint például a mesterséges intelligencia és a Deep learning</li></ul> | 
| Virtuálisgép-rendszerkép | <p>Az operációs rendszer igényei szerint válasszon az alábbi rendszerképek közül:</p><ul><li>[Data Science Virtual Machine – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18,04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Sablon virtuális gépe
A labor létrehozása után a rendszer a virtuális gép méretétől és a választott rendszerképtől függően létrehoz egy sablonból álló virtuális gépet. A sablonhoz tartozó virtuális gépet úgy konfigurálja, hogy a tanulók számára az adott osztály számára szükséges összes információt tartalmazza. További információ: [a sablon virtuális gép kezelése](how-to-create-manage-template.md). 

A Data Science VM rendszerképek alapértelmezés szerint számos adatelemzési keretrendszert és eszközt igényelnek az ilyen típusú osztályokhoz. A képek például a következők:

- [Jupyter jegyzetfüzetek](http://jupyter-notebook.readthedocs.io/): egy webalkalmazás, amely lehetővé teszi az adatszakértők számára a nyers adatok bevezetését, számítások futtatását, valamint az eredmények egyazon környezetben való megtekintését. Helyileg fog futni a sablon virtuális gépén.  
- [Visual Studio Code](https://code.visualstudio.com/): integrált fejlesztői környezet (ide), amely gazdag interaktív élményt nyújt a jegyzetfüzetek írásakor és tesztelésekor. További információ: [Jupyter notebookok használata a Visual Studio Code-ban](https://code.visualstudio.com/docs/python/jupyter-support).

### <a name="provide-notebooks-for-the-class"></a>Jegyzetfüzetek megadása az osztály számára
A következő feladat a tanulók számára a használni kívánt jegyzetfüzetek biztosítása. Saját jegyzetfüzetek megadásához a jegyzetfüzeteket helyileg is mentheti a sablon virtuális gépén. 

Ha Azure Machine Learningból származó jegyzetfüzeteket szeretne használni, tekintse meg a [környezet konfigurálása Jupyter notebookokkal](../machine-learning/how-to-configure-environment.md#jupyter)című témakört. 

### <a name="optional-enable-graphical-desktop-for-linux"></a>Opcionális: a grafikus asztali Linux engedélyezése 
Az **Data Science Virtual Machine – Ubuntu-** rendszerkép már ki van építve a X2GO-kiszolgálóval, és készen áll az ügyfélkapcsolatok fogadására. A sablon virtuális gép beállításakor nincs szükség további lépésekre. 

### <a name="publish-the-template-machine"></a>A sablon közzététele a gépen
Amikor közzéteszi a sablont, a laborban regisztrált minden diák megkapja a sablon virtuális gép másolatát az összes olyan helyi eszközzel és jegyzetfüzettel, amelyet beállított.

## <a name="how-students-connect-to-jupyter-notebooks"></a>Hogyan csatlakozhatnak a tanulók a Jupyter notebookokhoz?
Miután közzétette a sablont, minden diák hozzáférhet egy olyan virtuális géphez, amely az osztályhoz előre konfigurált összes adathoz tartozik, beleértve a Jupyter-jegyzetfüzeteket is. A következő fejezetek a tanulók Jupyter-jegyzetfüzetekhez való kapcsolódásának különböző módjait mutatják be. 

### <a name="for-windows-vms"></a>Windows rendszerű virtuális gépek esetén
Ha a diákokat Windows rendszerű virtuális gépekkel adta meg, akkor hozzá kell csatlakozniuk a virtuális gépekhez, és használniuk kell a helyileg elérhető Jupyter-jegyzetfüzeteket. 

Egy Windows rendszerű virtuális géphez való kapcsolódáshoz a tanulók távoli asztali kapcsolatot (RDP) használhatnak. A részletes lépésekért lásd: a [tantermi labor elérése](how-to-use-classroom-lab.md). 

A Mac vagy Chromebook használó tanulók a következő cikkek utasításait követve csatlakozhatnak az adatelemzési Windows virtuális géphez. 

- [Kapcsolódás virtuális géphez RDP használatával Mac gépen](connect-virtual-machine-mac-remote-desktop.md)
- [Kapcsolódás virtuális géphez RDP használatával egy Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Linux rendszerű virtuális gépek esetén
Ha a diákoknak Linux rendszerű virtuális gépeket adtak meg, akkor a tanulók több lehetőséget is használhatnak a Jupyter notebookokhoz való kapcsolódásra a virtuális gépeken:

- Jupyter-jegyzetfüzetek helyi elérése a virtuális géphez való csatlakozás után
    - SSH a virtuális géphez terminál-munkamenetek esetén
     - X2Go-kapcsolat a virtuális géppel grafikus munkamenetekhez
- Az SSH-bújtatás használatával csatlakozhat a tanuló helyi számítógépéről közvetlenül a Jupyter-kiszolgálóhoz a virtuális gépen. 

A következő szakaszokban részletesen ismertetjük a Jupyter-jegyzetfüzetekhez való kapcsolódás módját. 

#### <a name="ssh-to-virtual-machine"></a>SSH – virtuális gép
A diákok SSH-n keresztül csatlakozhatnak a Linux rendszerű virtuális gépekhez egy terminál-munkamenetből. A részletes lépésekért lásd: a [tantermi labor elérése](how-to-use-classroom-lab.md). Ha Windows-ügyfélszámítógépet használnak, az SSH-ügyfelet a [Putty](https://www.putty.org/) letöltésével vagy a [Windowsban](/windows-server/administration/openssh/openssh_install_firstuse) az SSH-val való engedélyezésével kell engedélyeznie a parancssorból. 

1.  Indítsa el a virtuális gépet.
2.  Ha a virtuális gép fut, kattintson a **Kapcsolódás** elemre, amely egy olyan párbeszédpanelt nyit meg, amely az SSH-parancs karakterláncát adja meg, amely a következő mintához hasonlóan fog kinézni:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Nyissa meg a parancssort vagy a terminált, és illessze be ezt a parancsot, majd nyomja le az **ENTER** billentyűt.
4.  Adja meg a virtuális gépre való bejelentkezéshez használandó jelszót. 

Ha a tanulók virtuális gépekhez csatlakoznak, a Jupyter notebookokat helyileg érhetik el és futtathatják.

#### <a name="x2go-to-virtual-machine"></a>X2Go a virtuális géphez
Az **Data Science Virtual Machine – Ubuntu-** rendszerkép már ki van építve a X2GO-kiszolgálóval, és készen áll az ügyfélkapcsolatok fogadására. A Linux rendszerű gép grafikus asztalára való kapcsolódáshoz a tanulóknak ezeket az egyszeri lépéseket kell követniük a X2Go beállításához az ügyfélgépeken:

1.  Töltse le és telepítse a [X2Go-ügyfelet](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) az ügyféloldali platformra.
2.  A [Azure Lab Services-portálon](https://labs.azure.com)győződjön meg arról, hogy a linuxos virtuális gép, amelyhez csatlakozni szeretne, elindul.
3.  Ha a virtuális gép fut, kattintson a **Kapcsolódás** gombra, amely az SSH-parancs karakterláncát tartalmazó párbeszédpanelt nyit meg, amely a következő mintához hasonlóan fog kinézni:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. Miután elvégezte ezt az információt, nyissa meg a X2Go ügyfélalkalmazás alkalmazást, és hozzon létre egy új munkamenetet. 
5.  Adja meg a következő értékeket a **munkamenet-beállítások** ablaktáblán:
    - **Munkamenet neve**: bármilyen igény szerint elvégezhető, de javasoljuk, hogy használja a tesztkörnyezet virtuális gépe nevét.
     - **Gazdagép**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **Bejelentkezés**: tanuló
     - **SSH-port**: 12345
     - **Munkamenet típusa**: Xfce
6. Válassza az **OK** lehetőséget. 

    > [!NOTE]
     > Új X2Go-munkamenet létrehozásakor ügyeljen arra, hogy az SSH portot használja, **nem** az RDP-portot.

A virtuális géphez való kapcsolódáshoz kövesse az alábbi lépéseket:    

1.  A X2Go-ügyfélben kattintson duplán arra a virtuális gépre, amelyhez csatlakozni szeretne. 

    ![X2Go-ügyfél](./media/class-type-jupyter-notebook/x2go-client.png)
2. Adja meg a virtuális géphez való kapcsolódáshoz használandó jelszót. (Előfordulhat, hogy X2Go engedélyt kell adnia a tűzfal megkerüléséhez a csatlakozás befejezéséhez.)
3.  Ekkor látnia kell az Ubuntu-Data Science VM grafikus felületét.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>SSH-alagút a Jupyter-kiszolgálóhoz a virtuális gépen
Előfordulhat, hogy néhány tanuló közvetlenül a helyi számítógépről szeretne csatlakozni a Jupyter-kiszolgálóhoz a virtuális gépeken belül. Az SSH protokoll lehetővé teszi a port továbbítását a helyi számítógép és egy távoli kiszolgáló (a mi esetünkben a Student-féle Tesztkörnyezet virtuális gépe) között, hogy egy adott porton futó alkalmazás a helyi számítógép leképezési portjára legyen **Bújtatásban** . A diákoknak az alábbi lépéseket kell követniük az SSH-alagúton a Jupyter-kiszolgálónak a labor virtuális gépeken:

1.  Győződjön meg arról, hogy a [Azure Lab Services-portálon](https://labs.azure.com)a csatlakozni kívánt linuxos virtuális gép elindult.
2.  Ha a virtuális gép fut, kattintson a **Kapcsolódás** gombra, amely az SSH-parancs karakterláncát tartalmazó párbeszédpanelt nyit meg, amely a következő sztringhez hasonlóan fog kinézni:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. A helyi számítógépen nyisson meg egy terminált vagy egy parancssort, és másolja az SSH-kapcsolódási karakterláncot. Ezután adja hozzá a `-L 8888:localhost:8888` parancsot a parancshoz, amely létrehozza az **alagutat** a portok között. A végső sztringnek a következőhöz hasonlóan kell kinéznie:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. A parancs futtatásához nyomja le az **ENTER** billentyűt. 
5.  Ha a rendszer kéri, adja meg a tesztkörnyezet virtuális géphez való csatlakozáshoz szükséges jelszót. 
6.  A virtuális géphez való csatlakozás után indítsa el a Jupyter-kiszolgálót a következő paranccsal: 

    ```bash
     jupyter notebook
     ```
7. A parancs futtatása megadja a terminál vagy a parancssor URL-címét. Az URL-címnek a következőhöz hasonlóan kell kinéznie:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Illessze be ezt az URL-címet egy böngészőbe a helyi számítógépen a Jupyter Notebook való kapcsolódáshoz és munkavégzéshez. 

    > [!NOTE]
    > A Visual Studio Code nagyszerű [Jupyter notebook szerkesztési élményt](https://code.visualstudio.com/docs/python/jupyter-support)is lehetővé tesz. Követheti a [távoli Jupyter-kiszolgálóhoz való kapcsolódást](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) , valamint az előző lépésben megjelenő URL-cím használatával való kapcsolódást a vs Code-ból a böngésző helyett. 


## <a name="cost-estimate"></a>Költségbecslés
Az osztályra vonatkozó lehetséges költségbecslés. 25 tanulós osztályt fogunk használni. 20 órányi ütemezett idő van. Emellett minden tanuló 10 órás kvótát kap a házi feladat vagy az ütemezett osztályon kívüli hozzárendelések számára. A kiválasztott virtuálisgép-méret kisméretű GPU (számítás) volt, amely 139 Lab egység. Ha a kisméretű (20 labor egység) vagy közepes méretű (42 labor-egység) használatát szeretné használni, az alábbi egyenletben a megfelelő számmal helyettesítheti a labor egység részét.  

Példa erre az osztályra vonatkozó lehetséges költségbecslés: 25 diák * (20 ütemezett óra + 10 kvóta óra) * 139 labor egység * 0,01 USD/óra = 1042,5 USD

További részletek a díjszabásról: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés
Ebből a cikkből megtudhatta, hogyan hozhat létre labort a Jupyter notebookok osztályhoz. Más gépi tanulási osztályokhoz is használhat hasonló beállításokat.

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)