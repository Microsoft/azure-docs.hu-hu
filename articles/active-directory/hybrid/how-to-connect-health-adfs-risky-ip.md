---
title: Azure AD Connect Health AD FS kockázatos IP-jelentéssel | Microsoft Docs
description: Ismerteti a Azure AD Connect Health AD FS kockázatos IP-jelentést.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0b76d2f943f254eb06208e2c190bae4d4088030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746070"
---
# <a name="risky-ip-report-public-preview"></a>Kockázatos IP-jelentés (nyilvános előzetes verzió)
AD FS ügyfelek jelszavas hitelesítési végpontokat tehetnek elérhetővé az interneten, hogy hitelesítési szolgáltatásokat nyújtsanak a végfelhasználók számára az SaaS-alkalmazások, például a Microsoft 365 eléréséhez. Ez lehetőséget ad kártékony elemek számára, hogy megkíséreljenek bejelentkezni az AD FS rendszerbe, és találgatással kiderítsék a végfelhasználó jelszavát az alkalmazás-erőforrások elérése érdekében. A Windows Server 2012 R2-n futó AD FS-től kezdve elérhető zárolási funkció az extranet-fiókokhoz az ilyen típusú támadások elkerülése érdekében. Ha korábbi verziót használ, erősen ajánlott AD FS rendszerét Windows Server 2016-ra frissíteni. <br />

Ezenkívül előfordulhat, hogy egy IP-címről többször próbálnak bejelentkezni több felhasználó fiókjába. Ilyen esetekben előfordulhat, hogy az egyes felhasználókat érintő kísérletek száma az AD FS fiókzárolási funkcióját aktiváló küszöbérték alatt marad. Az Azure AD Connect Health mostantól elérhető „Kockázatos IP jelentése” észleli az ilyen eseményeket, és értesíti azokról a rendszergazdákat. A jelentés előnyei a következők: 
- Észleli az IP-címeket, amelyek túllépik a sikertelen jelszóalapú belépési kísérletek küszöbértékét
- Támogatja a helytelen jelszó megadása vagy az extranet zárolási állapot miatti sikertelen bejelentkezéseket
- E-mailben értesíti a rendszergazdákat, ha ilyen eseményre kerül sor (az e-mail-beállítások testre szabhatók)
- A küszöbérték-beállítások testreszabhatóak, és összehangolhatók a vállalat biztonsági szabályzatával
- A jelentések letölthetők offline elemzéshez, és automatikusan integrálhatók más rendszerekbe

