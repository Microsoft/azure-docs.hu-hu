---
title: Gyakori riasztások és megoldások a Azure AD Domain Servicesban | Microsoft Docs
description: Ismerje meg, Hogyan oldhatók fel a Azure Active Directory Domain Services állapotának részeként generált általános riasztások
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 4caf804a274956556d6e9ca396c8f08594b11a87
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092877"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Ismert problémák: gyakori riasztások és megoldások a Azure Active Directory Domain Services

Az alkalmazások identitásának és hitelesítésének központi részeként Azure Active Directory Domain Services (Azure AD DS) időnként problémák léptek fel. Ha problémákba ütközik, bizonyos gyakori riasztások és a hozzájuk kapcsolódó hibaelhárítási lépések segítséget nyújtanak az újbóli futtatásához. A további hibaelhárítási segítségért [egy Azure-támogatási kérelem is megnyitható][azure-support] .

Ez a cikk az Azure-AD DS gyakori értesítéseinek hibaelhárítási információit tartalmazza.

## <a name="aadds100-missing-directory"></a>AADDS100: hiányzó könyvtár

### <a name="alert-message"></a>Riasztási üzenet

*Lehet, hogy törölték a felügyelt tartományhoz tartozó Azure AD-címtárat. A felügyelt tartomány már nem támogatott konfigurációban van. A Microsoft nem tudja figyelni, kezelni, javítani és szinkronizálni a felügyelt tartományt.*

### <a name="resolution"></a>Feloldás

Ezt a hibát általában az okozza, ha egy Azure-előfizetést új Azure AD-címtárba helyez át, és az Azure AD DShoz társított régi Azure AD-címtár törlődik.

Ez a hiba nem állítható helyre. A riasztás feloldásához [törölje a meglévő felügyelt tartományt](delete-aadds.md) , majd hozza létre újra az új címtárban. Ha nem sikerül törölni a felügyelt tartományt, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C fut ebben a könyvtárban

### <a name="alert-message"></a>Riasztási üzenet

*Azure AD Domain Services nem engedélyezhető Azure AD B2C könyvtárban.*

### <a name="resolution"></a>Feloldás

Az Azure AD DS automatikusan szinkronizál egy Azure AD-címtárral. Ha az Azure AD-címtár B2C használatára van konfigurálva, az Azure AD DS nem telepíthető és nem szinkronizálható.

Az Azure AD DS használatához az alábbi lépéseket követve újra létre kell hoznia a felügyelt tartományt egy nem Azure AD B2C könyvtárban:

1. [Törölje a felügyelt tartományt](delete-aadds.md) a meglévő Azure ad-címtárból.
1. Hozzon létre egy új Azure AD-címtárat, amely nem Azure AD B2C könyvtár.
1. [Hozzon létre egy helyettesítő felügyelt tartományt](tutorial-create-instance.md).

A felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: a cím nyilvános IP-tartományban van

### <a name="alert-message"></a>Riasztási üzenet

*Annak a virtuális hálózatnak az IP-címtartomány, amelyben engedélyezte a Azure AD Domain Services nyilvános IP-tartományban van. A Azure AD Domain Servicest engedélyezni kell egy magánhálózati IP-címtartományt használó virtuális hálózaton. Ez a konfiguráció befolyásolja a Microsoft képességét a felügyelt tartomány figyelésére, kezelésére, javítására és szinkronizálására.*

### <a name="resolution"></a>Feloldás

