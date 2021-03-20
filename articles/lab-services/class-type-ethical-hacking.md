---
title: Etikai hackelési tesztkörnyezet beállítása Azure Lab Servicesokkal | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be a labort a Azure Lab Services használatával az etikai hackerek tanításához.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 97cdf13f39cc73ee7f35fb402229469195f1456c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97616422"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Tesztkörnyezet beállítása az etikai hackelési osztály tanításához

Ez a cikk bemutatja, hogyan állíthat be egy olyan osztályt, amely az etikai feltörések kriminalisztikai oldalára koncentrál. A behatolási teszt, amelyet az etikai hackelési Közösség használ, akkor következik be, amikor valaki megpróbál hozzáférni a rendszerhez vagy a hálózathoz a rosszindulatú támadó által kihasználható biztonsági rések bemutatására.

Egy etikai hackelési osztályban a tanulók modern technikákat tanulnak a biztonsági rések elleni védelemhez. Minden tanuló egy olyan Windows Server Host virtuális gépet kap, amely két beágyazott virtuális géppel rendelkezik – egy [Metasploitable3](https://github.com/rapid7/metasploitable3) -lemezképpel rendelkező virtuális géppel és egy másik, [Kali Linux](https://www.kali.org/) -lemezképpel rendelkező géppel. A Metasploitable virtuális gép felhasználási célokra szolgál, és a Kali virtuális gép hozzáférést biztosít a kriminalisztikai feladatok végrehajtásához szükséges eszközökhöz.

Ez a cikk két fő szakaszt tartalmaz. Az első szakasz ismerteti, hogyan hozhatja létre az osztályterem labort. A második szakasz ismerteti, hogyan hozhatja létre a sablon gépet, amelyen engedélyezve van a beágyazott virtualizálás, valamint a szükséges eszközök és lemezképek. Ebben az esetben a Metasploitable-lemezkép és a Kali Linux-lemezkép egy olyan gépen, amelyen engedélyezve van a Hyper-V a lemezképek üzemeltetéséhez.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

A tesztkörnyezet beállításához Azure-előfizetésre van szükség a kezdéshez. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services, vagy használhat meglévő fiókot is. A következő oktatóanyagban talál egy új Labor-fiókot: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).

[Ezt az oktatóanyagot](tutorial-setup-classroom-lab.md) követve hozzon létre egy új labort, majd alkalmazza a következő beállításokat:

| Virtuális gép mérete | Kép |
| -------------------- | ----- |
| Közepes (beágyazott virtualizálás) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Sablon számítógép

A sablon létrehozása után indítsa el a gépet, és kapcsolódjon ahhoz, hogy elvégezze a következő három fő feladatot.

