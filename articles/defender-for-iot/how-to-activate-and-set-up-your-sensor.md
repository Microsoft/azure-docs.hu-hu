---
title: Az érzékelő aktiválása és beállítása
description: Ez a cikk azt ismerteti, hogyan jelentkezhet be és aktiválható az érzékelő konzolja.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 98d59ec4b37238005e89051064a60fa6a4407394
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449832"
---
# <a name="activate-and-set-up-your-sensor"></a>Az érzékelő aktiválása és beállítása

Ez a cikk bemutatja, hogyan aktiválhatja az érzékelőket, és hogyan hajthatja végre a kezdeti telepítést.

A rendszergazda felhasználói aktiválást hajtanak végre az első bejelentkezéskor, valamint az aktiválási felügyelet megadását. A telepítő biztosítja, hogy az érzékelő az optimális észleléshez és riasztáshoz legyen konfigurálva.

A biztonsági elemzők és a csak olvasási jogosultsággal rendelkező felhasználók nem aktiválnak érzékelőket, és nem hozhatnak új jelszót.

## <a name="sign-in-and-activation-for-administrator-users"></a>Bejelentkezés és aktiválás rendszergazda felhasználók számára

Azoknak a rendszergazdáknak, akik első alkalommal jelentkeznek be, ellenőrizniük kell, hogy hozzáférnek-e az érzékelő bevezetése során letöltött aktiválási és jelszó-helyreállítási fájlokhoz. Ha nem, szükségük van az Azure biztonsági rendszergazdára, az előfizetés közreműködői vagy az előfizetés tulajdonosára, hogy ezeket a fájlokat az Azure Defender for IoT portálon lehessen létrehozni.

### <a name="first-time-sign-in-and-activation-checklist"></a>Bejelentkezési és aktiválási ellenőrzőlista első alkalommal

Az érzékelő konzolba való bejelentkezés előtt a rendszergazda felhasználóknak hozzáféréssel kell rendelkezniük a következőhöz:

- A telepítés során definiált érzékelő IP-címe.

- Az érzékelő felhasználói bejelentkezési hitelesítő adatai. Ha letöltött egy ISO-t az érzékelőhöz, használja a telepítés során kapott alapértelmezett hitelesítő adatokat. Azt javasoljuk, hogy az aktiválás után hozzon létre egy új *rendszergazda* felhasználót.

- Egy kezdeti jelszó. Ha egy előre konfigurált érzékelőt vásárolt a nyílból, akkor az első bejelentkezéskor jelszót kell létrehoznia.

- Az érzékelőhöz tartozó aktiválási fájl. A fájl létrehozása és letöltése a Defender for IoT portálon az érzékelő bevezetése során történt.

- Egy, a vállalat által igényelt SSL/TLS-HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány.

### <a name="about-activation-files"></a>Az aktiválási fájlok ismertetése

Az érzékelőt egy adott felügyeleti módban IoT az Azure Defender számára:

| Mód típusa | Description |
|--|--|
| **Felhőhöz csatlakoztatott mód** | Az érzékelő által észlelt információk az érzékelő konzolján jelennek meg. A riasztási adatokat az IoT hub is továbbítja, és más Azure-szolgáltatásokkal, például az Azure Sentinelrel is megosztható. |
| **Helyileg csatlakoztatott mód** | Az érzékelő által észlelt információk az érzékelő konzolján jelennek meg. Az észlelési információk a helyszíni felügyeleti konzollal is megoszthatók, ha az érzékelő csatlakoztatva van hozzá. |

Egy helyileg csatlakoztatott vagy felhőben csatlakoztatott aktiválási fájl lett létrehozva, és a rendszer az előkészítés során letöltötte az érzékelőt. Az aktiválási fájl az érzékelő felügyeleti módjára vonatkozó utasításokat tartalmazza. *A rendszer minden egyes üzembe helyezett érzékelőre feltölt egy egyedi aktiválási fájlt.*  Amikor először jelentkezik be, fel kell töltenie az érzékelő megfelelő aktiválási fájlját.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender a IoT-portálon, bevezető érzékelő.":::

