---
title: A Azure Security Centerhoz mellékelt Microsoft Defender for Endpoint-licenc használata
description: Ismerkedjen meg a Microsoft Defender for Endpoint szolgáltatással, és telepítse azt Azure Security Centerból.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: memildin
ms.openlocfilehash: 9a8dba99435e1616b1c49d5209b30c3c523876db
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071340"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>A végpontok Security Center integrált EDR megoldással való ellátása: a Microsoft Defender for Endpoint

A Microsoft Defender for Endpoint egy holisztikus, felhőalapú végponti biztonsági megoldás. Főbb funkciói a következők:

- Kockázatalapú sebezhetőségi felügyelet és Értékelés 
- Támadási felület csökkentése
- Viselkedésen alapuló és felhőben működő védelem
- Végpontok észlelése és válasza (EDR)
- Automatikus vizsgálat és szervizelés
- Felügyelt vadászati szolgáltatások

> [!TIP]
> Eredetileg a **Windows DEFENDER ATP**-ként indult el, ez a végpont-észlelési és-válasz (EDR) termék a 2019-as, a **Microsoft Defender ATP**-ként lett átnevezve.
>
> A Ignite 2020-on elindítottuk a [Microsoft DEFENDER XDR Suite](https://www.microsoft.com/security/business/threat-protection) -ot, és ez a EDR-összetevő a **Microsoft Defender for Endpoint** névre lett átnevezve.


## <a name="availability"></a>Rendelkezésre állás

| Szempont                          | Részletek                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kiadás állapota:                  | Általánosan elérhető (GA)                                                                                                                                                                                                                                                                                      |
| Árképzési                        | [Az Azure Defender for Servers](security-center-pricing.md) használatát igényli                                                                                                                                                                                                                                             |
| Támogatott platformok:            | Windows rendszerű Azure-gépek<br>Windows rendszerű Azure arc-gépek|
| A Windows támogatott verziói:  |  • A Security Center támogatja az észlelést a Windows Server 2016, 2012 R2 és 2008 R2 SP1 rendszerben<br> • A kiszolgálói végpont monitorozása ezzel az integrációval le lett tiltva az Office 365 GCC-ügyfelek számára<br> • Nincs támogatás a Windows Server 2019, a Windows 10 1703 (és újabb) vagy a Linux rendszerhez|
| Szükséges szerepkörök és engedélyek: | Az integráció engedélyezése/letiltása: **biztonsági rendszergazda** vagy **tulajdonos**<br>MDATP-riasztások megtekintése a Security Center-ben: **biztonsági olvasó**, **olvasó**, **erőforráscsoport-közreműködő**, **erőforráscsoport-tulajdonos**, **biztonsági rendszergazda**, **előfizetés tulajdonosa** vagy **előfizetéshez tartozó közreműködő**|
| Felhők                         | ![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Kínai gov, egyéb gov<br>![No](./media/icons/no-icon.png) A munkaterheléseket futtató GCC-ügyfelek a globális Azure-felhőkben                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |


## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>A Microsoft Defender for Endpoint szolgáltatásai Security Center

A Microsoft Defender for Endpoint a következőket biztosítja:

- **Speciális, a szabálysértés utáni észlelési érzékelők**. A Windows rendszerű gépekhez tartozó, a végponti érzékelőkhöz tartozó Defender a viselkedési jelek nagy tömbjét gyűjti.

- **Elemzésen alapuló, felhőalapú, szabálysértés utáni észlelés**. A Defender for Endpoint gyorsan alkalmazkodik a fenyegetések változásához. Fejlett elemzési és big dataeket használ. Az ismeretlen fenyegetések észlelése érdekében a Intelligens biztonsági gráf hatékonysága fokozza a Windows, az Azure és az Office közötti jelekkel. Kezelhető riasztásokat biztosít, és lehetővé teszi a gyors reagálást.

- **Fenyegetések felderítése**. A Defender for Endpoint riasztásokat állít elő, amikor a támadó eszközöket, technikákat és eljárásokat azonosítja. A Microsoft Threat Hunters és a biztonsági csapatok által létrehozott, a partnerek által biztosított intelligenciával kiegészített adatok használatával működik.

A Defender Security Center-nal való integrálásával a következő kiegészítő képességeket veheti igénybe:

- **Automatizált** előkészítés. A Security Center automatikusan engedélyezi a Microsoft Defender for Endpoint Sensor használatát a Security Center által figyelt összes Windows-kiszolgálóhoz. A Windows Server 2019 rendszert futtatók kivételével, amelyeket helyi parancsfájllal, Csoportházirend objektummal (GPO) vagy a [Microsoft Endpoint Configuration Manager](https://docs.microsoft.com/mem/configmgr/) (korábban SCCM) kell bevezetni.

- **Egyetlen** üvegtábla. A Security Center-konzolon a Microsoft Defender for Endpoint riasztások jelennek meg. További vizsgálathoz használja a Microsoft Defendert a végpont saját portáljának oldalain, ahol további információkat talál, például a riasztási folyamat fáját és az incidens diagramot. Megtekintheti a részletes gépi idővonalat is, amely egy 6 hónapos időszakra visszamenőlegesen mutatja be az összes viselkedést.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="A Microsoft Defender a végpont saját Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender az Endpoint bérlő helyéhez

Ha a Azure Security Center használatával figyeli a kiszolgálókat, a rendszer automatikusan létrehoz egy Microsoft Defender for Endpoint bérlőt. A Defender által a végponthoz gyűjtött adatokat a rendszer a bérlő földrajzi helyén tárolja a kiépítés során azonosított módon. Ügyféladatok – az álneves formában – a Egyesült Államok központi tároló-és feldolgozási rendszereiben is tárolhatók. 

A hely konfigurálása után nem módosítható. Ha át kell helyeznie az adatait egy másik helyre, lépjen kapcsolatba Microsoft ügyfélszolgálata a bérlő alaphelyzetbe állításához.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>A Microsoft Defender engedélyezése az Endpoint Integration számára

1. **Az Azure Defender engedélyezése a kiszolgálókhoz**. Tekintse meg [a Azure Security Center díjszabását](security-center-pricing.md#enable-azure-defender).

    > [!NOTE]
    > Az Azure arc-kompatibilis gépek biztonsága érdekében kövesse a következő témakör utasításait [: hibrid gép összekapcsolása Azure arc-kompatibilis kiszolgálókkal](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).

1. Ha már licencelte és telepítette a Microsoft Defendert a kiszolgálókon a végpontokhoz, távolítsa el azt a [regisztrációjának megszüntetésére szolgáló Windows Servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)című témakörben leírt eljárással.
1. A Security Center menüjében válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki a módosítani kívánt előfizetést.
1. Válassza a **veszélyforrások észlelése** lehetőséget.
1. Jelölje be **az adathozzáférés engedélyezése a Microsoft Defender számára a végpont számára** lehetőséget, majd válassza a **Mentés** lehetőséget.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Az integráció engedélyezése Azure Security Center és a Microsoft EDR megoldása, a Microsoft Defender for Endpoint":::

    A Azure Security Center automatikusan előkészíti a kiszolgálókat a Microsoft Defender számára a végponthoz. A bevezetést akár 24 óráig is eltarthat.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Hozzáférés a Microsoft Defender for Endpoint portálhoz

1. Győződjön meg arról, hogy a felhasználói fiók rendelkezik a szükséges engedélyekkel. [További információ](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Győződjön meg arról, hogy rendelkezik-e olyan proxyval vagy tűzfallal, amely blokkolja a névtelen forgalmat. Az Endpoint szenzorhoz tartozó Defender a rendszerkörnyezetből csatlakozik, így a névtelen forgalomnak engedélyezettnek kell lennie. A következő témakörben található útmutatást követve biztosíthatja, hogy a rendszer ne akadályozza meg a Defender számára a végponti portált: a [hozzáférés engedélyezése a szolgáltatás URL-címeihez a proxykiszolgálón](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. Nyissa meg a [Microsoft Defender Security Center portált](https://securitycenter.windows.com/). A portál szolgáltatásairól és ikonjairól a [Microsoft Defender Security Center portál áttekintésében](/windows/security/threat-protection/microsoft-defender-atp/portal-overview)olvashat bővebben. 

## <a name="send-a-test-alert"></a>Teszt riasztás küldése

Jóindulatú Microsoft Defender létrehozása a végponti tesztelési riasztáshoz:

1. Hozzon létre egy "C:\test-MDATP-test" mappát.
1. A Távoli asztal használatával elérheti a Windows Server 2012 R2 vagy a Windows Server 2016 rendszerű virtuális gépeket.
1. Nyisson meg egy parancssori ablakot.
1. A parancssorba másolja és futtassa a következő parancsot. A parancssori ablak automatikusan bezáródik.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Egy parancssori ablak, amely a parancs használatával teszt riasztást hoz elő.":::

1. Ha a parancs sikeres, egy új riasztás jelenik meg a Azure Security Center irányítópulton és a Microsoft Defender for Endpoint portálon. Ez a riasztás néhány percet is igénybe vehet.
1. A Security Center riasztásának áttekintéséhez lépjen a **biztonsági riasztások**  >  **gyanús PowerShell parancssori** elemre.
1. A vizsgálat ablakban válassza ki a Microsoft Defender for Endpoint portálra mutató hivatkozást.


## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Gyakori kérdések a Security Center integrált Microsoft Defender for Endpoint

- [Milyen licencelési követelmények vonatkoznak a Microsoft Defender for Endpoint szolgáltatásra?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Ha már van licencem a Microsoft Defender for Endpoint szolgáltatáshoz, kedvezményt kaphatok az Azure Defender számára?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Hogyan váltani egy külső gyártótól származó EDR eszközről?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Milyen licencelési követelmények vonatkoznak a Microsoft Defender for Endpoint szolgáltatásra?
Az **Azure Defender for Servers** szolgáltatáshoz nem tartozik további díj a Defender for Endpoint szolgáltatáshoz. Azt is megteheti, hogy külön megvásárolható a 50-es vagy újabb gépeken.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Ha már van licencem a Microsoft Defender for Endpoint szolgáltatáshoz, kedvezményt kaphatok az Azure Defender számára?
Ha már rendelkezik Microsoft Defender-licenccel a végponthoz, nem kell fizetnie az Azure Defender-licenc adott részének.

A kedvezmény megerősítéséhez lépjen kapcsolatba Security Center támogatási csapatával, és adja meg a megfelelő munkaterület-azonosítót, régiót és licenc-információkat.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Hogyan váltani egy külső gyártótól származó EDR eszközről?
A nem a Microsofttól származó végponti megoldásról történő váltásra vonatkozó részletes utasítások a Microsoft Defender for Endpoint dokumentációjában találhatók: [áttelepítési áttekintés](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Center által támogatott platformok és szolgáltatások](security-center-os-coverage.md)
- [Azure Security Center biztonsági javaslatainak kezelése](security-center-recommendations.md): Ismerje meg, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.