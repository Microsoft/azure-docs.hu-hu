---
title: A helyszíni felügyeleti konzol aktiválása és beállítása
description: A felügyeleti konzol aktiválása biztosítja, hogy az érzékelők regisztrálva legyenek az Azure-ban, és információt küldjenek a helyszíni felügyeleti konzolra, valamint arról, hogy a helyszíni felügyeleti konzol felügyeleti feladatokat hajt végre a csatlakoztatott érzékelőkön.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/18/2021
ms.topic: how-to
ms.service: defender-for-iot
ms.openlocfilehash: f659b25abbbad5f2150ed44766d30dc1f64dd3bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602756"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>A helyszíni felügyeleti konzol aktiválása és beállítása 

A helyszíni felügyeleti konzol aktiválása és beállítása biztosítja a következőket:

- A csatlakoztatott érzékelőkön keresztül figyelt hálózati eszközök regisztrálva vannak egy Azure-fiókkal.

- Az érzékelők adatokat küldenek a helyszíni felügyeleti konzolra.

- A helyszíni felügyeleti konzol felügyeleti feladatokat végez a csatlakoztatott érzékelőkön.

- Telepített egy SSL-tanúsítványt.

## <a name="sign-in-for-the-first-time"></a>Bejelentkezés első alkalommal

Bejelentkezés a felügyeleti konzolra:

1. A rendszer telepítése során navigáljon a helyszíni felügyeleti konzolhoz kapott IP-címre.
 
1. A rendszer telepítése során adja meg a helyszíni felügyeleti konzolhoz kapott felhasználónevet és jelszót. 


