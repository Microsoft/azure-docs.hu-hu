---
title: Az IBM&T fejlesztési és tesztelési környezet telepítése az Azure-on | Microsoft Docs
description: Az IBM Z fejlesztési és tesztelési környezet (&T) üzembe helyezése az Azure-beli virtuális gépek (VM) szolgáltatáson (IaaS) keresztül.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 32b63256f89d6d051305890c3387140f243e1a70
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954326"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Az IBM&T fejlesztési és tesztelési környezet telepítése az Azure-ban

Ha fejlesztési/tesztelési környezetet kíván létrehozni az IBM Z rendszerekben található nagyszámítógépi számítási feladatokhoz, az IBM Z fejlesztői és tesztelési környezetet (&T) az Azure Virtual Machine (VM) infrastruktúra szolgáltatásként (IaaS) helyezheti üzembe.

A (Z) és a (Z)&T-T használja a kevésbé fontos fejlesztési és tesztelési környezetek x86 platformjának megtakarítására, majd visszaküldi a frissítéseket egy Z rendszerbeli éles környezetbe. További információkért tekintse meg az [IBM&T telepítési útmutatóját](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Az Azure és a Azure Stack a következő verziókat támogatja:

- &T Personal kiadás
- &T Parallel Sysplex
- &T Enterprise kiadás

A&T összes kiadása csak x86 Linux rendszereken fut, nem Windows Server. A Enterprise Edition Red Hat Enterprise Linux (RHEL) vagy Ubuntu/Debian rendszeren támogatott. A RHEL és a Debian rendszerű virtuálisgép-lemezképek is elérhetők az Azure-ban.

Ebből a cikkből megtudhatja, hogyan állíthatja be a&T Enterprise Edition rendszert az Azure-ban, hogy a kihasználható&T Enterprise Edition webkiszolgálót használja a környezetek létrehozásához és kezeléséhez. A&T-T nem telepítheti környezetbe. Ezeket külön kell létrehoznia telepítési csomagokként. Például az Application Developers által vezérelt disztribúciók (ADCD-EK) a tesztelési környezetek mennyiségi rendszerképei. Ezeket a zip-képek tartalmazzák az IBM-től elérhető adathordozó-terjesztésben. Lásd: [ADCD-környezet beállítása az Azure](demo.md)-ban.

További információkért tekintse meg az IBM Knowledge Center adatkezelési [&T áttekintését](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) .

Ez a cikk bemutatja, hogyan állíthatja be a Z fejlesztési és tesztelési környezet (&T) Enterprise Edition rendszert az Azure-ban. Ezután használhatja a&T Enterprise Edition webkiszolgálót a Z-alapú környezetek Azure-beli létrehozásához és kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Az IBM lehetővé teszi, hogy a&T Enterprise Edition csak fejlesztési és tesztelési környezetekben legyen telepítve –*nem* éles környezetekben.

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- Hozzáférésre van szüksége az adathordozóhoz, amely csak az IBM-ügyfelek és-partnerek számára érhető el. További információért forduljon az IBM-képviselőjéhez, vagy tekintse meg a kapcsolattartási adatokat a [&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) webhelyén.

- Egy [licencelési kiszolgáló](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Ez a környezetekhez való hozzáféréshez szükséges. A Létrehozás módja attól függ, hogyan történik az IBM szoftver licence:

     - A **hardveres licencelési kiszolgálónak** olyan USB-Hardvereszközre van szüksége, amely tartalmazza a szoftver összes részének eléréséhez szükséges racionális jogkivonatokat. Ezt az IBM-től kell megszereznie.

     - A **szoftveres licencelési kiszolgálónak** be kell állítania egy központi kiszolgálót a licencelési kulcsok kezeléséhez. Ez a módszer előnyben részesített, és megköveteli az IBM-től kapott kulcsok beállítását a felügyeleti kiszolgálón.

## <a name="create-the-base-image-and-connect"></a>Az alaprendszerkép létrehozása és a kapcsolat

1. A Azure Portal-ben [hozzon létre egy virtuális gépet](../../../linux/quick-create-portal.md) a kívánt operációsrendszer-konfigurációval. Ez a cikk feltételezi, hogy a B4ms virtuális gép (4 vCPU és 16 GB memóriával) Ubuntu 16,04 rendszert futtat.

2. A virtuális gép létrehozása után nyissa meg a 22-es bejövő portokat az SSH-hoz, 21 FTP-hez és 9443-hez a webkiszolgálóhoz.

3. Szerezze be a virtuális gép **Áttekintés** paneljén látható SSH hitelesítő adatokat a **kapcsolat** gomb használatával. Válassza az **SSH** fület, és másolja az SSH-bejelentkezési parancsot a vágólapra.

4. Jelentkezzen be egy [bash-rendszerhéjba](../../../../cloud-shell/quickstart.md) a helyi számítógépről, és illessze be a parancsot. Ez az **SSH \<user id\> \@ \<IP Address\>** formában jelenik meg. Ha a rendszer kéri a hitelesítő adatok megadását, adja meg őket a saját kezdőkönyvtár kapcsolatának létrehozásához.

## <a name="copy-the-installation-file-to-the-server"></a>A telepítési fájl másolása a kiszolgálóra

A webkiszolgáló telepítési fájlja a **ZDT \_ install \_ EE \_ v 12.0.0.1. tgz**. Ez az IBM által biztosított adathordozó részét képezi. Ezt a fájlt fel kell töltenie Ubuntu rendszerű virtuális gépre.

1. A parancssorban adja meg a következő parancsot, hogy minden naprakész legyen az újonnan létrehozott rendszerképben:

    ```
    sudo apt-get update
    ```

2. Hozza létre a telepítendő könyvtárat:

    ```
    mkdir ZDT
    ```

3. Másolja a fájlt a helyi gépről a virtuális gépre:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Ez a parancs a saját kezdőkönyvtár ZDT könyvtárába másolja a telepítőfájlt, amely attól függően változik, hogy az ügyfél Windows vagy Linux rendszert futtat-e.

## <a name="install-the-enterprise-edition"></a>Az Enterprise Edition telepítése

1. Nyissa meg a ZDT könyvtárat, és bontsa ki a ZDT \_ install \_ EE \_ v 12.0.0.1. tgz fájlt a következő parancsokkal:

    ```
    cd ZDT
    tar zxvf ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Futtassa a telepítőt:

    ```
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.x86_64
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Válassza az **1** lehetőséget a vállalati kiszolgáló telepítéséhez.

4. Nyomja le az **ENTER** billentyűt, és figyelmesen olvassa el a licencszerződéseket. A licenc végén adja meg az **Igen** értéket a folytatáshoz.

5. Amikor a rendszer felszólítja az újonnan létrehozott felhasználó jelszavának módosítására, **ibmsys1** használja a **sudo passwd ibmsys1** parancsot, és adja meg az új jelszót.

6. Annak ellenőrzése, hogy a telepítés sikeres volt-e

    ```
    dpkg -l | grep zdtapp
    ```

7. Ellenőrizze, hogy a kimenet tartalmazza-e a **zdtapp 12.0.0.0** karakterláncot, amely azt jelzi, hogy a csomag gázzal való telepítése sikerült

### <a name="starting-enterprise-edition"></a>Enterprise kiadás indítása

Ne feledje, hogy a webkiszolgáló indításakor a rendszer a telepítési folyamat során létrehozott&T felhasználói azonosító alatt fut.

1. A webkiszolgáló elindításához használja a root user azonosítót a következő parancs futtatásához:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Másolja ki az URL-kimenetet a parancsfájlból, amely a következőhöz hasonló:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Illessze be az URL-címet egy webböngészőbe, és nyissa meg a felügyeleti összetevőt a&T-telepítéshez.

## <a name="next-steps"></a>Következő lépések

[Alkalmazás-fejlesztőknek vezérelt terjesztés (ADCD) beállítása az IBM&T v1-ben](./demo.md)
