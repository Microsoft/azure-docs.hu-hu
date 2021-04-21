---
title: Defender for IoT telepítése
description: Ismerje meg, hogyan telepíthet érzékelőt és a helyszíni felügyeleti konzolt a Azure Defender for IoT.
ms.date: 4/20/2021
ms.topic: how-to
ms.openlocfilehash: e8366a3408e64d95e6c4d50e3ddef84309b4e8e5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829732"
---
# <a name="defender-for-iot-installation"></a>Defender for IoT telepítése

Ez a cikk a következő elemek telepítését ismerteti Azure Defender for IoT:

- **Érzékelő:** A Defender for IoT-érzékelők passzív (ügynök nélküli) monitorozással gyűjtik az ICS hálózati forgalmát. Passzív és nem zavaró, az érzékelőknek nincs hatása az ot- és IoT-hálózatokra és -eszközökre. Az érzékelő egy SPAN-porthoz vagy hálózati TAP-hoz csatlakozik, és azonnal megkezdi a hálózat monitorozását. Az észlelések megjelennek az érzékelő konzolján. Itt megtekintheti, megvizsgálhatja és elemezheti őket egy hálózati térképen, eszközleltárban és számos jelentésben. Ilyenek például a kockázatfelmérési jelentések, az adatbányászati lekérdezések és a támadási vektorok. További információ az érzékelők képességeiről: [Defender for IoT Sensor Felhasználói útmutató (közvetlen letöltés).](./getting-started.md)

- **Helyszíni felügyeleti konzol:** A helyszíni felügyeleti konzol lehetővé teszi az eszközkezelés, a kockázatkezelés és a biztonsági rések kezelését. Arra is használhatja, hogy a fenyegetésfigyelés és az incidensek válaszlépése a teljes vállalatnál el tudja végezni. Egységes nézetet biztosít az érzékelőket üzembe helyező létesítményekben észlelt összes hálózati eszközről, kulcsfontosságú IoT-ről és ot-kockázati jelzőről és riasztásról. A helyszíni felügyeleti konzollal megtekintheti és kezelheti a air-gaped hálózatok érzékelőit.

Ez a cikk a következő telepítési információkat tartalmazza:

  - **Hardver:** Dell és HPE fizikai berendezés adatai.

  - **Szoftver:** Az érzékelő és a helyszíni felügyeleti konzol szoftverének telepítése.

  - **Virtuális berendezések:** A virtuális gép részletei és a szoftverek telepítése.

A telepítés után csatlakoztassa az érzékelőt a hálózathoz.

## <a name="about-defender-for-iot-appliances"></a>A Defender for IoT-berendezésekről 

A következő szakaszok a Defender for IoT érzékelőkészülékekkel és a Defender for IoT helyszíni felügyeleti konzol készülékével kapcsolatos információkat tartalmaznak.

### <a name="physical-appliances"></a>Fizikai berendezések

A Defender for IoT-berendezés érzékelője egy SPAN-porthoz vagy hálózati TAP-hoz csatlakozik, és azonnal elkezdi gyűjteni az ICS hálózati forgalmát passzív (ügynök nélküli) monitorozással. Ez a folyamat nincs hatással az ot-hálózatokra és eszközökre, mert nem kerül az adatútvonalra, és nem vizsgálja aktívan az ot-eszközöket.

A következő állványra szerelhető berendezések érhetők el:

| **Központi telepítési típus** | **Vállalat** | **Vállalat** | **SMB** | **Vonal** |
|--|--|--|--|--|
| **Modellezés** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Portok figyelése** | legfeljebb 15 RJ45 vagy 8 OPT | legfeljebb 9 RJ45 vagy 6 OPT | legfeljebb 8 RJ45 vagy 6 OPT | 4 RJ45 |
| **Maximális sávszélesség\*** | 3 Gb/s | 1 Gb/s | 1 Gb/s | 100 MB/s |
| **Védett eszközök maximális** | 30,000 | 10,000 | 15 000 | 1,000 |

*A maximális sávszélesség a protokollterjesztéstől függően változhat.

### <a name="virtual-appliances"></a>Virtuális készülékek

A következő virtuális berendezések érhetők el:

| **Központi telepítési típus** | **Vállalat** | **SMB** | **Vonal** |
|--|--|--|--|
| **Leírás** | Virtuális berendezés vállalati üzembe helyezéshez | Virtuális berendezés SMB üzembe helyezéséhez | Virtuális berendezés vonaltelepítéshez |
| **Maximális sávszélesség\*** | 150 Mb/s | 15 Mb/s | 3 Mb/s |
| **Védett eszközök maximális** | 3,000 | 300 | 100 |
| **Központi telepítési típus** | Enterprise | SMB | Vonal |
| **Leírás** | Virtuális berendezés vállalati üzembe helyezéshez | Virtuális berendezés SMB üzembe helyezéséhez | Virtuális berendezés vonaltelepítéshez |

*A maximális sávszélesség a protokollterjesztéstől függően változhat.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>A helyszíni felügyeleti konzol hardversifikációi

 | Elem | Leírás |
 |----|--|
 **Leírás** | A többrétegű architektúrában a helyszíni felügyeleti konzol biztosítja a földrajzilag elosztott helyek láthatóságát és felügyeletét. Integrálható az SOC biztonsági vermekkel, beleértve a SIEM-eket, a jegykiadó rendszereket, az új generációs tűzfalakat, a biztonságos távelérési platformokat és a Defender for IoT ICS kártevő-védőfalat. |
 **Központi telepítési típus** | Enterprise |
 **Berendezés típusa**  | Dell R340, VM |
 **Felügyelt érzékelők száma** | Korlátlan |

## <a name="prepare-for-the-installation"></a>Felkészülés a telepítésre

### <a name="access-the-iso-installation-image"></a>Hozzáférés az ISO-telepítési lemezképhez

A telepítési lemezkép a Defender for IoT portálról érhető el.

A fájl elérése:

1. Jelentkezzen be a Defender for IoT-fiókjába.

1. A Hálózati **érzékelő vagy** a **Helyszíni** felügyeleti konzol oldalon válassza ki a letölteni kívánt verziót.

### <a name="install-from-dvd"></a>Telepítés DVD-ről

A telepítés előtt győződjön meg a következőről:

- Hordozható DVD-meghajtó AZ USB-csatlakozóval.

- Iso-telepítő lemezképe.

A telepítéshez:

1. A lemezkép írása DVD-re, vagy lemez előkészítése egy kulcson. Csatlakoztassa a hordozható DVD-meghajtót a számítógépéhez, kattintson a jobb gombbal az ISO-lemezképre, és válassza az **Írás lemezre lehetőséget.**

1. Csatlakoztassa a DVD-t vagy a lemezt egy kulcshoz, és konfigurálja a berendezést úgy, hogy egy kulcs DVD-ről vagy lemezről induljon.

### <a name="install-from-disk-on-a-key"></a>Telepítés lemezről kulcson

A telepítés előtt győződjön meg a következőről:

  - Telepített Rufus.
  
  - Lemez a kulcson 3.0-s és újabb USB-verzióval. A minimális méret 4 GB.

  - Iso-telepítő lemezképfájlja.

Ebben a folyamatban a kulcson lévő lemez törlődik.

Lemez előkészítése kulcson:

1. Futtassa a Rufust, és válassza az **SENSOR ISO lehetőséget.**

1. Csatlakoztassa a kulcson található lemezt az előlaphoz.

1. Állítsa be a kiszolgáló BIOS-ját az USB-ről való rendszerindításra.

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL telepítés

