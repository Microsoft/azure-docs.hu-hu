---
title: Defender a IoT telepítéséhez
description: Ismerje meg, hogyan telepítheti az Azure Defender for IoT érzékelőjét és helyszíni felügyeleti konzolját.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 1e361ad14f79ea32d83e8cff9dd9bb8607b7c766
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470686"
---
# <a name="defender-for-iot-installation"></a>Defender a IoT telepítéséhez

Ez a cikk azt ismerteti, hogyan telepítheti az Azure Defender következő elemeit a IoT:

- **Érzékelő**: a IoT érzékelők Defender a passzív (ügynök nélküli) figyelés használatával gyűjti az internetkapcsolattal rendelkező hálózati forgalmat. A passzív és a nem befolyásolják, az érzékelők nulla hatással vannak az OT és a IoT hálózatokra és eszközökre. Az érzékelő egy SPAN porthoz vagy egy hálózati KOPPINTÁShoz csatlakozik, és azonnal megkezdi a hálózat figyelését. Észlelések jelennek meg az érzékelő konzolon. Itt megtekintheti, megvizsgálhatja és elemezheti őket egy hálózati térképen, az eszközök leltárában és a jelentések széles skáláján. Ilyenek például a kockázatértékelési jelentések, az adatbányászati lekérdezések és a támadási vektorok. További információ az érzékelő képességeiről a [Defender for IoT szenzor felhasználói útmutatója (közvetlen letöltés) című](./getting-started.md)témakörben.

- Helyszíni **felügyeleti konzol**: a helyszíni felügyeleti konzol lehetővé teszi az eszközök felügyeletét, a kockázatkezelést és a sebezhetőségek kezelését. Azt is felhasználhatja, hogy elvégezze a fenyegetések figyelését és az incidensek megválaszolását a vállalaton belül. Egységes áttekintést nyújt az összes hálózati eszközről, a legfontosabb IoT, valamint az olyan létesítményekben észlelt kockázati mutatókról és riasztásokról, amelyeken az érzékelők üzembe vannak helyezve. A helyszíni felügyeleti konzol segítségével megtekintheti és kezelheti az érzékelők gapped hálózatokban történő megtekintését és kezelését.

Ez a cikk a következő telepítési információkat ismerteti:

  - **Hardver:** A Dell és a HPE fizikai berendezés részletei.

  - **Szoftver:** Az érzékelő és a helyszíni felügyeleti konzol szoftver telepítése.

  - **Virtuális készülékek:** A virtuális gép adatai és a szoftverek telepítése.

A telepítést követően kapcsolja össze az érzékelőt a hálózattal.

## <a name="about-defender-for-iot-appliances"></a>A IoT készülékekre vonatkozó Defender 

A következő szakaszokban információt talál a Defender for IoT szenzoros készülékekről, valamint a Defender for IoT helyszíni felügyeleti konzoljának berendezéséről.

### <a name="physical-appliances"></a>Fizikai berendezések

A Defender for IoT Appliance-érzékelő egy SPAN porthoz vagy egy hálózati KOPPINTÁShoz csatlakozik, és azonnal megkezdi az INTERNETKAPCSOLATtal rendelkező hálózati forgalom összegyűjtését passzív (ügynök nélküli) figyeléssel. Ez a folyamat nulla hatással van az OT-hálózatokra és-eszközökre, mert az nem az adatelérési útra van helyezve, és nem ellenőrzi aktívan az eszközökön az OT.

A következő rack Mount készülékek érhetők el:

| **Központi telepítés típusa** | **Vállalat** | **Vállalat** | **SMB** | **Vonal** |
|--|--|--|--|--|
| **Modellezés** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Portok figyelése** | legfeljebb 15 RJ45 vagy 8 OPT | legfeljebb 9 RJ45 vagy 6 OPT | legfeljebb 8 RJ45 vagy 6 OPT | 4 RJ45 |
| **Maximális sávszélesség\*** | 3 GB/mp | 1 GB/s | 1 GB/s | 100 MB/s |
| **Maximálisan védett eszközök** | 30 000 | 10,000 | 15 000 | 1,000 |

* A sávszélesség maximális kapacitása a protokollok eloszlása függvényében változhat.

### <a name="virtual-appliances"></a>Virtuális készülékek

A következő virtuális készülékek érhetők el:

| **Központi telepítés típusa** | **Vállalat** | **SMB** | **Vonal** |
|--|--|--|--|
| **Leírás** | Virtuális berendezés vállalati telepítésekhez | Virtuális berendezés SMB-telepítésekhez | Virtuális berendezés a vonalas üzembe helyezésekhez |
| **Maximális sávszélesség\*** | 150 MB/s | 15 MB/s | 3 MB/s |
| **Maximálisan védett eszközök** | 3,000 | 300 | 100 |
| **Központi telepítés típusa** | Enterprise | SMB | Vonal |
| **Leírás** | Virtuális berendezés vállalati telepítésekhez | Virtuális berendezés SMB-telepítésekhez | Virtuális berendezés a vonalas üzembe helyezésekhez |

* A sávszélesség maximális kapacitása a protokollok eloszlása függvényében változhat.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>A helyszíni felügyeleti konzol hardveres specifikációi

 | Elem | Leírás |
 |----|--|
 **Leírás** | Többrétegű architektúrában a helyszíni felügyeleti konzol a földrajzilag elosztott helyek között biztosítja a láthatóságot és a felügyeletet. Integrálható a SOC biztonsági veremekkel, beleértve a SIEM-ket, a Ticketing-rendszereket, a következő generációs tűzfalakat, a biztonságos távelérési platformokat, valamint a Defender for IoT ICS kártevő-munkaterületét. |
 **Központi telepítés típusa** | Enterprise |
 **Berendezés típusa**  | Dell R340, virtuális gép |
 **Felügyelt érzékelők száma** | Korlátlan |

## <a name="prepare-for-the-installation"></a>Felkészülés a telepítésre

### <a name="access-the-iso-installation-image"></a>Az ISO telepítési rendszerképének elérése

A telepítési rendszerkép a Defender for IoT portálról érhető el.

A fájl elérése:

1. Jelentkezzen be a Defender IoT-fiókjába.

1. Lépjen a **hálózati érzékelő** vagy **a helyszíni felügyeleti konzol** lapra, és válassza ki a letölteni kívánt verziót.

### <a name="install-from-dvd"></a>Telepítés DVD-ről

A telepítés előtt győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Egy hordozható DVD-meghajtó az USB-összekötővel.

- Egy ISO-telepítő rendszerképe.

Telepítés:

1. A rendszerkép írása DVD-re vagy lemez előkészítése egy kulcsra. Csatlakoztasson egy hordozható DVD-meghajtót a számítógéphez, kattintson a jobb gombbal az ISO-lemezképre, majd válassza az **írás lemezre** lehetőséget.

1. Csatlakoztasson egy kulcsot a DVD-ről vagy a lemezről, és konfigurálja a berendezést a kulcs DVD-ről vagy lemezről történő elindításához.

### <a name="install-from-disk-on-a-key"></a>Telepítés lemezről a kulcsból

