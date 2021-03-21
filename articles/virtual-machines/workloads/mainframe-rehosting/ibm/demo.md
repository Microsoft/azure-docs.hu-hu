---
title: Alkalmazás-fejlesztőknek szóló felügyelt terjesztés (ADCD) beállítása az IBM&T v1-ben | Microsoft Docs
description: Futtasson egy IBM Z fejlesztési és tesztelési környezetet (&T) az Azure Virtual Machines (VM) környezetben.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: workloads
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: d390cca483444dadd278d783c5f93e09a1bf1ff8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549447"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Alkalmazás-fejlesztőknek vezérelt terjesztés (ADCD) beállítása az IBM&T v1-ben

Futtathatja az IBM Z fejlesztési és tesztelési környezet (&T) környezetét az Azure Virtual Machines (VM) használatával. Ez a környezet emulálja az IBM Z Series architektúrát. Számos különböző, Z sorozatú operációs rendszert vagy telepítést (más néven Z példányokat vagy csomagokat) tartalmazhat, amelyeket az IBM Application Developers (ADCDs) által felügyelt disztribúcióknak nevezett testreszabott csomagokon keresztül lehet elérni.

Ez a cikk bemutatja, hogyan állíthat be egy ADCD-példányt az Azure-beli&T-környezetben. A ADCDs a teljes Z sorozatú operációs rendszer megvalósítását hozza létre a&T-ben futtatott fejlesztési és tesztelési környezetekhez.

