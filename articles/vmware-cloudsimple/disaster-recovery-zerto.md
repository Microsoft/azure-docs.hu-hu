---
title: Azure VMware-megoldás CloudSimple használatával – a helyszíni számítási feladatokhoz használhatja a saját felhőt
description: Ismerteti, hogyan állíthatja be a CloudSimple saját Felhőjét vész-helyreállítási webhelyként a helyszíni VMware-alapú számítási feladatokhoz
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898130"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>A CloudSimple saját felhő beállítása vész-helyreállítási helyként a helyszíni VMware-alapú számítási feladatokhoz

A CloudSimple saját felhője helyreállítási helyként állítható be a helyszíni alkalmazások számára, hogy vészhelyzet esetén is biztosítson üzletmenet-folytonosságot. A helyreállítási megoldás a replikációs és a Zerto virtuális Replikáción alapul. A kritikus infrastruktúrát és az alkalmazás virtuális gépeket folyamatosan replikálhatja a helyszíni vCenter a saját felhőbe. Használhatja a saját Felhőjét feladatátvételi teszteléshez, és az alkalmazás rendelkezésre állásának biztosításához vészhelyzetben. Hasonló módszer követhető úgy, hogy a privát felhőt olyan elsődleges helyként állítsa be, amelyet egy másik helyen található helyreállítási hely véd.

> [!NOTE]
> Tekintse át a Zerto-dokumentum [méretezési szempontjait a Zerto virtuális replikálásához](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) a vész-helyreállítási környezet méretezésére vonatkozó irányelvek alapján.

A CloudSimple megoldás:

* Nem kell külön adatközpontot beállítani a vész-helyreállításhoz (DR).
* Lehetővé teszi, hogy kihasználja azokat az Azure-telephelyeket, ahol a CloudSimple üzembe helyezése a globális földrajzi rugalmasság érdekében történik.
* Lehetőséget biztosít a központi telepítési költségek és a DR teljes tulajdonlási költségeinek csökkentésére.

A megoldáshoz a következőket kell tennie:

* Zerto telepítése, konfigurálása és kezelése a saját felhőben.
* Saját licenceket biztosíthat a Zerto, ha a titkos felhő a védett hely. A CloudSimple-helyen futó Zerto-ket a helyszíni hellyel is párosíthatja a licenceléshez.

Az alábbi ábra a Zerto-megoldás architektúráját mutatja be.

![Architektúra](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>A megoldás üzembe helyezése

A következő szakaszok azt ismertetik, hogyan helyezhet üzembe egy DR-megoldást a Zerto virtuális replikálás használatával a saját felhőben.

1. [Előfeltételek](#prerequisites)
2. [Opcionális konfiguráció a CloudSimple privát felhőben](#optional-configuration-on-your-private-cloud)
3. [A ZVM és a VRA beállítása a CloudSimple Private Cloud-on](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Zerto virtuális védelmi csoport beállítása](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek végrehajtásával engedélyezheti a Zerto virtuális replikálását a helyszíni környezetből a privát felhőbe.

1. [Hozzon létre egy helyek közötti VPN-kapcsolatot a helyszíni hálózat és a CloudSimple privát felhője között](set-up-vpn.md).
2. [Állítsa be a DNS-lekérdezést úgy, hogy a saját felhőalapú felügyeleti összetevői továbbítva legyenek a privát Felhőbeli DNS-kiszolgálókra](on-premises-dns-setup.md).  A DNS-címkeresés továbbításának engedélyezéséhez hozzon létre egy továbbítási zóna bejegyzést a helyszíni DNS-kiszolgálón a `*.cloudsimple.io` DNS-kiszolgálók CloudSimple.
3. DNS-címkeresés beállítása, hogy a helyszíni vCenter-összetevők továbbítva legyenek a helyszíni DNS-kiszolgálókra.  A DNS-kiszolgálóknak elérhetőnek kell lenniük a saját CloudSimple-felhőből a helyek közötti VPN-kapcsolaton keresztül. Ha segítségre van, nyújtson be egy [támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), amely a következő információkat biztosítja.  

    * Helyszíni DNS-tartománynév
    * Helyszíni DNS-kiszolgáló IP-címei

4. Telepítsen egy Windows-kiszolgálót a privát felhőbe. A kiszolgáló a Zerto Virtual Manager telepítésére szolgál.
5. [Bővítse CloudSimple-jogosultságait](escalate-private-cloud-privileges.md).
6. Hozzon létre egy új felhasználót a saját felhőalapú vCenter, és a rendszergazdai szerepkört használja a Zerto Virtual Manager szolgáltatáshoz.

### <a name="optional-configuration-on-your-private-cloud"></a>Opcionális konfiguráció a saját felhőben

1. Hozzon létre egy vagy több erőforráskészletet a saját Felhőbeli vCenter a helyszíni környezetből származó virtuális gépek célként szolgáló erőforráskészletként való használatára.
2. Hozzon létre egy vagy több mappát a saját Felhőbeli vCenter a helyszíni környezetből származó virtuális gépek célmappájának használatával.
3. Hozzon létre VLAN-okat a feladatátvételi hálózathoz, és állítsa be a tűzfalszabályok szabályait. Nyisson meg egy [támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a segítséghez.
4. Elosztott porttartomány létrehozása a feladatátvételi hálózathoz és a tesztelési hálózathoz a virtuális gépek feladatátvételének teszteléséhez.
5. Telepítse a [DHCP-és DNS-kiszolgálókat](dns-dhcp-setup.md) , vagy használjon egy Active Directory tartományvezérlőt a saját felhőalapú környezetében.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>A ZVM és a VRA beállítása a saját felhőben

1. Telepítse a Zerto Virtual Managert (ZVM) a Windows Serverre a privát felhőben.
2. Jelentkezzen be a ZVM-be az előző lépésekben létrehozott szolgáltatásfiók használatával.
3. A Zerto Virtual Manager licencelésének beállítása.
4. Telepítse a Zerto virtuális replikációs berendezést (VRA) a privát felhő ESXi-gazdagépére.
5. Párosítsa saját Felhőbeli ZVM a helyszíni ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Zerto virtuális védelmi csoport beállítása

1. Hozzon létre egy új virtuális védelmi csoportot (VPG), és határozza meg a VPG prioritását.
2. Válassza ki azokat a virtuális gépeket, amelyeknek védelmet kíván biztosítani az üzletmenet folytonossága érdekében, és szükség esetén szabja testre a rendszerindítási sorrendet
3. Válassza ki a helyreállítási helyet a privát felhőként, illetve az alapértelmezett helyreállítási kiszolgálót saját felhőalapú fürtként vagy a létrehozott erőforráscsoportként. Válassza ki a **vsanDatastore** a helyreállítási adattárhoz a saját felhőben.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Az egyes virtuális gépek gazdagépének beállítását a virtuálisgép-beállítások lehetőség alatt szabhatja testre.

4. Szükség szerint szabja testre a tárolási beállításokat.
5. Határozza meg a feladatátvételi hálózathoz és a feladatátvételi teszthez használt helyreállítási hálózatot a korábban létrehozott elosztott portok csoportjaként, és szükség szerint szabja testre a helyreállítási parancsfájlokat.
6. Szükség esetén szabja testre az egyes virtuális gépek hálózati beállításait, és hozza létre a VPG.
7. Feladatátvételi teszt a replikálás befejeződése után.

## <a name="reference"></a>Referencia

[A Zerto dokumentációja](https://www.zerto.com/myzerto/technical-documentation/)
