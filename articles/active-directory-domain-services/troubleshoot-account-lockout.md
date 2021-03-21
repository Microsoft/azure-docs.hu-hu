---
title: A fiók zárolásának hibáinak megoldása a Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a felhasználói fiókokat a Azure Active Directory Domain Servicesban kizárni kívánt gyakori problémákat.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 3341f290a5a5bb169b6e70ea22459a2afafedbbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103198962"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services felügyelt tartománnyal kapcsolatos fiókzárolási problémák elhárítása

A ismételt rosszindulatú bejelentkezési kísérletek megelőzése érdekében egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartomány zárolja a fiókokat egy meghatározott küszöbérték után. Ez a fiókzárolás a bejelentkezési támadás incidense nélkül is történhet véletlenül. Ha például a felhasználó többször is helytelen jelszót ad meg, vagy a szolgáltatás egy régi jelszót próbál használni, a rendszer kizárja a fiókot.

Ez a hibaelhárítási cikk azt ismerteti, hogy miért történik a fiókok zárolása, és hogyan konfigurálhatja a viselkedést, és hogyan tekintheti át a biztonsági naplózást a zárolási események hibaelhárítása érdekében.

## <a name="what-is-an-account-lockout"></a>Mi az a fiókzárolás?

Egy Azure AD DS felügyelt tartomány felhasználói fiókja ki van zárva, ha a sikertelen bejelentkezési kísérletek meghatározott küszöbértéke teljesül. Ez a fiókzárolási viselkedés úgy lett kialakítva, hogy megvédje a nem kötelezően kikényszerített, automatikus digitális támadásra utaló kísérleteket.

**Alapértelmezés szerint, ha 2 percen belül 5 hibás jelszóval próbálkozik, a fiók 30 percig ki lesz zárva.**

Az alapértelmezett fiókzárolási küszöbértékek a részletes jelszóházirendek használatával konfigurálhatók. Ha a követelmények meghatározott készletével rendelkezik, felülbírálhatja ezeket az alapértelmezett fiókzárolási küszöbértékeket. Azonban nem ajánlott a küszöbértékek növelését végrehajtani, hogy csökkentse a fiók zárolását. Először hárítsa el a fiókzárolási viselkedés forrását.

### <a name="fine-grained-password-policy"></a>Részletes jelszóházirendek

A részletes jelszóházirendek (Fgpp-EK) segítségével meghatározott korlátozásokat alkalmazhat a jelszó-és fiókzárolási házirendek számára a tartomány különböző felhasználói számára. A FGPP csak felügyelt tartományon belüli felhasználókat érint. Az Azure AD által felügyelt tartományba szinkronizált Felhőbeli felhasználókat és tartományi felhasználókat csak a felügyelt tartományon belüli jelszóházirend érinti. Az Azure AD-ben vagy egy helyszíni címtárban nem érinti a fiókjaikat.

A házirendeket a rendszer a felügyelt tartományon keresztül terjeszti a csoportba, és a következő felhasználói bejelentkezéskor alkalmazza a módosításokat. A házirend módosítása nem oldja fel a már kizárt felhasználói fiók zárolását.

A részletes jelszóházirendek, valamint a közvetlenül az Azure AD DSban létrehozott felhasználók közötti különbségekről, valamint az Azure AD-ből való szinkronizálásról további információt a [jelszó-és fiókzárolási házirendek konfigurálása][configure-fgpp]című témakörben talál.

## <a name="common-account-lockout-reasons"></a>Gyakori fiókzárolási okok

A fiókok zárolásának leggyakoribb okai a kártékony szándékok vagy tényezők nélkül, a következő esetekben:

* **A felhasználó zárolta magát.**
    * A legutóbbi jelszó módosítása után a felhasználó továbbra is egy korábbi jelszót használ? Az öt sikertelen kísérlet alapértelmezett fiókzárolási szabályzata 2 percen belül véletlenül a felhasználó által a régi jelszó ismételt kipróbálására is felhasználható.
* **Van olyan alkalmazás vagy szolgáltatás, amely régi jelszóval rendelkezik.**
    * Ha egy fiókot alkalmazások vagy szolgáltatások használnak, akkor előfordulhat, hogy az erőforrások többször is megpróbálnak bejelentkezni egy régi jelszóval. Ez a viselkedés a fiók zárolását okozza.
    * Próbálja meg csökkenteni a fiókok használatát több különböző alkalmazás vagy szolgáltatás között, és jegyezze fel a hitelesítő adatokat. Ha a fiók jelszava megváltozik, frissítse a kapcsolódó alkalmazásokat vagy szolgáltatásokat.