Mint például a&T ADCDs, csak az IBM-ügyfelek és-partnerek számára érhető el, és kizárólag fejlesztési és tesztelési célokra használhatók. Nem használhatók éles környezetekben. Számos IBM-es telepítőcsomag letölthető a [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) vagy az [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- Az Azure-ban korábban beállított [&T-környezet][ibm-install-z] . Ez a cikk azt feltételezi, hogy a korábban létrehozott Ubuntu 16,04 VM-rendszerképet használja.

- Hozzáférés az ADCD-adathordozóhoz az IBM PartnerWorld vagy a Passport Advantage használatával.

- Egy [licencelési kiszolgáló](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Ez az IBM&T-nek a futtatásához szükséges. A Létrehozás módja attól függ, hogyan történik az IBM szoftver licence:

  - A **hardveres licencelési kiszolgálónak** olyan USB-Hardvereszközre van szüksége, amely tartalmazza a szoftver összes részének eléréséhez szükséges racionális jogkivonatokat. Ezt az IBM-től kell megszereznie.

  - A **szoftveres licencelési kiszolgálónak** be kell állítania egy központi kiszolgálót a licencelési kulcsok kezeléséhez. Ez a módszer előnyben részesített, és megköveteli az IBM-től kapott kulcsok beállítását a felügyeleti kiszolgálón.

## <a name="download-the-installation-packages-from-passport-advantage"></a>A telepítési csomagok letöltése a Passport Advantage-ből

A ADCD adathordozóhoz való hozzáférés szükséges. Az alábbi lépések feltételezik, hogy Ön IBM-ügyfél, és használhatja a Passport előnyeit. Az IBM-partnerek használhatják az [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)-t.

> [!NOTE]
> Ez a cikk azt feltételezi, hogy a Windows rendszerű számítógépek a Azure Portal és az IBM-adathordozó letöltéséhez használhatók. Ha Mac-vagy Ubuntu-asztalt használ, az IBM-es adathordozó beszerzéséhez szükséges parancsok és folyamatok némileg eltérőek lehetnek.

1. Jelentkezzen be a [Passport-előnybe](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Válassza a **szoftverletöltések** és a **média-hozzáférés** lehetőséget.

3. Válassza a **program felajánlása és a szerződés száma** lehetőséget, majd kattintson a **Folytatás** gombra.

4. Adja meg a rész leírását vagy a cikkszám értéket, majd kattintson a **Finder** elemre.

5. Ha a termék neve alapján szeretné megjeleníteni és megtekinteni, kattintson a betűrendes sorrend listára.

6. Válassza ki az **összes operációs** rendszert az **operációs rendszer mezőben**, és a **nyelvek mező** **összes nyelvét** . Ezután kattintson az **Indítás** gombra.

7. Az **egyes fájlok kijelölése** elemre kattintva bontsa ki a listát, és jelenítse meg a letölteni kívánt adathordozót.

8. Ellenőrizze a letölteni kívánt csomag (oka) t, válassza a **Letöltés** lehetőséget, majd töltse le a fájlokat a kívánt könyvtárba.

## <a name="upload-the-adcd-packages"></a>A ADCD-csomag (ok) feltöltése

Most, hogy már rendelkezik a csomaggal, fel kell töltenie őket a virtuális gépre az Azure-ban.

1. A Azure Portal kezdeményez egy **SSH** -munkamenetet a létrehozott Ubuntu virtuális géppel. Nyissa meg a virtuális gépet, válassza az **Áttekintés** panelt, majd válassza a **Kapcsolódás** lehetőséget.

2. Válassza az **SSH** fület, majd másolja az SSH-parancsot a vágólapra.

3. Jelentkezzen be a virtuális gépre a hitelesítő adatai és a választott [SSH-ügyfél](../../../linux/use-remote-desktop.md) használatával. Ez a bemutató a Windows 10 rendszerhez készült Linux-bővítményeket használja, amely egy bash-felületet helyez el a Windows-parancssorba. A PuTTY is ugyanúgy működik.

4. Amikor bejelentkezett, hozzon létre egy könyvtárat az IBM-csomagok feltöltéséhez. Ne feledje, hogy a Linux kis-és nagybetűket megkülönböztet. Ez a bemutató például azt feltételezi, hogy a csomagok feltöltése a következőre történik:

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. Töltse fel a fájlokat egy SSH-ügyfél, például a[megnyerő](https://winscp.net/eng/index.php)használatával. Mivel az SCP az SSH része, a 22-es portot használja, amely az SSH-t használja. Ha a helyi számítógép nem Windows, beírhatja az [SCP-parancsot](http://man7.org/linux/man-pages/man1/scp.1.html) az SSH-munkamenetbe.

6. Kezdeményezzen feltöltést az Ön által létrehozott Azure-beli virtuálisgép-könyvtárba, amely a&T-T tartalmazó rendszerkép-tárterület lesz.

    > [!NOTE]
    > Győződjön meg arról, hogy **ADCDTOOLS.XML** szerepel a **Home/MyUserID/ZDT/adcd/nov2017** könyvtárba való feltöltésben. Erre később még szüksége lesz.

7. Várjon, amíg feltölti a fájlokat, ami az Azure-hoz való kapcsolódástól függően hosszabb időt is igénybe vehet.

8. A feltöltések befejezése után navigáljon a kötetek könyvtárba, és bontsa ki az összes **gz** -kötetet:

    ```console
    gunzip \*.gz
    ```
    
![A detömörített gz-köteteket megjelenítő fájlkezelő](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>A rendszerkép-tároló konfigurálása

A következő lépés, hogy a&T-T konfigurálja a feltöltött csomag (ok) használatára. A&T-n belüli lemezkép-tárolási folyamat lehetővé teszi a lemezképek csatlakoztatását és használatát. Használhatja az SSH-t vagy az FTP-t.

1. Indítsa el a **zDTServer**. Ehhez a legfelső szintű szinten kell lennie. Adja meg a következő két parancsot sorrendben:
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. Jegyezze fel a parancs URL-címét, és használja ezt az URL-címet a webkiszolgáló eléréséhez. A következőhöz hasonlóan néz ki:
     > https://(a virtuális gép neve vagy IP-címe): 9443/ZDTMC/index.html
     >
     > Ne feledje, hogy a webes elérés a 9443-es portot használja. Ezzel a szolgáltatással jelentkezhet be a webkiszolgálóra. A **zdtadmin** felhasználói azonosítója&T, a jelszó pedig **jelszó**.

    ![IBM&T Enterprise Edition üdvözlő képernyő](media/02-welcome.png)

3. A **gyorskonfigurálás** lap **Konfigurálás** területén válassza a **képtároló** elemet.

     ![IBM&T Enterprise Edition gyorskonfigurálás képernyő](media/03-quickstart.png)

4. A **rendszerkép-tároló konfigurálása** lapon válassza az **SSH File Transfer Protocol** lehetőséget.

5. Az **állomásnév** mezőbe írja be a **localhost** nevet, és adja meg a könyvtár elérési útját, ahová a lemezképeket feltöltötte. Például:/home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Adja meg a virtuális gép **felhasználói azonosítóját** és **jelszavát** . Ne használja a&T felhasználói AZONOSÍTÓját és jelszavát.

7. Tesztelje a kapcsolatot, és győződjön meg arról, hogy rendelkezik hozzáféréssel, majd válassza a **Mentés** lehetőséget a konfiguráció mentéséhez.

## <a name="configure-the-target-environments"></a>A célként megadott környezetek konfigurálása

A következő lépés, hogy konfigurálja a&T-T. Ez az emulált üzemeltetett környezet, ahol a lemezképek futnak.

1. A **gyorskonfigurálás** lapon a **Konfigurálás** területen válassza a **célként szolgáló környezetek** lehetőséget.

2. A **cél környezetek konfigurálása** lapon válassza a **cél hozzáadása** elemet.

3. Válassza a **Linux** lehetőséget. Az IBM a következő két típusú környezetet támogatja: Linux és Cloud (OpenStack), de ez a bemutató Linuxon fut.

4. A **cél környezet hozzáadása** lapon az Állomásnév mezőbe írja be a **localhost** **nevet**. Tartsa meg az **SSH-portot** **22**-re állítva.

5. A **cél környezet címkéje** mezőbe írjon be egy címkét, például **MyCICS.**

     ![Cél környezeti képernyő hozzáadása](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD konfigurálása és üzembe helyezése

Az előző konfigurációs lépések végrehajtása után be kell állítania a&T-T a csomag (ok) és a célként megadott környezet használatára. Újra használhatja a lemezkép-tárolási folyamatot a&T-ben, amely lehetővé teszi a lemezképek csatlakoztatását és használatát. Használhatja az SSH-t vagy az FTP-t.

1. A **gyorskonfigurálás** lap **Konfigurálás** területén válassza a **ADCD** lehetőséget. Megjelenik az utasítások egy készlete, amelyből megtudhatja, milyen lépéseket kell végrehajtania ahhoz, hogy egy ADCD-csomagot csatlakoztatni lehessen. Ez elmagyarázza, hogy miért nevezték el a cél könyvtárat, ahogy azt korábban tettük.

2. Feltéve, hogy az összes rendszerkép fel lett töltve a megfelelő könyvtárakba, kattintson a jobb alsó sarokban látható **ADCD** -hivatkozásra (a következő képernyőképen látható 7. lépés).

     ![IBM&T Enterprise Edition – a ADCD konfigurálása képernyő](media/05-adcd.png)

## <a name="create-the-image"></a>A rendszerkép létrehozása

Amikor az előző konfigurációs lépés befejeződik, megjelenik a **rendszerkép létrehozása ADCD Components használatával** oldal.

1. A köteten lévő különböző csomagok megjelenítéséhez válassza ki a kötetet (ebben az esetben november 2017).

2. Ebben a bemutatóban válassza az **Customer Information Control System (CICS)-5,3** lehetőséget.

3. A **rendszerkép neve** mezőbe írja be a rendszerkép nevét (például **MyCICS-rendszerkép**).

4. A jobb alsó sarokban kattintson a **rendszerkép létrehozása** gombra.

     ![IBM&T Enterprise Edition – rendszerkép létrehozása a ADCD Components képernyő használatával](media/06-adcd.png)

5. A megjelenő ablakban a lemezkép központi telepítését követően válassza a **lemezképek telepítése** lehetőséget.

6. A **rendszerkép üzembe helyezése a célszámítógépen** lapon válassza ki az előző oldalon létrehozott rendszerképet (**MyCICS-rendszerkép**) és a korábban létrehozott célként megadott környezetet (**MyCICS**).

7. A következő képernyőn adja meg a virtuális gép hitelesítő adatait (azaz nem a ztadmin hitelesítő adatait).

8. A Tulajdonságok ablaktáblán adja meg a **központi processzorok (CPS)** számát, a **rendszermemória (GB)** mennyiségét, valamint a futó rendszerkép **központi telepítési könyvtárát** . Mivel ez egy bemutató, kis méretűnek kell maradnia.

9. Győződjön meg arról, hogy a jelölőnégyzet be van jelölve a **telepítés után az IPL-parancs automatikus kijavításához a z/os-** re.

     ![Tulajdonságok képernyő](media/07-properties.png)

10. Válassza a **Befejezés** lehetőséget.

11. Válassza a **rendszerkép** központi telepítése a **lemezkép üzembe helyezése a cél környezetbe** lapra lehetőséget.

A lemezkép most már üzembe helyezhető, és készen áll arra, hogy csatlakoztatja a 3270-es terminál-emulátort.

> [!NOTE]
> Ha olyan hibaüzenetet kap, amely nem rendelkezik elegendő lemezterülettel, vegye figyelembe, hogy a régióhoz 151 GB szükséges.

Gratulálunk! Mostantól egy IBM mainframe-környezetet futtat az Azure-ban.

## <a name="learn-more"></a>Tudjon meg többet

- [Mainframe-áttelepítés: mítoszok és tények](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2-pureScale az Azure-ban](ibm-db2-purescale-azure.md)
- [Hibaelhárítás](../../../troubleshooting/index.yml)
- [Demystifying mainframe – Azure Migrálás](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