Mielőtt a szoftvert a Dell-berendezésre telepítené, módosítania kell a berendezés BIOS-konfigurációját:

  - [Dell PowerEdge R340 Front Panel](#dell-poweredge-r340-front-panel) és [Dell PowerEdge R340 Back Panel](#dell-poweredge-r340-back-panel) tartalmazza az elő- és háttérpanelek leírását, valamint a telepítéshez szükséges információkat, például illesztőprogramokat és portokat.

  - [A Dell BIOS Configuration](#dell-bios-configuration) a Dell berendezés felügyeleti felületéhez való csatlakozásról és a BIOS konfigurálásról nyújt információt.

  - [A Szoftvertelepítés (Dell R340) ismerteti](#software-installation-dell-r340) a Defender for IoT érzékelőszoftver telepítéséhez szükséges eljárást.

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL-követelmények 

A Dell PowerEdge R340XL berendezés telepítéséhez a következő szükséges:

- Vállalati licenc a Dell távelérés-vezérlőhöz (iDrac)

- BIOS-konfigurációs XML

- A kiszolgáló belső vezérlőprogramjának verziói:

  - BIOS 2.1.6-os verzió

  - iDrac 3.23.23.23-as verzió

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 előlap

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 előlap.":::

 1. Bal oldali vezérlőpult 
 1. Optikai meghajtó (nem kötelező) 
 1. Jobb vezérlőpanel 
 1. Információs címke 
 1. Meghajtók  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 back panel

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 háttérpanel.":::

1. Soros port 
1. Hálózati adapter portja (Gb 1) 
1. Hálózati adapter portja (Gb 1) 
1. Félmagasságú PCIe 
1. Teljes magasságú PCIe bővítőkártya-tárolóhely 
1. 1. tápegység 
1. 2. tápegység 
1. Rendszerazonosítás 
1. Rendszerállapot-jelző kábelport (CMA) gomb 
1. USB 3.0-port (2) 
1. iDRAC9 dedikált hálózati port 
1. VGA-port 

### <a name="dell-bios-configuration"></a>Dell BIOS-konfiguráció

A Dell BIOS konfigurációja szükséges ahhoz, hogy a Dell berendezés működjön a szoftverrel.

A BIOS-konfiguráció előre meghatározott konfigurációval történik. A fájl a [Súgóközpontból érhető el.](https://help.cyberx-labs.com/)

Importálja a konfigurációs fájlt a Dell berendezésre. A konfigurációs fájl használata előtt létre kell hoznia a kommunikációt a Dell berendezés és a felügyeleti számítógép között.

A Dell-berendezést egy integrált iDRAC kezeli életciklus-vezérlővel (LC). Az LC minden Dell PowerEdge-kiszolgálóba be van ágyazva, és olyan funkciókat biztosít, amelyek segítségével üzembe helyezheti, frissítheti, figyelheti és karbantarthatja Dell PowerEdge-berendezéseit.

A Dell-berendezés és a felügyeleti számítógép közötti kommunikáció létesítéséhez meg kell határoznia az iDRAC IP-címét és a felügyeleti számítógép IP-címét ugyanazon az alhálózaton.

A kapcsolat létrejöttekor a BIOS konfigurálható.

A Dell BIOS konfigurálása:

1. [Az iDRAC IP-cím konfigurálása](#configure-idrac-ip-address)

1. [A BIOS konfigurációs fájljának importálása](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>iDRAC IP-cím konfigurálása

1. Az érzékelő tápellátása.

1. Ha az operációs rendszer már telepítve van, az F2 billentyűvel adja meg a BIOS-konfigurációt.

1. Válassza **az iDRAC-beállítások lehetőséget.**

1. Válassza a **Hálózat lehetőséget.**

   > [!NOTE]
   > A telepítés során konfigurálnia kell a következő lépésekben említett alapértelmezett iDRAC IP-címet és jelszót. A telepítés után módosítania kell ezeket a definíciókat.

1. Módosítsa a statikus IPv4-címet **a következőre: 10.100.100.250.**

1. Módosítsa a statikus alhálózati maszkot **a következőre: 255.255.255.0.**

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="A statikus alhálózati maszkot bemutató képernyőkép.":::

1. Válassza a Back Finish   >  **(Vissza befejezés) lehetőséget.**

#### <a name="import-the-bios-configuration-file"></a>A BIOS konfigurációs fájljának importálása

Ez a cikk bemutatja, hogyan konfigurálhatja a BIOS-t a konfigurációs fájl használatával.

1. Csatlakoztassa a **10.100.100.200** IP-címmel konfigurált statikus IP-címet az **iDRAC-porthoz.**

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Az előre konfigurált IP-címport képernyőképe.":::

1. Nyisson meg egy böngészőt, és írja be a **10.100.100.250** webhelyet az iDRAC webes felülethez való csatlakozáshoz.

1. Jelentkezzen be a Dell alapértelmezett rendszergazdai jogosultságai szerint:

   - Felhasználónév: **root**

   - Jelszó: 

1. A berendezés hitelesítő adatai a következőek:

   - Felhasználónév: **XXX**

   - Jelszó: **XXX**

     Megkezdődött az importálási kiszolgáló profilművelete.

     > [!NOTE]
     > A fájl importálása előtt győződjön meg a következőről:
     > - Ön az egyetlen felhasználó, aki jelenleg csatlakozik az iDRAC-hez.
     > - A rendszer nem található a BIOS menüben.

1. Ugrás a **Konfigurációs kiszolgáló**  >  **konfigurációs profiljára.** Állítsa be a következő paramétereket:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="A kiszolgálóprofil konfigurációját bemutató képernyőkép.":::

   | Paraméter | Konfiguráció |
   |--|--|
   | Hely típusa | Válassza a **Helyi lehetőséget.** |
   | Fájl elérési útja | Válassza **a Fájl kiválasztása lehetőséget,** és adja hozzá a konfigurációs XML-fájlt. |
   | Összetevők importálása | Válassza a **BIOS, NIC, RAID lehetőséget.** |
   | Maximális várakozási idő | Válassza **a 20 perc lehetőséget.** |

1. Válassza az **Importálás** lehetőséget.

1. A folyamat figyelése érdekében a Karbantartási feladat  >  **várólistája részen követheti nyomon a következőt:**.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Képernyőkép a feladat-üzenetsorról.":::

#### <a name="manually-configuring-bios"></a>A BIOS manuális konfigurálása 

A berendezés BIOS-át manuálisan kell konfigurálnia, ha:

- A berendezést nem a Nyílból vásárolta meg.

- Rendelkezik egy berendezéssel, de nem rendelkezik hozzáféréssel az XML-konfigurációs fájlhoz.

A BIOS elérése után kattintson az **Eszközbeállítások elemre.**

Manuális konfigurálás:

1. A berendezés BIOS-ának elérése közvetlenül billentyűzettel és képernyővel, vagy iDRAC használatával.

   - Ha a berendezés nem Defender for IoT-berendezés, nyisson meg egy böngészőt, és nyissa meg a korábban konfigurált IP-címet. Jelentkezzen be a Dell alapértelmezett rendszergazdai jogosultságokkal. A **jelszóhoz** használja a gyökérnevet, a **jelszóhoz** pedig a rootot.

   - Ha a berendezés Egy Defender for IoT-berendezés, jelentkezzen be **xxx** felhasználónévvel és **XXX** jelszóval.

1. A BIOS elérése után kattintson az **Eszközbeállítások elemre.**

1. A RAID által vezérelt konfiguráció kiválasztásához válassza az **Integrated RAID controller 1: Dell PERC Configuration Utility (1. integrált RAID-vezérlő: Dell PERC \<PERC H330 Adapter\> konfigurációs segédprogram) lehetőséget.**

1. Válassza **a Konfigurációkezelés lehetőséget.**

1. Válassza **a Virtuális lemez létrehozása lehetőséget.**

1. A **RAID-szint kiválasztása mezőben** válassza a **RAID5 lehetőséget.** A Virtuális **lemez neve mezőbe írja be** a **ROOT** nevet, majd válassza a Fizikai **lemezek lehetőséget.**

1. Válassza **az Összes ellenőrzése,** majd a **Módosítások alkalmazása lehetőséget.**

1. Kattintson az **OK** gombra.

1. Görgessen lefelé, és válassza **a Virtuális lemez létrehozása lehetőséget.**

1. Jelölje be **a Megerősítés** jelölőnégyzetet, és válassza az **Igen lehetőséget.**

1. Válassza az **OK** lehetőséget.

1. Térjen vissza a fő képernyőre, és válassza a **Rendszer BIOS lehetőséget.**

1. Válassza **a Rendszerindítási beállítások lehetőséget.**

1. A **Rendszerindítási mód beállításhoz** válassza a **BIOS lehetőséget.**

1. Válassza **a Back (Vissza)** lehetőséget, majd **a Finish (Befejezés)** gombot a BIOS-beállításokból való kilépéshez.

### <a name="software-installation-dell-r340"></a>Szoftvertelepítés (Dell R340)

A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször újraindul.

A telepítéshez:

1. Ellenőrizze, hogy a verzió adathordozója a következő módokon van-e csatlakoztatva a berendezéshez:

   - Csatlakoztassa a külső CD-t vagy lemezt egy kulcson a kiadással.

   - Csatlakoztassa az ISO-lemezképet az iDRAC használatával. Miután bejelentkezett az iDRAC-be, válassza ki a virtuális konzolt, majd válassza a **Virtuális média lehetőséget.**

1. A **MAP CD/DVD (CD/DVD leképezés) szakaszban** válassza a **Choose File (Fájl kiválasztása) lehetőséget.**

1. A megnyíló párbeszédpanelen válassza ki a verzió ISO-lemezképfájlját.

1. Válassza az **Eszköz leképezés gombját.**

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="A leképezett eszközt bemutató képernyőkép.":::

1. Az adathordozó csatlakoztatva van. Válassza a **Bezárás** gombot.

1. Indítsa el a berendezést. Az iDRAC használata esetén a **Consul Control (Consul Control)** gombra kattintva újraindíthatja a kiszolgálókat. Ezután a **Billentyűzet makrói menüben** válassza az Alkalmaz gombot, amely elindítja a Ctrl+Alt+Delete billentyűkombinációt. 

1. Válassza az **Angol lehetőséget.**

1. Válassza **a SENSOR-RELEASE- \<version\> Enterprise lehetőséget.**

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Válassza ki az érzékelő verzióját és a vállalati típust.":::   

1. Adja meg a berendezésprofilt és a hálózati tulajdonságokat:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Képernyőkép a berendezésprofilról és a hálózati tulajdonságokról.":::   

   | Paraméter | Konfiguráció |
   |--|--|
   | **Hardverprofil** | **Vállalati** |
   | **Felügyeleti felület** | **eno1** |
   | **Hálózati paraméterek (az ügyfél által megadott)** | - |
   |**felügyeleti hálózat IP-címe:** | - |
   | **alhálózati maszk:** | - |
   | **berendezés állomásneve:** | - |
   | **Dns:** | - |
   | **alapértelmezett átjáró IP-címe:** | - |
   | **bemeneti felületek:** |  A rendszer létrehozza a bemeneti interfészek listáját. A bemeneti felületek tükrözéséhez másolja a listában megjelenő összes elemet egy vesszővel elválasztva. Nem kell konfigurálnia a hídillesztőt. Ez a lehetőség csak speciális esetekben használható. |

1. Körülbelül 10 perc múlva megjelenik a két hitelesítőadat-készlet. Az egyik a **CyberX-felhasználóknak,** a másik a Támogatási **felhasználónak.**  

1. Mentse a berendezés azonosítóját és jelszavát. Ezekre a hitelesítő adatokra szüksége lesz a platform első használatakor való hozzáféréshez.

1. A **folytatáshoz válassza az Enter** billentyűt.

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20 telepítés

Ez a cikk a HPE ProLiant DL20 telepítési folyamatot ismerteti, amely a következő lépéseket tartalmazza:

  - Engedélyezze a távelérést, és frissítse az alapértelmezett rendszergazdai jelszót.
  - A BIOS és a RAID beállításainak konfigurálása.
  - Telepítse a szoftvert.

### <a name="about-the-installation"></a>A telepítésről

  - Vállalati és SMB-berendezések telepíthetők. A telepítési folyamat mindkét berendezéstípus esetében azonos, kivéve a tömbkonfigurációt.
  - A rendszer egy alapértelmezett rendszergazda felhasználót ad meg. Javasoljuk, hogy a hálózati konfigurációs folyamat során módosítsa a jelszót.
  - A hálózati konfigurációs folyamat során az iLO-portot az 1. hálózati porton kell konfigurálni.
  - A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször újraindul.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20 előlap

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 előlap.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 háttérpanel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="A HPE ProLiant DL20 háttérpanelje.":::

### <a name="enable-remote-access-and-update-the-password"></a>Távelérés engedélyezése és a jelszó frissítése

Az alábbi eljárással állíthatja be a hálózati beállításokat, és frissítheti az alapértelmezett jelszót.

A jelszó engedélyezése és frissítése:

1. Csatlakoztassa a képernyőt és a billentyűzetet a HP-berendezéshez, kapcsolja be a berendezést, majd nyomja le az **F9 billentyűt.**

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="A HPE ProLiant ablakot bemutató képernyőkép.":::

1. A System **Utilities**  >  **System Configuration**  >  iLO 5 Configuration Utility Network Options **(ILO 5 konfigurációs segédprogram hálózati**  >  **beállításai) menüben.**

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="A Rendszerkonfiguráció ablakot bemutató képernyőkép.":::

    1.  A Hálózati adapter adapter mezőben válassza a Megosztott **hálózati** **port-LOM** lehetőséget.
    
    1.  Tiltsa le a DHCP-t.
    
    1.  Adja meg az IP-címet, az alhálózati maszkot és az átjáró IP-címét.

1. Válassza **az F10: Mentés lehetőséget.**

1. Válassza **az Esc lehetőséget** az **iLO 5** konfigurációs segédprogramhoz való csatlakozáshoz, majd válassza a **Felhasználókezelés lehetőséget.**

1. Válassza **a Felhasználó szerkesztése/eltávolítása lehetőséget.** A rendszergazda az egyetlen alapértelmezett felhasználó, aki definiálva van. 

1. Módosítsa az alapértelmezett jelszót, és válassza **az F10: Mentés lehetőséget.**

### <a name="configure-the-hpe-bios"></a>A HPE BIOS konfigurálása

Az alábbi eljárás bemutatja, hogyan konfigurálhatja a HPE BIOS-t a vállalat és az SMB-berendezések számára.

A HPE BIOS konfigurálása:

1. Válassza **a System Utilities**  >  **System Configuration**  >  **BIOS/Platform Configuration (RBSU) lehetőséget.**

1. A **BIOS/Platformkonfiguráció (RBSU) űrlapon** válassza a **Rendszerindítási beállítások lehetőséget.**

1. Módosítsa **a Rendszerindítási** módot **örökölt BIOS módra,** majd válassza **az F10: Mentés lehetőséget.**

1. Kattintson **kétszer az Esc** gombra a Rendszerkonfiguráció űrlap **bezárulásával.**

#### <a name="for-the-enterprise-appliance"></a>A vállalati berendezéshez

1. Válassza **az Embedded RAID 1: HPE Smart Array P408i-a SR Gen 10** Array  >  **Configuration** Create  >  **Array lehetőséget.**

1. A **Tömb létrehozása űrlapon** válassza ki az összes lehetőséget. A vállalati berendezéshez három **lehetőség áll** rendelkezésre.

#### <a name="for-the-smb-appliance"></a>Az SMB-berendezéshez

1. Válassza **az Embedded RAID 1: HPE Smart Array P208i-a SR Gen 10** Array  >  **Configuration** Create  >  **Array lehetőséget.**

1. Válassza **a Folytatás a következő űrlapra lehetőséget.**

1. A **RAID-szint beállítása űrlapon** állítsa a szintet **RAID 5-re** vállalati környezetek esetén, a **RAID 1-et** pedig SMB-környezetek esetén.

1. Válassza **a Folytatás a következő űrlapra lehetőséget.**

1. A Logikai **meghajtó címkéje űrlapon** adja meg az **1. logikai meghajtót.**

1. Válassza a **Módosítások elküldése lehetőséget.**

1. A Küldés **űrlapon** válassza a **Vissza a főmenühöz lehetőséget.**

1. Válassza **az F10: Mentés lehetőséget,** majd nyomja le **kétszer az Esc** billentyűt.

1. A Rendszer **segédprogramok ablakban** válassza az **One-Time Boot Menu (Egyszer indítható rendszerindítási menü) lehetőséget.**

1. Az **One-Time Boot Menu (Egyszeres rendszerindítási menü) űrlapon** válassza a **Legacy BIOS One-Time boot menu (Rendszerindítási menü) lehetőséget.**

1. Megjelenik **az Örökölt és a** **Rendszerindítás felülbírálása ablak.** Rendszerindítás felülbírálási beállításának kiválasztása; például CD-ROM-ra, USB-re, HDD-re vagy UEFI-rendszerhéjra.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Képernyőkép az első Rendszerindítás felülbírálása ablakról.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Képernyőkép a második Rendszerindítás felülbírálása ablakról.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Szoftvertelepítés (HPE ProLiant DL20-berendezés)

A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször újraindul.

A szoftver telepítése:

1. Csatlakoztassa a képernyőt és a billentyűzetet a berendezéshez, majd csatlakozzon a CLI-hez.

1. Kössön össze egy külső CD-t vagy lemezt a  kulcson a Defender for IoT portál Frissítések oldalára letöltött ISO-lemezképpel.

1. Indítsa el a berendezést.

1. Válassza az **Angol lehetőséget.**

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Az angol nyelv kiválasztása a CLI-ablakban.":::

1. Válassza **a SENSOR-RELEASE- <version> Enterprise lehetőséget.**

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Képernyőkép a verzió kiválasztására vonatkozó képernyőről.":::

1. A Telepítési varázslóban adja meg a hardverprofilt és a hálózati tulajdonságokat:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="A telepítővarázslót bemutató képernyőkép.":::

    | Paraméter | Konfiguráció |
    | ----------| ------------- |
    | **Hardverprofil** | Válassza **a Vállalati** vagy az **Office** lehetőséget az SMB-telepítéshez. |
    | **Felügyeleti felület** | **eno2** |
    | **Alapértelmezett hálózati paraméterek (a paramétereket általában az ügyfél biztosítja)** | **felügyeleti hálózat IP-címe:** <br/> <br/>**berendezés állomásneve:** <br/>**Dns:** <br/>**az alapértelmezett átjáró IP-címe:**|
    | **bemeneti felületek:** | A rendszer létrehozza a bemeneti interfészek listáját.<br/><br/>A bemeneti felületek tükrözéséhez másolja a listában megjelenő összes elemet egy vessző-elválasztóval: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**HPE DL20 esetében: Az eno1, enp1s0f4u4 (iLo interfészek) listának mellőzését**<br/><br/>**BRIDGE**: A hídillesztőt nem szükséges konfigurálni. Ez a lehetőség csak speciális esetekben használható. A folytatáshoz nyomja le az **Enter** billentyűt. |

1. Körülbelül 10 perc múlva megjelenik a két hitelesítőadat-készlet. Az egyik a **CyberX-felhasználóknak,** a másik a Támogatási **felhasználónak.**

1. Mentse a berendezés azonosítóját és jelszavát. A platform első eléréséhez szüksége lesz a hitelesítő adatokra.

1. A **folytatáshoz válassza** az Enter billentyűt.

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360 telepítés

  - A rendszer egy alapértelmezett rendszergazda felhasználót ad meg. Javasoljuk, hogy a hálózati konfiguráció során módosítsa a jelszót.

  - A hálózati konfiguráció során konfigurálni kell az iLO-portot.

  - A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször is újraindul.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360 előlap

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 előlap.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 háttérpanel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 háttérpanel.":::

### <a name="enable-remote-access-and-update-the-password"></a>A távelérés engedélyezése és a jelszó frissítése

A HPE ProLiant DL20 telepítéssel kapcsolatban lásd az előző szakaszokat:

  - "Távelérés engedélyezése és a jelszó frissítése"

  - "A HPE BIOS konfigurálása"

A vállalati konfiguráció azonos.

> [!Note]
> A tömb űrlapon ellenőrizze, hogy az összes lehetőséget kiválasztotta-e.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>iLO távoli telepítés (virtuális meghajtóról)

Ez az eljárás az iLO virtuális meghajtóról való telepítését ismerteti.

A telepítéshez:

1. Jelentkezzen be az iLO-konzolba, majd kattintson a jobb gombbal a kiszolgálók képernyőre.

1. Válassza a **HTML5-konzol lehetőséget.**

1. A konzolon válassza a CD ikont, majd a CD/DVD lehetőséget.

1. Válassza a **Helyi ISO-fájl lehetőséget.**

1. A párbeszédpanelen válassza ki a megfelelő ISO-fájlt.

1. Kattintson a bal oldali ikonra, válassza **a Power (Energiaellátás)** lehetőséget, majd a Reset (Alaphelyzetbe **állítás) lehetőséget.**

1. A berendezés újraindul, és futtatja az érzékelő telepítési folyamatát.

### <a name="software-installation-hpe-dl360"></a>Szoftvertelepítés (HPE DL360)

A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször újraindul.

A telepítéshez:

1. Csatlakoztassa a képernyőt és a billentyűzetet a berendezéshez, majd csatlakozzon a CLI-hez.

1. Külső CD-t vagy lemezt csatlakoztathat egy kulcshoz a  Defender for IoT portál Frissítések oldalára letöltött ISO-lemezképpel.

1. Indítsa el a berendezést.

1. Válassza az **Angol lehetőséget.**

1. Válassza **a SENSOR-RELEASE- <version> Enterprise lehetőséget.**

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="A verzió kiválasztását bemutató képernyőkép.":::

1. A Telepítővarázslóban adja meg a berendezés profilját és hálózati tulajdonságait.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="A telepítővarázslót bemutató képernyőkép.":::

    | Paraméter | Konfiguráció |
    | ----------| ------------- |
    | **Hardverprofil** | Válassza a **vállalati lehetőséget.** |
    | **Felügyeleti felület** | **eno2** |
    | **Alapértelmezett hálózati paraméterek (az ügyfél által biztosított)** | **felügyeleti hálózat IP-címe:** <br/>**alhálózati maszk:** <br/>**berendezés állomásneve:** <br/>**Dns:** <br/>**az alapértelmezett átjáró IP-címe:**|
    | **bemeneti felületek:**  | A rendszer létrehoz egy listát a bemeneti felületekről.<br/><br/>A bemeneti felületek tükrözéséhez másolja a listában megjelenő összes elemet egy vesszővel elválasztva.<br/><br/> Nem kell konfigurálnia a hídillesztőt. Ez a lehetőség csak speciális esetekben használható. |

1. Körülbelül 10 perc múlva megjelenik a két hitelesítőadat-készlet. Az egyik a **CyberX-felhasználóknak,** a másik a támogatási **felhasználónak.**

1. Mentse a berendezés azonosítóját és jelszavát. Ezekre a hitelesítő adatokra szüksége lesz a platform első eléréséhez.

1. A **folytatáshoz válassza** az Enter billentyűt.

## <a name="sensor-installation-for-the-virtual-appliance"></a>A virtuális berendezés érzékelőinek telepítése

A Defender for IoT-érzékelő virtuális gépét a következő architektúrákban helyezheti üzembe:


| Architektúra | Specifikációk | Használat | Megjegyzések |
|---|---|---|---|
| **Vállalat** | CPU: 8<br/>Memória: 32 GB RAM<br/>HDD: 1800 GB | Éles környezet | Alapértelmezett és leggyakoribb |
| **Kisvállalatok** | CPU: 4 <br/>Memória: 8 GB RAM<br/>HDD: 500 GB | Tesztelési vagy kis méretű éles környezetek | -  |
| **Office** | CPU: 4<br/>Memória: 8 GB RAM<br/>HDD: 100 GB | Kis méretű tesztkörnyezetek | -  |

### <a name="prerequisites"></a>Előfeltételek

A helyszíni felügyeleti konzol a VMware és a Hyper-V telepítési lehetőségeket is támogatja. A telepítés megkezdése előtt ellenőrizze, hogy a következő elemek vannak-e használatban:

  - Telepített és működő VMware (ESXi 5.5 vagy újabb) vagy Hyper-V hipervizor (Windows 10 Pro vagy Enterprise)

  - A virtuális gép rendelkezésre álló hardveres erőforrásai

  - Iso-telepítőfájl a Azure Defender for IoT érzékelőhöz

Ellenőrizze, hogy fut-e a hipervizor.

### <a name="create-the-virtual-machine-esxi"></a>A virtuális gép létrehozása (ESXi)

1. Jelentkezzen be az ESXi-be, válassza ki a megfelelő **adattárat,** majd válassza az **Adattárböngésző lehetőséget.**

1. **Töltse fel a** képet, és válassza a **Bezárás lehetőséget.**

1. Válassza a **Virtual Machines,** majd a **Virtuális gép létrehozása/regisztrálása lehetőséget.**

1. Válassza **az Új virtuális gép létrehozása,** majd a Tovább **lehetőséget.**

1. Adjon meg egy érzékelőnevet, és válassza a következőt:

   - Kompatibilitás: **&lt; az ESXi legújabb &gt; verziója**

   - Vendég operációs rendszer család: **Linux**

   - Vendég operációs rendszer verziója: **Ubuntu Linux (64 bites)**

1. Kattintson a **Tovább** gombra.

1. Válassza ki a megfelelő adattárat, majd kattintson a **Tovább gombra.**

1. Módosítsa a virtuális hardver paramétereit a szükséges architektúrának megfelelően.

1. Az **1. CD-/DVD-meghajtó esetén** válassza az **Adattár ISO-fájl** lehetőséget, majd válassza ki a korábban feltöltött ISO-fájlt.

1. Válassza a **Tovább** > **Befejezés** lehetőséget.

### <a name="create-the-virtual-machine-hyper-v"></a>A virtuális gép létrehozása (Hyper-V)

Ez az eljárás bemutatja, hogyan hozhat létre virtuális gépet a Hyper-V használatával.

Virtuális gép létrehozása:

1. Hozzon létre egy virtuális lemezt a Hyper-V kezelőben.

1. Válassza **a format = VHDX lehetőséget.**

1. Válassza **a type = Dynamic Expanding lehetőséget.**

1. Adja meg a virtuális merevlemez nevét és helyét.

1. Adja meg a szükséges méretet (az architektúra alapján).   

1. Tekintse át az összefoglalást, és válassza a **Befejezés lehetőséget.**

1. A Műveletek **menüben** hozzon létre egy új virtuális gépet.

1. Adja meg a virtuális gép nevét.

1. Válassza **a Generáció**  >  **generációjának megadása 1 lehetőséget.**

1. Adja meg a memóriafoglalást (az architektúra alapján), és jelölje be a dinamikus memória jelölőnégyzetét.

1. Konfigurálja a hálózati adaptert a kiszolgáló hálózati topológiájának megfelelően.

1. Csatlakoztassa a korábban létrehozott VHDX-et a virtuális géphez.

1. Tekintse át az összefoglalást, és válassza a **Befejezés lehetőséget.**

1. Kattintson a jobb gombbal az új virtuális gépre, és válassza a **Beállítások lehetőséget.**

1. Válassza **a Hardver hozzáadása lehetőséget,** és adjon hozzá egy új hálózati adaptert.

1. Válassza ki azt a virtuális kapcsolót, amely az érzékelőkezelési hálózathoz fog csatlakozni.

1. Cpu-erőforrások lefoglalása (az architektúra alapján).

1. Csatlakoztassa a felügyeleti konzol ISO-lemezképét egy virtuális DVD-meghajtóhoz.

1. Indítsa el a virtuális gépet.

2. A szoftverek **telepítésének** folytatásához a Műveletek menüben válassza a Csatlakozás lehetőséget. 

### <a name="software-installation-esxi-and-hyper-v"></a>Szoftvertelepítés (ESXi és Hyper-V)

Ez a szakasz az ESXi és a Hyper-V szoftver telepítését ismerteti.

A telepítéshez:

1. Nyissa meg a virtuális gép konzolját.

1. A virtuális gép az ISO-lemezképből indul, és megjelenik a nyelvválasztási képernyő. Válassza az **Angol lehetőséget.**

1. Válassza ki a szükséges architektúrát.

1. Adja meg a berendezés profilját és hálózati tulajdonságait:

    | Paraméter | Konfiguráció |
    | ----------| ------------- |
    | **Hardverprofil** | &lt;szükséges architektúra&gt; |
    | **Felügyeleti felület** | **ens192** |
    | **Hálózati paraméterek (az ügyfél által megadott)** | **felügyeleti hálózat IP-címe:** <br/>**alhálózati maszk:** <br/>**berendezés állomásneve:** <br/>**Dns:** <br/>**alapértelmezett átjáró:** <br/>**bemeneti felületek:**|
    | **hídillesztők:** | A hídillesztőt nem szükséges konfigurálni. Ez a lehetőség csak speciális esetekben használható. |

1. A **beállítások elfogadásához írja** be az Y billentyűt.

1. A bejelentkezési hitelesítő adatokat a rendszer automatikusan generálja és mutatja be. Másolja biztonságos helyre a felhasználónevet és a jelszót, mert a bejelentkezéshez és a felügyelethez szükséges.

      - **Támogatás:** A felhasználókezelés rendszergazda felhasználója.

      - **CyberX:** A berendezés eléréséhez szükséges root megfelelője.

1. A berendezés újraindul.

1. A felügyeleti konzolhoz a korábban konfigurált IP-címen keresztül férhet hozzá: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Képernyőkép a felügyeleti konzolhoz való hozzáférésről.":::

## <a name="on-premises-management-console-installation"></a>Helyszíni felügyeleti konzol telepítése

Mielőtt telepítené a szoftvert a berendezésre, módosítania kell a berendezés BIOS-konfigurációját:

### <a name="bios-configuration"></a>BIOS-konfiguráció

A BIOS konfigurálása a berendezéshez:

1. [Engedélyezze a távelérést, és frissítse a jelszót.](#enable-remote-access-and-update-the-password)

1. [Konfigurálja a BIOS-t.](#configure-the-hpe-bios)

### <a name="software-installation"></a>Szoftvertelepítés

A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször is újraindul. 

A telepítési folyamat során hozzáadhat egy másodlagos hálózati adaptert. Ha úgy dönt, hogy nem telepíti a másodlagos hálózati adaptert a telepítés során, később is hozzáadhat egy másodlagos [hálózati](#add-a-secondary-nic) adaptert. 

A szoftver telepítése:

1. Válassza ki a telepítési folyamat kívánt nyelvét.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="Válassza ki a telepítési folyamat kívánt nyelvét.":::     

1. Válassza **a \<version\> \<deployment type\> MANAGEMENT-RELEASE-** lehetőséget.

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="Válassza ki a verzióját.":::   

1. A telepítővarázslóban adja meg a hálózati tulajdonságokat:

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="A berendezés profilját bemutató képernyőkép.":::   

   | Paraméter | Konfiguráció |
   |--|--|
   | **felügyeleti hálózati adapter konfigurálása** | Dell: **eth0, eth1** <br /> HP esetében: **enu1, enu2** <br /> vagy <br />**lehetséges érték** |
   | **felügyeleti hálózat IP-címének konfigurálása:** | **Az ügyfél által megadott IP-cím** |
   | **alhálózati maszk konfigurálása:** | **Az ügyfél által megadott IP-cím** |
   | **DNS konfigurálása:** | **Az ügyfél által megadott IP-cím** |
   | **alapértelmezett átjáró IP-címének konfigurálása:** | **Az ügyfél által megadott IP-cím** |
   
1. **(Nem kötelező)** Ha másodlagos hálózati adaptert (NIC) szeretne telepíteni, adja meg a következő készülékprofilt és hálózati tulajdonságokat:

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="A másodlagos hálózati adapter telepítéséhez szükséges kérdéseket bemutató képernyőkép.":::

   | Paraméter | Konfiguráció |
   |--|--|
   | **érzékelőfigyelési felület konfigurálása (nem kötelező):** | **eth1**, vagy **lehetséges érték** |
   | **konfigurálja az érzékelő monitorozási felületének IP-címét:** | **Az ügyfél által megadott IP-cím** |
   | **alhálózati maszk konfigurálása az érzékelőfigyelési felülethez:** | **Az ügyfél által megadott IP-cím** |

1. Fogadja el a rendezést, és folytassa a következő beírásával: `Y` . 

1. Körülbelül 10 perc múlva megjelenik a két hitelesítőadat-készlet. Az egyik a **CyberX-felhasználóknak,** a másik a Támogatási **felhasználónak.**

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="Másolja ki ezeket a hitelesítő adatokat, mert azok nem jelennek meg újra.":::  

   Mentse a felhasználóneveket és jelszavakat. Ezekre a hitelesítő adatokra lesz szüksége a platform első használatának első alkalommal történő eléréséhez.

1. A **folytatáshoz válassza** az Enter billentyűt.

A fizikai port a berendezésen való keresésének részletes információiért lásd: [Find your port (A port megkeresse a portot).](#find-your-port)

### <a name="add-a-secondary-nic"></a>Másodlagos hálózati adapter hozzáadása

Másodlagos hálózati adapter hozzáadásával javíthatja a helyszíni felügyeleti konzol biztonságát. Egy másodlagos hálózati adapter hozzáadásával az egyik dedikált lesz a felhasználók számára, a másik pedig támogatni fogja az átjáró konfigurálást az útvonalhálózatok számára. A második hálózati adapter az IP-címtartományon belüli összes csatlakoztatott érzékelő számára dedikált.

Mindkét hálózati adapteren engedélyezve van a felhasználói felület. Ha nincs szükség útválasztásra, a felhasználói felület által támogatott összes szolgáltatás elérhető lesz a másodlagos hálózati adapteren. A magas rendelkezésre állás a másodlagos hálózati adapteren fog futni.

Ha úgy dönt, hogy nem helyez üzembe másodlagos hálózati adaptert, az összes szolgáltatás elérhető lesz az elsődleges hálózati adapteren keresztül. 

Ha már konfigurálta a helyszíni felügyeleti konzolt, és szeretne hozzáadni egy másodlagos hálózati adaptert a helyszíni felügyeleti konzolhoz, kövesse az alábbi lépéseket:

1. Használja a network reconfigure parancsot:

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. Adja meg a következő válaszokat a következő kérdésekre:

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="A berendezés konfiguráláshoz adja meg a következő válaszokat.":::

    | Paraméterek | Válasz a beírni |
    |--|--|
    | **Felügyeleti hálózat IP-címe** | `N` |
    | **Alhálózati maszk** | `N` |
    | **DNS** | `N` |
    | **Alapértelmezett átjáró IP-címe** | `N` |
    | **Érzékelőfigyelési felület (nem kötelező. Akkor alkalmazható, ha az érzékelők egy másik hálózati szegmensben vannak. További információkért lásd a telepítési utasításokat)**| `Y`, **válasszon ki egy lehetséges értéket** |
    | **Az érzékelőfigyelési felület IP-címe (az érzékelők számára elérhető)** | `Y`, **az ügyfél által megadott IP-cím**|
    | **Alhálózati maszk az érzékelőfigyelési felülethez (az érzékelők számára elérhető)** | `Y`, **az ügyfél által megadott IP-cím** |
    | **Hostname (Gazdanév)** | **az ügyfél által biztosított** |

1. Tekintse át az összes lehetőséget, és írja `Y` be a következőt a módosítások elfogadásához: . A rendszer újraindul.

### <a name="find-your-port"></a>A port megkeresi

Ha nem tudja megtalálni a fizikai portot az eszközön, a következő paranccsal:

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

A parancs hatására a porton a világítás a megadott időszakra felgyújt. A beírása esetén például az eno1 flash port 2 percig lesz elérhető, így a port a berendezés `sudo ethtool -p eno1 120` hátán található. 

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Virtuális berendezés: Helyszíni felügyeleti konzol telepítése

A helyszíni felügyeleti konzol virtuális gépe a következő architektúrákat támogatja:

| Architektúra | Specifikációk | Használat | 
|--|--|--|
| Enterprise <br/>(Alapértelmezett és leggyakoribb) | CPU: 8 <br/>Memória: 32 GB RAM<br/> HDD: 1,8 TB | Nagy méretű éles környezetek | 
| Enterprise | CPU: 4 <br/> Memória: 8 GB RAM<br/> HDD: 500 GB | Nagy méretű éles környezetek |
| Enterprise | CPU: 4 <br/>Memória: 8 GB RAM <br/> HDD: 100 GB | Kis méretű tesztkörnyezetek | 
   
### <a name="prerequisites"></a>Előfeltételek

A helyszíni felügyeleti konzol a VMware és a Hyper-V telepítési lehetőségeket is támogatja. A telepítés megkezdése előtt ellenőrizze a következőket:

- A VMware (ESXi 5.5 vagy újabb) vagy a Hyper-V hipervizor (Windows 10 Pro vagy Enterprise) telepítve van és működik.

- A hardveres erőforrások elérhetők a virtuális gép számára.

- A helyszíni felügyeleti konzol ISO-telepítőfájlja már megvan.
    
- A hipervizor fut.

### <a name="create-the-virtual-machine-esxi"></a>A virtuális gép létrehozása (ESXi)

Virtuális gép létrehozása (ESXi):

1. Jelentkezzen be az ESXi-be, válassza ki a megfelelő **adattárat,** majd válassza az **Adattárböngésző lehetőséget.**

1. Töltse fel a képet, és válassza a **Bezárás lehetőséget.**

1. Ugrás a **Virtual Machines.**

1. Válassza **a Virtuális gép létrehozása/regisztrálása lehetőséget.**

1. Válassza **az Új virtuális gép létrehozása, majd** a Tovább **lehetőséget.**

1. Adjon meg egy érzékelőnevet, és válassza a következőt:

   - Kompatibilitás: \<latest ESXi version>

   - Vendég operációs rendszer család: Linux

   - Vendég operációs rendszer verziója: Ubuntu Linux (64 bites)

1. Kattintson a **Tovább** gombra.

1. Válassza ki a megfelelő adattárat, majd kattintson a **Tovább gombra.**

1. Módosítsa a virtuális hardver paramétereit a szükséges architektúrának megfelelően.

1. Az **1. CD-/DVD-meghajtó esetén** válassza az **Adattár ISO-fájl** lehetőséget, majd válassza ki a korábban feltöltött ISO-fájlt.

1. Válassza a **Tovább** > **Befejezés** lehetőséget.

### <a name="create-the-virtual-machine-hyper-v"></a>A virtuális gép létrehozása (Hyper-V)

Virtuális gép létrehozása a Hyper-V használatával:

1. Hozzon létre egy virtuális lemezt a Hyper-V kezelőben.

1. Válassza ki a **VHDX formátumot.**

1. Kattintson a **Tovább** gombra.

1. Válassza a Dinamikus **kibontása típust.**

1. Kattintson a **Tovább** gombra.

1. Adja meg a virtuális merevlemez nevét és helyét.

1. Kattintson a **Tovább** gombra.

1. Adja meg a szükséges méretet (az architektúra alapján).

1. Kattintson a **Tovább** gombra.

1. Tekintse át az összefoglalást, és válassza a **Befejezés lehetőséget.**

1. A Műveletek **menüben** hozzon létre egy új virtuális gépet.

1. Kattintson a **Tovább** gombra.

1. Adja meg a virtuális gép nevét.

1. Kattintson a **Tovább** gombra.

1. Válassza **a Generation (Generáció)** lehetőséget, és állítsa **1. generációsra.**

1. Kattintson a **Tovább** gombra.

1. Adja meg a memóriafoglalást (az architektúra alapján), és jelölje be a dinamikus memória jelölőnégyzetét.

1. Kattintson a **Tovább** gombra.

1. Konfigurálja a hálózati adaptert a kiszolgáló hálózati topológiájának megfelelően.

1. Kattintson a **Tovább** gombra.

1. Csatlakoztassa a korábban létrehozott VHDX-et a virtuális géphez.

1. Kattintson a **Tovább** gombra.

1. Tekintse át az összefoglalást, és válassza a **Befejezés lehetőséget.**

1. Kattintson a jobb gombbal az új virtuális gépre, majd válassza a **Beállítások lehetőséget.**

1. Válassza **a Hardver hozzáadása lehetőséget,** és adjon hozzá egy új adaptert a Hálózati **adapterhez.**

1. A **Virtuális kapcsoló beállításban** válassza ki azt a kapcsolót, amely az érzékelőkezelési hálózathoz fog csatlakozni.

1. Cpu-erőforrások lefoglalása (az architektúra alapján).

1. Csatlakoztassa a felügyeleti konzol ISO-lemezképét egy virtuális DVD-meghajtóhoz.

1. Indítsa el a virtuális gépet.

1. A szoftver **telepítésének folytatásához** a Műveletek menüben válassza a Csatlakozás lehetőséget. 

### <a name="software-installation-esxi-and-hyper-v"></a>Szoftvertelepítés (ESXi és Hyper-V)

A virtuális gép indítása az ISO-lemezképből indítja el a telepítési folyamatot.

A szoftver telepítése:

1. Válassza az **Angol lehetőséget.**

1. Válassza ki az üzemelő példányhoz szükséges architektúrát.

1. Adja meg az érzékelőkezelési hálózat hálózati adapterét: adapter, IP, alhálózat, DNS-kiszolgáló és alapértelmezett átjáró.

1. A bejelentkezési hitelesítő adatok automatikusan létrejönnek. Mentse a felhasználónevet és a jelszavakat. Ezekre a hitelesítő adatokra lesz szüksége a platform első használatakor való hozzáféréshez.

   A berendezés ezután újraindul.

1. A felügyeleti konzolhoz a korábban konfigurált IP-címen keresztül férhet hozzá: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="A felügyeleti konzol bejelentkezési képernyőjét bemutató képernyőkép.":::

## <a name="post-installation-validation"></a>Telepítés utáni érvényesítés

Egy fizikai berendezés telepítésének ellenőrzéséhez számos tesztet kell végrehajtania. Ugyanez az ellenőrzési folyamat vonatkozik az összes berendezéstípusra.

Hajtsa végre az ellenőrzést a grafikus felhasználói felület vagy a cli használatával. Az ellenőrzés az ügyfélszolgálat és **a** CyberX felhasználó számára **érhető el.**

A telepítés utáni ellenőrzésnek a következő teszteket kell tartalmaznia:

  - **Sanitásteszt:** Ellenőrizze, hogy fut-e a rendszer.

  - **Verzió:** Ellenőrizze, hogy a verzió helyes-e.

  - **ifconfig:** Ellenőrizze, hogy a telepítési folyamat során konfigurált összes bemeneti adapter fut-e.

### <a name="checking-system-health-by-using-the-gui"></a>Rendszer állapotának ellenőrzése a grafikus felhasználói felület használatával

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Képernyőkép a rendszer állapotának ellenőrzésével.":::

#### <a name="sanity"></a>Józanság

- **Berendezés:** Futtatja a berendezés sanitás-ellenőrzését. Ugyanezt az ellenőrzést a PARANCSSORi felület parancsával is `system-sanity` elvégezheti.

- **Verzió:** Megjeleníti a berendezés verzióját.

- **Hálózati tulajdonságok:** Megjeleníti az érzékelőhálózat paramétereit.

#### <a name="redis"></a>Redis

- **Memória:** Átfogó képet nyújt a memóriahasználatról, például arról, hogy mennyi memóriát használtak fel, és mennyi maradt még.

- **Leghosszabb kulcs:** Megjeleníti azokat a leghosszabb kulcsokat, amelyek jelentős memóriahasználatot okozhatnak.

#### <a name="system"></a>Rendszer

- **Alapvető napló:** Az alapnapló utolsó 500 sorát biztosítja, így a legutóbbi naplósorok a teljes rendszernapló exportálása nélkül is megtekinthetők.

- **feladatkezelő:** A folyamattáblában megjelenő feladatokat a következő rétegekre fordítja le: 
  
  - Állandó réteg (Redis) 
  - Cash layer (SQL)

- **Hálózati statisztika:** Megjeleníti a hálózati statisztikákat.

- **TOP**: A folyamatok táblázatát jeleníti meg. Ez egy Linux-parancs, amely dinamikus, valós idejű nézetet biztosít a futó rendszerről.

- **Biztonsági mentési memória ellenőrzése:** A biztonsági mentési memória állapotát jelzi, és a következőket ellenőrzi:
  - A biztonsági mentési mappa helye 
  - A biztonsági mentési mappa mérete
  - A backup mappa korlátozásai
  - Az utolsó biztonsági mentés mikor történt
  - Mennyi hely áll rendelkezésre a további biztonsági mentési fájlok számára

- **ifconfig:** Megjeleníti a berendezés fizikai adapterének paramétereit.

- **CyberX nload:** A hálózati forgalmat és a sávszélességet jeleníti meg a hat másodperces tesztekkel.

- **Hibák a Core-ból, napló:** A központi naplófájl hibáit jeleníti meg.

Az eszköz elérése:

1. Jelentkezzen be az érzékelőbe a **Támogatási felhasználói** hitelesítő adatokkal.

1. A **Rendszerbeállítások ablakban** válassza a **Rendszerstatisztikák** lehetőséget.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Rendszer állapotának ellenőrzése a CLI használatával

**1. teszt: Sanitás**

Ellenőrizze, hogy a rendszer működik-e:

1. Csatlakozzon a CLI-hez a Linux-terminállal (például PuTTY) és a felhasználói **támogatással.**

1. Írja be a következő szöveget: `system sanity`.

1. Ellenőrizze, hogy az összes szolgáltatás zöld (fut).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="A futó szolgáltatásokat bemutató képernyőkép.":::

1. Ellenőrizze, hogy **a rendszer működik-e! (prod)** alul jelenik meg.

**2. teszt: Verzióellenőrzés**

Ellenőrizze, hogy a megfelelő verziót használja-e:

1. Csatlakozzon a CLI-hez a Linux-terminállal (például PuTTY) és a felhasználói **támogatással.**

1. Írja be a következő szöveget: `system version`.

1. Ellenőrizze, hogy a megfelelő verzió jelenik-e meg.

**3. teszt: Hálózatérvényesítés**

Ellenőrizze, hogy a telepítési folyamat során konfigurált összes bemeneti felület fut-e:

1. Csatlakozzon a CLI-hez a Linux-terminállal (például PuTTY) és a felhasználói **támogatással.**

1. Írja `network list` be a következőt: (a Linux-parancs `ifconfig` megfelelője).

1. Ellenőrizze, hogy megjelennek-e a szükséges bemeneti felületek. Ha például két quad réz hálózati adapter van telepítve, 10 adapternek kell lennie a listában.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Az interfészek listáját bemutató képernyőkép.":::

**4. teszt: Felügyeleti hozzáférés a felhasználói felülethez**

Ellenőrizze, hogy hozzáfér-e a konzol webes grafikus felhasználói felületéhez:

1. Csatlakoztassa a laptopot Ethernet-kábellel a felügyeleti porthoz **(Gb1).**

1. Határozza meg, hogy a laptop hálózati adapterének címe a berendezésével azonos tartományban legyen.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Képernyőkép a felhasználói felület felügyeleti hozzáférésről.":::

1. Pingelje a berendezés IP-címét a laptopról a kapcsolat ellenőrzéséhez (alapértelmezés: 10.100.10.1).

1. Nyissa meg a Chrome böngészőt a laptopon, és adja meg a berendezés IP-címét.

1. A Your **connection is not private (A kapcsolat nem privát) ablakban** válassza a Speciális **lehetőséget, és** folytassa a műveletet.

1. A teszt sikeres, amikor megjelenik a Defender for IoT bejelentkezési képernyő.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Képernyőkép a felügyeleti konzolhoz való hozzáférésről.":::

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="you-cant-connect-by-using-a-web-interface"></a>Nem lehet webes felülettel csatlakozni

1. Ellenőrizze, hogy a csatlakoztatni próbált számítógép ugyanazon a hálózaton található-e, mint a berendezés.

1. Ellenőrizze, hogy a grafikus felhasználói felületi hálózat csatlakozik-e a felügyeleti porthoz.

1. Pingelje a berendezés IP-címét. Ha nincs pingelés:

   1. Monitor és billentyűzet csatlakoztatása a berendezéshez.

   1. A **bejelentkezéshez** használja a Támogatás felhasználóját és jelszavát.

   1. Az paranccsal `network list` tekintse meg az aktuális IP-címet.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="A hálózati listát bemutató képernyőkép.":::

1. Ha a hálózati paraméterek helytelenül vannak konfigurálva, a következő eljárással módosíthatja őket:

   1. Használja a `network edit-settings` parancsot.

   1. A felügyeleti hálózat IP-címének beállításához válassza az **Y lehetőséget.**

   1. Az alhálózati maszk beállításának beállítását az **Y kiválasztásával módosíthatja.**

   1. A DNS-hez válassza az **Y lehetőséget.**

   1. Az alapértelmezett átjáró IP-címének beállítását az **Y kiválasztásával módosíthatja.**

   1. A bemeneti felület módosítása (csak érzékelő) esetén válassza az **N lehetőséget.**

   1. A beállítások alkalmazáshoz válassza az **Y lehetőséget.**

1. Újraindítás után csatlakozzon a támogatási felhasználó hitelesítő adataival, és az paranccsal ellenőrizze, hogy a paraméterek `network list` módosultak-e.

1. Próbáljon meg újra pingelni, majd csatlakozni a grafikus felhasználói felületről.

### <a name="the-appliance-isnt-responding"></a>A berendezés nem válaszol

1. Csatlakoztathat egy monitort és billentyűzetet a berendezéshez, vagy a PuTTY használatával távolról kapcsolódhat a CLI-hez.

1. A **bejelentkezéshez** használja a támogatási felhasználó hitelesítő adatait.

1. Használja a `system sanity` parancsot, és ellenőrizze, hogy minden folyamat fut-e.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Képernyőkép a rendszer-sanitás parancsról.":::

Egyéb problémák esetén lépjen kapcsolatba a [Microsoft ügyfélszolgálata.](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>A függelék: Tükrözés port vSwitchen (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>SPAN-port konfigurálása meglévő vSwitchen

A vSwitch nem rendelkezik tükrözés funkciókkal, de áthidaló megoldással megvalósíthat egy SPAN-portot.

SPAN-port konfigurálása:

1. Nyissa meg a vSwitch tulajdonságait.

1. Válassza a **Hozzáadás** lehetőséget.

1. Válassza **a Következő virtuális** gép  >  **lehetőséget.**

1. Szúrjon be egy hálózati címkét **SPAN-hálózat,** válassza az Összes **VLAN-azonosítót,**  >  majd kattintson a Tovább **gombra.**

1. Válassza a **Befejezés** gombot.

1. Válassza **a SPAN Network** > * Edit *lehetőséget.*

1. Válassza **a Biztonság** lehetőséget, és ellenőrizze, hogy a Válogatásos mód szabályzat Elfogadás **módra van-e** állítva. 

1. Kattintson **az OK** gombra, majd a **Bezárás** gombra a vSwitch tulajdonságainak a bezáráshoz.

1. Nyissa meg **az XSense virtuális gép** tulajdonságait.

1. A **2. hálózati adapterhez** válassza ki a **SPAN-hálózatot.**

1. Válassza az **OK** lehetőséget.

1. Csatlakozzon az érzékelőhöz, és ellenőrizze, hogy a tükrözés működik-e.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>B függelék: Hozzáférés-érzékelők a helyszíni felügyeleti konzolról

A rendszer biztonságát úgy javíthatja, hogy megakadályozza az érzékelő közvetlen felhasználói hozzáférését. Ehelyett használja a proxyalagútkezelést, hogy a felhasználók egyetlen tűzfalszabály használatával érzék el az érzékelőt a helyszíni felügyeleti konzolról. Ez a technika az érzékelőn túlra szűkíti a hálózati környezethez való jogosulatlan hozzáférés lehetőségét. A felhasználói élmény az érzékelőbe való bejelentkezés során változatlan marad.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Képernyőkép az érzékelőhöz való hozzáférésről.":::

A bújtatás engedélyezése:

1. Jelentkezzen be a helyszíni felügyeleti konzol CLI-jbe a **CyberX vagy** **a Támogatási** felhasználói hitelesítő adatok használatával.

1. Írja be a következő szöveget: `sudo cyberx-management-tunnel-enable`.

1. Nyomja le az **Enter** billentyűt.

1. Írja be a következő szöveget: `--port 10000`.

### <a name="next-steps"></a>Következő lépések

[A hálózat beállítása](how-to-set-up-your-network.md)