---
title: Azure AD Connect Health és felhasználói adatvédelem | Microsoft Docs
description: Ez a dokumentum ismerteti a felhasználók adatvédelmét a Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ecdfefa79716bcc0a69063293d5a1067693a78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89182360"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Felhasználói adatvédelem és Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ez a cikk a Azure AD Connect Health és a felhasználói adatvédelmet tárgyalja.  A Azure AD Connect és a felhasználói adatok védelméről a cikk [itt](reference-connect-user-privacy.md)található.

## <a name="user-privacy-classification"></a>Felhasználói adatvédelem besorolása
Azure AD Connect Health a GDPR-besorolás **adatfeldolgozó** kategóriájára esik. Adatfeldolgozó folyamatként a szolgáltatás adatfeldolgozási szolgáltatásokat biztosít a legfontosabb partnerek és a végfelhasználók számára. Azure AD Connect Health nem állít elő felhasználói adatokat, és nincs független vezérlése a személyes adatok gyűjtésének és felhasználásának módjától. A Azure AD Connect Health adatok beolvasása, összesítése, elemzése és jelentéskészítése a meglévő helyszíni adatokon alapul. 

## <a name="data-retention-policy"></a>Adatmegőrzési szabályzat
Azure AD Connect Health nem készít jelentéseket, elemzéseket hajt végre, vagy 30 napon belül betekintést nyújt. Ezért a Azure AD Connect Health nem tárol, dolgoz fel és nem tart fenn semmilyen, 30 napnál nem régebben tárolt adatát. Ez a kialakítás megfelel a GDPR-szabályozásoknak, a Microsoft adatvédelmi rendelkezéseinek és az Azure AD adatmegőrzési szabályzatának. 

Az aktív állapotfigyelő **szolgáltatás adataival** rendelkező kiszolgálók nem naprakészek a több mint 30 egymást követő napra **vonatkozó riasztások** esetén arra utalnak, hogy az adott időtartományban egyetlen adat sem érte el a kapcsolódási állapotot. Ezek a kiszolgálók le lesznek tiltva, és nem jelennek meg a kapcsolat állapota portálon. A kiszolgálók újbóli engedélyezéséhez el kell távolítania és [újra kell telepítenie az állapotfigyelő ügynököt](how-to-connect-health-agent-install.md). Vegye figyelembe, hogy ez nem vonatkozik a **figyelmeztetésekre** ugyanazzal a riasztási típussal. A figyelmeztetések azt jelzik, hogy a részleges adatok hiányoznak a riasztásban lévő kiszolgálóról. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Az adatgyűjtési és-figyelési Azure AD Connect Health letiltása
Azure AD Connect Health lehetővé teszi az adatgyűjtés leállítását minden egyes megfigyelt kiszolgáló vagy egy figyelt szolgáltatás egy példánya számára. Például leállíthatja az adatgyűjtést a Azure AD Connect Health használatával figyelt egyes ADFS-(Active Directory összevonási szolgáltatások (AD FS)-) kiszolgálókon. Az adatgyűjtés a Azure AD Connect Health használatával figyelt teljes ADFS-példányon is leállítható. Ha ezt választja, a rendszer törli a megfelelő kiszolgálókat a Azure AD Connect Health-portálról az adatgyűjtés leállítása után. 

>[!IMPORTANT]
> A figyelt kiszolgálók Azure AD Connect Healthról való törléséhez Azure AD globális rendszergazdai jogosultságokkal vagy az Azure RBAC közreműködői szerepkörrel kell rendelkeznie.
>
> Kiszolgáló vagy szolgáltatás példányának Azure AD Connect Healthból való eltávolítása nem visszafordítható művelet. 

### <a name="what-to-expect"></a>Mi várható?
Ha leállítja az adatgyűjtést és-figyelést egy adott figyelt kiszolgáló vagy egy figyelt szolgáltatás egy példánya esetében, vegye figyelembe a következőket:

- A figyelt szolgáltatás egy példányának törlésekor a rendszer eltávolítja a példányt a portálon a Azure AD Connect Health figyelési szolgáltatás listájáról. 
- Ha töröl egy figyelt kiszolgálót vagy egy figyelt szolgáltatás egy példányát, a rendszer nem távolítja el és nem távolítja el a kiszolgálóról a rendszerállapot-ügynököt. Az állapotfigyelő szolgáltatás úgy van konfigurálva, hogy ne küldjön adatküldést Azure AD Connect Healthba. Manuálisan kell eltávolítania az állapotfigyelő ügynököt a korábban figyelt kiszolgálókon.
- Ha a lépés elvégzése előtt nem távolította el az állapotfigyelő ügynököt, akkor az állapotfigyelő ügynökhöz kapcsolódó kiszolgáló (k) ben hibaüzenetek jelenhetnek meg.
- A figyelt szolgáltatás példányaihoz tartozó összes adatok törlődnek a Microsoft Azure adatmegőrzési házirend szerint.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Az adatgyűjtés és a figyelés letiltása a figyelt szolgáltatás egy példánya esetében
Tekintse [meg, hogyan távolíthat el egy Service-példányt a Azure ad Connect Healthból](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Az adatgyűjtés és a figyelés letiltása a figyelt kiszolgálókon
Lásd: [kiszolgáló eltávolítása Azure ad Connect Healthról](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Az adatgyűjtési és-figyelés letiltása a Azure AD Connect Health összes figyelt szolgáltatásához
A Azure AD Connect Health lehetőséget biztosít a bérlő **összes** regisztrált szolgáltatásának adatgyűjtésének leállítására is. Javasoljuk, hogy a művelet végrehajtása előtt körültekintően vegye figyelembe az összes globális rendszergazdát. A folyamat megkezdése után a csatlakoztatott állapotfigyelő szolgáltatás leállítja az összes szolgáltatás adatainak fogadását, feldolgozását és jelentését. A csatlakozási állapotfigyelő szolgáltatásban található meglévő adat 30 napnál nem hosszabb ideig tart.
Ha le szeretné állítani az adott kiszolgáló adatgyűjtését, kövesse az egyes kiszolgálók törlésének lépéseit. A bérlői Wise-adatgyűjtés leállításához kövesse az alábbi lépéseket az adatgyűjtés leállításához és a bérlő összes szolgáltatásának törléséhez.

1. Kattintson a fő panel konfiguráció területén található **általános beállítások** elemre. 
2. Kattintson az **adatgyűjtés leállítása** gombra a panel tetején. A bérlői konfigurációs beállítások egyéb beállításai a folyamat elindításakor le lesznek tiltva.  
 
   ![Adatgyűjtés leállítása](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Győződjön meg arról, hogy az adatgyűjtemények leállítása által érintett beépített szolgáltatások listája. 
4. Adja meg a bérlő pontos nevét, hogy engedélyezze a művelet **törlése** gombot
5. Kattintson a **delete (Törlés** ) elemre az összes szolgáltatás törlésének elindításához. A kapcsolat állapota leállítja a beérkező szolgáltatásokból érkező adatok fogadását, feldolgozását és jelentéskészítését. A teljes folyamat akár 24 órát is igénybe vehet. Figyelje meg, hogy ez a lépés nem vonható vissza. 
6. A folyamat befejezése után többé nem jelennek meg a kapcsolati állapotban regisztrált szolgáltatások. 

   ![Az adatgyűjtés leállítása után](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Az adatgyűjtés és a figyelés újbóli engedélyezése Azure AD Connect Health
A korábban törölt figyelt szolgáltatás Azure AD Connect Health figyelésének újbóli engedélyezéséhez el kell távolítania és [újra kell telepítenie az állapotfigyelő ügynököt](how-to-connect-health-agent-install.md) az összes kiszolgálón.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Az adatgyűjtés és a figyelés újbóli engedélyezése az összes figyelt szolgáltatáshoz

A bérlői Wise-adatgyűjtést Azure AD Connect Health lehet folytatni. Javasoljuk, hogy a művelet végrehajtása előtt körültekintően vegye figyelembe az összes globális rendszergazdát.

>[!IMPORTANT]
> A következő lépések 24 órás letiltási művelet után lesznek elérhetők.
> Az adatgyűjtés engedélyezése után a kapcsolat állapota című bemutatott betekintési és figyelési adatok nem jelenítik meg a korábban összegyűjtött örökölt adatokat. 

1. Kattintson a fő panel konfiguráció területén található **általános beállítások** elemre. 
2. Kattintson az **adatgyűjtés engedélyezése** gombra a panel tetején. 
 
   ![Az adatgyűjtés engedélyezése](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Adja meg a bérlő pontos nevét az **Engedélyezés** gomb aktiválásához.
4. Az **Engedélyezés** gombra kattintva engedélyezheti az adatgyűjtés elérését a kapcsolódási állapot szolgáltatásban. A változást hamarosan alkalmazza a rendszer. 
5. Kövesse a [telepítési folyamatot](how-to-connect-health-agent-install.md) , és telepítse újra az ügynököt a figyelni kívánt kiszolgálókon, és a szolgáltatások jelen lesznek a portálon.  


## <a name="next-steps"></a>Következő lépések
* [A Microsoft adatvédelmi szabályzatának áttekintése a megbízhatósági központban](https://www.microsoft.com/trustcenter)
* [Azure AD Connect és felhasználói adatvédelem](reference-connect-user-privacy.md)

