---
title: A Azure Active Directory Domain Services állapotának ellenõrzése | Microsoft Docs
description: Megtudhatja, hogyan ellenőrizhető egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartomány állapota, és hogyan értelmezhető az állapotüzenetek a Azure Portal használatával.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 92cc6157c590d3e05b4002e0f071c08e4ec23a16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620324"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services felügyelt tartomány állapotának ellenőrzését

Azure Active Directory Domain Services (Azure AD DS) néhány háttérben futó feladatot futtat a felügyelt tartomány kifogástalan állapotának és naprakész állapotának megőrzése érdekében. Ezek a feladatok magukban foglalják a biztonsági másolatok készítését, a biztonsági frissítések alkalmazását és az adatok szinkronizálását az Azure AD-ból. Ha problémák merülnek fel az Azure AD DS felügyelt tartományával kapcsolatban, előfordulhat, hogy ezek a feladatok nem hajthatók végre sikeresen. A problémák áttekintéséhez és megoldásához a Azure Portal használatával ellenőrizheti a felügyelt tartományok állapotát.

Ez a cikk bemutatja, hogyan tekintheti meg az Azure AD DS állapotát, és megismerheti a megjelenített információkat vagy riasztásokat.

## <a name="view-the-health-status"></a>Állapot megtekintése

A felügyelt tartomány állapotának megtekintése a Azure Portal használatával történik. A legutóbbi biztonsági mentési időpontra és az Azure AD-vel való szinkronizálásra vonatkozó információk láthatók a felügyelt tartomány állapotával kapcsolatos problémákat jelző riasztásokkal együtt. Egy felügyelt tartomány állapotának megtekintéséhez hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services** lehetőséget.
1. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. Az Azure AD DS erőforrás ablak bal oldalán válassza az **állapot** lehetőséget. Az alábbi képernyőképen egy kifogástalanul felügyelt tartomány látható, valamint az utolsó biztonsági mentés és az Azure AD szinkronizálás állapota:

    ![A Azure Active Directory Domain Services állapotot megjelenítő Azure Portal állapot oldalának áttekintése](./media/check-health/health-page.png)

Az állapot lap *utolsó kiértékelt* időbélyege a felügyelt tartomány utolsó ellenőrzése után jelenik meg. A felügyelt tartomány állapota óránként lesz kiértékelve. Ha módosításokat végez egy felügyelt tartományon, várjon, amíg a következő kiértékelési ciklus megtekinti a frissített állapotot.

A jobb felső sarokban a felügyelt tartomány általános állapota látható. Az állapot a tartomány összes meglévő riasztását befolyásolja. Az alábbi táblázat az elérhető állapotjelzőket ismerteti:

| Állapot | Ikon | Magyarázat |
| --- | :----: | --- |
| Futó | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | A felügyelt tartomány megfelelően fut, és nem rendelkezik kritikus vagy figyelmeztető riasztásokkal. Előfordulhat, hogy a tartomány tájékoztató riasztásokkal rendelkezik. |
| Figyelmet igényel (figyelmeztetés) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Nincsenek kritikus riasztások a felügyelt tartományon, de egy vagy több figyelmeztető riasztást kell kezelni. |
| Figyelmet igényel (kritikus) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | A felügyelt tartományon legalább egy kritikus riasztást kell kezelni. Figyelmeztetési és/vagy tájékoztató riasztásokat is tartalmazhat. |
| Telepítése | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | A felügyelt tartomány üzembe helyezése folyamatban van. |

## <a name="understand-monitors-and-alerts"></a>A figyelők és a riasztások ismertetése

A felügyelt tartomány állapota két típusú információt jelenít meg – *figyelők* és *riasztások*. A figyelők megjelenítik az alapvető háttér-feladatok befejezésének időpontját. A riasztások a felügyelt tartomány stabilitásának javításához nyújtanak információt vagy javaslatokat.

### <a name="monitors"></a>Monitorozások

A figyelők egy felügyelt tartomány olyan területei, amelyeknek ellenőrzése rendszeresen megtörténik. Ha vannak aktív riasztások a felügyelt tartományhoz, akkor előfordulhat, hogy az egyik figyelő egy problémát jelent. Az Azure AD DS jelenleg a következő területekre vonatkozó figyelőket tartalmaz:

* Backup
* Szinkronizálás az Azure AD-vel

#### <a name="backup-monitor"></a>Biztonsági mentési figyelő

A biztonsági mentési figyelő ellenőrzi, hogy sikeresen futtatta-e a felügyelt tartomány automatizált rendszeres biztonsági mentését. A következő táblázat az elérhető biztonsági mentési figyelő állapotát részletezi:

| Részletességi érték | Magyarázat |
| --- | --- |
| Soha nem készített biztonsági mentést | Ez az állapot az új felügyelt tartományok esetében normális. Az első biztonsági mentést a felügyelt tartomány üzembe helyezése után 24 órával kell létrehozni. Ha ez az állapot tartósan fennáll, [Nyisson meg egy Azure-támogatási kérelmet][azure-support]. |
| A legutóbbi biztonsági mentés 1 – 14 nappal ezelőtt történt | Ez az időtartomány a biztonsági mentési figyelő várt állapota. Ebben az időszakban automatikusan rendszeres biztonsági mentést kell készíteni. |
| A legutóbbi biztonsági mentés több mint 14 napja is megtörtént. | A két hétnél hosszabb TimeSpan azt jelzi, hogy probléma van az automatizált rendszeres biztonsági mentésekkel. Az aktív kritikus riasztások akadályozhatják a felügyelt tartomány biztonsági mentését. Oldja meg a felügyelt tartományhoz tartozó aktív riasztásokat. Ha a biztonsági mentési figyelő nem frissíti az állapotot a legutóbbi biztonsági másolat jelentésére, [Nyisson meg egy Azure-támogatási kérelmet][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Szinkronizálás az Azure AD-figyelővel

A felügyelt tartományok rendszeresen szinkronizálva vannak Azure Active Directoryokkal. A felhasználók és a csoport objektumainak száma, valamint az Azure AD-címtárban az utolsó szinkronizálás óta végrehajtott módosítások száma befolyásolja, hogy mennyi ideig tart a szinkronizálás. Ha a felügyelt tartomány utolsó szinkronizálása három nappal ezelőtt történt, keresse meg és oldja fel az aktív riasztásokat. Ha a szinkronizációs figyelő nem frissíti az állapotot, hogy az aktív riasztások kezelése után egy legutóbbi szinkronizálás jelenjen [meg, nyisson meg egy Azure-támogatási kérelmet][azure-support].

### <a name="alerts"></a>Riasztások

Riasztások jönnek létre olyan felügyelt tartománybeli problémák esetén, amelyeket a szolgáltatás megfelelő futtatásához kell kezelnie. Minden riasztás ismerteti a problémát, és olyan URL-címet ad meg, amely a probléma megoldásának konkrét lépéseit ismerteti. A lehetséges riasztásokról és azok megoldásáról további információt a [riasztások hibaelhárítása](troubleshoot-alerts.md)című témakörben talál.

Az állapot riasztásai a következő súlyossági szintekre vannak kategorizálva:

 * A **kritikus riasztások** olyan problémák, amelyek súlyosan érintik a felügyelt tartományt. Ezeket a riasztásokat azonnal meg kell oldani. Az Azure platform a problémák megoldása előtt nem tudja figyelni, kezelni, javítani és szinkronizálni a felügyelt tartományt.
 * A **figyelmeztető riasztások** értesítik a felügyelt tartományi műveletekkel kapcsolatos problémákról, ha a probléma továbbra is fennáll. Ezek a riasztások a felügyelt tartomány biztonságossá tételére vonatkozó javaslatokat is kínálnak.
 * Az **tájékoztató riasztások** olyan értesítések, amelyek negatív hatással vannak a felügyelt tartományra. Az tájékoztató riasztások betekintést nyújtanak a felügyelt tartományba tartozó adatokra.

## <a name="next-steps"></a>Következő lépések

Az állapot lapon megjelenő riasztásokkal kapcsolatos további információkért lásd: [riasztások feloldása a felügyelt tartományon][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