### <a name="about-certificates"></a>Információ a tanúsítványokról

Az érzékelő telepítése után létrejön egy helyi önaláírt tanúsítvány, amely az érzékelő konzoljának elérésére szolgál. Miután a rendszergazda első alkalommal bejelentkezik a konzolra, a rendszer az SSL/TLS-tanúsítvány bevezetését kéri.

Két biztonsági szint érhető el:

- A HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány feltöltésével teljesítheti a szervezet által kért bizonyos tanúsítvány-és titkosítási követelményeket.
- A felügyeleti konzol és a csatlakoztatott érzékelők közötti ellenőrzés engedélyezése. Az érvényesítés kiértékelése a visszavont tanúsítványok listáján és a tanúsítvány lejárati dátumán történik. *Ha az ellenőrzés sikertelen, a felügyeleti konzol és az érzékelő közötti kommunikáció leáll, és egy érvényesítési hiba jelenik meg a konzolon.* Ez a beállítás alapértelmezés szerint engedélyezve van a telepítés után.  

A konzol a következő típusú tanúsítványokat támogatja:

- Magán-és nagyvállalati kulcsokra épülő infrastruktúra (privát PKI)

- Nyilvános kulcsokra épülő infrastruktúra (nyilvános PKI)

- Helyileg generált a készüléken (helyileg önaláírt) 

  > [!IMPORTANT]
  > Azt javasoljuk, hogy ne használja az alapértelmezett önaláírt tanúsítványt. A tanúsítvány nem biztonságos, és csak tesztelési környezetekhez használható. Nem lehet érvényesíteni a tanúsítvány tulajdonosát, és a számítógép biztonsága nem tartható fenn. Soha ne használja ezt a lehetőséget éles hálózatokhoz.

### <a name="sign-in-and-activate-the-sensor"></a>Bejelentkezés és az érzékelő aktiválása

A bejelentkezéshez és az aktiváláshoz:

1. Nyissa meg az érzékelő konzolt a böngészőben a telepítés során megadott IP-cím használatával. Megnyílik a bejelentkezés párbeszédpanel.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender a IoT-érzékelőhöz.":::

