---
title: A Cisco ISE pxGrid integrációjának ismertetése
description: A Defender IoT és a Cisco ISE pxGrid szolgáltatással való összevetése lehetővé teszi a biztonsági csapatoknak, hogy példátlan eszközként láthassák a vállalati ökoszisztémát.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784084"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>A Cisco ISE pxGrid integrációjának ismertetése

A IoT Defender a Blue-Team Experts által készített IoT kiberbiztonsági platformot biztosítja, amely a kritikus nemzeti infrastruktúrát, valamint az egyetlen olyan platformot tartalmazza, amely a szabadalmaztatott INTERNETKAPCSOLATtal rendelkező fenyegetések elemzését és a gépi tanulást is támogatja. A IoT Defender a következőket biztosítja:

- Azonnali információk az IC-eszközökről, a sebezhetőségekről, valamint az ismert és a zéró napi fenyegetésekről.

- IC-Aware – az OT-protokollok, az eszközök, az alkalmazások és a viselkedésük mély beágyazott ismerete.

- Automatizált ICS-veszélyforrások modellezési technológiája, amellyel előre jelezheti a célként megadott IC-támadások legvalószínűbb elérési útját a saját elemzések révén

## <a name="powerful-device-visibility-and-control"></a>Hatékony eszköz láthatósága és vezérlése

A Cisco ISE pxGrid való IoT-integrációs Defender a központosított láthatóságot, monitorozást és vezérlést biztosító új szintet biztosít az OT-környezet számára.

Ezek a Áthidalt platformok lehetővé teszik az eszközök automatikus láthatóságát és védelmét a korábban nem elérhető ICS-és IIoT-eszközök számára.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>IC-és IIoT-eszköz láthatósága: átfogó és mély

A IoT-technológiák szabadalmaztatott védelmezője átfogó és mélyreható INTERNETKAPCSOLATot és IIoT biztosít a vállalati adatkezeléshez.

Valós időben frissülnek az eszközök típusai, a gyártók, a nyitott portok, a sorozatszámok, a belső vezérlőprogram-változatok, az IP-címek és a MAC-címek. A IoT Defender a kritikus vállalati adatforrások integrálásával tovább növelheti a láthatóságot, a felderítést és az elemzést ebből az alaptervből. Ilyenek például a CMDBs, a DNS, a tűzfalak és a webes API-k.

Emellett a Defender for IoT platform ötvözi a passzív figyelést és opcionális szelektív tesztelési technikákat, amelyekkel a legpontosabb és részletes leltárt készíthet az ipari és kritikus fontosságú infrastrukturális szervezetekben található eszközökről.

### <a name="bridged-capabilities"></a>Áthidalt képességek

Ezen képességek a Cisco ISE pxGrid való áthidalása lehetővé teszi a biztonsági csapatoknak, hogy példátlan eszközként láthassák a vállalati ökoszisztémát.

A Cisco ISE pxGrid való zökkenőmentes és robusztus integráció biztosítja, hogy az OT-eszköz ne legyen felderítve, és ne maradjon meg az eszköz adatai.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="A végponti kategóriák mintája.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Minta végpontok a rendszeren":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Képernyőkép a rendszeren található attribútumokról.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Használati esetek lefedettsége: a Defender IoT-attribútumain alapuló ISE-szabályzatok

Hatékony ISE-szabályzatok használata a Defender által a IoT mély tanuláshoz az ICS és a IoT használati eseteinek kielégítése érdekében.

A szabályzatok használata lehetővé teszi a biztonsági ciklus bezárását és a hálózat valós idejű megfelelőségének biztosítását.

Például az ügyfelek használhatják a Defendert a IoT ICS és a IoT attribútumok használatával olyan házirendek létrehozásához, amelyek a következő használati eseteket kezelik, például:

- Engedélyezési házirend létrehozása, amely lehetővé teszi az ismert és engedélyezett eszközök bizalmas zónába való használatát az engedélyezett attribútumok alapján – például a Rockwell Automation PLC adott belső vezérlőprogram-verziója.

- Értesítse a biztonsági csapatokat, ha egy internetkapcsolattal nem rendelkező zónában észlelt egy ICS-eszközt.

- Elavult vagy nem megfelelő szállítókkal rendelkező gépek szervizelése.

- Szükség szerint karanténba helyezheti és letilthatja az eszközöket.

- Jelentések készítése a PLC vagy a RTUs-t futtató belső vezérlőprogram ismert sebezhetőségekkel (CVEs).

### <a name="about-this-article"></a>A cikkről

Ez a cikk azt ismerteti, hogyan állíthatja be a pxGrid és a Defender for IoT platformot annak biztosítására, hogy a IoT Defender a Cisco ISE-ben adja meg az OT-attribútumokat.

### <a name="getting-more-information"></a>További információk

A Cisco ISE pxGrid integrációs követelményeivel kapcsolatos további információkért lásd: <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Integrációs rendszerre vonatkozó követelmények

Ez a cikk az integrációs rendszerre vonatkozó követelményeket ismerteti:

Defender a IoT követelményeihez

- Defender a IoT 2,5-es verziójához

Cisco-követelmények

- pxGrid 2,0-es verzió

- Cisco ISE 2,4-es verzió

A hálózatra vonatkozó követelmények

- Győződjön meg arról, hogy a Defender for IoT berendezés hozzáfér a Cisco ISE felügyeleti felületéhez.