* **A jelszó egy másik környezetben módosult, és az új jelszó még nincs szinkronizálva.**
    * Ha a fiók jelszava módosítva van a felügyelt tartományon kívül, például egy helyszíni AD DS környezetben, eltarthat néhány percig, amíg a jelszó megváltozása szinkronizálva lesz az Azure AD-vel és a felügyelt tartományba.
    * Olyan felhasználó, aki a jelszó-szinkronizálási folyamat befejeződése előtt megpróbál bejelentkezni egy erőforrásba a felügyelt tartományba, a fiókját ki kell zárni.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Fiókok zárolásának hibakeresése biztonsági naplózással

Az [Azure AD DS biztonsági naplózásának engedélyezésével][security-audit-events]elháríthatja a fiókzárolási események bekövetkezésekor felmerülő problémákat. A naplózási események csak a szolgáltatás engedélyezésének időpontjában vannak rögzítve. Ideális esetben a biztonsági naplózást is engedélyeznie kell, *mielőtt* probléma merül fel a hibakeresés során. Ha egy felhasználói fióknak többször is van zárolási problémája, akkor a következő esetekben engedélyezheti a biztonsági naplózást.

A biztonsági naplózás engedélyezése után az alábbi példák bemutatják, hogyan tekintheti át a fiókzárolási *eseményeket*, a *4740* kódot.

Az összes fiókzárolási esemény megtekintése az elmúlt hét napban:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

A *driley* nevű fiók utolsó hét napjához tartozó összes fiókzárolási esemény megtekintése.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Az összes fiókzárolási esemény megtekintése a 2020. június 26. között, 9:00 és 2020 éjfél, a dátum és idő szerint növekvő sorrendbe rendezve:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

**Megjegyzés**

Az 4776-es és a 4740-es esemény részleteit a "forrás munkaállomás:" üres elemnél találja. Ennek az az oka, hogy a rossz jelszó más eszközökön keresztül történt a hálózati bejelentkezés során.
Például: Ha rendelkezik RADIUS-kiszolgálóval, amely továbbítja a hitelesítést a HRE DS-be. Annak ellenőrzéséhez, hogy az RDP-t a DC háttérbe konfigurálja, konfigurálja a Netlogon naplókat.

03/04 19:07:29 [bejelentkezés] [10752] contoso: SamLogon: a contoso\Nagappan.Veerappan tranzitív hálózati bejelentkezése (a LOB11-RADIUS használatával) megadva 

03/04 19:07:29 [bejelentkezés] [10752] contoso: SamLogon: a contoso\Nagappan.Veerappan tranzitív hálózati bejelentkezése (a LOB11-RADIUS használatával) visszaadja a 0xC000006A

03/04 19:07:35 [bejelentkezés] [10753] contoso: SamLogon: a contoso\Nagappan.Veerappan tranzitív hálózati bejelentkezése (a LOB11-RADIUS használatával) megadva 

03/04 19:07:35 [bejelentkezés] [10753] contoso: SamLogon: a contoso\Nagappan.Veerappan tranzitív hálózati bejelentkezése (a LOB11-RADIUS használatával) visszaadja a 0xC000006A

Engedélyezze az RDP-t a NSG lévő tartományvezérlők számára a diagnosztika-rögzítés konfigurálásához (azaz Netlogon) https://docs.microsoft.com/azure/active-directory-domain-services/alert-nsg#inbound-security-rules Ha már módosította az alapértelmezett NSG, kövesse a PSlet módot az engedélyezéshez https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#port-3389---management-using-remote-desktop

A Netlogon-napló engedélyezése bármely kiszolgálón https://docs.microsoft.com/troubleshoot/windows-client/windows-security/enable-debug-logging-netlogon-service

## <a name="next-steps"></a>Következő lépések

A fiókzárolási küszöbértékek beállítására szolgáló részletes jelszóházirendek részletes ismertetését lásd: [jelszó-és fiókzárolási házirendek konfigurálása][configure-fgpp].

Ha továbbra sem sikerül a virtuális gép csatlakoztatása a felügyelt tartományhoz, [keressen segítséget a Azure Active Directory támogatási jegyének megnyitásához][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
