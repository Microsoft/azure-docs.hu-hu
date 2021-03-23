---
title: Palo Alto-integráció
description: A IoT Defender a következő generációs tűzfalakkal integrálta a folyamatos ICS-figyelő platformot, amely lehetővé teszi a kritikus veszélyforrások, gyorsabb és hatékonyabb blokkolását.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783931"
---
# <a name="about-the-palo-alto-integration"></a>A Palo Alto-integráció ismertetése

A IoT Defender a következő generációs tűzfalakkal integrálta a folyamatos ICS-figyelő platformot, amely lehetővé teszi a kritikus veszélyforrások, gyorsabb és hatékonyabb blokkolását.

A következő integrációs típusok érhetők el:

- Automatikus blokkolási lehetőség: a közvetlen Defender a IoT-Palo Alto-integrációhoz

- Javaslatok küldése a központi felügyeleti rendszer blokkolására: Defender IoT-Panorama Integration

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>A megadott Palo Alto-tűzfal azonnali blokkolásának konfigurálása

Kritikus esetekben, például a kártevővel kapcsolatos riasztások esetén engedélyezheti az automatikus blokkolást. Ezt úgy teheti meg, hogy a Defender olyan továbbítási szabályát konfigurálja a IoT, amely letiltási parancsot küld közvetlenül egy adott Palo Alto-tűzfalra.

Ha a IoT Defender a kritikus fenyegetést azonosítja, riasztást küld, amely tartalmazza a fertőzött forrás blokkolásának lehetőségét. A riasztás részleteiben a **forrás blokkolása** elem kiválasztásával aktiválja a továbbítási szabályt, amely elküldi a blokkoló parancsot a megadott Palo Alto-tűzfalra.

Konfigurálás:

1. A bal oldali ablaktáblán válassza a **továbbítás** lehetőséget.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="A továbbítási riasztás képernyője.":::

1. Válassza a **továbbítási szabály létrehozása** lehetőséget.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Továbbítási szabály létrehozása":::

1. A Palo Alto NGFW továbbítási szabályának konfigurálása:  
 
   - Adja meg a szabványos szabály paramétereit, és a **műveletek** legördülő listából válassza a **Küldés a Palo Alto NGFW** lehetőséget.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="A továbbítási szabály szerkesztése.":::

1. A Műveletek ablaktáblán állítsa be a következő paramétereket:

   - **Gazdagép**: adja meg a NGFW-kiszolgáló IP-címét.
   - **Port**: adja meg a NGFW-kiszolgáló portját.
   - **Felhasználónév**: adja meg a NGFW-kiszolgáló felhasználónevét.
   - **Password (jelszó**): adja meg a NGFW-kiszolgáló jelszavát.
   - **Konfigurálás**: a következő beállítások beállításával engedélyezheti a gyanús források blokkolását a Palo Alto tűzfalon:
     - **Tiltott kódok blokkolása**: protokollok megsértése – az érvénytelen mező értéke sérti az ICS protokoll specifikációját (lehetséges kiaknázás).
     - A nem **engedélyezett PLC programozási/belső vezérlőprogram frissítéseinek letiltása**: jogosulatlan PLC-változások.
     - A nem **engedélyezett PLC leállításának tiltása**: PLC leállítása (leállás).
     - **Kártevők elleni riasztások letiltása**: az ipari kártevő-kísérletek blokkolása (TRITON, NotPetya stb.). Választhatja az **automatikus blokkolás** lehetőségét. Ebben az esetben a blokkolás automatikusan és azonnal elindul.
     - **Jogosulatlan vizsgálat letiltása**: jogosulatlan vizsgálat (lehetséges felderítés).
     
1. Válassza a **Küldés** lehetőséget.

A gyanús forrás blokkolása: 

1. A **riasztások** ablaktáblán válassza ki a Palo Alto-integrációval kapcsolatos riasztást. Megjelenik a **riasztás részletei** párbeszédpanel.
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Riasztás részletei":::

1. A gyanús forrás automatikus blokkolásához válassza a **forrás blokkolása** elemet. Megjelenik a **Confirm (megerősítés** ) párbeszédpanel.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Erősítse meg a blokkolást a Confirm (megerősítés) képernyőn.":::

1. A **megerősítés jóváhagyása** párbeszédpanelen kattintson az **OK gombra**. A gyanús forrást a Palo Alto tűzfal blokkolja.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Blokkoló házirendek küldése a Palo Alto Panorama-be

A IoT és a Palo Alto hálózatok védelmezője a polcon kívüli integrációt is tartalmaz, amely automatikusan új szabályzatokat hoz létre a Palo Alto Networks-ben, a Panorama-ben. Az integrációhoz a panoráma-rendszergazda megerősítést igényel, és nem engedélyezi az automatikus blokkolást.