1. Állítsa be a gépet a beágyazott virtualizálás számára. Lehetővé teszi az összes megfelelő Windows-funkció (például a Hyper-V) használatát, és beállítja a Hyper-V-lemezképek hálózatkezelését, hogy képes legyen kommunikálni egymással és az internettel.
2. A [Kali](https://www.kali.org/) Linux-rendszerkép beállítása. A Kali egy Linux-disztribúció, amely a behatolás tesztelésére és a biztonsági naplózásra szolgáló eszközöket tartalmaz.
3. Állítsa be a Metasploitable-rendszerképet. Ebben a példában a rendszer a [Metasploitable3](https://github.com/rapid7/metasploitable3) rendszerképet fogja használni. Ez a rendszerkép szándékosan biztonsági réseket hoz létre.

A cikk további része a fenti feladatok elvégzéséhez szükséges manuális lépéseket tárgyalja.  Azt is megteheti, hogy futtatja a [labor Services Hyper-V parancsfájlokat](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) és a [labor Services etikai hacker-parancsfájlokat](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Sablon számítógépének előkészítése beágyazott virtualizálás esetén

Az utasításokat követve [engedélyezze a beágyazott virtualizálás](how-to-enable-nested-virtualization-template-vm.md) számára, hogy előkészítse a sablon virtuális gépet a beágyazott virtualizálás számára.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Beágyazott virtuális gép beállítása a Kali Linux-lemezképpel

A Kali egy Linux-disztribúció, amely a behatolás tesztelésére és a biztonsági naplózásra szolgáló eszközöket tartalmaz.

1. Lemezkép letöltése a [sértő biztonsági Kali linuxos virtuálisgép-rendszerképeiből](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/).  Jegyezze fel a letöltési oldalon megjelenő alapértelmezett felhasználónevet és jelszót.
    1. Töltse le a **Kali Linux vmware 64-bit (7z)** rendszerképét a VMware-hez.
    1. Bontsa ki a. 7z fájlt.  Ha még nem rendelkezik 7 zip-vel, töltse le a következő címről: [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) . Jegyezze meg a kibontott mappa helyét, mert később szüksége lesz rá.
1. Alakítsa át a kinyert VMDK-fájlt egy vhdx-fájlba, hogy a vhdx-fájlt a Hyper-V használatával használhassa. A VMware-lemezképek Hyper-V-lemezképekre való átalakításához több eszköz is rendelkezésre áll.  A [STARWIND V2V átalakítót](https://www.starwindsoftware.com/starwind-v2v-converter)fogjuk használni.  A letöltéshez lásd: [STARWIND V2V Converter letöltési oldal](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Indítsa el a **STARWIND V2V átalakítót**.
    1. A **konvertálandó rendszerkép kiválasztása** lapon válassza a **helyi fájl** lehetőséget.  Kattintson a **Tovább** gombra.
    1. A **forrás rendszerkép** lapon navigáljon az előző lépésben kibontott Kali Linux VMDK-fájlra, és válassza ki a **fájlnév** beállításnál.  A fájl a Kali-Linux-{Version}-VMware-amd64. VMDK formátumban lesz.  Kattintson a **Tovább** gombra.
    1. A **cél rendszerképének kiválasztása** területen válassza a **helyi fájl** lehetőséget.  Kattintson a **Tovább** gombra.
    1. A **cél képformátumának kiválasztása** lapon válassza a **VHD/VHDX** elemet.  Kattintson a **Tovább** gombra.
    1. A **VHD/VHDX képformátumának kiválasztása** lapon válassza a VHDX- **felszaporodható rendszerkép** lehetőséget.  Kattintson a **Tovább** gombra.
    1. A **célfájl nevének kiválasztása** lapon fogadja el az alapértelmezett fájlnevet.  Válassza a **Konvertálás** lehetőséget.
    1. A **Konvertálás** lapon várja meg a rendszerkép konvertálását.  A folyamat befejezése eltarthat néhány percig.  A konverzió befejeződése után válassza a **Befejezés** lehetőséget.
1. Hozzon létre egy új Hyper-V virtuális gépet.
    1. Nyissa meg a **Hyper-V kezelőjét**.
    1. Válassza az  ->  **új**  ->  **virtuális gép** művelet lehetőséget.
    1. Az **új virtuális gép varázsló** alapismeretek **lapján kattintson a** **Tovább gombra**.
    1. A **név és hely megadása** lapon írja be a **Kali-Linux** nevet a **név** mezőbe, majd kattintson a **Tovább gombra**.
    1. A **generáció megadása** lapon fogadja el az alapértelmezett értékeket, majd kattintson a **tovább** gombra.
    1. A **memória kiosztása** lapon adja meg a **2048 MB** értéket az **indítási memória** számára, majd kattintson a **Tovább gombra**.
    1. A **hálózat konfigurálása** lapon hagyja a kapcsolatot **nem csatlakoztatottként**. A hálózati adaptert később kell beállítania.
    1. A **virtuális merevlemez összekapcsolása** lapon válassza a **meglévő virtuális merevlemez használata** lehetőséget. Keresse meg az előző lépésben létrehozott **Kali-Linux-{Version}-VMware-amd64. VMDK** fájlt, és kattintson a **tovább** gombra.
    1. Az **új virtuális gép varázsló befejezése** lapon kattintson a **Befejezés gombra**.
    1. A virtuális gép létrehozása után válassza ki azt a Hyper-V kezelőjében. Ne kapcsolja be még a gépet.  
    1. Válassza a **műveleti**  ->  **Beállítások** lehetőséget.
    1. A a **Kali-Linux párbeszédpanel Beállítások** területén válassza a **hardver hozzáadása** elemet.
    1. Válassza az **örökölt hálózati adapter** lehetőséget, majd kattintson a **Hozzáadás** gombra.
    1. Az **örökölt hálózati adapter** lapon válassza a **LabServicesSwitch** lehetőséget a **virtuális kapcsoló** beállításnál, majd kattintson az **OK gombra**. A rendszer létrehozta a LabServicesSwitch a Hyper-V sablon-számítógépének előkészítésekor a **beágyazott virtualizációs sablon előkészítése sablonjában** .
    1. A Kali-Linux rendszerkép most már készen áll a használatra. A **Hyper-V kezelőjében** válassza a **művelet**  ->  **indítása** lehetőséget, majd válassza a **művelet**  ->  **Kapcsolódás** lehetőséget a virtuális géphez való kapcsolódáshoz.  Az alapértelmezett Felhasználónév a **Kali** , és a jelszó a **Kali**.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Beágyazott virtuális gép beállítása Metasploitable-lemezképpel  

A Rapid7 Metasploitable-rendszerkép a biztonsági rések mellett konfigurált rendszerkép. Ezt a rendszerképet fogja használni a problémák teszteléséhez és kereséséhez. Az alábbi utasítások bemutatják, hogyan használható egy előre létrehozott Metasploitable-rendszerkép. Ha azonban a Metasploitable-rendszerkép újabb verziójára van szükség, tekintse meg a következőt: [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) .

1. Töltse le a Metasploitable-rendszerképet.
    1. Navigáljon a következőhöz: [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html) . Töltse ki az űrlapot a rendszerkép letöltéséhez, és kattintson a **Submit (Küldés** ) gombra.
    2. Válassza a **Letöltés Metasploitable most** gombot.
    3. A zip-fájl letöltésekor bontsa ki a zip-fájlt, és jegyezze fel a Metasploitable. VMDK fájl helyét.
1. Alakítsa át a kinyert VMDK-fájlt egy vhdx-fájlba, hogy a vhdx-fájlt a Hyper-V használatával használhassa. A VMware-lemezképek Hyper-V-lemezképekre való átalakításához több eszköz is rendelkezésre áll.  A [STARWIND V2V átalakítót](https://www.starwindsoftware.com/starwind-v2v-converter) újra fogjuk használni.  A letöltéshez lásd: [STARWIND V2V Converter letöltési oldal](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Indítsa el a **STARWIND V2V átalakítót**.
    1. A **konvertálandó rendszerkép kiválasztása** lapon válassza a **helyi fájl** lehetőséget.  Kattintson a **Tovább** gombra.
    1. A **forrás rendszerkép** lapon navigáljon az előző lépésben kibontott Metasploitable. VMDK elemre, és válassza ki a **fájl neve** beállítást.  Kattintson a **Tovább** gombra.
    1. A **cél rendszerképének kiválasztása** területen válassza a **helyi fájl** lehetőséget.  Kattintson a **Tovább** gombra.
    1. A **cél képformátumának kiválasztása** lapon válassza a **VHD/VHDX** elemet.  Kattintson a **Tovább** gombra.
    1. A **VHD/VHDX képformátumának kiválasztása** lapon válassza a VHDX- **felszaporodható rendszerkép** lehetőséget.  Kattintson a **Tovább** gombra.
    1. A **célfájl nevének kiválasztása** lapon fogadja el az alapértelmezett fájlnevet.  Válassza a **Konvertálás** lehetőséget.
    1. A **Konvertálás** lapon várja meg a rendszerkép konvertálását.  A folyamat befejezése eltarthat néhány percig.  A konverzió befejeződése után válassza a **Befejezés** lehetőséget.
1. Hozzon létre egy új Hyper-V virtuális gépet.
    1. Nyissa meg a **Hyper-V kezelőjét**.
    1. Válassza az  ->  **új**  ->  **virtuális gép** művelet lehetőséget.
    1. Az **új virtuális gép varázsló** alapismeretek **lapján kattintson a** **Tovább gombra**.
    1. A **név és hely megadása** lapon adja meg a **Metasploitable** nevet a **név** mezőben, majd kattintson a **Tovább gombra**.

        ![Új virtuálisgép-rendszerkép varázsló](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. A **generáció megadása** lapon fogadja el az alapértelmezett értékeket, majd kattintson a **tovább** gombra.
    1. A **memória kiosztása** lapon adja meg a **512 MB** értéket az **indítási memória** számára, majd kattintson a **Tovább gombra**.

        ![Memória kiosztása lap](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. A **hálózat konfigurálása** lapon hagyja a kapcsolatot **nem csatlakoztatottként**. A hálózati adaptert később kell beállítania.
    1. A **virtuális merevlemez összekapcsolása** lapon válassza a **meglévő virtuális merevlemez használata** lehetőséget. Keresse meg az előző lépésben létrehozott **metasploitable. vhdx** fájl helyét, és kattintson a **tovább** gombra.

        ![Virtuális hálózati lemez összekapcsolásának lapja](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Az **új virtuális gép varázsló befejezése** lapon kattintson a **Befejezés gombra**.
    1. A virtuális gép létrehozása után válassza ki azt a Hyper-V kezelőjében. Ne kapcsolja be még a gépet.  
    1. Válassza a **műveleti**  ->  **Beállítások** lehetőséget.
    1. A **Metasploitable beállításai** párbeszédpanelen válassza a **hardver hozzáadása** elemet.
    1. Válassza az **örökölt hálózati adapter** lehetőséget, majd kattintson a **Hozzáadás** gombra.

        ![Hálózati adapter lap](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. Az **örökölt hálózati adapter** lapon válassza a **LabServicesSwitch** lehetőséget a **virtuális kapcsoló** beállításnál, majd kattintson az **OK gombra**. A rendszer létrehozta a LabServicesSwitch a Hyper-V sablon-számítógépének előkészítésekor a **beágyazott virtualizációs sablon előkészítése sablonjában** .

        ![Örökölt hálózati adapter lap](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. A Metasploitable-rendszerkép most már használatra kész. A **Hyper-V kezelőjében** válassza a **művelet**  ->  **indítása** lehetőséget, majd válassza a **művelet**  ->  **Kapcsolódás** lehetőséget a virtuális géphez való kapcsolódáshoz.  Az alapértelmezett Felhasználónév a **msfadmin** , a jelszó pedig **msfadmin**.

A sablon frissítve lett, és a rendszerképek szükségesek egy etikai hackelési bevezetési tesztelési osztályhoz, amely egy olyan eszközzel rendelkezik, amely a behatolási teszteket és egy másik, biztonsági réseket felderítő képet tartalmaz. A sablon képe mostantól közzétehető az osztályban. Kattintson a **Közzététel** gombra a sablon lapon a sablonnak a laborba való közzétételéhez.

## <a name="cost"></a>Költségek  

Ha a labor költségeit szeretné megbecsülni, a következő példát használhatja:

Egy 25 tanulós osztály esetében, amely 20 órányi ütemezett időpontot és 10 órányi munkafeladatot vagy hozzárendelést használ, a labor ára a következő lesz:

25 tanuló \* (20 + 10) óra \* 55 labor egység \* 0,01 USD/óra = 412,50 USD

>[!IMPORTANT]
>A költségbecslés csak példaként szolgál. A díjszabással kapcsolatos aktuális információk: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés

Ez a cikk végigvezeti a tesztkörnyezet etikai feltörési osztályhoz való létrehozásának lépésein. Ez magában foglalja a beágyazott virtualizálás beállításának lépéseit két virtuális gép létrehozásához a gazda virtuális gépen az áthatoló tesztelés érdekében.

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztráció a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users).
