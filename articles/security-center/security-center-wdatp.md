---
title: Az alkalmazáshoz mellékelt Microsoft Defender for Endpoint licenc Azure Security Center
description: Ismerje meg a Végponthoz való Microsoft Defendert, és hogyan helyezheti üzembe Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: e12578fa6da679587d41fb25b17b00eb1645299a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718412"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>A végpontok védelme a Security Center integrált EDR-megoldásával: Microsoft Defender végponthoz

A Microsoft Defender for Endpoint egy holisztikus, felhőben elérhető végpontbiztonsági megoldás. A fő funkciói a következőek:

- Kockázatalapú biztonsági rések kezelése és értékelése 
- Támadási felület csökkentése
- Viselkedésalapú és felhőalapú védelem
- Végpontészlelés és -válasz (EDR)
- Automatikus vizsgálat és szervizelés
- Felügyeltkeresési szolgáltatások

> [!TIP]
> Az eredetileg **ATP Windows Defender ként** indított végpontészlelés és -válasz (Endpoint Detection and Response, EDR) termék 2019-ben Microsoft Defender ATP-ként lett **átnevezve.**
>
> Az Ignite 2020-ban elindítottuk a [Microsoft Defender XDR](https://www.microsoft.com/security/business/threat-protection) csomagot, és ez az EDR-összetevő a Következő végpontra lett **átnevezve: Microsoft Defender.**


## <a name="availability"></a>Rendelkezésre állás

| Szempont                          | Részletek                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kiadási állapot:                  | Általánosan elérhető (GA)                                                                                                                                                                                                                                                                                      |
| Árképzés:                        | A [kiszolgálókhoz Azure Defender szükséges](defender-for-servers-introduction.md)                                                                                                                                                                                                                                             |
| Támogatott platformok:            |  • Windowst futtató Azure-gépek<br> • Azure Arc Windows rendszerű gépek|
| A Windows támogatott verziói az észleléshez:  |  • Windows Server 2019, 2016, 2012 R2 és 2008 R2 SP1<br> • [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)<br> • [Windows 10 Enterprise (korábban](../virtual-desktop/windows-10-multisession-faq.yml) Enterprise for Virtual Desktops (EVD)|
| Nem támogatott operációs rendszerek:  |  • Windows 10 (nem EVD vagy WVD)<br> • Linux|
| Szükséges szerepkörök és engedélyek: | Az integráció engedélyezése/letiltása: **Biztonsági rendszergazda vagy** **tulajdonos**<br>Az MDATP-riasztások megtekintéséhez a **Security Center: biztonsági olvasó,** **Olvasó,** **Erőforráscsoport-közreműködő, Erőforráscsoport-tulajdonos,** Biztonsági **rendszergazda,** **Előfizetés** tulajdonosa vagy **Előfizetés-közreműködő** |
| Felhők:                         | ![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov, Other Gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Microsoft Defender a végponti szolgáltatásokhoz a Security Center

A Microsoft Defender for Endpoint a következő eket biztosítja:

- **Speciális behatolásészlelési érzékelők.** A Windows rendszerű gépek végpontérzékelőihez való Defender a viselkedési jelek hatalmas tömbjét gyűjti össze.

- **Elemzésalapú, felhőalapú, biztonsági incidens utáni észlelés.** A Defender for Endpoint gyorsan alkalmazkodik a változó fenyegetésekhez. Fejlett analitikát és big data. Ezt a funkciókat a power of the power of the power of Intelligens biztonsági gráf with signals across Windows, Azure, and Office to detect unknown threats (Az alkalmazás a windowsos, az Azure-beli és az Office-Intelligens biztonsági gráf az ismeretlen fenyegetések észlelése érdekében). Beavatkozást lehetővé tő riasztásokat biztosít, és lehetővé teszi a gyors válaszadást.

- **Fenyegetések felderítése**. A Defender for Endpoint riasztásokat hoz létre, amikor támadóeszközöket, technikákat és eljárásokat azonosít. A Microsoft fenyegetési csapatai és biztonsági csapatai által létrehozott adatokat használja, a partnerek által biztosított intelligenciával kiegészítve.

Ha integrálja a Defender for Endpoint Security Center, az alábbi további képességeket élvezheti:

- **Automatikus onboarding**. Security Center automatikusan engedélyezi a Microsoft Defender végpontérzékelőjét az összes windowsos kiszolgálón, amelyet a Security Center.

- **Egyablakos ablak.** A Security Center konzol megjeleníti a Microsoft Defender végpontriasztásokat. A további vizsgálathoz használja a Microsoft Defendert a végpont saját portáloldalához, ahol további információkat, például a riasztási folyamat fáját és az incidens grafikonját láthatja. A gép részletes idővonalát is láthatja, amely egy legfeljebb hat hónapos előzményidőszak minden viselkedését megjeleníti.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender a végpont saját Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender a végpont bérlői helyéhez

Amikor a Azure Security Center a kiszolgálók figyelése érdekében, a rendszer automatikusan létrehoz egy Microsoft Defender végponthoz bérlőt. A Defender által a végponthoz gyűjtött adatok a bérlő földrajzi helyén vannak tárolva az üzembehelyezés során azonosítottak szerint. Az ügyféladatok – álnevesített formában – a központi tároló- és feldolgozórendszerekben is tárolhatók a Egyesült Államok. 

A hely konfigurálása után nem módosíthatja. Ha saját Microsoft Defender-licenccel rendelkezik a végponthoz, és az adatokat egy másik helyre kell áthelyezni, lépjen kapcsolatba a Microsoft ügyfélszolgálata a bérlő alaphelyzetbe állításhoz.


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>A Microsoft Defender engedélyezése végpontintegrációhoz

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gép megfelel a Defender végponthoz szükséges követelményeinek:

1. Az eszközproxy és az internetkapcsolat [beállításainak konfigurálásacímen](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet) ismertetett hálózati beállítások konfigurálása
1. Ha a Defendert egy helyszíni gépen telepíti a végponton, csatlakoztassa a Azure Arc a hibrid gépek csatlakoztatása engedélyezett kiszolgálókkal Azure Arc [leírtak szerint](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
1. Csak Windows Server 2019 rendszerű gépek esetén ellenőrizze, hogy a gépeken érvényes ügynök fut-e, és hogy a MicrosoftMonitoringAgent bővítménye van-e
1. Engedélyezze **Azure Defender kiszolgálókra vonatkozó adatokat.** Lásd: [Rövid útmutató: A Azure Defender](enable-azure-defender.md)engedélyezése.
1. Ha már licencelte és telepítette a Microsoft Defender for Endpoints szolgáltatást a kiszolgálókon, távolítsa el a [Windows-kiszolgálók ki- és](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)kikapcsolása eljárás segítségével.
1. Ha áthelyezte az előfizetését az Azure-bérlők között, néhány manuális előkészítő lépésre is szükség van. További részletekért forduljon [a Microsoft támogatási szolgálatához.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


### <a name="enable-the-integration"></a>Az integráció engedélyezése
1. A Security Center válassza a Díjszabási beállítások **&,** majd válassza ki a módosítani kívánt előfizetést.
1. Válassza **a Fenyegetésészlelés lehetőséget.**
1. Jelölje be az Allow Microsoft Defender for Endpoint to Access my data (Microsoft **Defender engedélyezése a végpont számára az adataim eléréséhez)** lehetőséget, majd kattintson a Save **(Mentés) gombra.**

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Az integráció engedélyezése Azure Security Center Microsoft végponthoz elérhető EDR-megoldása, a Microsoft Defender között":::

    Azure Security Center a kiszolgálókat automatikusan a Microsoft Defender for Endpoint szolgáltatásba fogja bevetni. A be- és berakás akár 24 órát is igénybe vehet.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>A Microsoft Defender for Endpoint portál elérése

1. Győződjön meg arról, hogy a felhasználói fiók rendelkezik a szükséges engedélyekkel. További információ: [Felhasználói hozzáférés hozzárendelése Microsoft Defender biztonsági központ.](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)

1. Ellenőrizze, hogy rendelkezik-e olyan proxyval vagy tűzfallal, amely blokkolja a névtelen forgalmat. A Defender for Endpoint Sensor a rendszerkörnyezetből csatlakozik, ezért engedélyezni kell a névtelen forgalmat. A Defender for Endpoint portál akadálytalan hozzáférésének biztosításához kövesse a következő útmutatót: Enable access to service URLs in the proxy server (A szolgáltatás URL-címéhez való hozzáférés engedélyezése a [proxykiszolgálón).](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)

1. Nyissa meg [a Microsoft Defender biztonsági központ portált.](https://securitycenter.windows.com/) További információ a portál funkcióiról és ikonjairól: Microsoft Defender biztonsági központ [portál áttekintése.](/windows/security/threat-protection/microsoft-defender-atp/portal-overview) 

## <a name="send-a-test-alert"></a>Tesztriasztás küldése

Egy jóindulatú Microsoft Defender létrehozása végponti tesztriasztáshoz:

1. Hozzon létre egy "C:\test-MDATP-test" mappát.
1. A Távoli asztal géphez való hozzáféréshez használja a Távoli asztal.
1. Nyisson meg egy parancssori ablakot.
1. A parancssorban másolja ki és futtassa a következő parancsot. A parancssor ablaka automatikusan bezárul.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Egy parancssori ablak, amely a tesztriasztás generáló parancsát is be van keretezve.":::

1. Ha a parancs sikeres, egy új riasztás jelenik meg a Azure Security Center irányítópultján és a Microsoft Defender végponti portálon. A riasztás megjelenése eltarthat néhány percig.
1. A riasztás áttekintését a Security Center a **Biztonsági riasztások** gyanús  >  **PowerShell-parancssorban.**
1. A vizsgálat ablakában kattintson a hivatkozásra a Microsoft Defender végponti portálra való ugráshoz.

    > [!TIP]
    > A riasztás információs **súlyosságúként aktiválódik.**

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Gyakori kérdések Security Center integrált Microsoft Defender végponthoz való használatával kapcsolatban

- [Mik a Microsoft Defender végponthoz való licencelési követelményei?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Ha már rendelkezik Microsoft Defender-licenccel a végponthoz, kaphatok kedvezményt a Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Hogyan külső EDR-eszközről?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Mik a Microsoft Defender végponthoz való licencelési követelményei?
A Defender for Endpoint használata további költség nélkül jár a **kiszolgálói Azure Defender használata esetén.** 50 vagy több géphez külön is megvásárolható.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Ha már van Microsoft Defender-licencem a végponthoz, kaphatok kedvezményt a Azure Defender?
Ha már rendelkezik Microsoft Defender-licenccel a végponthoz, akkor nem kell fizetnie a microsoftos licenc ezen Azure Defender ért.

A kedvezmény megerősítéséhez lépjen kapcsolatba Security Center ügyfélszolgálatával, és adja meg a megfelelő munkaterület-azonosítót, régiót és licencinformációt az egyes kapcsolódó licencekhez.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Hogyan egy külső EDR-eszközről?
A nem a Microsofttól származó végpontmegoldásról való váltásra vonatkozó teljes útmutatás a Microsoft Defender for Endpoint dokumentációjában érhető el: [Migrálás áttekintése.](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)
  


## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Center által támogatott platformok és szolgáltatások](security-center-os-coverage.md)
- [Biztonsági javaslatok kezelése a Azure Security Center:](security-center-recommendations.md)Megtudhatja, hogyan segítik a javaslatok az Azure-erőforrások védelmét.