A telepítés előtt győződjön meg róla, hogy rendelkezik az alábbiakkal:

  - Rufus telepítve.
  
  - Egy, a 3,0-es és újabb USB-s verzióval rendelkező lemez. A minimális méret 4 GB.

  - Egy ISO telepítő lemezképfájl.

A rendszer törli a kulcs lemezét a folyamatban.

Lemez előkészítése a kulcsra:

1. Futtassa a Rufus parancsot, és válassza az **érzékelő ISO** elemet.

1. Csatlakoztassa egy kulcs lemezét az előlapon.

1. Állítsa be a kiszolgáló BIOS-át úgy, hogy az USB-ről induljon el.

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL telepítése

Mielőtt telepítené a szoftvert a Dell készülékre, módosítania kell a készülék BIOS-konfigurációját:

  - A [Dell PowerEdge R340 előlapja](#dell-poweredge-r340-front-panel) és a [Dell PowerEdge R340 back panel](#dell-poweredge-r340-back-panel) tartalmazza az elülső és a hátsó panelek leírását, valamint a telepítéshez szükséges adatokat, például az illesztőprogramokat és a portokat.

  - A [Dell BIOS-konfiguráció](#dell-bios-configuration) információt nyújt arról, hogyan csatlakozhat a Dell Appliance felügyeleti felületéhez, és hogyan konfigurálhatja a BIOS-t.

  - A [Szoftvertelepítés (Dell R340)](#software-installation-dell-r340) leírja a Defender IoT-érzékelő szoftver telepítéséhez szükséges eljárást.

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL követelmények 

A Dell PowerEdge R340XL készülék telepítéséhez a következők szükségesek:

- Vállalati licenc a Dell távelérési vezérlőhöz (iDrac)

- BIOS konfigurációs XML

- Kiszolgáló belső vezérlőprogram-verziói:

  - BIOS-verzió 2.1.6

  - iDrac-verzió 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 – elülső panel

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 előlapján.":::

 1. Bal oldali Vezérlőpult 
 1. Optikai meghajtó (nem kötelező) 
 1. Jobb oldali Vezérlőpult 
 1. Információs címke 
 1. Meghajtók  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 háttér panelje

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 hátlapja":::

1. Soros port 
1. NIC-port (GB 1) 
1. NIC-port (GB 1) 
1. Fél magasságú PCIe 
1. Teljes magasságú PCIe kiegészítő kártya tárolóhelye 
1. Tápegység 1. egysége 
1. Tápegység 2. egysége 
1. Rendszerazonosítás 
1. Rendszerállapot jelző kábel portja (CMA) gomb 
1. USB 3,0 port (2) 
1. iDRAC9 dedikált hálózati port 
1. VGA-port 

### <a name="dell-bios-configuration"></a>Dell BIOS-konfiguráció

A Dell készüléknek a szoftverrel való együttműködéshez való beállításához Dell BIOS-konfiguráció szükséges.

A BIOS-konfiguráció előre definiált konfiguráción keresztül történik. A fájl elérhető a [Súgó központból](https://help.cyberx-labs.com/).

Importálja a konfigurációs fájlt a Dell készülékre. A konfigurációs fájl használata előtt létre kell hoznia a Dell készülék és a felügyeleti számítógép közötti kommunikációt.

A Dell készüléket az életciklus-vezérlővel (LC) integrált iDRAC felügyeli. Az LC minden Dell PowerEdge-kiszolgálón be van ágyazva, és olyan funkciókat kínál, amelyek segítségével üzembe helyezheti, frissítheti, figyelheti és karbantarthatja a Dell PowerEdge készülékeit.

A Dell készülék és a felügyeleti számítógép közötti kommunikáció létrehozásához meg kell határoznia a iDRAC IP-címét és a felügyeleti számítógép IP-címét ugyanazon az alhálózaton.

A kapcsolatok létrehozásakor a BIOS konfigurálható.

A Dell BIOS konfigurálása:

1. [A iDRAC IP-címének konfigurálása](#configure-idrac-ip-address)

1. [A BIOS konfigurációs fájljának importálása](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>IDRAC IP-cím konfigurálása

1. Kapcsolja be az érzékelőt.

1. Ha az operációs rendszer már telepítve van, válassza az F2 billentyűt a BIOS-konfiguráció megadásához.

1. Válassza ki a **IDRAC beállításait**.

1. Válassza a **hálózat** lehetőséget.

   > [!NOTE]
   > A telepítés során konfigurálnia kell a következő lépésekben említett alapértelmezett iDRAC IP-címet és jelszót. A telepítés után módosítania kell ezeket a definíciókat.

1. Módosítsa a statikus IPv4-címeket a **10.100.100.250**.

1. Módosítsa a statikus alhálózati maszkot a **255.255.255.0** értékre.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="A statikus alhálózati maszkot megjelenítő képernyőkép.":::

1. Válassza a **vissza**  >  **Befejezés** lehetőséget.

#### <a name="import-the-bios-configuration-file"></a>A BIOS konfigurációs fájljának importálása

Ez a cikk bemutatja, hogyan konfigurálhatja a BIOS-t a konfigurációs fájl használatával.

1. Csatlakoztassa egy statikus előre konfigurált IP- **10.100.100.200** rendelkező számítógépet a **iDRAC** -porthoz.

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Képernyőkép az előre konfigurált IP-cím portról.":::

1. Nyisson meg egy böngészőt, és írja be a **10.100.100.250** -t a iDRAC webes felületéhez való kapcsolódáshoz.

1. Jelentkezzen be a Dell alapértelmezett rendszergazdai jogosultságokkal:

   - Felhasználónév: **gyökér**

   - Jelszó: **Calvin**

1. A készülék hitelesítő adatai a következők:

   - Felhasználónév: **xxx**

   - Jelszó: **xxx**

     A rendszer kezdeményezte az importálási kiszolgáló profiljának műveletét.

     > [!NOTE]
     > A fájl importálása előtt győződjön meg az alábbiakról:
     > - Ön az egyetlen felhasználó, aki jelenleg csatlakozik a iDRAC-hez.
     > - A rendszer nem a BIOS menüben található.

1. Nyissa meg a **konfigurációs**  >  **kiszolgáló konfigurációs profilját**. Állítsa be a következő paramétereket:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="A kiszolgáló profiljának konfigurációját bemutató képernyőkép.":::

   | Paraméter | Konfiguráció |
   |--|--|
   | Hely típusa | Válassza a **helyi** lehetőséget. |
   | Fájl elérési útja | Válassza a **fájl kiválasztása** lehetőséget, és adja hozzá a konfigurációs XML-fájlt. |
   | Összetevők importálása | Válassza a **BIOS, NIC, RAID** lehetőséget. |
   | Maximális várakozási idő | Válasszon **20 percet**. |

1. Válassza az **Importálás** lehetőséget.

1. A folyamat figyeléséhez nyissa meg a **karbantartási**  >  **feladatok várólistáját**.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="A feladatok várólistáját megjelenítő képernyőkép.":::

#### <a name="manually-configuring-bios"></a>A BIOS manuális konfigurálása 

A készülék BIOS-át manuálisan kell konfigurálnia, ha:

- Nem vásárolta meg a készüléket a nyílról.

- Rendelkezik egy berendezéssel, de nem fér hozzá az XML-konfigurációs fájlhoz.

A BIOS-hozzáférés után lépjen az **eszközbeállítások** menüpontra.

A manuális konfiguráláshoz:

1. Közvetlenül egy billentyűzettel és képernyővel érheti el a készülék BIOS-át, vagy használhatja a iDRAC.

   - Ha a készülék nem Defender a IoT készülékhez, nyisson meg egy böngészőt, és keresse meg a korábban konfigurált IP-címet. Jelentkezzen be a Dell alapértelmezett rendszergazdai jogosultságokkal. A jelszóhoz használja a **root** nevet a felhasználónévhez és a **kázmérhoz** .

   - Ha a készülék a IoT készülék Defenderje, jelentkezzen be a felhasználónévvel és **xxx** -nal a jelszóhoz. 

1. A BIOS-hozzáférés után lépjen az **eszközbeállítások** menüpontra.

1. Válassza ki a RAID-vezérelt konfigurációt az **integrált RAID-vezérlő 1: Dell perc \<PERC H330 Adapter\> konfigurációs segédprogram** kiválasztásával.

1. Válassza a **konfiguráció kezelése** lehetőséget.

1. Válassza a **virtuális lemez létrehozása** lehetőséget.

1. A **RAID szintjének kiválasztása** mezőben válassza a **RAID5** lehetőséget. A **virtuális lemez neve** mezőben adja meg a **root** értéket, és válassza a **fizikai lemezek** lehetőséget.

1. Jelölje be **az összes kijelölése jelölőnégyzetet** , majd válassza a **módosítások alkalmazása** lehetőséget.

1. Kattintson az **OK** gombra.

1. Görgessen le, és válassza a **virtuális lemez létrehozása** lehetőséget.

1. Jelölje be a **Confirm (megerősítés** ) jelölőnégyzetet, és válassza az **Igen** lehetőséget.

1. Válassza az **OK** lehetőséget.

1. Térjen vissza a fő képernyőre, és válassza a **rendszer BIOS**-t.

1. Válassza a **rendszerindítási beállítások** lehetőséget.

1. A **rendszerindítási mód** beállításnál válassza a **BIOS** lehetőséget.

1. Válassza a **vissza** lehetőséget, majd kattintson a **Befejezés** gombra a BIOS-beállítások kilépéséhez.

### <a name="software-installation-dell-r340"></a>Szoftvertelepítés (Dell R340)

A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször is újraindul.

Telepítés:

1. A következő módszerek egyikével ellenőrizze, hogy a verzió adathordozója csatlakoztatva van-e a berendezéshez:

   - A külső CD vagy lemez összekapcsolása egy kulccsal a kiadással.

   - Csatlakoztassa az ISO-lemezképet a iDRAC használatával. Miután bejelentkezett a iDRAC, válassza ki a virtuális konzolt, majd válassza a **virtuális média** elemet.

1. A **map CD/DVD** szakaszban válassza a **fájl kiválasztása** lehetőséget.

1. A megnyíló párbeszédpanelen válassza ki a verzió ISO-lemezképfájlját.

1. Kattintson a **Térkép eszköz** gombra.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="A leképezett eszközt megjelenítő képernyőkép.":::

1. Az adathordozó csatlakoztatva van. Válassza a **Bezárás** gombot.

1. Indítsa el a készüléket. A iDRAC használatakor a **konzuli vezérlő** gombra kattintva indíthatja újra a kiszolgálókat. Ezután a **billentyűzet makrók** területen válassza az **alkalmaz** gombot, amely elindítja a CTRL + ALT + DELETE sorozatot.

1. Válassza az **angol** lehetőséget.

1. Válassza ki az **érzékelő-kiadás- \<version\> Enterprise** elemet.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="A verzió kijelölését bemutató képernyőkép.":::   

1. Adja meg a berendezés profilját és a hálózati tulajdonságokat:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Képernyőkép, amely megjeleníti a berendezés profilját.":::   

   | Paraméter | Konfiguráció |
   |--|--|
   | **Hardverprofil** | **Enterprise** |
   | **Felügyeleti felület** | **eno1** |
   | **Hálózati paraméterek (az ügyfél által biztosított)** | - |
   |**felügyeleti hálózati IP-cím:** | - |
   | **alhálózati maszk:** | - |
   | **berendezés állomásneve:** | - |
   | **DNS** | - |
   | **alapértelmezett átjáró IP-címe:** | - |
   | **bemeneti felületek:** |  A rendszer létrehozza a bemeneti felületek listáját. A bemeneti felületek tükrözéséhez másolja a listában bemutatott összes elemet egy vesszővel elválasztó karakterrel. Nem kell konfigurálnia a híd felületet. Ez a beállítás csak különleges használati esetekben használható. |

1. Körülbelül 10 perc elteltével megjelenik a hitelesítő adatok két halmaza. Az egyik egy **CyberX** -felhasználó, az egyik pedig egy **támogatási** felhasználó számára.  

1. Mentse a készülék AZONOSÍTÓját és jelszavát. Ezekre a hitelesítő adatokra akkor van szükség, amikor először használja a platformot.

1. A folytatáshoz válassza az **ENTER billentyűt** .

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20 telepítése

Ez a cikk a HPE ProLiant DL20 telepítési folyamatát ismerteti, amely a következő lépéseket tartalmazza:

  - Engedélyezze a távelérést, és frissítse az alapértelmezett rendszergazdai jelszót.
  - A BIOS és a RAID beállításainak konfigurálása.
  - Telepítse a szoftvert.

### <a name="about-the-installation"></a>A telepítés ismertetése

  - A vállalati és SMB-készülékek telepíthetők. A telepítési folyamat megegyezik mindkét berendezés típusával, kivéve a tömb konfigurációját.
  - Alapértelmezett rendszergazda felhasználó van megadva. Javasoljuk, hogy a hálózati konfigurációs folyamat során módosítsa a jelszót.
  - A hálózati konfigurációs folyamat során konfigurálja az iLO portot az 1. hálózati porton.
  - A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször is újraindul.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20 – elülső panel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20-előlap.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 hátlapja

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="A HPE ProLiant DL20 hátlapja.":::

### <a name="enable-remote-access-and-update-the-password"></a>Távoli hozzáférés engedélyezése és a jelszó frissítése

Az alábbi eljárással állíthatja be a hálózati beállításokat, és frissítheti az alapértelmezett jelszót.

A jelszó engedélyezése és frissítése:

1. Csatlakoztasson egy képernyőt és egy billentyűzetet a HP készülékhez, kapcsolja be a készüléket, majd nyomja le az **F9** billentyűt.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Képernyőkép, amely a HPE ProLiant ablakot jeleníti meg.":::

1. Lépjen a **System Utilities**  >  **rendszerkonfiguráció**  >  **ILO 5 konfigurációs segédprogram**  >  **hálózati beállítások** menüpontra.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="A rendszer konfigurációs ablakát megjelenítő képernyőkép.":::

    1.  Válassza a **megosztott hálózati port – lom** elemet a **hálózati** adapterek mezőjéből.
    
    1.  Tiltsa le a DHCP-t.
    
    1.  Adja meg az IP-címet, az alhálózati maszkot és az átjáró IP-címét.

1. Válassza az **F10: Save** elemet.

1. Válassza az **ESC** lehetőséget az **ILO 5 konfigurációs segédprogramjának** visszaszerzéséhez, majd válassza a **felhasználói kezelés** lehetőséget.

1. Válassza a **felhasználó szerkesztése/eltávolítása** lehetőséget. A rendszergazda az egyetlen alapértelmezett felhasználó által definiált. 

1. Módosítsa az alapértelmezett jelszót, és válassza az **F10: Save** lehetőséget.

### <a name="configure-the-hpe-bios"></a>A HPE BIOS konfigurálása

Az alábbi eljárás ismerteti, hogyan konfigurálhatja a HPE BIOS-t a vállalati és az SMB-készülékekhez.

A HPE BIOS konfigurálása:

1. Válassza a **rendszersegédprogramok**  >  **rendszerkonfiguráció**  >  **BIOS/platform konfigurálása (RBSU)** elemet.

1. A **BIOS/platform konfigurálása (RBSU)** képernyőn válassza a **rendszerindítási beállítások** lehetőséget.

1. Módosítsa a **rendszerindítási módot** **örökölt BIOS módra**, majd válassza az **F10: Save** lehetőséget.

1. A **rendszerkonfiguráció** űrlap bezárásához kattintson kétszer az **ESC** gombra.

#### <a name="for-the-enterprise-appliance"></a>A vállalati készülék esetében

1. Válassza **a beágyazott RAID 1: HPE Smart Array P408i – egy SR Gen 10**  >  **tömb konfigurációjának**  >  **létrehozása tömböt**.

1. A **tömb létrehozása** űrlapon válassza az összes lehetőséget. A **vállalati** készülék három lehetőség közül választhat.

#### <a name="for-the-smb-appliance"></a>Az SMB-készülék esetében

1. Válassza **a beágyazott RAID 1: HPE Smart Array P208i – egy SR Gen 10**  >  **tömb konfigurációjának**  >  **létrehozása tömböt**.

1. Válassza **a folytatás a következő űrlapra** lehetőséget.

1. A **RAID-szint beállítása** űrlapon állítsa a szintet a **RAID 5** értékre a vállalati telepítések és a **RAID 1** esetében az SMB-telepítések esetében.

1. Válassza **a folytatás a következő űrlapra** lehetőséget.

1. A **logikai meghajtó címkéje** űrlapon adja meg az **1. logikai meghajtót**.

1. Válassza a **módosítások elküldése** lehetőséget.

1. A **Küldés** űrlapon válassza a **vissza a főmenübe** lehetőséget.

1. Válassza az **F10: Save** , majd az **ESC billentyűt** kétszer.

1. A **Rendszersegédprogramok** ablakban válassza az **egyszeri rendszerindítás menüt**.

1. Az **egyszeri rendszerindítási menü** űrlapján válassza a **régi BIOS One-Time rendszerindító menü** lehetőséget.

1. Ekkor megjelenik a **rendszerindítás a régi** és a **rendszerindítás felülbírálásával** ablak. Válasszon egy rendszerindítási felülbírálási lehetőséget; például CD-ROM-, USB-, HDD-vagy UEFI-rendszerhéjhoz.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Képernyőkép, amely az első rendszerindítási felülbírálási ablakot mutatja.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="A második rendszerindítás felülbírálási ablakát bemutató képernyőkép.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Szoftver telepítése (HPE ProLiant DL20 Appliance)

A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször is újraindul.

A szoftver telepítése:

1. Kapcsolja össze a képernyőt és a billentyűzetet a berendezéssel, majd kapcsolódjon a CLI-hez.

1. Csatlakoztasson egy külső CD-t vagy lemezt a kulcshoz a Defender for IoT portál **frissítések** lapjáról letöltött ISO-lemezképpel.

1. Indítsa el a készüléket.

1. Válassza az **angol** lehetőséget.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Az angol nyelv kiválasztása a CLI-ablakban.":::

1. Válassza ki az **érzékelő-kiadás- <version> Enterprise** elemet.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Képernyőkép a képernyőről a verzió kiválasztásához.":::

1. A telepítővarázsló adja meg a berendezés profilját és a hálózati tulajdonságokat:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="A telepítő varázslót bemutató képernyőkép.":::

    | Paraméter | Konfiguráció |
    | ----------| ------------- |
    | **Hardverprofil** | Válassza a **vállalati** vagy az **Office** lehetőséget az SMB-telepítésekhez. |
    | **Felügyeleti felület** | **eno2** |
    | **Alapértelmezett hálózati paraméterek (általában az ügyfél által megadott paramétereket)** | **felügyeleti hálózati IP-cím:** <br/> <br/>**berendezés állomásneve:** <br/>**DNS** <br/>**az alapértelmezett átjáró IP-címe:**|
    | **bemeneti felületek:** | A rendszer létrehozza a bemeneti felületek listáját.<br/><br/>A bemeneti felületek tükrözéséhez másolja a listában bemutatott összes elemet egy vesszővel elválasztó karakterrel: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**HPE DL20 esetében: ne sorolja fel a eno1, a enp1s0f4u4 (iLo-felületek) listáját**<br/><br/>**Híd**: nincs szükség a híd interfész konfigurálására. Ez a beállítás csak különleges használati esetekben használható. A folytatáshoz nyomja le az **Enter** billentyűt. |

1. Körülbelül 10 perc elteltével megjelenik a hitelesítő adatok két halmaza. Az egyik egy **CyberX** -felhasználó, az egyik pedig egy **támogatási** felhasználó számára.

1. Mentse a készülék AZONOSÍTÓját és jelszavát. A platform első alkalommal való eléréséhez szüksége lesz a hitelesítő adatokra.

1. A folytatáshoz válassza az **ENTER billentyűt** .

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360 telepítése

  - Alapértelmezett rendszergazda felhasználó van megadva. Azt javasoljuk, hogy a hálózati konfiguráció során változtassa meg a jelszót.

  - A hálózati konfiguráció során konfigurálnia kell az iLO portot.

  - A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször is újraindul.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360 – elülső panel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360-előlap.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 hátlapja

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 hátlapja":::

### <a name="enable-remote-access-and-update-the-password"></a>Távoli hozzáférés engedélyezése és a jelszó frissítése

Tekintse meg a HPE ProLiant DL20 telepítésének előző fejezeteit:

  - "A távelérés engedélyezése és a jelszó frissítése"

  - "A HPE BIOS konfigurálása"

A vállalati konfiguráció azonos.

> [!Note]
> A tömb űrlapon ellenőrizze, hogy az összes beállítást kijelöli-e.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>iLO távoli telepítés (virtuális meghajtóról)

Ez az eljárás az iLO virtuális meghajtóról történő telepítését ismerteti.

Telepítés:

1. Jelentkezzen be az iLO-konzolra, majd kattintson a jobb gombbal a kiszolgálók képernyőjére.

1. Válassza a **HTML5-konzol** lehetőséget.

1. A konzolon válassza a CD ikont, majd válassza a CD/DVD lehetőséget.

1. Válassza a **helyi ISO-fájl** lehetőséget.

1. A párbeszédpanelen válassza ki a megfelelő ISO-fájlt.

1. Nyissa meg a bal oldali ikont, válassza a **teljesítmény** lehetőséget, majd kattintson az **Alaphelyzetbe állítás** elemre.

1. A készülék újraindul, és futtatja az érzékelő telepítési folyamatát.

### <a name="software-installation-hpe-dl360"></a>Szoftver telepítése (HPE DL360)

A telepítési folyamat körülbelül 20 percet vesz igénybe. A telepítés után a rendszer többször is újraindul.

Telepítés:

1. Kapcsolja össze a képernyőt és a billentyűzetet a berendezéssel, majd kapcsolódjon a CLI-hez.

1. Csatlakoztasson egy külső CD-t vagy lemezt egy kulcson a IoT portál **frissítések** lapjáról letöltött ISO-lemezképpel.

1. Indítsa el a készüléket.

1. Válassza az **angol** lehetőséget.

1. Válassza ki az **érzékelő-kiadás- <version> Enterprise** elemet.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="A verzió kijelölését bemutató képernyőkép.":::

1. A telepítővarázsló adja meg a berendezés profilját és a hálózat tulajdonságait.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="A telepítő varázslót bemutató képernyőkép.":::

    | Paraméter | Konfiguráció |
    | ----------| ------------- |
    | **Hardverprofil** | Válassza a **vállalati** lehetőséget. |
    | **Felügyeleti felület** | **eno2** |
    | **Alapértelmezett hálózati paraméterek (az ügyfél által biztosított)** | **felügyeleti hálózati IP-cím:** <br/>**alhálózati maszk:** <br/>**berendezés állomásneve:** <br/>**DNS** <br/>**az alapértelmezett átjáró IP-címe:**|
    | **bemeneti felületek:**  | A rendszer létrehoz egy listát a bemeneti felületek listájáról.<br/><br/>A bemeneti felületek tükrözéséhez másolja a listában bemutatott összes elemet egy vesszővel elválasztó karakterrel.<br/><br/> Nem kell konfigurálnia a híd felületet. Ez a beállítás csak különleges használati esetekben használható. |

1. Körülbelül 10 perc elteltével megjelenik a hitelesítő adatok két halmaza. Az egyik egy **CyberX** -felhasználó, az egyik pedig egy **támogatási** felhasználó számára.

1. Mentse a készülék AZONOSÍTÓját és jelszavát. Ezekre a hitelesítő adatokra akkor van szükség, amikor először szeretné elérni a platformot.

1. A folytatáshoz válassza az **ENTER billentyűt** .

## <a name="sensor-installation-for-the-virtual-appliance"></a>A virtuális berendezés érzékelő-telepítése

A Defender for IoT-érzékelőhöz tartozó virtuális gépet a következő architektúrákban helyezheti üzembe:


| Architektúra | Specifikációk | Használat | Megjegyzések |
|---|---|---|---|
| **Vállalat** | CPU: 8<br/>Memória: 32G RAM<br/>HDD: 1800 GB | Éles környezet | Alapértelmezett és leggyakoribb |
| **Kisvállalatok** | CPU: 4 <br/>Memória: 8G RAM<br/>HDD: 500 GB | Tesztelés vagy kis üzemi környezetek | -  |
| **Office** | CPU: 4<br/>Memória: 8G RAM<br/>HDD: 100 GB | Kis tesztelési környezetek | -  |

### <a name="prerequisites"></a>Előfeltételek

A helyszíni felügyeleti konzol támogatja a VMware és a Hyper-V telepítési lehetőségeket is. A telepítés megkezdése előtt győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

  - VMware (ESXi 5,5 vagy újabb) vagy Hyper-V hypervisor (Windows 10 Pro vagy Enterprise) telepítve és működőképes

  - A virtuális gép számára elérhető hardver-erőforrások

  - ISO telepítési fájl az Azure Defender for IoT-érzékelőhöz

Győződjön meg arról, hogy a hypervisor fut.

### <a name="create-the-virtual-machine-esxi"></a>A virtuális gép (ESXi) létrehozása

1. Jelentkezzen be az ESXi- **be, válassza ki a megfelelő** adattárat, majd válassza az **adattárbeli böngésző** elemet.

1. **Töltse fel** a képet, és válassza a **Bezárás** lehetőséget.

1. Nyissa meg a **Virtual Machines**, majd válassza a **virtuális gép létrehozása/regisztrálása** lehetőséget.

1. Válassza az **új virtuális gép létrehozása** lehetőséget, majd kattintson a **tovább** gombra.

1. Adja meg az érzékelő nevét, és válassza a következőket:

   - Kompatibilitás: **&lt; legújabb ESXi- &gt; verzió**

   - Vendég operációsrendszer-család: **Linux**

   - Vendég operációs rendszer verziója: **Ubuntu Linux (64 bites)**

1. Kattintson a **Tovább** gombra.

1. Válassza ki a megfelelő adattárt, és válassza a **tovább** lehetőséget.

1. Módosítsa a virtuális hardver paramétereit a szükséges architektúrának megfelelően.

1. Az **1. CD/DVD-meghajtó** esetében válassza az **adattár ISO-fájlja** elemet, és válassza ki a korábban feltöltött ISO-fájlt.

1. Válassza a **Tovább** > **Befejezés** lehetőséget.

### <a name="create-the-virtual-machine-hyper-v"></a>A virtuális gép létrehozása (Hyper-V)

Ez az eljárás azt ismerteti, hogyan hozható létre virtuális gép a Hyper-V használatával.

Virtuális gép létrehozása:

1. Hozzon létre egy virtuális lemezt a Hyper-V kezelőjében.

1. Válassza a **Format = VHDX** elemet.

1. Válassza a **típus = dinamikus bővítés** lehetőséget.

1. Adja meg a virtuális merevlemez nevét és helyét.

1. Adja meg a szükséges méretet (az architektúra szerint).   

1. Tekintse át az összegzést, és válassza a **Befejezés** lehetőséget.

1. A **műveletek** menüben hozzon létre egy új virtuális gépet.

1. Adja meg a virtuális gép nevét.

1. Válassza az  >  **1**. generációs generáció megadása lehetőséget.

1. Adja meg a memória kiosztását (az architektúra szerint), és jelölje be a dinamikus memória jelölőnégyzetét.

1. Konfigurálja a hálózati adaptert a kiszolgáló hálózati topológiája alapján.

1. Kapcsolódjon a korábban létrehozott VHDX a virtuális géphez.

1. Tekintse át az összegzést, és válassza a **Befejezés** lehetőséget.

1. Kattintson a jobb gombbal az új virtuális gépre, és válassza a **Beállítások** lehetőséget.

1. Válassza a **hardver hozzáadása** lehetőséget, és adjon hozzá egy új hálózati adaptert.

1. Válassza ki azt a virtuális kapcsolót, amely csatlakozni fog az érzékelő felügyeleti hálózatához.

1. A CPU-erőforrások (az architektúra alapján) foglalása.

1. A felügyeleti konzol ISO-rendszerképének csatlakoztatása egy virtuális DVD-meghajtóhoz.

1. Indítsa el a virtuális gépet.

2. A Szoftvertelepítés folytatásához a **műveletek** menüben válassza a **Kapcsolódás** lehetőséget.

### <a name="software-installation-esxi-and-hyper-v"></a>Szoftvertelepítés (ESXi és Hyper-V)

Ez a szakasz az ESXi és a Hyper-V szoftver telepítését ismerteti.

Telepítés:

1. Nyissa meg a virtuális gép konzolját.

1. A virtuális gép az ISO-rendszerképből indul el, és a nyelv kiválasztása képernyő fog megjelenni. Válassza az **angol** lehetőséget.

1. Válassza ki a szükséges architektúrát.

1. Adja meg a berendezés profilját és a hálózati tulajdonságokat:

    | Paraméter | Konfiguráció |
    | ----------| ------------- |
    | **Hardverprofil** | &lt;szükséges architektúra&gt; |
    | **Felügyeleti felület** | **ens192** |
    | **Hálózati paraméterek (az ügyfél által biztosított)** | **felügyeleti hálózati IP-cím:** <br/>**alhálózati maszk:** <br/>**berendezés állomásneve:** <br/>**DNS** <br/>**alapértelmezett átjáró:** <br/>**bemeneti felületek:**|
    | **híd felületek:** | Nincs szükség a híd felületének konfigurálására. Ez a beállítás csak a különleges használati esetekben használható. |

1. A beállítások elfogadásához adja meg az **Y** értéket.

1. A bejelentkezési hitelesítő adatok automatikusan jönnek létre és jelennek meg. Másolja a felhasználónevet és a jelszót egy biztonságos helyre, mert a bejelentkezéshez és a felügyelethez szükséges.

    - **Támogatás**: a rendszergazda felhasználó a felhasználók felügyeletéhez.

    - **CyberX**: a berendezéshez való hozzáférés gyökerének megfelelője.

1. A készülék újraindul.

1. A felügyeleti konzolt a korábban konfigurált IP-címen keresztül érheti el: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Képernyőkép, amely a felügyeleti konzolhoz való hozzáférést mutatja.":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Virtuális berendezés: helyszíni felügyeleti konzol telepítése

A helyszíni felügyeleti konzol virtuális gépe a következő architektúrákat támogatja:

| Architektúra | Specifikációk | Használat | 
|--|--|--|
| Enterprise <br/>(Alapértelmezett és leggyakoribb) | CPU: 8 <br/>Memória: 32G RAM<br/> HDD: 1,8 TB | Nagyméretű éles környezetek | 
| Enterprise | CPU: 4 <br/> Memória: 8G RAM<br/> HDD: 500 GB | Nagyméretű éles környezetek |
| Enterprise | CPU: 4 <br/>Memória: 8G RAM <br/> HDD: 100 GB | Kis tesztelési környezetek | 
   
### <a name="prerequisites"></a>Előfeltételek

A helyszíni felügyeleti konzol támogatja a VMware és a Hyper-V telepítési lehetőségeket is. A telepítés megkezdése előtt ellenőrizze a következőket:

- A VMware (ESXi 5,5 vagy újabb) vagy a Hyper-V hypervisor (Windows 10 Pro vagy Enterprise) telepítve van és működőképes.

- A hardver erőforrásai elérhetők a virtuális géphez.

- Rendelkezik a helyszíni felügyeleti konzol ISO-telepítési fájljával.
    
- A hypervisor fut.

### <a name="create-the-virtual-machine-esxi"></a>A virtuális gép (ESXi) létrehozása

Hozzon létre egy virtuális gépet (ESXi):

1. Jelentkezzen be az ESXi- **be, válassza ki a megfelelő** adattárat, majd válassza az **adattárbeli böngésző** elemet.

1. Töltse fel a képet, és válassza a **Bezárás** lehetőséget.

1. Lépjen **Virtual Machines**.

1. Válassza a **virtuális gép létrehozása/regisztrálása** lehetőséget.

1. Válassza az **új virtuális gép létrehozása** lehetőséget, és kattintson a **tovább** gombra.

1. Adja meg az érzékelő nevét, és válassza a következőket:

   - Kompatibilitási \<latest ESXi version>

   - Vendég operációsrendszer-család: Linux

   - Vendég operációs rendszer verziója: Ubuntu Linux (64 bites)

1. Kattintson a **Tovább** gombra.

1. Válassza a megfelelő adattár lehetőséget, majd kattintson a **Tovább gombra**.

1. Módosítsa a virtuális hardver paramétereit a szükséges architektúrának megfelelően.

1. Az **1. CD/DVD-meghajtó** esetében válassza az **adattár ISO-fájlja** elemet, és válassza ki a korábban feltöltött ISO-fájlt.

1. Válassza a **Tovább** > **Befejezés** lehetőséget.

### <a name="create-the-virtual-machine-hyper-v"></a>A virtuális gép létrehozása (Hyper-V)

Virtuális gép létrehozása a Hyper-V használatával:

1. Hozzon létre egy virtuális lemezt a Hyper-V kezelőjében.

1. Válassza ki a **VHDX** formátumát.

1. Kattintson a **Tovább** gombra.

1. Válassza a **dinamikus bővítés** típust.

1. Kattintson a **Tovább** gombra.

1. Adja meg a virtuális merevlemez nevét és helyét.

1. Kattintson a **Tovább** gombra.

1. Adja meg a szükséges méretet (az architektúra szerint).

1. Kattintson a **Tovább** gombra.

1. Tekintse át az összegzést, és válassza a **Befejezés** lehetőséget.

1. A **műveletek** menüben hozzon létre egy új virtuális gépet.

1. Kattintson a **Tovább** gombra.

1. Adja meg a virtuális gép nevét.

1. Kattintson a **Tovább** gombra.

1. Válassza a **Létrehozás** lehetőséget, és állítsa az **1. generációra**.

1. Kattintson a **Tovább** gombra.

1. Adja meg a memória kiosztását (az architektúra szerint), és jelölje be a dinamikus memória jelölőnégyzetét.

1. Kattintson a **Tovább** gombra.

1. Konfigurálja a hálózati adaptert a kiszolgáló hálózati topológiája alapján.

1. Kattintson a **Tovább** gombra.

1. Kapcsolódjon a korábban létrehozott VHDX a virtuális géphez.

1. Kattintson a **Tovább** gombra.

1. Tekintse át az összegzést, és válassza a **Befejezés** lehetőséget.

1. Kattintson a jobb gombbal az új virtuális gépre, majd válassza a **Beállítások** lehetőséget.

1. Válassza a **hardver hozzáadása** lehetőséget, és adjon hozzá egy új adaptert a **hálózati adapterhez**.

1. A **virtuális kapcsolónál** válassza ki azt a kapcsolót, amely csatlakozni fog az érzékelő felügyeleti hálózatához.

1. A CPU-erőforrások (az architektúra alapján) foglalása.

1. A felügyeleti konzol ISO-rendszerképének csatlakoztatása egy virtuális DVD-meghajtóhoz.

1. Indítsa el a virtuális gépet.

1. A Szoftvertelepítés folytatásához a **műveletek** menüben válassza a **Kapcsolódás** lehetőséget.

### <a name="software-installation-esxi-and-hyper-v"></a>Szoftvertelepítés (ESXi és Hyper-V)

A virtuális gép indításakor a rendszer elindítja a telepítési folyamatot az ISO-rendszerképből. A biztonság növelése érdekében létrehozhat egy második hálózati adaptert a helyszíni felügyeleti konzolon. Az egyik hálózati adapter dedikált a felhasználók számára, és támogatja az átjáró konfigurálását az átirányított hálózatokhoz. A második hálózati adapter egy IP-címtartomány összes csatlakoztatott érzékelőjét dedikálta.

Mindkét hálózati csatolón engedélyezve van a felhasználói felület (UI), és a felhasználói felület által támogatott összes funkció elérhető lesz a másodlagos hálózati adapteren, ha az Útválasztás nem szükséges. A magas rendelkezésre állás a másodlagos hálózati adapteren fog futni.

Ha úgy dönt, hogy nem telepít másodlagos hálózati adaptert, az összes funkció elérhető lesz az elsődleges hálózati adapteren keresztül. 

A szoftver telepítése:

1. Válassza az **angol** lehetőséget.

1. Válassza ki az üzemelő példányhoz szükséges architektúrát.

1. Adja meg az érzékelő felügyeleti hálózat hálózati adapterét: Interface, IP, alhálózat, DNS-kiszolgáló és alapértelmezett átjáró.

1. Választható Adjon hozzá egy második hálózati adaptert a helyszíni felügyeleti konzolhoz.

    1. `Please type sensor monitoring interface (Optional. Applicable when sensors are on a different network segment. For more information see the Installation instructions): <name of interface>`
    
    1. `Please type an IP address for the sensor monitoring interface (accessible by the sensors): <ip address>`
    
    1. `Please type a subnet mask for the sensor monitoring interface (accessible by the sensors): <subnet>`

1. A bejelentkezési hitelesítő adatok automatikusan jönnek létre és jelennek meg. Tartsa meg ezeket a hitelesítő adatokat biztonságos helyen, mert a bejelentkezéshez és az adminisztrációhoz szükségesek.

  - **Támogatás**: a rendszergazda felhasználó a felhasználók felügyeletéhez.

  - **CyberX**: a berendezéshez való hozzáférés gyökerének megfelelője.

1. A készülék újraindul.

1. A felügyeleti konzolt a korábban konfigurált IP-címen keresztül érheti el: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="A felügyeleti konzol bejelentkezési képernyőjén látható képernyőkép.":::

## <a name="post-installation-validation"></a>Telepítés utáni ellenőrzés

A fizikai berendezések telepítésének ellenőrzéséhez számos tesztet kell végrehajtania. Ugyanez az érvényesítési folyamat az összes berendezés típusára vonatkozik.

Hajtsa végre az ellenőrzést a grafikus felhasználói felület vagy a parancssori felület használatával. Az érvényesítés a felhasználói **támogatás** és a felhasználói **CyberX** számára érhető el.

A telepítés utáni ellenőrzésnek tartalmaznia kell a következő teszteket:

  - **Józan ész teszt**: Ellenőrizze, hogy fut-e a rendszer.

  - **Verzió**: Ellenőrizze, hogy helyes-e a verzió.

  - **ifconfig**: Ellenőrizze, hogy a telepítési folyamat során konfigurált összes bemeneti csatoló fut-e.

### <a name="checking-system-health-by-using-the-gui"></a>A rendszerállapot ellenőrzése a grafikus felhasználói felület használatával

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="A rendszerállapot-ellenőrzését bemutató képernyőkép.":::

#### <a name="sanity"></a>Józanság

- **Berendezés**: futtatja a berendezés józan eszét-ellenőrzését. Ugyanezt az ellenőrzést a CLI-parancs használatával is végrehajthatja `system-sanity` .

- **Verzió**: megjeleníti a készülék verzióját.

- **Hálózati tulajdonságok**: az érzékelő hálózati paramétereit jeleníti meg.

#### <a name="redis"></a>Redis

- **Memória**: megadja a memóriahasználat teljes képét, például a memória kihasználtságának mennyiségét és a hátralévő mennyiségét.

- **Leghosszabb kulcs**: a leghosszabb kulcsokat jeleníti meg, amelyek nagy mennyiségű memóriát okozhatnak.

#### <a name="system"></a>Rendszer

- **Alapnapló**: az alapnapló utolsó 500 sorát biztosítja, amely lehetővé teszi a legutóbbi naplófájlok megtekintését a teljes rendszernapló exportálása nélkül.

- **Feladatkezelő**: lefordítja a folyamatok táblázatban megjelenő feladatokat a következő rétegekbe: 
  
  - Állandó réteg (Redis) 
  - Cash Layer (SQL)

- **Hálózati statisztika**: megjeleníti a hálózati statisztikát.

- **Top**: a folyamatok táblázatát jeleníti meg. Ez egy Linux-parancs, amely dinamikus, valós idejű nézetet biztosít a futó rendszerről.

- **Biztonsági mentési memória ellenőrzése**: megadja a biztonsági mentési memória állapotát, és ellenőrzi a következőket:
  - A biztonsági mentési mappa helye 
  - A biztonsági mentési mappa mérete
  - A biztonsági mentési mappa korlátai
  - Az utolsó biztonsági mentés megtörténte után
  - Mennyi hely van a további biztonsági mentési fájlok számára

- **ifconfig**: megjeleníti a berendezés fizikai felületeinek paramétereit.

- **CyberX endszerekhez**: a hálózati forgalmat és a sávszélességet jeleníti meg a hat másodperces tesztek használatával.

- **Alapszintű hibák, napló**: az alapvető naplófájlban található hibákat jeleníti meg.

Az eszköz elérése:

1. Jelentkezzen be az érzékelőbe a **támogatási** felhasználó hitelesítő adataival.

1. Válassza ki a **rendszerstatisztika** elemet a **Rendszerbeállítások** ablakban.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>A rendszerállapot ellenőrzése a parancssori felület használatával

**1. teszt: józan ész**

Ellenőrizze, hogy a rendszer működik-e:

1. Kapcsolódjon a CLI-hez a Linux-terminállal (például a PuTTY) és a felhasználó **támogatásával**.

1. Írja be a következő szöveget: `system sanity`.

1. Győződjön meg arról, hogy az összes szolgáltatás zöld (fut).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="A futó szolgáltatásokat bemutató képernyőkép.":::

1. Ellenőrizze, hogy a **rendszer működik-e. (éles)** alul látható.

**2. teszt: verzió ellenőrzése**

Ellenőrizze, hogy a megfelelő verzió van-e használatban:

1. Kapcsolódjon a CLI-hez a Linux-terminállal (például a PuTTY) és a felhasználó **támogatásával**.

1. Írja be a következő szöveget: `system version`.

1. Győződjön meg arról, hogy a megfelelő verzió jelenik meg.

**3. teszt: hálózati ellenőrzés**

Ellenőrizze, hogy a telepítési folyamat során konfigurált összes bemeneti felület fut-e:

1. Kapcsolódjon a CLI-hez a Linux-terminállal (például a PuTTY) és a felhasználó **támogatásával**.

1. Adja meg a `network list` (a Linux-parancs megfelelőjét `ifconfig` ).

1. Ellenőrizze, hogy megjelenik-e a szükséges bemeneti felületek. Ha például két négymagos réz hálózati adapter van telepítve, akkor a listában 10 illesztőfelületnek kell szerepelnie.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Képernyőkép, amely a felületek listáját jeleníti meg.":::

**4. teszt: a KEZELŐFELÜLEThez való hozzáférés kezelése**

Ellenőrizze, hogy el tudja-e érni a konzol webes grafikus felhasználói felületét:

1. Csatlakoztasson egy laptopot egy Ethernet-kábellel a felügyeleti porthoz (**Gb1**).

1. Adja meg a laptop hálózati adapterének a berendezéssel megegyező tartományba tartozó címeit.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Képernyőkép, amely a KEZELŐFELÜLEThez való felügyeleti hozzáférést mutatja.":::

1. Pingelje a készülék IP-címét a laptopról a kapcsolat ellenőrzéséhez (alapértelmezett: 10.100.10.1).

1. Nyissa meg a Chrome böngészőt a laptopon, és adja meg a készülék IP-címét.

1. A **saját kapcsolatok nem privát** ablakban válassza a **speciális** és a Folytatás lehetőséget.

1. A teszt sikeres, ha megjelenik a Defender for IoT bejelentkezési képernyője.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Képernyőkép, amely a felügyeleti konzolhoz való hozzáférést mutatja.":::

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="you-cant-connect-by-using-a-web-interface"></a>Webes felületen keresztül nem lehet kapcsolatot létesíteni

1. Ellenőrizze, hogy a csatlakoztatni kívánt számítógép ugyanazon a hálózaton van-e, mint a készülék.

1. Ellenőrizze, hogy a GUI-hálózat csatlakoztatva van-e a felügyeleti porthoz.

1. Pingelje a készülék IP-címét. Ha nincs pingelés:

   1. Csatlakoztasson egy figyelőt és egy billentyűzetet a készülékhez.

   1. A bejelentkezéshez használja a **támogatási** felhasználót és a jelszót.

   1. A parancs használatával `network list` tekintse meg az aktuális IP-címet.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="A hálózat listáját bemutató képernyőkép.":::

1. Ha a hálózati paraméterek helytelenül vannak konfigurálva, akkor a következő eljárással módosíthatja azokat:

   1. Használja az parancsot `network edit-settings` .

   1. A felügyeleti hálózat IP-címének módosításához válassza az **Y** lehetőséget.

   1. Az alhálózati maszk módosításához válassza az **Y** lehetőséget.

   1. A DNS módosításához válassza az **Y** lehetőséget.

   1. Az alapértelmezett átjáró IP-címének módosításához válassza az **Y** lehetőséget.

   1. A bemeneti felület változása (csak szenzor) esetében válassza az **N** lehetőséget.

   1. A beállítások alkalmazásához válassza az **Y** lehetőséget.

1. Az újraindítás után kapcsolódjon a támogatási felhasználó hitelesítő adataival, és a `network list` parancs használatával ellenőrizze, hogy a paraméterek módosultak-e.

1. Próbálkozzon újra a pingeléssel, és kapcsolódjon újra a grafikus felhasználói felületről.

### <a name="the-appliance-isnt-responding"></a>A készülék nem válaszol

1. Csatlakoztasson egy figyelőt és egy billentyűzetet a berendezéshez, vagy használja a PuTTY-t a CLI-hez való távoli kapcsolódáshoz.

1. A bejelentkezéshez használja a **támogatási** felhasználó hitelesítő adatait.

1. Használja a `system sanity` parancsot, és győződjön meg arról, hogy az összes folyamat fut.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Képernyőkép, amely a rendszerjózanság parancsot mutatja.":::

Bármilyen egyéb probléma esetén forduljon [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>A függelék: tükrözési port a vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>SPAN port konfigurálása meglévő vSwitch

A vSwitch nem rendelkezik tükrözési képességekkel, de használhat egy megkerülő megoldást is a SPAN port megvalósításához.

A SPAN port konfigurálása:

1. Nyissa meg a vSwitch tulajdonságait.

1. Válassza a **Hozzáadás** lehetőséget.

1. Válassza a  >  **következőt**: virtuális gép.

1. Helyezzen be egy hálózati feliratú **tartományba tartozó hálózatot**, válassza   >  **az összes** VLAN-azonosító lehetőséget, majd kattintson a **tovább** gombra.

1. Válassza a **Befejezés** gombot.

1. Válassza a **span Network** > **Szerkesztés* lehetőséget.

1. Válassza a **Biztonság** lehetőséget, és győződjön meg arról, hogy a **kevert mód** házirendje az **elfogadás** módra van beállítva.

1. Válassza az **OK**, majd a **Bezárás** lehetőséget a vSwitch tulajdonságainak bezárásához.

1. Nyissa meg a **XSense virtuális gép** tulajdonságait.

1. A **2. hálózati adapter** esetében válassza ki a **span** hálózatot.

1. Válassza az **OK** lehetőséget.

1. Kapcsolódjon az érzékelőhöz, és ellenőrizze, hogy a tükrözés működik-e.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>B függelék: hozzáférés érzékelők a helyszíni felügyeleti konzolról

A rendszerbiztonság fokozása érdekében megakadályozhatja, hogy a felhasználók közvetlenül hozzáférjenek az érzékelőhöz. Ehelyett használja a proxy bújtatást, hogy a felhasználók egyetlen tűzfalszabály használatával férhessenek hozzá az érzékelőhöz a helyszíni felügyeleti konzolról. Ez a módszer leszűkíti az érzékelőn túli hálózati környezethez való jogosulatlan hozzáférés lehetőségét. A felhasználói élmény az érzékelőre való bejelentkezéskor is változatlan marad.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Képernyőkép, amely az érzékelőhöz való hozzáférést mutatja.":::

Bújtatás engedélyezése:

1. Jelentkezzen be a helyszíni felügyeleti konzol CLI- **CyberX** , vagy **támogassa** a felhasználói hitelesítő adatokat.

1. Írja be a következő szöveget: `sudo cyberx-management-tunnel-enable`.

1. Nyomja le az **Enter** billentyűt.

1. Írja be a következő szöveget: `--port 10000`.

### <a name="next-steps"></a>Következő lépések

[A hálózat beállítása](how-to-set-up-your-network.md)