- Ellenőrizze, hogy van-e CLI-hozzáférése az összes Defender számára a IoT készülékekhez a vállalatnál.

> [!NOTE]
> Csak a MAC-címmel rendelkező eszközök szinkronizálhatók a Cisco ISE pxGrid.

## <a name="cisco-ise-pxgrid-setup"></a>Cisco ISE pxGrid-telepítő

Ez a cikk a következőket ismerteti:

- Kommunikáció beállítása a pxGrid

- Előfizetés a végponti eszköz témakörére

- Tanúsítványok előállítása

- PxGrid-beállítások megadása

## <a name="set-up-communication-with-pxgrid"></a>Kommunikáció beállítása a pxGrid

Ez a cikk a pxGrid-mel való kommunikáció beállítását ismerteti.

Kommunikáció beállítása:

1. Jelentkezzen be a Cisco ISE-be.

1. Válassza az **adminisztrációs** > **rendszer** és **központi telepítés** lehetőséget.

1. Válassza ki a szükséges csomópontot. Az általános beállítások lapon jelölje be a **pxGrid jelölőnégyzetet**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Győződjön meg arról, hogy a pxgrid jelölőnégyzet be van jelölve.":::

1. Válassza a **profilkészítési konfiguráció** fület.

1. Jelölje be a **pxGrid jelölőnégyzetet**. Adjon meg egy leírást.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="A Leírás hozzáadása képernyőkép":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Előfizetés a végponti eszköz témakörére

Győződjön meg arról, hogy az ISE pxGrid-csomópont előfizetett a végponti eszköz témakörre. Navigáljon a **felügyeleti** > **pxGrid Services** > **webes ügyfeleihez**. Itt ellenőrizheti, hogy az ISE előfizetett-e az Endpoint Device témakörre.

## <a name="generate-certificates"></a>Tanúsítványok előállítása

Ez a cikk a tanúsítványok létrehozását ismerteti.

Létrehozás:

1. Válassza ki az **Adminisztráció**  >  **pxGrid Services** elemet, majd válassza a **tanúsítványok** lehetőséget.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Tanúsítvány létrehozásához válassza a tanúsítványok lapot.":::

1. A szeretnék **mezőben válassza az** **egyetlen tanúsítvány létrehozása (tanúsítvány-aláírási kérelem nélkül)** lehetőséget.

1. Töltse ki a fennmaradó mezőket, és válassza a **Létrehozás** lehetőséget.

1. Hozza létre az ügyféltanúsítvány-kulcsot és a kiszolgálói tanúsítványt, majd alakítsa át a Java-alapú kulcstartó formátumára. Ezeket át kell másolnia az egyes Defender-IoT-érzékelőkre a hálózaton.

## <a name="define-pxgrid-settings"></a>PxGrid-beállítások megadása

Beállítások megadása:

1. Válassza az **Adminisztráció**  >  **pxGrid Services** elemet, majd válassza a **Beállítások** lehetőséget.

1. Engedélyezze az **új tanúsítványalapú fiókok automatikus jóváhagyását** , és **engedélyezze a jelszó-alapú fiókok létrehozását.**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Győződjön meg arról, hogy mindkét jelölőnégyzet be van jelölve.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>A Defender beállítása a IoT készülékekhez

Ez a cikk azt ismerteti, hogyan állítható be a Defender a IoT készülékekhez a pxGrid való kommunikációhoz. A konfigurációt minden olyan IoT készüléken végre kell hajtani, amely a Cisco ISE-be fogja adni az adatbevitelt.

Berendezések beállítása:

1. Jelentkezzen be az érzékelő CLI-be.

1. `trust.jks`A és a (z) másolása, amely korábban az érzékelőn jött létre. Másolja őket a következőre: `/var/cyberx/properties/` .

1. Szerkesztés `/var/cyberx/properties/pxgrid.properties` :

    1. Adjon hozzá egy kulcsot és egy megbízhatóságot, majd tárolja a fájlneveket és a jelszavakat.

    2. Adja hozzá a pxGrid-példány állomásnevét.

    3. `Enabled=true`

1. Indítsa újra a készüléket.

1. Ismételje meg ezeket a lépéseket a vállalat minden olyan készülékén, amely az adatbevitelt fogja beadni.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Észlelések megtekintése és kezelése a Cisco ISE-ben

1. A végponti észlelések az ISE környezet **láthatósági**  >  **végpontok** lapján jelennek meg.

1. Válassza a **házirend**-  >  **profilkészítés**  >  **Hozzáadás**  >  **szabályok**  >  **+ feltétel**  >  **új feltétel létrehozása** elemet.

1. Válassza ki az **attribútumot** , és a IOT-eszköz szótárak használatával hozzon létre egy profilkészítési szabályt a befecskendezett attribútumok alapján. A következő attribútumok vannak befecskendezve.

    - Eszköz típusa

    - Hardveres változat

    - IP-cím

    - MAC-cím

    - Name

    - Termék azonosítója

    - Protokoll

    - Sorozatszám

    - Szoftveres változat

    - Szállító

Csak a MAC-címmel rendelkező eszközök szinkronizálása történik meg.

## <a name="troubleshooting-and-logs"></a>Hibaelhárítás és naplók

A naplók a következő címen találhatók:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [riasztási információk továbbításáról](how-to-forward-alert-information-to-partners.md).