Mielőtt elkezdené, győződjön meg róla, hogy érti a [magánhálózati IP v4-címeket](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

A virtuális hálózatokon belül a virtuális gépek az alhálózathoz konfigurált azonos IP-címtartomány alapján is elvégezhetik az Azure-erőforrásokra irányuló kéréseket. Ha egy alhálózat nyilvános IP-címtartományt konfigurál, előfordulhat, hogy a virtuális hálózaton belül átirányított kérések nem jutnak el a kívánt webes erőforrásokhoz. Ez a konfiguráció kiszámíthatatlan hibákat eredményezhet az Azure AD DS használatával.

> [!NOTE]
> Ha a virtuális hálózatban konfigurált IP-címtartomány tulajdonosa, akkor ez a riasztás figyelmen kívül hagyható. Azure AD Domain Services azonban nem tudja véglegesíteni az [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) -t ezzel a konfigurációval, mert kiszámíthatatlan hibákat eredményezhet.

A riasztás feloldásához törölje a meglévő felügyelt tartományt, majd hozza létre újra egy magánhálózati IP-címtartományt tartalmazó virtuális hálózaton. Ez a folyamat zavaró, mivel a felügyelt tartomány nem érhető el, és az Ön által létrehozott egyéni erőforrások, például a szervezeti egységek vagy a szolgáltatásfiókok elvesznek.

1. [Törölje a felügyelt tartományt](delete-aadds.md) a címtárból.
1. A virtuális hálózat IP-címtartomány frissítéséhez keresse meg és válassza ki a *virtuális hálózatot* a Azure Portalban. Válassza ki az Azure AD DS virtuális hálózatát, amely helytelenül rendelkezik nyilvános IP-címtartomány készlettel.
1. A **Beállítások** területen válassza a *címterület* lehetőséget.
1. Frissítse a címtartományt a meglévő címtartomány kiválasztásával és szerkesztésével, vagy további címtartomány hozzáadásával. Győződjön meg arról, hogy az új IP-címtartomány egy magánhálózati IP-tartományban van. Ha elkészült, **mentse** a módosításokat.
1. Válassza ki az **alhálózatok** elemet a bal oldali navigációs sávon.
1. Válassza ki a szerkeszteni kívánt alhálózatot, vagy hozzon létre egy további alhálózatot.
1. Frissítsen vagy határozzon meg egy magánhálózati IP-címtartományt, majd **mentse** a módosításokat.
1. [Hozzon létre egy helyettesítő felügyelt tartományt](tutorial-create-instance.md). Ügyeljen arra, hogy a frissített virtuális hálózati alhálózatot magánhálózati IP-címtartomány szerint válassza.

A felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: az Azure-előfizetés nem található

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartományhoz tartozó Azure-előfizetést törölték.  Azure AD Domain Services működésének folytatásához aktív előfizetésre van szükség.*

### <a name="resolution"></a>Feloldás

Az Azure AD DS aktív előfizetést igényel, és nem helyezhető át másik előfizetésbe. Ha a felügyelt tartományhoz társított Azure-előfizetés törölve lett, újra létre kell hoznia egy Azure-előfizetést és egy felügyelt tartományt.

1. [Hozzon létre egy Azure-előfizetést](../cost-management-billing/manage/create-subscription.md).
1. [Törölje a felügyelt tartományt](delete-aadds.md) a meglévő Azure ad-címtárból.
1. [Hozzon létre egy helyettesítő felügyelt tartományt](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: az Azure-előfizetés le van tiltva

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartományhoz társított Azure-előfizetés nem aktív.  Azure AD Domain Services működésének folytatásához aktív előfizetésre van szükség.*

### <a name="resolution"></a>Feloldás

Az Azure AD DS használatához aktív előfizetés szükséges. Ha az Azure-előfizetés, amelyhez a felügyelt tartomány társítva van, nem aktív, meg kell újítania az előfizetés újraaktiválásához.

1. [Újítsa meg az Azure-előfizetését](../cost-management-billing/manage/subscription-disabled.md).
2. Az Előfizetés megújítása után egy Azure AD DS-értesítés lehetővé teszi a felügyelt tartomány újbóli engedélyezését.

Ha a felügyelt tartomány ismét engedélyezve van, a felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: az előfizetés áthelyezett könyvtárai

### <a name="alert-message"></a>Riasztási üzenet

*Az Azure AD Domain Services által használt előfizetés át lett helyezve egy másik könyvtárba. Azure AD Domain Services a megfelelő működéséhez aktív előfizetéssel kell rendelkeznie ugyanabban a címtárban.*

### <a name="resolution"></a>Feloldás

Az Azure AD DS aktív előfizetést igényel, és nem helyezhető át másik előfizetésbe. Ha a felügyelt tartományhoz társított Azure-előfizetés át lett helyezve, helyezze vissza az előfizetést az előző könyvtárba, vagy [törölje a felügyelt tartományt](delete-aadds.md) a meglévő címtárból, és [hozzon létre egy helyettesítő felügyelt tartományt a kiválasztott előfizetésben](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: a felügyelt tartomány erőforrásai nem találhatók

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartományhoz használt erőforrás törölve lett. Ez az erőforrás szükséges ahhoz, hogy a Azure AD Domain Services megfelelően működjön.*

### <a name="resolution"></a>Feloldás

Az Azure AD DS a megfelelő működés érdekében további erőforrásokat hoz létre, például a nyilvános IP-címeket, a virtuális hálózati adaptereket és a Load balancert. Ha bármelyik erőforrás törölve lett, a felügyelt tartomány nem támogatott állapotban van, és megakadályozza a tartomány felügyeletét. További információ ezekről az erőforrásokról: az [Azure AD DS által használt hálózati erőforrások](network-considerations.md#network-resources-used-by-azure-ad-ds).

Ez a riasztás akkor jön létre, ha az egyik szükséges erőforrás törölve van. Ha az erőforrást kevesebb mint 4 órával törölték, akkor az Azure-platform automatikusan újra létrehozhatja a törölt erőforrást. Az alábbi lépések azt ismertetik, hogyan ellenőrizhető az erőforrás-törlés állapota és időbélyege:

1. A Azure Portal keresse meg és válassza ki a **tartományi szolgáltatások** elemet. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. A bal oldali navigációs sávon válassza az **állapot** lehetőséget.
1. Az állapot lapon válassza ki a riasztást a *AADDS109* azonosítóval.
1. A riasztás időbélyeggel rendelkezik, amikor először talál. Ha az időbélyeg kevesebb, mint 4 órával ezelőtt, az Azure-platform automatikusan újra létrehozhatja az erőforrást, és saját maga is megoldhatja a riasztást.

    Ha a riasztás 4 óránál régebbi, a felügyelt tartomány helyreállíthatatlan állapotban van. [Törölje a felügyelt tartományt](delete-aadds.md) , majd [hozzon létre egy helyettesítő felügyelt tartományt](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: a felügyelt tartományhoz társított alhálózat megtelt

### <a name="alert-message"></a>Riasztási üzenet

*A Azure AD Domain Services telepítéséhez kiválasztott alhálózat megtelt, és nem rendelkezik a létrehozandó további tartományvezérlőhöz szükséges hellyel.*

### <a name="resolution"></a>Feloldás

Az Azure AD DS virtuális hálózati alhálózatának elegendő IP-címmel kell rendelkeznie az automatikusan létrehozott erőforrásokhoz. Ez az IP-címtartomány magában foglalja a helyettesítő erőforrások létrehozásának szükségességét, ha van karbantartási esemény. Az elérhető IP-címek kialakulásának kockázatának csökkentése érdekében ne helyezzen üzembe további erőforrásokat, például saját virtuális gépeket a felügyelt tartományhoz tartozó virtuális hálózati alhálózatba.

Ez a hiba nem állítható helyre. A riasztás feloldásához [törölje a meglévő felügyelt tartományt](delete-aadds.md) , majd hozza létre újból. Ha nem sikerül törölni a felügyelt tartományt, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: az egyszerű szolgáltatás nem engedélyezett

### <a name="alert-message"></a>Riasztási üzenet

*Egy egyszerű szolgáltatásnév, amelyet Azure AD Domain Services a tartomány kiszolgálására használ, nincs jogosultsága az Azure-előfizetésben lévő erőforrások kezelésére. Az egyszerű szolgáltatásnak engedélyt kell nyernie a felügyelt tartomány kiszolgálásához.*

### <a name="resolution"></a>Feloldás

Néhány automatikusan létrehozott egyszerű szolgáltatás a felügyelt tartomány erőforrásainak kezelésére és létrehozására szolgál. Ha az egyik egyszerű szolgáltatásnév hozzáférési engedélyei módosulnak, a tartomány nem tudja megfelelően kezelni az erőforrásokat. A következő lépések bemutatják, hogyan értelmezheti és engedélyezheti a hozzáférési engedélyeket egy egyszerű szolgáltatásnév számára:

1. További információ [Az Azure szerepköralapú hozzáférés-vezérléséről és az alkalmazásokhoz való hozzáférés engedélyezéséről a Azure Portal](../role-based-access-control/role-assignments-portal.md).
2. Tekintse át az *ABBA844E-BC0E-44B0-947A-DC74E5D09022* azonosítóval rendelkező szolgáltatásnév hozzáférését, és adja meg a korábbi időpontban megtagadott hozzáférést.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: nincs elegendő IP-cím a felügyelt tartományban

### <a name="alert-message"></a>Riasztási üzenet

*Megállapítottuk, hogy a tartomány virtuális hálózatának alhálózata nem rendelkezhet elegendő IP-címmel. Azure AD Domain Services az alhálózaton belül legalább két elérhető IP-címet kell használnia, a alkalmazásban engedélyezve van. Javasoljuk, hogy az alhálózaton belül legalább 3-5 tartalék IP-címet kapjon. Ez akkor fordulhat elő, ha az alhálózaton belül más virtuális gépek is üzembe vannak helyezve, így a rendelkezésre álló IP-címek száma kiesik, vagy ha az alhálózat rendelkezésre álló IP-címeinek száma korlátozást tartalmaz.*

### <a name="resolution"></a>Feloldás

Az Azure AD DS virtuális hálózati alhálózatának elegendő IP-címet kell tartalmaznia az automatikusan létrehozott erőforrásokhoz. Ez az IP-címtartomány magában foglalja a helyettesítő erőforrások létrehozásának szükségességét, ha van karbantartási esemény. Az elérhető IP-címek kialakulásának kockázatának csökkentése érdekében ne helyezzen üzembe további erőforrásokat, például saját virtuális gépeket a felügyelt tartományhoz tartozó virtuális hálózati alhálózatba.

A riasztás feloldásához törölje a meglévő felügyelt tartományt, majd hozza létre újra egy olyan virtuális hálózaton, amely elég nagy méretű IP-címtartományt használ. Ez a folyamat zavaró, mivel a felügyelt tartomány nem érhető el, és az Ön által létrehozott egyéni erőforrások, például a szervezeti egységek vagy a szolgáltatásfiókok elvesznek.

1. [Törölje a felügyelt tartományt](delete-aadds.md) a címtárból.
1. A virtuális hálózat IP-címtartomány frissítéséhez keresse meg és válassza ki a *virtuális hálózatot* a Azure Portalban. Válassza ki azt a felügyelt tartományhoz tartozó virtuális hálózatot, amely a kis IP-címtartományt tartalmaz.
1. A **Beállítások** területen válassza a *címterület* lehetőséget.
1. Frissítse a címtartományt a meglévő címtartomány kiválasztásával és szerkesztésével, vagy további címtartomány hozzáadásával. Győződjön meg arról, hogy az új IP-címtartomány elég nagy a felügyelt tartomány alhálózatának tartományához. Ha elkészült, **mentse** a módosításokat.
1. Válassza ki az **alhálózatok** elemet a bal oldali navigációs sávon.
1. Válassza ki a szerkeszteni kívánt alhálózatot, vagy hozzon létre egy további alhálózatot.
1. Frissítsen vagy adja meg a megfelelő méretű IP-címtartományt, majd **mentse** a módosításokat.
1. [Hozzon létre egy helyettesítő felügyelt tartományt](tutorial-create-instance.md). Győződjön meg arról, hogy a frissített virtuális hálózati alhálózat elég nagy méretű IP-címtartományt kapott.

A felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: az erőforrások nem állíthatók helyre

### <a name="alert-message"></a>Riasztási üzenet

*Az Azure AD Domain Services által használt erőforrások váratlan állapotban észlelhetők, és nem állíthatók helyre.*

### <a name="resolution"></a>Feloldás

Ez a hiba nem állítható helyre. A riasztás feloldásához [törölje a meglévő felügyelt tartományt](delete-aadds.md) , majd hozza létre újból. Ha nem sikerül törölni a felügyelt tartományt, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

## <a name="aadds114-subnet-invalid"></a>AADDS114: az alhálózat érvénytelen

### <a name="alert-message"></a>Riasztási üzenet

*A Azure AD Domain Services telepítéséhez kiválasztott alhálózat érvénytelen, és nem használható.*

### <a name="resolution"></a>Feloldás

Ez a hiba nem állítható helyre. A riasztás feloldásához [törölje a meglévő felügyelt tartományt](delete-aadds.md) , majd hozza létre újból. Ha nem sikerül törölni a felügyelt tartományt, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

## <a name="aadds115-resources-are-locked"></a>AADDS115: az erőforrások zárolva vannak

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartomány által használt hálózati erőforrások közül egy vagy több nem használható, mert a célként megadott hatókör zárolva van.*

### <a name="resolution"></a>Feloldás

Az erőforrás-zárolások az Azure-erőforrásokra alkalmazhatók a módosítás vagy törlés megakadályozása érdekében. Mivel az Azure AD DS felügyelt szolgáltatás, az Azure platformnak szüksége van a konfigurációs módosítások elvégzésére. Ha egyes Azure AD DS-összetevőkön erőforrás-zárolást alkalmaz, az Azure platform nem tudja végrehajtani a felügyeleti feladatait.

Az Azure AD DS-összetevőkön található erőforrás-zárolások kereséséhez és eltávolításához végezze el a következő lépéseket:

1. A felügyelt tartomány minden olyan hálózati összetevőjénél, mint például a virtuális hálózat, a hálózati adapter vagy a nyilvános IP-cím, a Azure Portalban tekintse meg a műveleti naplókat. A műveleti naplóknak jelezniük kell, hogy a művelet miért sikertelen, és hol van alkalmazva az erőforrás-zárolás.
1. Válassza ki azt az erőforrást, amelynél zárolás van alkalmazva, majd **a zárolások területen válassza** ki és távolítsa el a zárolás (oka) t.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: az erőforrások használhatatlanok

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartomány által használt hálózati erőforrások közül egy vagy több nem hajtható végre a házirend-korlátozás (ok) miatt.*

### <a name="resolution"></a>Feloldás

A szabályzatok az Azure-erőforrásokra és-csoportokra vonatkoznak, amelyek meghatározzák, hogy milyen konfigurációs műveleteket lehet engedélyezni. Mivel az Azure AD DS felügyelt szolgáltatás, az Azure platformnak szüksége van a konfigurációs módosítások elvégzésére. Ha egy házirendet az Azure AD DS egyes összetevőire alkalmaz, előfordulhat, hogy az Azure platform nem tudja végrehajtani a felügyeleti feladatait.

A következő lépések végrehajtásával ellenőrizheti az alkalmazott házirendeket az Azure AD DS összetevőin, és frissítheti őket:

1. Az erőforráscsoport összes felügyelt tartományának hálózati összetevőjénél (például virtuális hálózat, hálózati adapter vagy nyilvános IP-cím) tekintse meg a Azure Portalban található műveleti naplókat. A műveleti naplóknak jelezniük kell, hogy egy művelet miért nem sikerül, és hol van érvényben korlátozó szabályzat.
1. Válassza ki azt az erőforrást, ahol a **szabályzatot** alkalmazni kívánja, majd a házirendek területen válassza ki és szerkessze a házirendet, hogy kevésbé legyen korlátozó.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: a szinkronizálás egy darabig nem fejeződött be

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartományt utoljára szinkronizálták az Azure AD-vel [date]. Előfordulhat, hogy a felhasználók nem tudnak bejelentkezni a felügyelt tartományba, vagy a csoporttagságok nem szinkronizálhatók az Azure AD-vel.*

### <a name="resolution"></a>Feloldás

A felügyelt tartomány konfigurációjában felmerülő problémákat jelző riasztások esetén [jelölje be az Azure AD DS Health](check-health.md) című részt. A hálózati konfigurációval kapcsolatos problémák letilthatják az Azure AD-vel való szinkronizálást. Ha olyan riasztásokat tud feloldani, amelyek konfigurációs problémát jeleznek, várjon két órát, és térjen vissza, és ellenőrizze, hogy a szinkronizálás sikeresen befejeződött-e.

A következő gyakori okok azt okozzák, hogy a szinkronizálás egy felügyelt tartományban leáll:

* A szükséges hálózati kapcsolat blokkolva van. Ha többet szeretne megtudni az Azure-beli virtuális hálózat problémákról és a szükséges információkról, tekintse meg a [hálózati biztonsági csoportok](alert-nsg.md) és az [Azure AD DS hálózati követelményeinek](network-considerations.md)elhárítása című témakört.
*  A jelszó-szinkronizálás nem lett beállítva vagy sikeresen befejeződött a felügyelt tartomány telepítésekor. Beállíthatja a jelszó-szinkronizálást a [csak felhőalapú felhasználók](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) vagy [hibrid felhasználók](tutorial-configure-password-hash-sync.md)számára.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: a biztonsági mentés egy darabig nem történt meg.

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartomány utolsó biztonsági mentése a következő időpontban: [date].*

### <a name="resolution"></a>Feloldás

A felügyelt tartomány konfigurációjában felmerülő problémákat jelző [Azure AD DS állapotú](check-health.md) riasztások keresése. A hálózati konfigurációval kapcsolatos problémák letilthatják az Azure-platformot a biztonsági mentések sikeres elvégzése miatt. Ha olyan riasztásokat tud feloldani, amelyek konfigurációs problémát jeleznek, várjon két órát, és térjen vissza, és ellenőrizze, hogy a szinkronizálás sikeresen befejeződött-e.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: felfüggesztés a letiltott előfizetés miatt

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartomány fel van függesztve, mert a tartományhoz társított Azure-előfizetés nem aktív.*

### <a name="resolution"></a>Feloldás

> [!WARNING]
> Ha egy felügyelt tartomány hosszabb ideig fel van függesztve, fennáll annak a veszélye, hogy törölve van. Oldja meg a felfüggesztés okát a lehető leggyorsabban. További információ: [Az Azure AD DS felfüggesztett állapotának megismerése](suspension.md).

Az Azure AD DS használatához aktív előfizetés szükséges. Ha az Azure-előfizetés, amelyhez a felügyelt tartomány társítva van, nem aktív, meg kell újítania az előfizetés újraaktiválásához.

1. [Újítsa meg az Azure-előfizetését](../cost-management-billing/manage/subscription-disabled.md).
2. Az Előfizetés megújítása után egy Azure AD DS-értesítés lehetővé teszi a felügyelt tartomány újbóli engedélyezését.

Ha a felügyelt tartomány ismét engedélyezve van, a felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Érvénytelen konfiguráció miatti felfüggesztés

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartomány Érvénytelen konfiguráció miatt fel van függesztve. A szolgáltatás hosszú ideje nem tudta kezelni, javítani vagy frissíteni a felügyelt tartomány tartományvezérlőit.*

### <a name="resolution"></a>Feloldás

> [!WARNING]
> Ha egy felügyelt tartomány hosszabb ideig fel van függesztve, fennáll annak a veszélye, hogy törölve van. Oldja meg a felfüggesztés okát a lehető leggyorsabban. További információ: [Az Azure AD DS felfüggesztett állapotának megismerése](suspension.md).

A felügyelt tartomány konfigurációjában felmerülő problémákat jelző [Azure AD DS állapotú](check-health.md) riasztások keresése. Ha olyan riasztásokat tud feloldani, amelyek konfigurációs problémát jeleznek, várjon két órát, és térjen vissza, és ellenőrizze, hogy befejeződött-e a szinkronizálás. Ha elkészült, [Nyisson meg egy Azure-támogatási kérést][azure-support] a felügyelt tartomány újbóli engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

Ha továbbra is problémákba ütközik, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md