Ha elfelejtette a jelszavát, válassza a **jelszó helyreállítása**  lehetőséget, és tekintse meg a jelszó- [helyreállítás](how-to-manage-the-on-premises-management-console.md#password-recovery) című témakört, amely útmutatást nyújt a jelszó helyreállításához.

## <a name="get-and-upload-an-activation-file"></a>Aktiválási fájl beszerzése és feltöltése

Az első bejelentkezés után aktiválni kell a helyszíni felügyeleti konzolt az aktiválási fájl beszerzésével és feltöltésével. 

Aktiválási fájl beszerzése:

1. Navigáljon az Azure Defender IoT-portál **díjszabási** lapjára. 
1. Válassza ki azt az előfizetést, amelyhez a helyszíni felügyeleti konzolt hozzá kívánja rendelni.
1. Válassza az **aktiválási fájl letöltése a felügyeleti konzolhoz** lapot. Az aktiválási fájl letöltése megtörténik.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Töltse le az aktiválási fájlt.":::

Aktiválási fájl feltöltése:

1. Navigáljon a **Rendszerbeállítások** lapra a helyszíni felügyeleti konzolon.
1. Válassza az **aktiválás** ikont :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/activation-icon.png" border="false"::: .
1. Válassza a **fájl kiválasztása** lehetőséget, majd válassza ki a letöltött fájlt.

A kezdeti aktiválás után a figyelt eszközök száma túllépheti a bevezetéskor definiált véglegesített eszközök számát. Ez akkor fordul elő, ha további érzékelőkkel csatlakozik a felügyeleti konzolhoz. Ha a figyelt eszközök száma és a véglegesített eszközök száma között eltérés tapasztalható, egy figyelmeztetés jelenik meg a felügyeleti konzolon. Ha ez történik, töltsön fel egy új aktiválási fájlt.

## <a name="set-up-a-certificate"></a>Tanúsítvány beállítása

A felügyeleti konzol telepítése után létrejön egy helyi önaláírt tanúsítvány, amely a konzol elérésére szolgál. Miután a rendszergazda első alkalommal bejelentkezett a felügyeleti konzolra, a rendszer az SSL/TLS-tanúsítvány bevezetését kéri. 

Két biztonsági szint érhető el:

- A HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány feltöltésével teljesítheti a szervezet által kért bizonyos tanúsítvány-és titkosítási követelményeket.
- A felügyeleti konzol és a csatlakoztatott érzékelők közötti ellenőrzés engedélyezése. Az érvényesítés kiértékelése a visszavont tanúsítványok listáján és a tanúsítvány lejárati dátumán történik. *Ha az ellenőrzés sikertelen, a felügyeleti konzol és az érzékelő közötti kommunikáció leállt, és a konzolon egy érvényesítési hiba jelenik meg.* Ez a beállítás alapértelmezés szerint engedélyezve van a telepítés után.  

A konzol a következő típusú tanúsítványokat támogatja:

- Magán-és nagyvállalati kulcsokra épülő infrastruktúra (privát PKI)

- Nyilvános kulcsokra épülő infrastruktúra (nyilvános PKI)

- Helyileg generált a készüléken (helyileg önaláírt) 

  > [!IMPORTANT]
  > Javasoljuk, hogy ne használjon önaláírt tanúsítványt. A tanúsítvány nem biztonságos, és csak tesztelési környezetekhez használható. Nem lehet érvényesíteni a tanúsítvány tulajdonosát, és a számítógép biztonsága nem tartható fenn. Soha ne használja ezt a lehetőséget éles hálózatokhoz.

Tanúsítvány feltöltése:

1. Ha a rendszer a bejelentkezés után kéri, adja meg a tanúsítvány nevét.
1. Töltse fel a CRT és a kulcs fájljait.
1. Szükség esetén adjon meg egy hozzáférési kódot, és töltsön fel egy PEM-fájlt.

Előfordulhat, hogy a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány feltöltése után frissítenie kell a képernyőt.

A felügyeleti konzol és a csatlakoztatott érzékelők közötti ellenőrzés letiltása:

1. Kattintson a **Tovább** gombra.
1. A **rendszerszintű ellenőrzés engedélyezése** váltógomb kikapcsolása.

Új tanúsítvány, támogatott tanúsítványfájl és kapcsolódó elemek feltöltésével kapcsolatos információkért lásd: [a helyszíni felügyeleti konzol kezelése](how-to-manage-the-on-premises-management-console.md).

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Érzékelők összekapcsolása a helyszíni felügyeleti konzollal

Gondoskodnia kell arról, hogy az érzékelők adatokat küldjenek a helyszíni felügyeleti konzolra, valamint arról, hogy a helyszíni felügyeleti konzol biztonsági mentést végezzen, kezelje a riasztásokat, és végezzen el más tevékenységet az érzékelőkön. Ehhez kövesse az alábbi eljárásokat annak ellenőrzéséhez, hogy az érzékelők és a helyszíni felügyeleti konzol között van-e kezdeti kapcsolat.

Két lehetőség érhető el az Azure Defender IoT-érzékelőkhöz a helyszíni felügyeleti konzolhoz való csatlakoztatásához:

- Kapcsolódjon az érzékelő konzolról

- Kapcsolat bújtatás használatával

A csatlakozás után be kell állítania egy helyet az érzékelőkkel.

### <a name="connect-sensors-from-the-sensor-console"></a>Érzékelők összekötése az érzékelő konzolról

Adott érzékelők összekapcsolása a helyszíni felügyeleti konzollal az érzékelő konzolról:

1. Az érzékelő konzol bal oldali paneljén válassza a **Rendszerbeállítások** elemet.

2. Válassza **a felügyelethez való kapcsolódás** lehetőséget.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="Képernyőkép a helyszíni felügyeleti konzol állapotjelző ablakáról, amely a kapcsolat nélküli állapotot jeleníti meg.":::

3. A **cím** szövegmezőbe írja be annak a helyszíni felügyeleti konzolnak az IP-címét, amelyhez csatlakozni szeretne.

4. Válassza a **Kapcsolódás** lehetőséget. Az állapot módosul:

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="Képernyőkép a helyszíni felügyeleti konzol állapotjelző ablakáról, amely a csatlakoztatott állapotot mutatja.":::

### <a name="connect-sensors-by-using-tunneling"></a>Érzékelők összekötése bújtatás használatával

Engedélyezzen biztonságos bújtatási kapcsolatot a szervezeti érzékelők és a helyszíni felügyeleti konzol között. A telepítő kikerüli az interakciót a szervezeti tűzfallal, és ennek eredményeképpen csökkenti a támadási felületet.

A bújtatás használata lehetővé teszi a helyszíni felügyeleti konzolhoz való csatlakozást az IP-címről, és egyetlen portot (azaz 9000) bármely érzékelőhöz.

Bújtatás beállítása a helyszíni felügyeleti konzolon:

- Jelentkezzen be a helyszíni felügyeleti konzolba, és futtassa a következő parancsokat:

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

Bújtatás beállítása az érzékelőn:

1. Az érzékelőn (Network. properties) a 9000-es TCP-portot manuálisan nyissa meg. Ha a port nincs megnyitva, akkor az érzékelő elutasítja a csatlakozást a helyszíni felügyeleti konzolról.

2. Jelentkezzen be az egyes érzékelőkbe, és futtassa a következő parancsokat:

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Hely beállítása

Az alapértelmezett vállalati Térkép általános áttekintést nyújt az eszközökről a földrajzi hely különböző szintjeinek megfelelően.

Előfordulhat, hogy az eszközök nézete szükséges, ahol a szervezeti struktúra és a felhasználói engedélyek összetettak. Ilyen esetekben előfordulhat, hogy a hely beállítását egy globális szervezeti struktúra határozza meg, a normál hely vagy a zóna szerkezete mellett.

A környezet támogatásához létre kell hoznia egy globális üzleti topológiát, amely a szervezete üzleti egységei, régiói, helyei és zónái alapján érhető el. Hozzáférési csoportok használatával is meg kell határoznia a felhasználói hozzáférési engedélyeket ezen entitások köré.

A hozzáférési csoportok lehetővé teszik, hogy a felhasználók felügyelik és elemezzék az eszközöket a Defender for IoT platformon.

### <a name="how-it-works"></a>Működés

Az egyes helyekhez megadhat egy üzleti egységet és egy régiót. Ezután hozzáadhat zónákat, amelyek logikai entitások a hálózatban. 

Mindegyik zónához legalább egy érzékelőt hozzá kell rendelni. Az öt szintű modell biztosítja a szervezet struktúráját tükröző védelmi rendszer biztosításához szükséges rugalmasságot és részletességet.

A helyeket közvetlenül a Térkép bármelyik nézetéről módosíthatja. Amikor térképes nézetből nyit meg egy helyet, a megnyitott riasztások száma az egyes zónák mellett jelenik meg.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Képernyőkép a helyszíni felügyeleti konzol térképéről a berlini adatátfedéssel.":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Az érzékelőket és a regionális kapcsolatot bemutató diagram.":::

Hely beállítása:

1. Vegyen fel új üzleti egységeket a szervezet logikai struktúrájának megfelelően.

2. Vegyen fel új régiókat a szervezet régióinak megfelelően.

3. Adjon hozzá egy helyet.

4. Zónák hozzáadása egy helyhez.

5. Kapcsolja össze az érzékelőket.

6. Érzékelő társítása a helyek zónájához.

Üzleti egységek hozzáadása:

1. A vállalati nézetben válassza a **minden hely**  >  **részlegek kezelése Munkaegységek** lehetőséget.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="Képernyőfelvétel: az üzleti egységek kezelése nézet.":::

2. Adja meg az új üzleti egység nevét, és válassza a **Hozzáadás** lehetőséget.

Új régió hozzáadása:

1. A vállalati nézetben válassza a **minden régió**  >  **terület kezelése** lehetőséget.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="A régiók kezelése nézetet bemutató képernyőkép.":::

2. Adja meg az új régió nevét, és válassza a **Hozzáadás** lehetőséget.

Új hely hozzáadása:

1. A vállalati nézetben válassza a :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: felső sávot. A kurzor plusz jelre () jelenik meg **+** .

2. Helyezze **+** el az új hely helyét, és jelölje ki. Megnyílik az **új hely létrehozása** párbeszédpanel.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Képernyőkép az új hely létrehozása nézetről.":::

3. Adja meg az új hely nevét és fizikai címeit, majd válassza a **Mentés** lehetőséget. Az új hely megjelenik az oldaltérképen.

Hely törlése:

1. A **hely kezelése** ablakban válassza ki azt :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a sávot, amely a hely nevét tartalmazza, majd válassza a **hely törlése** lehetőséget. Megjelenik a megerősítő mező, és ellenőrizze, hogy szeretné-e törölni a helyet.

2. A megerősítés mezőben válassza az **Igen** lehetőséget. A megerősítési mező bezárul, és a **hely kezelése** ablak a törölt hely nélkül jelenik meg.

## <a name="create-enterprise-zones"></a>Vállalati zónák létrehozása

A zónák logikai entitások, amelyek lehetővé teszik, hogy a különböző jellemzők szerint csoportosítsa az eszközöket egy adott helyen belül. Létrehozhat például csoportokat az üzemi vonalakhoz, alállomásokhoz, hely területekhez vagy eszközök típusaihoz. A zónák a szervezete számára megfelelő sajátosságok alapján adhatók meg.

A zónák a hely konfigurációs folyamatának részeként konfigurálhatók.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Képernyőkép a hely felügyeleti zónák nézetéről.":::

A következő táblázat a **hely kezelése** ablakban található paramétereket ismerteti.

| Paraméter | Leírás |
|--|--|
| Név | Az érzékelő neve. Ezt a nevet csak az érzékelőből lehet módosítani. További információ: Defender for IoT felhasználói útmutató. |
| IP | Az érzékelő IP-címe. |
| Verzió | Az érzékelő verziója. |
| Kapcsolat | Az érzékelő kapcsolati állapota. Az állapot lehet **csatlakoztatni** vagy **leválasztani**. |
| Legutóbbi frissítés | Az utolsó frissítés dátuma. |
| Frissítési folyamat | A folyamatjelző sáv a frissítési folyamat állapotát mutatja a következőképpen:<br />– Csomag feltöltése<br />– Felkészülés a telepítésre<br />– Folyamatok leállítása<br />– Az adatbiztonsági mentés<br />-Pillanatkép készítése<br />– Konfiguráció frissítése<br />-Függőségek frissítése<br />-Kódtárak frissítése<br />– Adatbázisok javítása<br />-Folyamatok indítása<br />-A rendszerjózanság ellenőrzése<br />– Az érvényesítés sikerült<br />– Sikeres<br />– Hiba<br />– A frissítés elindult<br />-Telepítés indításaogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >A frissítéssel kapcsolatos részletekért tekintse meg a [Microsoft ügyfélszolgálata](https://support.microsoft.com/) segítséget. |
| Eszközök | Az érzékelő által figyelt eszközök száma. |
| Riasztások | A riasztások száma az érzékelőn. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Az érzékelő zónákhoz való hozzárendelésének engedélyezése. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Lehetővé teszi a leválasztott érzékelő törlését a helyről. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Azt jelzi, hogy jelenleg hány érzékelő van csatlakoztatva a zónához. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Azt jelzi, hogy jelenleg hány eszköz van csatlakoztatva a zónához. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | A zónához rendelt érzékelők által elküldett riasztások számát jelzi. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Érzékelők kiosztása a zónákból. |

Zóna hozzáadása egy helyhez:

1. A **hely kezelése** ablakban válassza ki azt :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a sávot, amely a hely nevét tartalmazza, majd válassza a **zóna hozzáadása** elemet. Megjelenik az **új zóna létrehozása** párbeszédpanel.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Képernyőkép az új zóna létrehozása nézetről.":::

2. Adja meg a zóna nevét.

3. Adja meg az új zóna leírását, amely egyértelműen meghatározza a hely zónákra osztásához használt jellemzőket.

4. Válassza a **Mentés** lehetőséget. Az új zóna megjelenik a **hely kezelése** ablakban azon hely alatt, amelyhez ez a zóna tartozik.

Zóna szerkesztése:

1. A **hely kezelése** ablakban válassza ki azt :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a sávot, amely tartalmazza a zóna nevét, majd válassza a **zóna szerkesztése** lehetőséget. Megjelenik a **zóna szerkesztése** párbeszédpanel.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="A zóna szerkesztése párbeszédpanelt megjelenítő képernyőkép.":::

2. Szerkessze a zóna paramétereit, majd válassza a **Mentés** lehetőséget.

Zóna törlése:

1. A **hely kezelése** ablakban válassza ki azt :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a sávot, amely tartalmazza a zóna nevét, majd válassza a **zóna törlése** lehetőséget.

2. A megerősítés mezőben válassza az **Igen** lehetőséget.

Szűrés a kapcsolat állapota szerint:

- A bal felső sarokban válassza a tovább lehetőséget a :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: **kapcsolat** elemnél, majd válasszon a következő lehetőségek közül:

  - **All**: megadja az összes, a helyszíni felügyeleti konzolra jelentett érzékelőt.

  - **Csatlakoztatva**: a csak csatlakoztatott érzékelőket mutatja be.

  - **Leválasztva**: csak a leválasztott érzékelőket jeleníti meg.

Szűrés a frissítési állapotnak megfelelően:

- A bal felső sarokban kattintson a :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: Tovább gombra a **frissítés állapota** elemre, és válasszon a következő lehetőségek közül:

  - **All**: megadja az összes, a helyszíni felügyeleti konzolra jelentett érzékelőt.

  - **Érvényes**: az érzékelők érvényes frissítési állapottal rendelkeznek.

  - **Folyamatban**: a verziófrissítési folyamatban lévő érzékelőket mutatja be.

  - **Sikertelen**: olyan érzékelőket jelenít meg, amelyek frissítési folyamata meghiúsult.

## <a name="assign-sensors-to-zones"></a>Érzékelők kiosztása zónákhoz

Minden zónához hozzá kell rendelnie a helyi forgalom elemzését és a riasztásokat végrehajtó érzékelőket. Csak a helyszíni felügyeleti konzolhoz csatlakozó érzékelők rendelhetők hozzá.

Érzékelő kiosztása:

1. Válassza a **hely kezelése** lehetőséget. A nem hozzárendelt érzékelők a párbeszédpanel bal felső sarkában jelennek meg.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Képernyőkép a nem hozzárendelt érzékelők nézetről.":::

2. Ellenőrizze, hogy a **kapcsolat** állapota csatlakoztatva van-e. Ha nem, tekintse [meg az érzékelők csatlakoztatása a helyszíni felügyeleti konzolhoz](#connect-sensors-to-the-on-premises-management-console) című témakört a csatlakozással kapcsolatos részletekért. 

3. Válassza ki :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: a hozzárendelni kívánt érzékelőt.

4. Az **érzékelő** kiosztása párbeszédpanelen válassza ki a hozzárendelni kívánt üzleti egységet, régiót, helyet és zónát.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Képernyőkép az érzékelő kiosztása nézetről.":::

5. Válassza a **hozzárendelés** lehetőséget.

Érzékelő hozzárendelésének megszüntetése és törlése:

1. Válassza le az érzékelőt a helyszíni felügyeleti konzolról. További részletekért lásd: [érzékelők összekapcsolása a helyszíni felügyeleti konzolhoz](#connect-sensors-to-the-on-premises-management-console) .

2. A **hely kezelése** ablakban válassza ki az érzékelőt, és válassza a elemet :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: . Az érzékelő néhány pillanat múlva megjelenik a hozzá nem rendelt érzékelők listájában.

3. Ha törölni szeretné a nem hozzárendelt érzékelőt a helyről, válassza ki az érzékelőt a hozzá nem rendelt érzékelők listájából, és válassza a lehetőséget :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: .

## <a name="see-also"></a>Lásd még

[Az érzékelő és a helyszíni felügyeleti konzol hibaelhárítása](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
