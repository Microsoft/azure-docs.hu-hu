---
title: A Microsoft Defender for Endpoint licenc használata a Azure Security Center
description: Ismerje meg a Végponthoz való Microsoft Defendert, és hogyan helyezheti üzembe Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: a9997fac66dd49af04f4ed78737118d605e27072
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829888"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>A végpontok védelme a Security Center integrált EDR-megoldásával: Microsoft Defender végponthoz

A Microsoft Defender for Endpoint egy holisztikus, felhőalapú végpontbiztonsági megoldás. Fő funkciói a következőek:

- Kockázatalapú biztonsági rések kezelése és felmérése 
- Támadási felület csökkentése
- Viselkedésalapú és felhőalapú védelem
- Végpontészlelés és -válasz (EDR)
- Automatikus vizsgálat és szervizelés
- Felügyeltkeresési szolgáltatások

> [!TIP]
> Az eredetileg **ATP Windows Defender ként** indított végpontészlelési és -választermék 2019-ben Microsoft Defender ATP-ként lett **átnevezve.**
>
> Az Ignite 2020-ban elindítottuk a [Microsoft Defender XDR](https://www.microsoft.com/security/business/threat-protection) csomagot, és ezt az EDR-összetevőt Microsoft Defender névre átnevezték **végpontként.**


## <a name="availability"></a>Rendelkezésre állás

| Szempont                          | Részletek                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kiadási állapot:                  | Általánosan elérhető (GA)                                                                                                                                                                                                                                                                                      |
| Árképzés:                        | Kiszolgálókhoz [Azure Defender szükséges](defender-for-servers-introduction.md)                                                                                                                                                                                                                                             |
| Támogatott platformok:            |  • Windowst futtató Azure-gépek<br> • Azure Arc Windows rendszerű gépek|
| A Windows támogatott verziói az észleléshez:  |  • Windows Server 2019, 2016, 2012 R2 és 2008 R2 SP1<br> • [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)<br> • [Windows 10 Enterprise (korábban](../virtual-desktop/windows-10-multisession-faq.yml) Enterprise for Virtual Desktops (EVD)|
| Nem támogatott operációs rendszerek:  |  • Windows 10 (nem EVD vagy WVD)<br> • Linux|
| Szükséges szerepkörök és engedélyek: | Az integráció engedélyezése/letiltása: **Biztonsági rendszergazda vagy** **tulajdonos**<br>Az MDATP-riasztások megtekintéséhez a **Security Center: biztonsági olvasó,** **Olvasó,** **Erőforráscsoport-közreműködő, Erőforráscsoport-tulajdonos,** **Biztonsági rendszergazda,** **Előfizetés** tulajdonosa vagy **Előfizetés-közreműködő** |
| Felhők:                         | ![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov, Other Gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Microsoft Defender a végponti szolgáltatásokhoz a Security Center

A Microsoft Defender végponthoz a következő eket biztosítja:

- **Speciális behatolásészlelési érzékelők.** A Windows rendszerű gépek végpontérzékelőihez való Defender a viselkedési jelek hatalmas tömbjét gyűjti össze.

- **Elemzésalapú, felhőalapú, biztonsági incidens utáni észlelés.** A Defender for Endpoint gyorsan alkalmazkodik a változó fenyegetésekhez. Fejlett elemzéseket és big data. Ezt az ismeretlen fenyegetések észlelésére a Intelligens biztonsági gráf, azure-beli és office-beli jelek jelzik. Beavatkozást figyelő riasztásokat biztosít, és lehetővé teszi a gyors válaszadást.

- **Fenyegetések felderítése**. A Végponthoz való Defender riasztásokat hoz létre, amikor azonosítja a támadók eszközeit, módszereit és eljárásait. A Microsoft fenyegetési és biztonsági csapatai által létrehozott, partnerek által biztosított intelligenciával kiegészített adatokat használ.

Ha integrálja a Defender for Endpoint Security Center, az alábbi további képességeket is élvezheti:

- **Automatikus onboarding**. Security Center automatikusan engedélyezi a Microsoft Defender végpontérzékelőjét a által figyelt összes Windows-Security Center.

- **Egyablakos ablaktábla.** A Security Center konzol megjeleníti a Microsoft Defender végpontriasztásokat. A további vizsgálathoz használja a Microsoft Defendert a végpont saját portáloldalához, ahol további információkat, például a riasztási folyamat fáját és az incidens grafikonját láthatja. A gép részletes idővonalát is láthatja, amely egy legfeljebb hat hónapos előzményidőszak minden viselkedését megjeleníti.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender az Endpoint saját Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>Mik a Microsoft Defender for Endpoint bérlő követelményei?

Ha a kiszolgálók Azure Security Center használja, a rendszer automatikusan létrehoz egy Microsoft Defender végponthoz bérlőt. 

- **Hely:** A Defender által végpontként gyűjtött adatok a bérlő földrajzi helyén vannak tárolva az üzembehelyezés során azonosítottak szerint. Az ügyféladatok – álnevesített formában – a központi tároló- és feldolgozórendszerekben is tárolhatók a Egyesült Államok. Miután konfigurálta a helyet, azt nem módosíthatja. Ha saját Microsoft Defender-licenccel rendelkezik a végponthoz, és az adatokat egy másik helyre kell áthelyezni, lépjen kapcsolatba a Microsoft ügyfélszolgálata a bérlő alaphelyzetbe állításhoz.
- **Előfizetések áthelyezése:** Ha áthelyezte Azure-előfizetését az Azure-bérlők között, néhány manuális előkészítő lépésre van szükség, mielőtt Security Center a Defendert a végponthoz. További részletekért forduljon [a Microsoft támogatási szolgálatához.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>A Microsoft Defender engedélyezése végpontintegrációhoz

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gép megfelel a Defender végponthoz szükséges követelményeinek:

1. Győződjön meg arról, hogy a gép szükség szerint csatlakozik az Azure-hoz:

    - **Windows-kiszolgálók** esetén konfigurálja az Eszközproxy és az internetkapcsolat beállításainak [konfigurálásacímű alatt leírt hálózati beállításokat.](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
    - Helyszíni **gépek esetén csatlakoztassa** a virtuális géphez Azure Arc a Hibrid gépek csatlakoztatása Azure Arc engedélyezett [kiszolgálókkal](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
    - **A Windows Server 2019** és Windows Virtual Desktop [(WVD)](../virtual-desktop/overview.md) gépeken ellenőrizze, hogy a gépeken fut-e a Log Analytics-ügynök, és hogy a MicrosoftMonitoringAgent bővítményt használja-e.
    
1. Engedélyezze **a Azure Defender kiszolgálókhoz.** Lásd: [Rövid útmutató: Azure Defender.](enable-azure-defender.md)
1. Ha már licencelte és telepítette a Microsoft Defender for Endpoints szolgáltatást a kiszolgálókon, távolítsa el a [Windows-kiszolgálók ki-](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)és kikapcsolása eljárás használatával.
1. Ha azure-bérlők között áthelyezte az előfizetését, néhány manuális előkészítő lépésre is szükség van. További részletekért forduljon [a Microsoft ügyfélszolgálatához.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


### <a name="enable-the-integration"></a>Az integráció engedélyezése
1. A Security Center menüjében válassza a Díjszabási beállítások **&,** majd válassza ki a módosítani kívánt előfizetést.
1. Válassza **a Fenyegetésészlelés lehetőséget.**
1. Jelölje **be Az adatok elérésének engedélyezése a Végpont** számára a Microsoft Defender számára lehetőséget, majd válassza a Mentés **lehetőséget.**

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="A végponthoz Azure Security Center Microsoft EDR-megoldása, a Microsoft Defender közötti integráció engedélyezése":::

    Azure Security Center a rendszer automatikusan beveszi a kiszolgálókat a Microsoft Defender számára a végponthoz. A be- és berakás akár 24 órát is igénybe vehet.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>A Microsoft Defender for Endpoint portál elérése

1. Győződjön meg arról, hogy a felhasználói fiók rendelkezik a szükséges engedélyekkel. További információ: [Felhasználói hozzáférés hozzárendelése Microsoft Defender biztonsági központ.](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)

1. Ellenőrizze, hogy rendelkezik-e olyan proxyval vagy tűzfallal, amely blokkolja a névtelen forgalmat. A Defender for Endpoint Sensor a rendszerkörnyezetből csatlakozik, ezért engedélyezni kell a névtelen forgalmat. A Defender for Endpoint portál akadálytalan hozzáférésének biztosításához kövesse a következő útmutatót: Enable access to service URLs in the proxy server (A szolgáltatás URL-címéhez való hozzáférés engedélyezése a [proxykiszolgálón).](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)

1. Nyissa meg [a Microsoft Defender biztonsági központ portált.](https://securitycenter.windows.com/) További információ a portál funkcióiról és ikonjairól: áttekintés [Microsoft Defender biztonsági központ portálról.](/windows/security/threat-protection/microsoft-defender-atp/portal-overview) 

## <a name="send-a-test-alert"></a>Tesztriasztás küldése

Jóindulatú Microsoft Defender létrehozása végponti tesztriasztáshoz:

1. Hozzon létre egy "C:\test-MDATP-test" mappát.
1. A Távoli asztal géphez való hozzáféréshez használja a Távoli asztal.
1. Nyisson meg egy parancssori ablakot.
1. Amikor a rendszer kéri, másolja ki és futtassa a következő parancsot. A parancssor ablaka automatikusan bezárul.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Egy parancssori ablak a paranccsal egy tesztriasztás létrehozásához.":::

1. Ha a parancs sikeres, egy új riasztás jelenik meg a Azure Security Center irányítópultján és a Microsoft Defender végponthoz portálon. A riasztás megjelenése eltarthat néhány percig.
1. A riasztás áttekintését a Security Center a **Biztonsági riasztások**  >  **gyanús PowerShell-parancssorban.**
1. A vizsgálat ablakában válassza ki a Microsoft Defender végponthoz portálra mutató hivatkozást.

    > [!TIP]
    > A riasztás információs **súlyosságúként** aktiválódik.

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Az Security Center Microsoft Defender for Endpoint integrált verziójával kapcsolatos gyakori kérdések

- [Mik a Microsoft Defender for Endpoint licencelési követelményei?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Ha már van Microsoft Defender-licencem a végponthoz, kaphatok kedvezményt a Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Hogyan külső EDR-eszközről?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Mik a Microsoft Defender végponthoz való licencelési követelményei?
A Defender for Endpoint használata további költség nélkül jár a **kiszolgálói Azure Defender használata esetén.** 50 vagy több géphez külön is megvásárolható.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Ha már rendelkezik Microsoft Defender-licenccel a végponthoz, kaphatok kedvezményt a Azure Defender?
Ha már rendelkezik Microsoft Defender-licenccel a végponthoz, nem kell fizetnie a licenc ezen Azure Defender ért.

A kedvezmény megerősítéséhez lépjen kapcsolatba Security Center ügyfélszolgálatával, és adja meg az egyes licencek megfelelő munkaterület-azonosítóját, régióját és licencinformációit.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Hogyan külső EDR-eszközről?
A nem a Microsofttól származó végpontmegoldásról való váltásra vonatkozó teljes útmutatót a Microsoft Defender végponti dokumentációjában, a migrálás [áttekintésében találhatja](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)meg.
  


## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Center által támogatott platformok és szolgáltatások](security-center-os-coverage.md)
- [Biztonsági javaslatok kezelése a Azure Security Center:](security-center-recommendations.md)Megtudhatja, hogyan segítik a javaslatok az Azure-erőforrások védelmét.