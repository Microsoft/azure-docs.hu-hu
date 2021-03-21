---
title: SMT-kiszolgáló beállítása az Azure-beli SAP HANAhoz (nagyméretű példányok) | Microsoft Docs
description: SMT-kiszolgáló beállítása az Azure-beli SAP HANAhoz (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 990274f34dac0234c2c2d521ca6a260f999671bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674519"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Az SMT-kiszolgáló beállítása SUSE Linux rendszerhez
SAP HANA nagyméretű példányai nem rendelkeznek közvetlen kapcsolattal az internettel. Ez nem egy egyszerű folyamat, amellyel regisztrálhatja az adott egységet az operációs rendszer szolgáltatójával, valamint letöltheti és alkalmazhatja a frissítéseket. A SUSE Linux rendszerhez készült megoldás egy SMT-kiszolgáló beállítása egy Azure-beli virtuális gépen. A virtuális gépet egy olyan Azure-beli virtuális hálózatban üzemelteti, amely a HANA Large-példányhoz csatlakozik. Ilyen SMT-kiszolgáló esetén a HANA nagyméretű példány egysége regisztrálhatja és letöltheti a frissítéseket. 

A SUSE-ről további dokumentációt a [SLES 12 SP2-hez készült előfizetés-kezelési eszköze](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf)tartalmaz. 

A HANA nagyméretű példányokra vonatkozó feladatot teljesítő SMT-kiszolgáló telepítésének előfeltételei a következők:

- Egy olyan Azure-beli virtuális hálózat, amely a HANA nagyméretű példány ExpressRoute-áramkörhöz csatlakozik.
- Egy szervezethez társított SUSE-fiók. A szervezetnek érvényes SUSE-előfizetéssel kell rendelkeznie.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Az SMT-kiszolgáló telepítése Azure-beli virtuális gépen

Először jelentkezzen be a [SUSE Customer Center webhelyre](https://scc.suse.com/).

Lépjen a **szervezeti**  >  **szervezet hitelesítő adataihoz**. Ebben a szakaszban az SMT-kiszolgáló beállításához szükséges hitelesítő adatokat kell megkeresnie.

Ezután telepítsen egy SUSE Linux rendszerű virtuális GÉPET az Azure Virtual Network-ben. A virtuális gép üzembe helyezéséhez készítsen SLES 12 SP2 katalógust az Azure-ról (válassza a BYOS SUSE-lemezképet). A telepítési folyamat során ne adjon meg DNS-nevet, és ne használjon statikus IP-címeket.

![Képernyőfelvétel a virtuális gépekről az SMT-kiszolgáló üzembe helyezéséről](./media/hana-installation/image3_vm_deployment.png)

A telepített virtuális gép kisebb, és megkapta a belső IP-címet a 10.34.1.4 Azure-beli virtuális hálózatában. A virtuális gép neve *smtserver*. A telepítés után a HANA nagyméretű példány-egységhez vagy-egységhez való kapcsolódás be van jelölve. A névfeloldás megszervezésének módjától függően előfordulhat, hogy konfigurálnia kell a HANA nagyméretű példány-egységek feloldását az Azure-beli virtuális gép etc/gazdagépei között. 

Adjon hozzá egy lemezt a virtuális géphez. Ezt a lemezt használja a frissítések tárolására, és maga a rendszerindító lemez túl kicsi lehet. Itt a lemez a/SRV/www/htdocs-hez van csatlakoztatva, ahogy az alábbi képernyőképen is látható. A 100 GB-os lemeznek elegendőnek kell lennie.

![A képernyőfelvétel a PuTTY ablakban jeleníti meg a hozzáadott lemezt.](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Jelentkezzen be a HANA nagyméretű példány-egységbe vagy-egységbe, tartsa karban a/etc/hosts, és győződjön meg arról, hogy elérhető-e az az Azure-beli virtuális gép, amely az SMT-kiszolgálót a hálózaton keresztül futtatja.

Az előfizetés után jelentkezzen be az Azure-beli virtuális gépre, amelynek futtatnia kell az SMT-kiszolgálót. Ha a PuTTY használatával jelentkezik be a virtuális gépre, futtassa a következő parancsokat a bash-ablakban:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

A beállítások aktiválásához indítsa újra a bashot. Ezután indítsa el a YAST.

Kapcsolja össze a virtuális gépet (smtserver) a SUSE webhellyel.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Miután a virtuális gép csatlakoztatva van a SUSE-helyhez, telepítse az SMT-csomagokat. Az SMT-csomagok telepítéséhez használja a következő Putty-parancsot.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Az SMT-csomagok telepítéséhez a YAST eszközt is használhatja. A YAST-ben lépjen a **szoftver karbantartása** elemre, és keressen rá az SMT kifejezésre. Válassza ki az **SMT**-t, amely automatikusan YaST2-SMT-re vált.

![Képernyőfelvétel a YAST-beli SMT-ről](./media/hana-installation/image5_smt_in_yast.PNG)


Fogadja el a kijelölést a smtserver való telepítéshez. A telepítés befejezése után nyissa meg az SMT-kiszolgáló konfigurációját. Adja meg a korábban lekért SUSE Customer Center szervezeti hitelesítő adatait. Adja meg az Azure-beli virtuális gép állomásnevét is az SMT-kiszolgáló URL-címének megadásával. Ebben a bemutatóban ez a https: \/ /smtserver.

![Az SMT-kiszolgáló konfigurációjának képernyőképe](./media/hana-installation/image6_configuration_of_smtserver1.png)

Most tesztelje, hogy működik-e a SUSE Customer centerrel létesített kapcsolódás. Ahogy az alábbi képernyőképen is látható, ebben a bemutató esetben is működött.

![Képernyőkép a SUSE Customer centerrel létesített kapcsolatok teszteléséről](./media/hana-installation/image7_test_connect.png)

A SMT telepítésének megkezdése után adja meg az adatbázis jelszavát. Mivel ez egy új telepítés, az alábbi képernyőképen látható módon kell megadnia a jelszót.

![Az adatbázis jelszavának definiálására szolgáló képernyőkép](./media/hana-installation/image8_define_db_passwd.PNG)

A következő lépés egy tanúsítvány létrehozása.

![Képernyőfelvétel az SMT-kiszolgáló tanúsítványának létrehozásáról](./media/hana-installation/image9_certificate_creation.PNG)

A konfiguráció végén néhány percet is igénybe vehet a szinkronizálási vizsgálat futtatása. Az SMT-kiszolgáló telepítése és konfigurálása után keresse meg a címtár-tárházat a csatlakoztatási pont/SRV/www/htdocs/. A tárházban vannak alkönyvtárak is. 

Indítsa újra az SMT-kiszolgálót és kapcsolódó szolgáltatásait ezekkel a parancsokkal.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Csomagok letöltése SMT-kiszolgálóra

Az összes szolgáltatás újraindítása után válassza ki a megfelelő csomagokat az SMT-kezelésben a YAST használatával. A csomag kiválasztása a HANA nagyméretű példány-kiszolgáló operációs rendszerének rendszerképétől függ. A csomag kiválasztása nem függ az SMT-kiszolgálót futtató virtuális gép SLES vagy verziójától. Az alábbi képernyőképen egy példa látható a kijelölési képernyőre.

![Képernyőfelvétel a csomagok kiválasztásáról](./media/hana-installation/image10_select_packages.PNG)

Ezután indítsa el a Select Packages (csomagok kiválasztása) kezdeti másolatát a beállított SMT-kiszolgálón. Ez a másolat a rendszerhéjban a SMT-Mirror parancs használatával aktiválódik.

![Képernyőfelvétel a csomagok SMT-kiszolgálóra való letöltéséről](./media/hana-installation/image11_download_packages.PNG)

A csomagokat a csatlakoztatási pont/SRV/www/htdocs. létrehozott könyvtárakba kell másolni. Ez a folyamat akár egy órát is igénybe vehet, attól függően, hogy hány csomagot választott ki. Ahogy a folyamat befejeződik, váltson át a SMT-ügyfél telepítésére. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Az SMT-ügyfél beállítása HANA nagyméretű példány-egységeken

Az ügyfél vagy az ügyfelek ebben az esetben a HANA nagyméretű példányok egységei. Az SMT-kiszolgáló telepítője átmásolta a szkript clientSetup4SMT.sh az Azure-beli virtuális gépre. Másolja át a szkriptet a nagyméretű HANA-példány egységbe, amelyhez csatlakozni szeretne az SMT-kiszolgálóhoz. Indítsa el a szkriptet a-h kapcsolóval, és adja meg az SMT-kiszolgáló nevét paraméterként. Ebben a példában a név a következő: *smtserver*.

![Képernyőfelvétel az SMT-ügyfél konfigurálásáról](./media/hana-installation/image12_configure_client.PNG)

Lehetséges, hogy a tanúsítványnak a kiszolgálóról az ügyfél által betöltött terhelése sikeres, de a regisztráció meghiúsul, ahogy az alábbi képernyőfelvételen is látható.

![Az ügyfél-regisztrációs hiba képernyőképe](./media/hana-installation/image13_registration_failed.PNG)

Ha a regisztráció meghiúsul, tekintse meg a [SUSE támogatási dokumentumát](https://www.suse.com/de-de/support/kb/doc/?id=7006024), és futtassa az itt ismertetett lépéseket.

> [!IMPORTANT] 
> A kiszolgálónév mezőben adja meg a virtuális gép nevét (ebben az esetben *smtserver*) a teljes tartománynév nélkül. 

A lépések futtatása után futtassa a következő parancsot a HANA nagyméretű példány-egységen:

```
SUSEConnect –cleanup
```

> [!Note] 
> Várjon néhány percet a lépés után. Ha azonnal futtatja a clientSetup4SMT.sh-t, akkor előfordulhat, hogy hibaüzenetet kap.

Ha olyan problémába ütközik, amelyet a SUSE-cikk lépései alapján kell kijavítania, indítsa újra a clientSetup4SMT.sh a HANA nagyméretű példány-egységen. Most már sikeresen be kell fejeznie.

![Az ügyfél regisztrációjának sikerességét bemutató képernyőkép](./media/hana-installation/image14_finish_client_config.PNG)

Az Azure-beli virtuális gépen telepített SMT-kiszolgálóhoz való kapcsolódáshoz konfigurálta a HANA nagyméretű példány-egység SMT-ügyfelét. Most már elvégezheti a "Zypper up" vagy a "Zypper in" lehetőséget az operációs rendszer frissítéseinek a HANA nagyméretű példányokra való telepítéséhez, vagy további csomagok telepítéséhez. Csak az SMT-kiszolgálón letöltött frissítéseket lehet letölteni.

## <a name="next-steps"></a>Következő lépések
- [HANA-telepítés a HLI-on](hana-example-installation.md).