1. Adja meg az érzékelő telepítésekor megadott hitelesítő adatokat, vagy válassza a **jelszó-helyreállítás** lehetőséget. Ha előre konfigurált érzékelőt vásárolt a nyílból, előbb létrehoz egy jelszót. A jelszó-helyreállítással kapcsolatos további információkért lásd: a [jelszó meghibásodásának vizsgálata a kezdeti bejelentkezéskor](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. A bejelentkezést követően megnyílik az **aktiválás** párbeszédpanel. Válassza a **feltöltés** lehetőséget, és lépjen az érzékelő bevezetése során letöltött aktiválási fájlhoz.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Válassza a feltöltés lehetőséget, majd nyissa meg az aktiválási fájlt.":::

1. Ha az aktiválás előtt módosítani szeretné az érzékelő hálózati konfigurációját, válassza az **érzékelő hálózati konfiguráció** hivatkozást. Az [Aktiválás előtt lásd: az érzékelő hálózati konfigurációjának frissítése](#update-sensor-network-configuration-before-activation).

1. Fogadja el a használati feltételeket.

1. Válassza az **aktiválás** lehetőséget. Megnyílik az SSL/TLS-tanúsítvány párbeszédpanel.

1. Adja meg a tanúsítvány nevét.
1. Töltse fel a CRT és a kulcs fájljait.
1. Adja meg a hozzáférési kódot, és töltse fel a PEM-fájlt, ha szükséges.
1. Kattintson a **Tovább** gombra. Megnyílik az érvényesítési képernyő. Alapértelmezés szerint a felügyeleti konzol és a csatlakoztatott érzékelők közötti ellenőrzés engedélyezve van.
1. Az ellenőrzés letiltásához kapcsolja ki a **rendszerszintű ellenőrzés engedélyezése** kapcsolót. Javasoljuk, hogy engedélyezze az érvényesítést.
1. Kattintson a **Mentés** gombra.  

Előfordulhat, hogy a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány feltöltése után frissítenie kell a képernyőt.

További információ az új tanúsítvány, a támogatott tanúsítvány paramétereinek feltöltéséről, valamint a CLI-tanúsítvány parancsainak használatáról: [Egyéni érzékelők kezelése](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Az érzékelő hálózati konfigurációjának frissítése az aktiválás előtt  

Az érzékelő hálózati konfigurációs paraméterei definiálva lettek a Szoftvertelepítés során, vagy ha előre konfigurált érzékelőt vásárolt. A következő paraméterek lettek definiálva:

- IP-cím
- DNS
- Alapértelmezett átjáró
- Alhálózati maszk
- Állomásnév

Előfordulhat, hogy frissítenie kell ezt az információt az érzékelő aktiválása előtt. Előfordulhat például, hogy módosítania kell a nyíl által meghatározott előre konfigurált paramétereket. A proxybeállításokat az érzékelő aktiválása előtt is megadhatja.

Az érzékelő hálózati konfigurációs paramétereinek frissítése:

1. Válassza az **érzékelő hálózati konfiguráció** hivatkozását az **aktiválás** párbeszédpanelben.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Érzékelő hálózati konfigurációja.":::

2. Megjelenik a telepítés során meghatározott paraméterek. A proxy definiálásának lehetősége is elérhető. Szükség szerint frissítse a beállításokat, majd válassza a **Mentés** lehetőséget.

### <a name="subsequent-sign-ins"></a>Későbbi bejelentkezések

Az első aktiválás után a bejelentkezés után megnyílik az Azure Defender for IoT-érzékelő konzol, és nem igényel aktiválási fájlt. Csak a bejelentkezési hitelesítő adataira van szükség.

A bejelentkezés után megnyílik az Azure Defender for IoT-konzol.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Azure Defender a IoT-konzolhoz.":::

## <a name="initial-setup-and-learning-for-administrators"></a>Kezdeti beállítás és tanulás (rendszergazdák számára)

Az első bejelentkezés után az Azure Defender for IoT érzékelő automatikusan megkezdi a hálózat figyelését. A hálózati eszközök megjelennek az eszközök leképezése és az eszközök leltározása szakaszban. A IoT készült Azure Defender megkezdi a hálózatban előforduló biztonsági és működési incidensek észlelését és riasztását. Ezután jelentéseket és lekérdezéseket hozhat létre az észlelt információk alapján.

Kezdetben ez a tevékenység tanulási módban zajlik, amely arra utasítja az érzékelőt, hogy megismerje a hálózat szokásos tevékenységeit. Az érzékelő például megtanulja a hálózatban felderített eszközöket, a hálózatban észlelt protokollokat, valamint az adott eszközök közötti fájlátvitelt. Ez a tevékenység lesz a hálózat alapterv-tevékenysége.

### <a name="review-and-update-basic-system-settings"></a>Alapszintű rendszerbeállítások áttekintése és frissítése

Tekintse át az érzékelő rendszerbeállításait, és ellenőrizze, hogy az érzékelő konfigurálva van-e az optimális észleléshez és riasztáshoz.

Adja meg az érzékelő rendszerbeállításait. Például:

- Az ICS (vagy IoT) és az elkülönített alhálózatok definiálása.

- Adja meg a helyhez tartozó protokollok aliasait.

- Adja meg a használatban lévő VLAN-okat és neveket.

- Ha a DHCP használatban van, adja meg a legitim DHCP-tartományokat.

- Szükség szerint határozza meg a Active Directory és a levelezési kiszolgáló integrációját.

### <a name="disable-learning-mode"></a>Tanulási mód letiltása

A rendszerbeállítások módosítása után lehetővé teheti, hogy az Azure Defender a IoT-érzékelő tanulási módban fusson, amíg nem érzi, hogy a rendszer észlelése pontosan tükrözze a hálózati tevékenységet.

A tanulási mód a hálózat méretétől és összetettségtől függően 2 – 6 hétig fut. A tanulási mód letiltása után az alaptervtől eltérő tevékenységek riasztást indítanak.

A tanulási mód letiltása:

- Válassza a **Rendszerbeállítások** lehetőséget, és kapcsolja ki a **tanulási** lehetőséget.

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Első bejelentkezés a biztonsági elemzők és a csak olvasási jogosultsággal rendelkező felhasználók számára

A bejelentkezés előtt ellenőrizze, hogy rendelkezik-e a következővel:

- Az érzékelő IP-címe.
- A rendszergazda által megadott bejelentkezési hitelesítő adatok.

## <a name="console-tools-overview"></a>Konzol eszközei: áttekintés

A konzol eszközei az oldal menüjéből érhetők el.

**Navigáció** 

| Ablak | Ikon | Description |
| -----------|--|--|
| Irányítópult | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Megtekintheti a hálózat biztonsága állapotának intuitív pillanatképét. |
| Eszköz térképe | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | A hálózati eszközök, az eszközök kapcsolatai és az eszköz tulajdonságainak megtekintése a térképen. A hálózat megjelenítéséhez különböző nagyítások, csúcsfények és szűrési lehetőségek érhetők el. |
| Eszköz leltározása | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | Az eszközök leltára az érzékelő által észlelt eszközbeállítások listáját jeleníti meg. A következő lehetőségek érhetők el: <br /> -Rendezheti vagy szűrheti az adatokat a tábla mezőinek megfelelően, és megtekintheti a megjelenő szűrt adatokat. <br /> – Adatok exportálása CSV-fájlba. <br /> – Importálja a Windows beállításjegyzékének részleteit.|
| Riasztások | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Riasztások megjelenítése, ha a szabályzat megsértése bekövetkezik, az alapkonfigurációtól való eltérés, vagy a hálózatban bármilyen gyanús tevékenység észlelhető. |
| Jelentések | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Az adatbányászati lekérdezéseken alapuló jelentések megtekintése. |

**Elemzés**

| Ablak| Ikon | Description |
|---|---|---|
| Esemény idővonala | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Megtekintheti a riasztásokkal, a hálózati eseményekkel (tájékoztatással) és a felhasználói műveletekkel kapcsolatos információkat, például a felhasználói bejelentkezéseket és a felhasználók törlését.|

**Navigáció**

| Ablak | Ikon | Description |
|---|---|---|
| Adatbányászat | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Átfogó és részletes információkat hozhat a hálózat eszközeiről a különböző rétegeken. |
| Vizsgálat | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | A trendeket és a statisztikát a widgetek széles skáláján tekintheti meg. |
| Kockázatértékelés | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | A **biztonsági rések** ablak megjelenítése. |

**Felügyelet**

| Ablak | Ikon | Description |
|---|---|---|
| Felhasználók | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Felhasználók és szerepkörök definiálása különböző hozzáférési szintekkel. |
| Továbbítás | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Továbbíthatja a riasztási információkat a partnereknek és a belső forrásoknak (például az Azure Sentinelnek) a Defender for IoT, az e-mail-címek, a webhook-kiszolgálók és sok egyéb szolgáltatás integrálásával. <br /> További részletekért lásd: [riasztási információk továbbítása](how-to-forward-alert-information-to-partners.md) . |
| Rendszerbeállítások | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Konfigurálja a rendszerbeállításokat. Például adja meg a DHCP-beállításokat, adja meg a levelezési kiszolgáló adatait, vagy hozzon létre port-aliasokat. |
| Importálási beállítások | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Az **importálási beállítások** ablak megjelenítése. Az eszköz adataiban manuálisan is végezhet módosításokat.<br /> További részletekért lásd az [eszköz adatainak importálása](how-to-import-device-information.md) című témakört. |

**Támogatás**

| Ablak| Ikon | Description |
|----|---|---|
| Támogatás | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Segítségért forduljon [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/) . |

## <a name="see-also"></a>Lásd még

[Érzékelő előkészítése](getting-started.md#onboard-a-sensor)

[Érzékelő-aktiválási fájlok kezelése](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[A monitorozandó forgalom szabályozása](how-to-control-what-traffic-is-monitored.md)