Az integráció a következő incidensekhez készült:

- **Jogosulatlan PLC-változások:** Egy eszköz ladder-logikájának vagy belső vezérlőprogramjának frissítése. Ez legitim tevékenységet vagy az eszköz sérülésére tett kísérletet jelenthet. A kompromisszum a kártékony kód, például egy távoli elérésű trójai (patkány) vagy a fizikai folyamatot okozó paraméterek (például a fonási turbina) megadásával történhet, hogy nem biztonságos módon működjön.

- **Protokoll megsértése:** A protokoll specifikációját sértő csomagméret vagy mezőérték. Ez helytelenül konfigurált alkalmazást vagy rosszindulatú kísérletet jelenthet az eszköz sérülésére. Például a megcélzott eszköz puffer túlcsordulási feltételét okozza.

- **PLC leállítása:** Az eszköz működésének leállását okozó parancs, amely veszélyezteti a PLC által vezérelt fizikai folyamatot.

- **Az internetkapcsolatot megosztó hálózatban található ipari kártevő:** Kártevő szoftver, amely a natív protokollokkal, például a TRITONtal és a Industroyer-vel manipulálja az INTERNETKAPCSOLATot használó eszközöket. A Defender for IoT emellett észleli azokat a kártevőket is, amelyeket később áthelyezett az ICS-be és a SCADA környezetbe, például a Conficker, a WannaCry és a NotPetya.

- **Kártevők vizsgálata:** Felderítő eszközök, amelyek a Rendszerkonfigurációval kapcsolatos adatokat gyűjtenek a támadás előtti fázisban. A Havex trójai például az OPC-t használó eszközök ipari hálózatait vizsgálja, ez egy szabványos protokoll, amelyet a Windows-alapú SCADA rendszerek használnak az ICS-eszközökkel való kommunikációhoz.

## <a name="the-process"></a>A folyamat

Ha a IoT Defender előre konfigurált használati esetet észlel, a rendszer hozzáadja a **blokk forrása** gombot a riasztáshoz. Ezután, amikor a **CyberX** felhasználó kiválasztja a **forrás tiltása** gombot, a Defender for IoT házirendeket hoz létre a Panorama számára az előre definiált továbbítási szabály elküldésével.

A házirend csak akkor érvényes, ha a panoráma rendszergazdája leküldi a hálózat megfelelő NGFW.

Az informatikai hálózatokban lehetnek dinamikus IP-címek. Ezért ezekhez az alhálózatokhoz a szabályzatnak a teljes tartományneven (DNS-név) és nem az IP-címen kell alapulnia. A Defender for IoT fordított keresést hajt végre, és minden beállított számú órában megfelel a dinamikus IP-címmel rendelkező eszközöknek a teljes tartománynevének (DNS-név).

Emellett a Defender for IoT egy e-mailt küld a megfelelő panoráma-felhasználónak, hogy értesítést kapjon arról, hogy a Defender által létrehozott új szabályzat jóváhagyásra vár a IoT. Az alábbi ábra a Defender IoT-Panorama integrációs architektúráját mutatja be.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="CyberX – panoráma integrációs architektúra":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Panorama-blokkoló szabályzatok létrehozása a Defenderben a IoT-konfigurációhoz

### <a name="to-configure-dns-lookup"></a>A DNS-címkeresés konfigurálása

1. A bal oldali ablaktáblán válassza a **Rendszerbeállítások** elemet.

1. A **Rendszerbeállítások** panelen válassza a **DNS-beállítások** lehetőséget :::image type="icon" source="media/integration-paloalto/settings.png"::: .

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="Konfigurálja a DNS-beállításokat.":::

1. A **DNS-beállítások szerkesztése** párbeszédpanelen adja meg a következő paramétereket:

   - **Állapot**: a DNS-feloldó állapota.

   - **DNS-kiszolgáló címe**: adja meg a hálózati DNS-kiszolgáló IP-címét vagy teljes tartománynevét.
   - **DNS-kiszolgáló portja**: adja meg a DNS-kiszolgáló lekérdezéséhez használt portot.
   - **Alhálózatok**: állítsa be a dinamikus IP-cím alhálózati tartományát. Az a tartomány, amelyet a Defender a IoT megfordít, megkeresi az IP-címüket a DNS-kiszolgálón, hogy az megfeleljen a jelenlegi FQDN-névnek.
   - **Fordított keresés ütemezése**: adja meg az ütemezési beállításokat az alábbiak szerint:
     - Adott időpontok szerint: Itt adhatja meg, hogy mikor kell naponta elvégezni a névlekérdezést.
     - Rögzített időközökkel (órában): állítsa be a névkeresési művelet gyakoriságát.
   - **Címkék száma**: utasítsa a Defendert a IoT számára, hogy automatikusan oldja fel a hálózati IP-címeket az eszköz teljes tartománynevére. <br />A DNS FQDN-feloldás konfigurálásához adja meg a megjelenítendő tartományi címkék számát. Balról jobbra legfeljebb 30 karakter jelenik meg.