> [!NOTE]
> A jelentés használatához győződjön meg róla, hogy az AD FS-naplózás engedélyezve van. További információkért tekintse meg az [AD FS-naplózás engedélyezését](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Az előzetes verzió eléréséhez globális rendszergazda vagy [biztonsági olvasó](../../role-based-access-control/built-in-roles.md#security-reader) szerepkörű felhasználói engedély szükséges.  
>

## <a name="what-is-in-the-report"></a>Mi a jelentés?
A sikertelen bejelentkezési tevékenység ügyfél IP-címei a webalkalmazás-proxy kiszolgálókon keresztül vannak összesítve. A Kockázatos IP jelentés minden eleme összesített adatokat tartalmaz a sikertelen AD FS-bejelentkezési kísérletekről, amelyek túllépték a kijelölt küszöbértéket. A következő információkat tartalmazza: ![ képernyőkép, amely egy kockázatos IP-jelentést jelenít meg az oszlopfejlécek kiemelésével.](./media/how-to-connect-health-adfs/report4a.png)

| Jelentéselem | Leírás |
| ------- | ----------- |
| Időbélyegző | Az észlelési időszak kezdetét jelző, az Azure Portal helyi ideje szerinti időbélyegző.<br /> Az összes napi eseményt UTC szerint éjfélkor állítja elő a rendszer. <br />Az óránkénti eseményeknél az időbélyeg az óra kezdetére van kerekítve. Az exportált fájlban a „firstAuditTimestamp” érték mutatja az első tevékenység kezdetét. |
| Eseményindító típusa | Az észlelési időszak típusa. Az összesítési eseményindítók óránkénti és a napi típusúak lehetnek. Ez hasznos lehet a nagy gyakoriságú találgatásos támadások észlelésére a lassú támadásokkal szemben, ahol a kísérletek eloszlanak egy adott napon belül. |
| IP-cím | Olyan kockázatos IP-cím, amelyről helytelen jelszót adtak meg, vagy amelyen extranet zárolást eredményező bejelentkezési tevékenység volt tapasztalható. Ez lehet IPv4 vagy IPv6-cím. |
| Helytelen jelszó típusú hibák száma | Az IP-címről előforduló helytelen jelszó típusú hibák száma az észlelési időszakban. A helytelen jelszó típusú hiba többször is előfordulhat az egyes felhasználók esetében. Fontos: a lejárt jelszó miatti sikertelen bejelentkezések nem tartoznak ide. |
| Extranet zárolási hibák száma | Az IP-címről előforduló extranet zárolási hibák száma az észlelési időszakban. Extranet zárolási hibák többször is előfordulhatnak az egyes felhasználók esetében. Ez csak akkor jelenik meg, ha az AD FS-ben konfigurálta az extranet zárolást (a 2012 R2-es és újabb verziókban). <b>Megjegyzés:</b> Ha a jelszóalapú extranet bejelentkezés engedélyezve van, erősen ajánlott bekapcsolni ezt a funkciót. |
| Megkísérelt egyedi felhasználók | Azon egyedi felhasználói fiókok száma, amelyekbe megkíséreltek bejelentkezni az IP-címről az észlelési időszakban. Ez segíthet megkülönböztetni az egy és a több felhasználó elleni támadásokat.  |

Például az alábbi jelentésben 2018. 02. 28-án 18:00 és 19:00 között a <i>104.2XX.2XX.9</i> IP-címről helytelen jelszó típusú hiba nem volt, azonban 284 extranet zárolási hiba fordult elő. 14 egyedi felhasználó volt érintett. A tevékenység meghaladta az óránkénti jelentési küszöbértéket. 

![Képernyőkép, amely egy kockázatos IP-jelentés bejegyzésére mutat példát.](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - A jelentés listájában kizárólag a küszöbértéket meghaladó tevékenységek jelennek meg. 
> - A jelentés legfeljebb az előző 30 napra vonatkozhat.
> - Ebben a figyelmeztető jelentésben nem jelennek meg az Exchange-IP-címek és a magánhálózati IP-címek, az exportálási listában azonban szerepelnek. 
>

![Képernyőkép, amely a kockázatos IP-jelentést a "Letöltés", az "értesítési beállítások" és a "küszöbérték-beállítások" kiemelésével jeleníti meg.](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>Terheléselosztó IP-címei a listában
A terheléselosztó összesítési bejelentkezési tevékenységei meghiúsultak, és elérte a riasztási küszöbértéket. Ha terheléselosztói IP-címek jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának. Konfigurálja megfelelően a terheléselosztót a továbbított ügyfél IP-címének továbbítására. 

## <a name="download-risky-ip-report"></a>Kockázatos IP-jelentés letöltése 
A **letöltési** funkcióval az összes kockázatos IP-cím listája exportálható a Connect Health portálról az elmúlt 30 napra vonatkozóan. Az exportált listában szerepel az egyes észlelési időszakokban előforduló összes sikertelen AD FS-bejelentkezési tevékenység, így az exportálás után testreszabhatja a szűrési feltételeket. A portálon kiemelt összesítések mellett az exportált lista IP-címekre lebontva további részleteket is tartalmaz a sikertelen bejelentkezési tevékenységekről:

|  Jelentéselem  |  Leírás  | 
| ------- | ----------- | 
| firstAuditTimestamp | A sikertelen tevékenységek kezdetének első időbélyegzője az észlelési időszakon belül.  | 
| lastAuditTimestamp | A sikertelen tevékenységek végének utolsó időbélyegzője az észlelési időszakon belül.  | 
| attemptCountThresholdIsExceeded | Annak a jelzője, ha az aktuális tevékenységek túllépték a figyelmeztetési küszöbértéket.  | 
| isWhitelistedIpAddress | Annak a jelzője, ha az IP-cím ki lett szűrve a figyelmeztetésekből és a jelentésekből. A magánhálózati IP-címek (<i>10.x.x.x, 172.x.x.x és 192.168.x.x</i>) és az Exchange-IP-címek szűrve vannak, és True értékűként vannak megjelölve. Ha magánhálózati IP-tartományok jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának.  | 

## <a name="configure-notification-settings"></a>Értesítési beállítások konfigurálása
A jelentés rendszergazdai elérhetőségi adatait az **értesítési beállításokban** lehet frissíteni. Alapértelmezés szerint a kockázatos IP-címekkel kapcsolatos e-mail-értesítés küldése ki van kapcsolva. Az értesítés bekapcsolásához át kell állítani az „E-mail-értesítés küldése, ha egy IP-cím túllépi a sikertelen tevékenységek figyelmeztetési küszöbértékét” beállítás kapcsológombját. A Connect Health általános értesítéseihez hasonlóan itt állíthatja be, hogy kik kapják meg az értesítési jelentést a kockázatos IP-címekről. A módosításról értesíthet minden globális rendszergazdát. 

## <a name="configure-threshold-settings"></a>Küszöbérték-beállítások konfigurálása
A figyelmeztetési küszöbérték küszöbérték-beállításokban módosítható. A rendszerben be van állítva egy alapértelmezett küszöbérték. Az alapértelmezett értékek az alábbiakban láthatók. A kockázatos IP-címek esetében négy küszöbérték-beállítási kategória közül választhat:

![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/report4d.png)

| Küszöbértékelem | Leírás |
| --- | --- |
| (Helytelen felhasználónév/jelszó + extranet zárolás) naponta  | A küszöbérték-beállítás megadásával jelentés készül a tevékenységről, és figyelmeztető értesítés jön létre, ha a helytelen jelszavak és az extranet zárolások **napi** száma meghaladja az értéket. Az alapértelmezett érték a 100.|
| (Helytelen felhasználónév/jelszó + extranet zárolás) óránként | A küszöbérték-beállítás megadásával jelentés készül a tevékenységről, és figyelmeztető értesítés jön létre, ha a helytelen jelszavak és az extranet zárolások **óránkénti** száma meghaladja az értéket. Az alapértelmezett érték a 50.|
| Extranet zárolások naponta | A küszöbérték-beállítás megadásával jelentés készül a tevékenységről, és figyelmeztető értesítés jön létre, ha az extranet zárolások **napi** száma meghaladja az értéket. Az alapértelmezett érték 50.|
| Extranet zárolások óránként| A küszöbérték-beállítás megadásával jelentés készül a tevékenységről, és figyelmeztető értesítés jön létre, ha az extranet zárolások **óránkénti** száma meghaladja az értéket. Az alapértelmezett érték 25|

> [!NOTE]
> - A jelentési küszöbérték változása egy órával a beállítás módosítása után lép érvénybe. 
> - A korábban jelentett elemeket nem érinti a küszöbérték változása. 
> - Azt javasoljuk, hogy a környezetben előforduló események számának megfelelően módosítsa a küszöbértéket. 
>
>

## <a name="faq"></a>GYIK
**Miért jelennek meg magánhálózati IP-címtartományok a jelentésben?**  <br />
A magánhálózati IP-címek (<i>10. x. x. x, 172. x. x. x & 192.168. x. x</i>) és az Exchange IP-címek szűrve vannak, és True (igaz) jelöléssel vannak megjelölve az IP által jóváhagyott listában. Ha magánhálózati IP-tartományok jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának.

**Miért jelennek meg terheléselosztói IP-címek a jelentésben?**  <br />
Ha terheléselosztói IP-címek jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának. Konfigurálja megfelelően a terheléselosztót a továbbított ügyfél IP-címének továbbítására. 

**Hogyan blokkolhatok IP-címeket?**  <br />
Az azonosított kártevő IP-címeket érdemes hozzáadni a tűzfalhoz vagy blokkolni az Exchange-ben.   <br />

**Miért nem jelenik meg egyetlen elem sem a jelentésben?** <br />
- A sikertelen bejelentkezési tevékenységek nem érték el a beállított küszöbértéket.
- Ellenőrizze, hogy nincsen-e aktív „Az állapotszolgáltatás nem naprakész” figyelmeztetés az AD FS-kiszolgálók listáján.  Itt további információkat olvashat [a figyelmeztetés hibaelhárításáról](how-to-connect-health-data-freshness.md).
- Az AD FS-farmokon nincs engedélyezve a naplózás.

**Miért nem látható hozzáférés a jelentéshez?**  <br />
Globális rendszergazda vagy [biztonsági olvasó](../../role-based-access-control/built-in-roles.md#security-reader) szerepkörű felhasználói engedély szükséges. Hozzáférésért forduljon a globális rendszergazdához.


## <a name="next-steps"></a>Következő lépések
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Az Azure AD Connect Health-ügynök telepítése](how-to-connect-health-agent-install.md)