1. Válassza a **Mentés** lehetőséget.
1. A DNS-beállítások helyességének biztosításához válassza a **keresési teszt** lehetőséget. A teszt biztosítja, hogy a DNS-kiszolgáló IP-címe és a DNS-kiszolgáló portja megfelelően legyen beállítva.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Továbbítási szabály konfigurálása a feltételezett forgalom tiltásához a Palo Alto-tűzfallal

1. A bal oldali ablaktáblán válassza a **továbbítás** lehetőséget. Megjelenik a továbbítás panel.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="A továbbítási képernyő.":::

1. A **továbbítás** ablaktáblán válassza a **továbbítási szabály létrehozása** lehetőséget.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Továbbítási szabály létrehozása":::

1. A Palo Alto Panorama-továbbítási szabály konfigurálása:

   Adja meg a szabványos szabály paramétereit, és a **műveletek** legördülő listából válassza a **Küldés a Palo Alto panoramabe** lehetőséget. Megjelenik a művelet részletei ablaktábla.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Művelet kiválasztása":::

1. A Műveletek ablaktáblán állítsa be a következő paramétereket:

   - **Gazdagép**: adja meg a panoráma kiszolgáló IP-címét.

   - **Port**: adja meg a panoráma kiszolgáló portját.
   - **Felhasználónév**: adja meg a panoráma kiszolgáló felhasználónevét.
   - **Password (jelszó**): adja meg a panoráma kiszolgáló jelszavát.
   - **Jelentés címe**: határozza meg a blokkolás végrehajtását, a következőképpen:
   
     - **IP-cím szerint**: az IP-cím alapján mindig blokkolja a letiltási házirendeket a panorámában.
     
     - **FQDN vagy IP-cím szerint**: a (z) a teljes tartománynév alapján blokkolja a zárolási házirendeket, ha ez létezik, máskülönben az IP-cím.
     
   - **E-mail**: adja meg a szabályzat értesítő e-mail-címét
     > [!NOTE]
     > Győződjön meg arról, hogy konfigurált egy levelezési kiszolgálót a Defender for IoT. Ha nincs e-mail-cím megadva, a Defender for IoT nem küld értesítő e-mailt.
   - **DNS-címkeresés végrehajtása riasztások észlelése után (jelölőnégyzet)**: Ha a teljes tartománynév vagy IP-cím beállítás be van állítva a jelentés címében. Alapértelmezés szerint ez a jelölőnégyzet be van jelölve. Ha csak az IP-cím van beállítva, akkor ez a beállítás le van tiltva.
   - **Konfigurálás**: a következő beállítások beállításával engedélyezheti a gyanús források blokkolását a Palo Alto Panorama használatával:
   
     - **Tiltott kódok blokkolása**: protokollok megsértése – nem engedélyezett a mező értéke megsértve az internetkapcsolatot, a protokoll specifikációja (lehetséges kiaknázás).
     
     - A nem **engedélyezett PLC programozási/belső vezérlőprogram frissítéseinek letiltása**: jogosulatlan PLC-változások.
     
     - A nem **engedélyezett PLC leállításának tiltása**: PLC leállítása (leállás).
     
     - **Kártevők elleni riasztások letiltása**: az ipari kártevő-kísérletek blokkolása (TRITON, NotPetya stb.). Választhatja az **automatikus blokkolás** lehetőségét. Ebben az esetben a blokkolás automatikusan és azonnal elindul.
     
     - **Jogosulatlan vizsgálat letiltása**: jogosulatlan vizsgálat (lehetséges felderítés).
     
1. Válassza a **Küldés** lehetőséget.

### <a name="to-block-the-suspicious-source"></a>A gyanús forrás blokkolása

1. A **riasztások** ablaktáblán válassza ki a Palo Alto-integrációval kapcsolatos riasztást. Megjelenik a **riasztás részletei** párbeszédpanel.

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Riasztás részletei":::        

1. A gyanús forrás automatikus blokkolásához válassza a **forrás blokkolása** elemet.

1. A **megerősítés jóváhagyása** párbeszédpanelen kattintson az **OK gombra.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Ellenőrizze":::

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [riasztási információk továbbításáról](how-to-forward-alert-information-to-partners.md).
