---
title: Az önkiszolgáló jelszó-visszaállítás visszaírási – Azure Active Directory
description: Ismerje meg, hogy miként lehet elhárítani a gyakori problémákat és a megoldási lépéseket az önkiszolgáló jelszó-visszaállítási visszaírási Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0620304de1866d24719b137836419502cd25bee9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682237"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>Az önkiszolgáló jelszó-visszaállítási visszaírási hibáinak megoldása Azure Active Directory

Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára, hogy a felhőben visszaállítsa a jelszavukat. A Password visszaírási egy olyan szolgáltatás, amely [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) lehetővé teszi, hogy a jelszó módosításait a felhőben valós időben lehessen visszaírni egy meglévő helyszíni címtárba.

Ha problémái vannak a SSPR visszaírási, az alábbi hibaelhárítási lépések és gyakori hibák segíthetnek. Ha nem találja a választ a problémájára, a [támogatási csapatunk mindig](#contact-microsoft-support) szívesen segít Önnek.

## <a name="troubleshoot-connectivity"></a>Kapcsolatok hibaelhárítása

Ha problémái vannak a Azure AD Connect jelszavának visszaírási, tekintse át a következő lépéseket, amelyek segíthetnek a probléma megoldásában. A szolgáltatás helyreállításához javasoljuk, hogy kövesse az alábbi lépéseket a következő sorrendben:

* [Hálózati kapcsolat megerősítése](#confirm-network-connectivity)
* [A Azure AD Connect Sync szolgáltatás újraindítása](#restart-the-azure-ad-connect-sync-service)
* [A jelszó visszaírási funkció letiltása és újbóli engedélyezése](#disable-and-re-enable-the-password-writeback-feature)
* [A legújabb Azure AD Connect kiadásának telepítése](#install-the-latest-azure-ad-connect-release)
* [Jelszó-visszaírási hibáinak megoldása](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>Hálózati kapcsolat megerősítése

A leggyakoribb meghibásodási pont az, hogy a tűzfal vagy a proxy portok, vagy a tétlen időtúllépések helytelenül vannak konfigurálva.

A Azure AD Connect *1.1.443.0* és újabb verziók esetén a *kimenő HTTPS* -hozzáférés a következő címekhez szükséges:

* *\*. passwordreset.microsoftonline.com*
* *\*. servicebus.windows.net*

Azure [gov-végpontok](../../azure-government/compare-azure-government-global-azure.md#guidance-for-developers):

* *\*. passwordreset.microsoftonline.us*
* *\*. servicebus.usgovcloudapi.net*

Ha további részletességre van szüksége, tekintse meg [Microsoft Azure adatközpont IP-címtartományok listáját](https://www.microsoft.com/download/details.aspx?id=41653). A lista minden szerdán frissül, és a következő hétfőn lép érvénybe.

További információ: [Azure ad Connect csatlakoztatásának előfeltételei](../hybrid/how-to-connect-install-prerequisites.md).

### <a name="restart-the-azure-ad-connect-sync-service"></a>A Azure AD Connect Sync szolgáltatás újraindítása

A kapcsolódási problémák vagy a szolgáltatással kapcsolatos egyéb átmeneti problémák megoldásához végezze el a következő lépéseket a Azure AD Connect Sync szolgáltatás újraindításához:

1. A Azure AD Connectt futtató kiszolgáló rendszergazdája válassza az **Indítás** lehetőséget.
1. Adja meg a *Services. msc* kifejezést a keresőmezőbe, és kattintson az **ENTER** gombra.
1. Keresse meg a *Microsoft Azure ad szinkronizálási* bejegyzést.
1. Kattintson a jobb gombbal a szolgáltatási bejegyzésre, válassza az **Újraindítás** lehetőséget, és várjon, amíg a művelet befejeződik.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="A Azure AD-szinkronizáló szolgáltatás újraindítása a grafikus felhasználói felület használatával" border="false":::

Ezek a lépések újra létrehozza a kapcsolatot az Azure AD-vel, és fel kell oldania a kapcsolódási problémákat.

Ha a Azure AD Connect Sync szolgáltatás újraindítása nem oldja meg a problémát, próbálja meg letiltani, majd ismét engedélyezni a jelszó visszaírási szolgáltatást a következő szakaszban.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>A jelszó visszaírási funkció letiltása és újbóli engedélyezése

A problémák elhárításához végezze el a következő lépéseket a jelszó visszaírási szolgáltatás letiltásához és újbóli engedélyezéséhez:

1. A Azure AD Connectt futtató kiszolgáló rendszergazdája nyissa meg a **Azure ad Connect konfigurációs varázslót**.
1. A **Kapcsolódás az Azure ad-hoz** mezőbe írja be az Azure ad globális rendszergazdai hitelesítő adatait.
1. A **kapcsolódás ad DShoz** mezőben adja meg a helyszíni Active Directory Domain Services rendszergazdai hitelesítő adatait.
1. A **felhasználók egyedi azonosításához** kattintson a **tovább** gombra.
1. A **választható funkciók** területen törölje a **jelszó visszaírási** jelölőnégyzet jelölését.
1. Kattintson a **tovább** gombra a fennmaradó párbeszédpaneleken, anélkül, hogy bármit módosítaná, amíg be nem fejeződik a **konfigurálásra kész** lapra.
1. Győződjön meg arról, hogy a **konfigurálásra kész lapon** a *jelszó visszaírási* beállítás *le van tiltva*. A módosítások elvégzéséhez kattintson a zöld **beállítás** gombra.
1. A **kész** területen törölje a **szinkronizálás most** lehetőséget, majd kattintson a **Befejezés** gombra a varázsló bezárásához.
1. Nyissa meg újra a **Azure ad Connect konfigurációs varázslót**.
1. Ismételje meg a 2-8 lépést, ezúttal a **választható szolgáltatások** lapon a *jelszó visszaírási* lehetőség kiválasztásával engedélyezze újra a szolgáltatást.

Ezek a lépések újra létrehozza a kapcsolatot az Azure AD-vel, és fel kell oldania a kapcsolódási problémákat.

Ha a visszaírási szolgáltatás letiltása és újbóli engedélyezése nem oldja meg a problémát, telepítse újra Azure AD Connect a következő szakaszban.

### <a name="install-the-latest-azure-ad-connect-release"></a>A legújabb Azure AD Connect kiadásának telepítése

Az Azure AD Connect újratelepítése az Azure AD és a helyi Active Directory Domain Services-környezet közötti konfigurációs és kapcsolódási problémák megoldására is képes. Azt javasoljuk, hogy ezt a lépést csak akkor hajtsa végre, ha az előző lépéseket a kapcsolat ellenőrzéséhez és hibaelhárításához próbálja meg végrehajtani.

> [!WARNING]
> Ha testre szabta a beépített szinkronizálási szabályokat, *a frissítés folytatása előtt végezze el a biztonsági mentést, majd manuálisan végezze el újból a telepítést a befejezés után.*

1. Töltse le a Azure AD Connect legújabb verzióját a [Microsoft letöltőközpontból](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Ahogy már telepítette a Azure AD Connectt, végezzen helyben történő frissítést, hogy frissítse a Azure AD Connect telepítését a legújabb verzióra.

    Futtassa a letöltött csomagot, és kövesse a képernyőn megjelenő utasításokat a Azure AD Connect frissítéséhez.

Ezekkel a lépésekkel újra létre kell hoznia a kapcsolatot az Azure AD-vel, és fel kell oldania a kapcsolódási problémákat.

Ha a Azure AD Connect-kiszolgáló legújabb verziójának telepítése nem oldja meg a problémát, próbálja meg letiltani, majd újból engedélyezni a jelszó-visszaírási a legújabb kiadás telepítése után.

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>Győződjön meg arról, hogy a Azure AD Connect rendelkezik a szükséges engedélyekkel

A Azure AD Connect megköveteli a jelszó- **visszaállítási** engedély AD DS visszaírási. Annak vizsgálatához, hogy Azure AD Connect rendelkezik-e a szükséges engedéllyel egy adott helyszíni AD DS felhasználói fiókhoz, használja a **Windows hatályos engedély** szolgáltatást:

1. Jelentkezzen be a Azure ad Connect-kiszolgálóra, és indítsa el a **synchronization Service Manager** a   >  **szinkronizációs szolgáltatás** elindítása lehetőség kiválasztásával.
1. Az **Összekötők** lapon válassza ki a helyszíni **Active Directory Domain Services** -összekötőt, majd válassza a **Tulajdonságok** lehetőséget.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="A tulajdonságok szerkesztését bemutató Synchronization Service Manager" border="false":::
  
1. Az előugró ablakban válassza a **kapcsolódás Active Directory erdőhöz** lehetőséget, és jegyezze fel a **Felhasználónév** tulajdonságot. Ez a tulajdonság a Azure AD Connect által a címtár-szinkronizálás végrehajtásához használt AD DS fiók.

    A jelszó-visszaírási elvégzéséhez a AD DS fióknak új jelszó kérése engedéllyel kell rendelkeznie. Azure AD Connect A következő lépésekben ellenőrizheti a felhasználói fiók engedélyeit.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="A szinkronizálási szolgáltatás megkeresése Active Directory felhasználói fiókkal" border="false":::
  
1. Jelentkezzen be egy helyszíni tartományvezérlőre, és indítsa el a **Active Directory felhasználók és számítógépek** alkalmazást.
1. Válassza a nézet lehetőséget, és győződjön **meg** arról, hogy a **speciális szolgáltatások** beállítás engedélyezve van.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="Active Directory felhasználók és számítógépek speciális funkciókat mutatnak be" border="false":::
  
1. Keresse meg az ellenőrizni kívánt AD DS felhasználói fiókot. Kattintson a jobb gombbal a fiók nevére, és válassza a **Tulajdonságok** lehetőséget.  
1. Az előugró ablakban lépjen a **Biztonság** lapra, és válassza a **speciális** lehetőséget.  
1. A rendszergazda előugró ablak **speciális biztonsági beállításai** lapján lépjen a **hatályos hozzáférés** lapra.
1. Válassza a **felhasználó kiválasztása** lehetőséget, válassza ki a Azure ad Connect által használt AD DS fiókot, majd válassza a **hatályos hozzáférés megtekintése** lehetőséget.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="A szinkronizálási fiókot megjelenítő hatályos hozzáférés lap" border="false":::
  
1. Görgessen lefelé, és keresse meg a **jelszó alaphelyzetbe állítása**. Ha a bejegyzésben pipa szerepel, a AD DS fiók jogosult a kiválasztott Active Directory felhasználói fiók jelszavának alaphelyzetbe állítására.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="Annak ellenőrzése, hogy a szinkronizálási fiók a jelszó alaphelyzetbe állítása engedéllyel rendelkezik-e" border="false":::

## <a name="common-password-writeback-errors"></a>Gyakori jelszó-visszaírási hibák

A jelszó visszaírási a következő konkrétabb problémák merülhetnek fel. Ha ezen hibák valamelyikével rendelkezik, tekintse át a javasolt megoldást, és ellenőrizze, hogy a jelszó visszaírási megfelelően működik-e.

| Hiba | Megoldás |
| --- | --- |
| A jelszó-visszaállítási szolgáltatás nem indul el a helyszínen. 6800-es hiba jelenik meg az Azure AD Connect gép alkalmazás-eseménynaplójában. <br> <br> A bevezetést, az összevont, az átmenő hitelesítést, vagy a jelszó-kivonattal szinkronizált felhasználók nem állíthatják alaphelyzetbe a jelszavukat. | Ha a jelszó visszaírási engedélyezve van, a Szinkronizáló motor meghívja a visszaírási-könyvtárat a konfiguráció (Bevezetés) elvégzéséhez a felhőalapú bevezetési szolgáltatással való kommunikációval. Az előkészítés során felmerülő hibák, illetve a jelszó-visszaírási Windows Communication Foundation-(WCF-) végpontjának elindítása a Azure AD Connect gép eseménynaplójában hibákat eredményez. <br> <br> A Azure AD-szinkronizáló (ADSync) szolgáltatás újraindításakor, ha a visszaírási konfigurálva lett, a WCF-végpont elindul. Ha azonban a végpont indítása sikertelen, akkor a 6800-es eseményt naplózza, és a szinkronizálási szolgáltatás indítását is lehetővé teszi. Ennek az eseménynek a jelenléte azt jelenti, hogy a jelszó visszaírási végpontja nem indult el. A 6800-es esemény eseménynaplójának adatai, valamint az PasswordResetService összetevő által generált Eseménynapló-bejegyzések, azt jelzi, hogy miért nem indítható el a végpont. Tekintse át ezeket az Eseménynapló-hibákat, és próbálja meg újraindítani a Azure AD Connect, ha a jelszó visszaírási továbbra sem működik. Ha a probléma továbbra is fennáll, próbálja meg letiltani, majd ismét engedélyezni a jelszó-visszaírási.
| Ha a felhasználó egy jelszó alaphelyzetbe állítását vagy egy olyan fiók zárolásának feloldását kísérli meg, amelyen engedélyezve van a jelszó visszaírási, a művelet sikertelen lesz. <br> <br> Emellett megjelenik egy olyan esemény a Azure AD Connect eseménynaplóban, amely tartalmazza a következőt: "a szinkronizációs motor hibát jelzett, HR = 800700CE, üzenet = a fájlnév vagy a kiterjesztés túl hosszú" a feloldási művelet után. | Keresse meg a Azure AD Connect Active Directory fiókját, és állítsa alaphelyzetbe a jelszót, hogy ne tartalmazzon több mint 256 karaktert. Ezután nyissa meg a **szinkronizálási szolgáltatást** a **Start** menüből. Tallózással keresse meg az **összekötőket** , és keresse meg az **Active Directory-összekötőt**. Jelölje ki, majd válassza a **Tulajdonságok** lehetőséget. Keresse meg a **hitelesítő adatok** lapot, és adja meg az új jelszót. A lap bezárásához kattintson **az OK gombra** . |
| A Azure AD Connect telepítési folyamatának utolsó lépéseként megjelenik egy hibaüzenet, amely azt jelzi, hogy a jelszó visszaírási nem konfigurálható. <br> <br> A Azure AD Connect alkalmazás eseménynaplójában a "hiba történt az Auth-token beszerzése" hibaüzenet szövege 32009 szerepel. | Ez a hiba a következő két esetben fordul elő: <br><ul><li>Helytelen jelszót adott meg a Azure AD Connect telepítési folyamat elején megadott globális rendszergazdai fiókhoz.</li><li>Egy összevont felhasználót próbált használni a Azure AD Connect telepítési folyamatának elején megadott globális rendszergazdai fiókhoz.</li></ul> A probléma megoldásához ellenőrizze, hogy nem használ-e összevont fiókot a telepítési folyamat elején megadott globális rendszergazda számára, és hogy a megadott jelszó helyes-e. |
| A Azure AD Connect Machine eseménynaplója a PasswordResetService futtatásával eldobott 32002-es hibát tartalmaz. <br> <br> A hiba szövege: "hiba történt a ServiceBus való csatlakozáskor. A jogkivonat-szolgáltató nem tudott biztonsági jogkivonatot biztosítani. " | A helyszíni környezet nem tud csatlakozni a felhőben lévő Azure Service Bus végponthoz. Ezt a hibát általában egy tűzfalszabály okozta, amely blokkolja a kimenő kapcsolatokat egy adott porton vagy webcímen. További információért lásd a [kapcsolat előfeltételeit](../hybrid/how-to-connect-install-prerequisites.md) . A szabályok frissítése után indítsa újra a Azure AD Connect-kiszolgálót, és a jelszó visszaírási újra kell kezdenie a munkát. |
| Néhány alkalommal, összevont, átmenő hitelesítés vagy jelszó-kivonatoló szinkronizált felhasználó használata után nem állíthatja alaphelyzetbe a jelszavukat. | Bizonyos ritka esetekben előfordulhat, hogy a jelszó-visszaírási szolgáltatás nem tud újraindulni, amikor a Azure AD Connect újraindul. Ezekben az esetekben először ellenőrizze, hogy a jelszó visszaírási engedélyezve van-e a helyszínen. A Azure AD Connect varázsló vagy a PowerShell használatával is megtekintheti. Ha úgy tűnik, hogy a funkció engedélyezve van, próbálkozzon újra a szolgáltatás engedélyezésével vagy letiltásával. Ha ez a hibaelhárítási lépés nem működik, próbálkozzon a Azure AD Connect teljes eltávolításával és újratelepítésével. |
| Összevont, átmenő hitelesítés vagy jelszó-kivonatolással szinkronizált felhasználók, akik megpróbálják visszaállítani a jelszavukat, a jelszavuk beküldésére tett kísérlet után hibaüzenetet látnak. A hiba azt jelzi, hogy probléma történt a szolgáltatásban. <br ><br> A probléma mellett a jelszó-visszaállítási műveletek során előfordulhat, hogy a felügyeleti ügynök a helyszíni eseménynaplókban való hozzáférése megtagadva hibát észlelt. | Ha az eseménynaplóban ezeket a hibákat látja, ellenőrizze, hogy a varázslóban megadott Active Directory felügyeleti ügynök (ADMA) fiók rendelkezik-e a jelszó visszaírási szükséges engedélyekkel. <br> <br> Miután megadta az engedélyt, akár egy óráig is eltarthat, amíg az engedélyek le nem szivárognak a `sdprop` tartományvezérlőn (DC) a háttérben feladaton keresztül. <br> <br> A jelszó-visszaállítás működéséhez az engedélyt meg kell pecsételni azon felhasználói objektum biztonsági leíróján, amelynek a jelszavát alaphelyzetbe állítja. Amíg ez az engedély nem jelenik meg a felhasználói objektumon, a jelszó-visszaállítás továbbra is meghiúsul egy hozzáférés-megtagadási üzenettel. |
| Összevont, átmenő hitelesítés vagy jelszó-kivonatolással szinkronizált felhasználók, akik megpróbálják alaphelyzetbe állítani a jelszavukat, a jelszavuk elküldése után hibaüzenetben talál. A hiba azt jelzi, hogy probléma történt a szolgáltatásban. <br> <br> A probléma mellett a jelszó-visszaállítási műveletek során előfordulhat, hogy a Azure AD Connect szolgáltatásban az "objektum nem található" hibaüzenetet jelző hibaüzenet jelenik meg. | Ez a hiba általában azt jelzi, hogy a Szinkronizáló motor nem találja az Azure AD-összekötő területének felhasználói objektumát vagy a csatolt Metaverse (MV) vagy az Azure AD-összekötő terület objektumát. <br> <br> A probléma megoldásához ellenőrizze, hogy a felhasználó valóban szinkronizálva van-e a helyszíniről az Azure AD-ba a Azure AD Connect aktuális példányán keresztül, és vizsgálja meg az összekötők és az MV közötti objektumok állapotát. Győződjön meg arról, hogy a Active Directory tanúsítványszolgáltatás (AD CS) objektum a "Microsoft.InfromADUserAccountEnabled.xxx" szabály használatával csatlakozik az MV-objektumhoz.|
| Összevont, átmenő hitelesítés vagy jelszó-kivonatolással szinkronizált felhasználók, akik jelszó-visszaállítást próbálnak meg, a jelszavuk elküldése után hibaüzenetet kapnak. A hiba azt jelzi, hogy probléma történt a szolgáltatásban. <br> <br> A probléma mellett a jelszó-visszaállítási műveletek során előfordulhat, hogy a Azure AD Connect szolgáltatásban egy hibaüzenet jelenik meg, amely azt jelzi, hogy "több találat található" hibaüzenet jelenik meg. | Ez azt jelzi, hogy a Szinkronizáló motor azt észlelte, hogy az MV-objektum több AD CS-objektumhoz van csatlakoztatva a "Microsoft.InfromADUserAccountEnabled.xxx" használatával. Ez azt jelenti, hogy a felhasználó több erdőben is engedélyezett fiókkal rendelkezik. Ez a forgatókönyv nem támogatott a jelszó-visszaírási. |
| Konfigurációs hiba miatt sikertelen volt a jelszó-művelet. Az alkalmazás eseménynaplója Azure AD Connect 6329-es hibát tartalmaz a következő szöveggel: "0x8023061f (a művelet meghiúsult, mert a jelszó-szinkronizálás nincs engedélyezve ezen a felügyeleti ügynökön)". | Ez a hiba akkor fordul elő, ha a Azure AD Connect konfiguráció új Active Directory erdő hozzáadására szolgál (vagy egy meglévő erdő eltávolításához és olvasásához), miután a Password visszaírási funkció már engedélyezve van. A legutóbb hozzáadott erdőkben lévő felhasználók jelszavas műveletei sikertelenek. A probléma megoldásához tiltsa le, majd engedélyezze újra a jelszó visszaírási szolgáltatást az erdő konfigurációs módosításainak befejeződése után. |

## <a name="password-writeback-event-log-error-codes"></a>Jelszó visszaírási eseménynaplójának hibakódai

Az ajánlott eljárás, ha a jelszó visszaírási kapcsolatos problémák elhárítása során a Azure AD Connect számítógépen ellenőrzi az alkalmazás eseménynaplóját. Ez az Eseménynapló két forrásból származó eseményeket tartalmaz a jelszó visszaírási. A *PasswordResetService* forrás a visszaírási jelszavának működésével kapcsolatos műveleteket és problémákat ismerteti. A *AdSync* forrás leírja a Active Directory Domain Services-környezet jelszavainak beállításával kapcsolatos műveleteket és problémákat.

### <a name="if-the-source-of-the-event-is-adsync"></a>Ha az esemény forrása ADSync

| Code | Név vagy üzenet | Description |
| --- | --- | --- |
| 6329 | ÓVADÉK: MMS (4924) 0x80230619: "A korlátozás megakadályozza a jelszó módosítását az aktuálisan megadott értékre." | Ez az esemény akkor fordul elő, ha a Password visszaírási szolgáltatás olyan jelszót próbál meg beállítani a helyi címtárban, amely nem felel meg a tartomány jelszavának életkora, előzményei, összetettsége vagy szűrési követelményeinek. <br> <br> Ha a jelszó minimális kora, és a közelmúltban módosította a jelszót az adott időkereten belül, nem tudja újra módosítani a jelszót, amíg el nem éri a megadott kort a tartományban. Tesztelési célból a minimális korhatárt 0-ra kell állítani. <br> <br> Ha engedélyezve van a jelszó-előzményekre vonatkozó követelmények, ki kell választania az utolsó *N* -időpontban nem használt jelszót, ahol *N* a korábbi jelszavakat tartalmazó beállítás. Ha olyan jelszót választ, amelyet az utolsó *N* alkalommal használt, akkor ebben az esetben hiba jelenik meg. Tesztelési célból a korábbi jelszavakat 0-ra kell állítani. <br> <br> Ha a jelszó bonyolultságára vonatkozó követelményekkel rendelkezik, mindegyiket kényszeríti a rendszer, amikor a felhasználó megpróbál változtatni vagy alaphelyzetbe állítani egy jelszót. <br> <br> Ha engedélyezve vannak a jelszavas szűrők, és a felhasználó olyan jelszót választ, amely nem felel meg a szűrési feltételeknek, akkor az Alaphelyzetbe állítás vagy a módosítás művelet meghiúsul. |
| 6329 | MMS (3040): admaexport. cpp (2837): a kiszolgáló nem tartalmazza az LDAP-jelszó házirendjének vezérlőjét. | Ez a probléma akkor fordul elő, ha LDAP_SERVER_POLICY_HINTS_OID vezérlő (1.2.840.113556.1.4.2066) nincs engedélyezve a tartományvezérlőn. A jelszó-visszaírási funkció használatához engedélyeznie kell a vezérlőt. Ehhez a tartományvezérlőknek Windows Server 2008R2 vagy újabb rendszeren kell lenniük. |
| HR 8023042 | A Szinkronizáló motor hibát adott vissza: HR = 80230402, üzenet = az objektum lekérésére tett kísérlet sikertelen volt, mert duplikált bejegyzések vannak ugyanazzal a horgonysal. | Ez a hiba akkor fordul elő, ha ugyanaz a felhasználói azonosító több tartományban is engedélyezve van. Ilyen például a fiók-és erőforrás-erdők szinkronizálása, valamint az egyes erdőkben lévő és engedélyezett felhasználói AZONOSÍTÓk. <br> <br> Ez a hiba akkor is előfordulhat, ha nem egyedi Anchor attribútumot, például aliast vagy UPN-t használ, és két felhasználó osztozik ugyanazzal a horgony attribútummal. <br> <br> A probléma megoldásához győződjön meg arról, hogy nem rendelkezik duplikált felhasználókkal a tartományon belül, és hogy minden felhasználóhoz egyedi Anchor attribútumot használ. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Ha az esemény forrása PasswordResetService

| Code | Név vagy üzenet | Description |
| --- | --- | --- |
| 31001 | PasswordResetStart | Ez az esemény azt jelzi, hogy a helyszíni szolgáltatás új jelszó kérését észlelte egy összevont, átmenő hitelesítés vagy jelszó-kivonatoló szinkronizált felhasználó számára, amely a felhőből származik. Ez az esemény az első esemény minden jelszó-visszaállítási visszaírási művelet során. |
| 31002 | PasswordResetSuccess | Ez az esemény azt jelzi, hogy a felhasználó új jelszót adott meg a jelszó-visszaállítási művelet során. Megállapítottuk, hogy ez a Jelszó megfelel a vállalati jelszó követelményeinek. A jelszót sikerült visszaírni a helyi Active Directory környezetbe. |
| 31003 | PasswordResetFail | Ez az esemény azt jelzi, hogy egy felhasználó jelszót adott meg, és a jelszó sikeresen megérkezett a helyszíni környezetbe. De amikor megpróbálta beállítani a jelszót a helyi Active Directory környezetben, hiba történt. Ez a hiba több okból is előfordulhat: <br><ul><li>A felhasználó jelszava nem felel meg a tartomány életkor-, előzmény-, összetettségi vagy szűrési követelményeinek. A probléma megoldásához hozzon létre egy új jelszót.</li><li>Az ADMA-szolgáltatásfiók nem rendelkezik a megfelelő engedélyekkel az új jelszó megadásához a szóban forgó felhasználói fiókban.</li><li>A felhasználó fiókja egy védett csoportban található, például a tartomány vagy a vállalati rendszergazda csoport, amely nem engedélyezi a jelszó-beállítás műveleteit.</li></ul>|
| 31004 | OnboardingEventStart | Ez az esemény akkor fordul elő, ha engedélyezi a visszaírási a Azure AD Connect, és elindítottuk a céget a Password visszaírási webszolgáltatásba. |
| 31005 | OnboardingEventSuccess | Ez az esemény azt jelzi, hogy a bevezetési folyamat sikeres volt, és hogy a jelszó-visszaírási képesség készen áll a használatra. |
| 31006 | ChangePasswordStart | Ez az esemény azt jelzi, hogy a helyszíni szolgáltatás a felhőből származó összevont, átmenő hitelesítés vagy jelszó-kivonatoló szinkronizált felhasználó jelszavának módosítására vonatkozó kérelmet észlelt. Ez az esemény az első esemény minden jelszó-változási visszaírási műveletben. |
| 31007 | ChangePasswordSuccess | Ez az esemény azt jelzi, hogy egy felhasználó új jelszót adott meg a jelszó-módosítási művelet során, azt állapították meg, hogy a Jelszó megfelel a vállalati jelszó követelményeinek, és a jelszót sikeresen visszaírta a helyi Active Directory környezetbe. |
| 31008 | ChangePasswordFail | Ez az esemény azt jelzi, hogy a felhasználó jelszót adott meg, és a jelszó sikeresen megérkezett a helyszíni környezetbe, de amikor megpróbálta beállítani a jelszót a helyi Active Directory környezetben, hiba történt. Ez a hiba több okból is előfordulhat: <br><ul><li>A felhasználó jelszava nem felel meg a tartomány életkor-, előzmény-, összetettségi vagy szűrési követelményeinek. A probléma megoldásához hozzon létre egy új jelszót.</li><li>Az ADMA-szolgáltatásfiók nem rendelkezik a megfelelő engedélyekkel az új jelszó megadásához a szóban forgó felhasználói fiókban.</li><li>A felhasználó fiókja egy védett csoportban található, például tartomány vagy vállalati rendszergazda, amely letiltja a jelszó-megadási műveleteket.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | A helyszíni szolgáltatás új jelszó kérését észlelte egy összevont, átmenő hitelesítéshez vagy jelszó-kivonatolással szinkronizált felhasználóhoz, amely a rendszergazda nevében egy felhasználó nevében található. Ez az esemény a rendszergazda által kezdeményezett összes jelszó-visszaállítási visszaírási művelet első eseménye. |
| 31010 | ResetUserPasswordByAdminSuccess | A rendszergazda új jelszót adott meg egy rendszergazda által kezdeményezett jelszó-visszaállítási művelet során. Megállapítottuk, hogy ez a Jelszó megfelel a vállalati jelszó követelményeinek. A jelszót sikerült visszaírni a helyi Active Directory környezetbe. |
| 31011 | ResetUserPasswordByAdminFail | A rendszergazda kiválasztott egy jelszót a felhasználó nevében. A jelszó sikeresen megérkezett a helyszíni környezetbe. De amikor megpróbálta beállítani a jelszót a helyi Active Directory környezetben, hiba történt. Ez a hiba több okból is előfordulhat: <br><ul><li>A felhasználó jelszava nem felel meg a tartomány életkor-, előzmény-, összetettségi vagy szűrési követelményeinek. Próbáljon ki egy új jelszót a probléma megoldásához.</li><li>Az ADMA-szolgáltatásfiók nem rendelkezik a megfelelő engedélyekkel az új jelszó megadásához a szóban forgó felhasználói fiókban.</li><li>A felhasználó fiókja egy védett csoportban található, például tartomány vagy vállalati rendszergazda, amely letiltja a jelszó-megadási műveleteket.</li></ul>  |
| 31012 | OffboardingEventStart | Ez az esemény akkor fordul elő, ha letiltotta a visszaírási a Azure AD Connect, és azt jelzi, hogy elindítottuk a offboarding a visszaírási Web Service-ben. |
| 31013| OffboardingEventSuccess| Ez az esemény azt jelzi, hogy a offboarding folyamat sikeres volt, és a jelszó-visszaírási funkció sikeresen le lett tiltva. |
| 31014| OffboardingEventFail| Ez az esemény azt jelzi, hogy a offboarding folyamat sikertelen volt. Ennek oka lehet egy, a konfiguráció során megadott Felhőbeli vagy helyszíni rendszergazdai fiók engedélyeinek hibája. A hiba akkor is előfordulhat, ha összevont Felhőbeli globális rendszergazdát próbál használni a jelszó visszaírási letiltásakor. A probléma megoldásához ellenőrizze a rendszergazdai jogosultságokat, és győződjön meg arról, hogy nem használ összevont fiókot a jelszó-visszaírási funkció konfigurálása során.|
| 31015| WriteBackServiceStarted| Ez az esemény azt jelzi, hogy a jelszó visszaírási szolgáltatás sikeresen elindult. Készen áll a jelszó-kezelési kérelmek fogadására a felhőből.|
| 31016| WriteBackServiceStopped| Ez az esemény azt jelzi, hogy a jelszó visszaírási szolgáltatás leállt. A Felhőbeli jelszóházirend-kérések nem lesznek sikeresek.|
| 31017| AuthTokenSuccess| Ez az esemény azt jelzi, hogy sikeresen beolvasott egy engedélyezési jogkivonatot a Azure AD Connect telepítőjének során megadott globális rendszergazda számára a offboarding vagy a bevezetési folyamat elindításához.|
| 31018| KeyPairCreationSuccess| Ez az esemény azt jelzi, hogy sikeresen létrehozta a jelszó-titkosítási kulcsot. Ezzel a kulccsal titkosíthatja a felhőben tárolt jelszavakat a helyszíni környezetbe.|
| 31034| ServiceBusListenerError| Ez az esemény azt jelzi, hogy hiba történt a bérlő Service Bus figyelőhöz való csatlakozáskor. Ha a hibaüzenet tartalmazza a "távoli tanúsítvány érvénytelen" értéket, ellenőrizze, hogy a Azure AD Connect-kiszolgáló rendelkezik-e az [Azure TLS-tanúsítvány változásai](../../security/fundamentals/tls-certificate-changes.md)című témakörben leírtak szerint az összes szükséges legfelső szintű hitelesítésszolgáltatóval. |
| 32000| UnknownError| Ez az esemény azt jelzi, hogy ismeretlen hiba történt egy jelszavas kezelési művelet során. További részletekért tekintse meg az esemény kivételének szövegét. Ha problémákat tapasztal, próbálja meg letiltani, majd újból engedélyezni a jelszó-visszaírási. Ha ez nem segít, adja meg az Eseménynapló másolatát a támogatási kérés megnyitásakor megadott követési AZONOSÍTÓval együtt.|
| 32001| ServiceError| Ez az esemény azt jelzi, hogy hiba történt a felhőalapú jelszó-átállítási szolgáltatáshoz való csatlakozás során. Ez a hiba általában akkor fordul elő, ha a helyszíni szolgáltatás nem tudott kapcsolódni a jelszó-visszaállítási webszolgáltatáshoz.|
| 32002| ServiceBusError| Ez az esemény azt jelzi, hogy hiba történt a bérlő Service Bus példányához való csatlakozáskor. Ez akkor fordulhat elő, ha blokkolja a kimenő kapcsolatokat a helyszíni környezetben. Ellenőrizze a tűzfalon, hogy engedélyezi-e a kapcsolódást a TCP 443-es és a-as porton keresztül https://ssprdedicatedsbprodncu.servicebus.windows.net , majd próbálkozzon újra. Ha továbbra is problémákat tapasztal, próbálja meg letiltani, majd újból engedélyezni a jelszó-visszaírási.|
| 32003| InPutValidationError| Ez az esemény azt jelzi, hogy a webszolgáltatási API-nak átadott adatok érvénytelenek. Próbálja megismételni a műveletet.|
| 32004| DecryptionError| Ez az esemény azt jelzi, hogy hiba történt a felhőből érkezett jelszó visszafejtése során. Ennek oka az lehet, hogy a felhőalapú szolgáltatás és a helyszíni környezet nem egyezik a visszafejtési kulccsal. A probléma megoldásához tiltsa le, majd engedélyezze újra a jelszó-visszaírási a helyszíni környezetben.|
| 32005| ConfigurationError| A bevezetési folyamat során a bérlő-specifikus információkat a helyszíni környezetben található konfigurációs fájlban menti. Ez az esemény azt jelzi, hogy hiba történt a fájl mentésekor vagy a szolgáltatás indításakor. hiba történt a fájl olvasásakor. A probléma megoldásához próbálja meg letiltani, majd újból engedélyezni a jelszó-visszaírási, hogy kényszerítse a konfigurációs fájl újraírását.|
| 32007| OnBoardingConfigUpdateError| Az előkészítés során az adatok a felhőből a helyszíni jelszó-visszaállítási szolgáltatásba küldhetők. Ezeket az adatfájlokat a rendszer egy memóriában tárolt fájlba írja, mielőtt elküldi a szinkronizálási szolgáltatásnak, hogy biztonságosan a lemezen legyen tárolva. Ez az esemény azt jelzi, hogy probléma van a memóriában tárolt adatírással vagy frissítéssel. A probléma megoldásához próbálja meg letiltani, majd újból engedélyezni a jelszó-visszaírási, hogy kényszerítse a konfigurációs fájl újraírását.|
| 32008| ValidationError| Ez az esemény azt jelzi, hogy érvénytelen választ kapott a jelszó-visszaállítási webszolgáltatás. A probléma megoldásához próbálja meg letiltani, majd újból engedélyezni a jelszó-visszaírási.|
| 32009| AuthTokenError| Ez az esemény azt jelzi, hogy nem sikerült engedélyezési jogkivonatot beolvasni a Azure AD Connect beállítása során megadott globális rendszergazdai fiókhoz. Ezt a hibát a globális rendszergazdai fiókhoz megadott rossz Felhasználónév vagy jelszó okozhatja. Ez a hiba akkor is előfordul, ha a megadott globális rendszergazdai fiók összevont. A probléma megoldásához futtassa újra a konfigurációt a megfelelő felhasználónévvel és jelszóval, és győződjön meg arról, hogy a rendszergazda felügyelt (csak felhőalapú vagy jelszóval szinkronizált) fiók.|
| 32010| CryptoError| Ez az esemény azt jelzi, hogy hiba történt a jelszó-titkosítási kulcs létrehozása vagy a felhőalapú szolgáltatástól érkező jelszó visszafejtése során. Ez a hiba valószínűleg a környezettel kapcsolatos problémát jelez. A probléma megoldásával kapcsolatos további információkért tekintse meg az Eseménynapló részleteit. Kipróbálhatja a jelszó-visszaírási szolgáltatás letiltását és újbóli engedélyezését is.|
| 32011| OnBoardingServiceError| Ez az esemény azt jelzi, hogy a helyszíni szolgáltatás nem tudott megfelelően kommunikálni a jelszó-visszaállítási webszolgáltatással a bevezetési folyamat elindításához. Ez egy tűzfalszabály miatt fordulhat elő, vagy ha probléma merül fel a bérlőhöz tartozó hitelesítési jogkivonat beszerzése során. A probléma megoldásához ellenőrizze, hogy nem blokkolja-e a kimenő kapcsolatokat a 443-as és a TCP 9350-9354-es TCP-kapcsolaton keresztül https://ssprdedicatedsbprodncu.servicebus.windows.net . Győződjön meg arról is, hogy a bevezetéshez használt Azure AD-rendszergazdai fiók nem összevont.|
| 32013| OffBoardingError| Ez az esemény azt jelzi, hogy a helyszíni szolgáltatás nem tudott megfelelően kommunikálni a jelszó-visszaállítási webszolgáltatással a offboarding folyamat elindításához. Ez egy tűzfalszabály miatt fordulhat elő, vagy ha probléma merül fel a bérlőhöz tartozó engedélyezési jogkivonat beszerzése során. A probléma megoldásához ellenőrizze, hogy nem blokkolja-e a 443-es vagy a https://ssprdedicatedsbprodncu.servicebus.windows.net -es kimenő kapcsolatokat, és hogy a regisztrációjának megszüntetésére szolgáló használt Azure Active Directory rendszergazdai fiók nem összevont-e.|
| 32014| ServiceBusWarning| Ez az esemény azt jelzi, hogy újra kellett próbálkozni a bérlő Service Bus-példányához való kapcsolódással. Normális körülmények között ez nem jelent problémát, de ha többször is látja ezt az eseményt, érdemes lehet ellenőrizni a hálózati kapcsolat Service Bus, különösen, ha nagy késéssel vagy alacsony sávszélességű kapcsolattal rendelkezik.|
| 32015| ReportServiceHealthError| A jelszó-visszaírási szolgáltatás állapotának figyeléséhez öt percenként küldünk szívverési adatait a jelszó-visszaállítási webszolgáltatásnak. Ez az esemény azt jelzi, hogy hiba történt az állapotadatok Felhőbeli webszolgáltatásba való visszaküldésekor. Ezek az állapotadatok nem tartalmaznak személyes adatokat, és csak a szívverési és alapszintű szolgáltatások statisztikái, így biztosítható a szolgáltatás állapotára vonatkozó információ a felhőben.|
| 33001| ADUnKnownError| Ez az esemény azt jelzi, hogy Active Directory által visszaadott ismeretlen hiba történt. További információért olvassa el a Azure AD Connect Server eseménynaplóját a ADSync forrás eseményeiről.|
| 33002| ADUserNotFoundError| Ez az esemény azt jelzi, hogy a helyszíni címtárban nem található a jelszó alaphelyzetbe állítására vagy módosítására irányuló felhasználó. Ez a hiba akkor fordulhat elő, ha a felhasználó törölve lett a helyszínen, de nem a felhőben. Ez a hiba akkor is előfordulhat, ha a szinkronizálási probléma merül fel. További információért olvassa el a szinkronizálási naplókat és az utolsó néhány szinkronizálási Futtatás részleteit.|
| 33003| ADMutliMatchError| Ha a jelszó-visszaállítási vagy-módosítási kérelem a felhőből származik, a Azure AD Connect telepítési folyamata során megadott Felhőbeli horgonyt használjuk annak meghatározására, hogy a kérés hogyan kapcsolható vissza a helyszíni környezetben lévő felhasználóhoz. Ez az esemény azt jelzi, hogy két felhasználót találtunk a helyszíni címtárban ugyanazzal a Cloud Anchor attribútummal. További információért olvassa el a szinkronizálási naplókat és az utolsó néhány szinkronizálási Futtatás részleteit.|
| 33004| ADPermissionsError| Ez az esemény azt jelzi, hogy a Active Directory felügyeleti ügynök (ADMA) szolgáltatásfiók nem rendelkezik a megfelelő engedélyekkel a szóban forgó fiókhoz új jelszó megadásához. Győződjön meg arról, hogy a felhasználó erdőjében található ADMA-fiók új jelszót adott vissza az erdőben lévő összes objektumra vonatkozóan. Az engedélyek beállításával kapcsolatos további tudnivalókért tekintse meg a 4. lépés: a megfelelő Active Directory engedélyek beállítása című témakört. Ez a hiba akkor is előfordulhat, ha a felhasználó attribútuma AdminCount értéke 1.|
| 33005| ADUserAccountDisabled| Ez az esemény azt jelzi, hogy kísérlet történt egy olyan fiók jelszavának alaphelyzetbe állítására vagy módosítására, amely a helyszínen le lett tiltva. Engedélyezze a fiókot, majd próbálja megismételni a műveletet.|
| 33006| ADUserAccountLockedOut| Ez az esemény azt jelzi, hogy kísérlet történt egy olyan fiók jelszavának alaphelyzetbe állítására vagy módosítására, amelyet a helyszínen zártak ki. A zárolás akkor fordulhat elő, ha egy felhasználó rövid időn belül többször is megpróbált módosítani vagy visszaállítani a jelszó-visszaállítási műveletet. Oldja fel a fiókot, majd próbálja megismételni a műveletet.|
| 33007| ADUserIncorrectPassword| Ez az esemény azt jelzi, hogy a felhasználó helytelen aktuális jelszót adott meg a jelszó-módosítási művelet végrehajtásakor. Válassza ki a megfelelő aktuális jelszót, és próbálkozzon újra.|
| 33008| ADPasswordPolicyError| Ez az esemény akkor fordul elő, ha a Password visszaírási szolgáltatás olyan jelszót próbál meg beállítani a helyi címtárban, amely nem felel meg a tartomány jelszavának életkora, előzményei, összetettsége vagy szűrési követelményeinek. <br> <br> Ha a jelszó minimális kora, és a közelmúltban módosította a jelszót az adott időkereten belül, nem tudja újra módosítani a jelszót, amíg el nem éri a megadott kort a tartományban. Tesztelési célból a minimális korhatárt 0-ra kell állítani. <br> <br> Ha engedélyezve van a jelszó-előzményekre vonatkozó követelmények, ki kell választania egy olyan jelszót, amely még nem volt használatban az utolsó *N* -időpontokban, ahol *N* a korábbi jelszavakra vonatkozó beállítás. Ha olyan jelszót választ, amelyet az utolsó *N* alkalommal használt, akkor ebben az esetben hiba jelenik meg. Tesztelési célból a korábbi jelszavakat 0-ra kell állítani. <br> <br> Ha a jelszó bonyolultságára vonatkozó követelményekkel rendelkezik, mindegyiket kényszeríti a rendszer, amikor a felhasználó megpróbál változtatni vagy alaphelyzetbe állítani egy jelszót. <br> <br> Ha engedélyezve vannak a jelszavas szűrők, és a felhasználó olyan jelszót választ, amely nem felel meg a szűrési feltételeknek, akkor az Alaphelyzetbe állítás vagy a módosítás művelet meghiúsul.|
| 33009| ADConfigurationError| Ez az esemény azt jelzi, hogy hiba történt a jelszónak a helyszíni címtárba való visszaírásakor, a Active Directory konfigurációs problémája miatt. A hiba előfordulásával kapcsolatos további információkért olvassa el a Azure AD Connect gép alkalmazás-eseménynaplójában található üzeneteket a ADSync szolgáltatásból.|

## <a name="azure-ad-forums"></a>Azure AD-fórumok

Ha általános kérdései vannak az Azure AD-vel és az önkiszolgáló jelszó-visszaállítással kapcsolatban, kérheti a Közösség segítségét a [Microsoft Q&a Azure Active Directory kérdéseit tartalmazó oldalt](/answers/topics/azure-active-directory.html). A Közösség tagjai közé tartoznak a mérnökök, a termékmenedzsment, a MVP és az informatikai szakemberek.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

Ha nem találja a választ a problémára, a támogatási csapatunk mindig szívesen segít Önnek.

A megfelelő segítség érdekében kérjük, hogy az esetek megnyitásakor a lehető legrészletesebben adja meg az adatokat. Ezek az adatok a következőket tartalmazzák:

* **A hiba általános leírása**: mi a hiba? Mi volt az észlelt viselkedés? Hogyan lehet reprodukálni a hibát? A lehető legrészletesebben adja meg az adatokat.
* **Page**: milyen oldalon voltál a hiba? Adja meg az URL-címet, ha tudja, és képernyőképet is tartalmaz az oldalról.
* **Támogatási kód**: mi volt a támogatási kód, amely akkor jött létre, amikor a felhasználó meglátta a hibát?
   * A kód megkereséséhez reprodukálja a hibát, majd válassza a képernyő alján található **támogatási kód** hivatkozást, majd küldje el az eredményeket tartalmazó GUID-t a támogatási szakembernek.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="A támogatási kód a webböngésző ablakának jobb alsó sarkában található.":::

  * Ha egy oldalon nem található támogatási kód, válassza az F12 lehetőséget, és keresse meg a SID és a CID kifejezést, és küldje el a két eredményt a támogatási szakembernek.
* **Dátum, idő és időzóna**: adja meg a pontos dátumot és időt a hiba előfordulási *időzónájában* .
* **Felhasználói azonosító**: ki volt a felhasználó, aki látta a hibát? Ilyen például a *felhasználói \@ contoso.com*.
   * Ez egy összevont felhasználó?
   * Ez egy áteresztő hitelesítési felhasználó?
   * Ez egy jelszó-kivonattal szinkronizált felhasználó?
   * Ez egy csak felhőalapú felhasználó?
* **Licencelés**: van-e hozzárendelve a felhasználó Azure ad-licenccel?
* **Alkalmazás-eseménynapló**: ha jelszó-visszaírási használ, és a hiba a helyszíni infrastruktúrában található, az alkalmazás eseménynaplójának tömörített másolatát adja meg az Azure ad Connect-kiszolgálóról.

## <a name="next-steps"></a>Következő lépések

További információ a SSPR-ről [: Hogyan működik az Azure ad önkiszolgáló jelszó-visszaállítás](concept-sspr-howitworks.md) , vagy [Hogyan működik az önkiszolgáló jelszó-visszaállítási VISSZAÍRÁSI az Azure ad-ben?](concept-sspr-writeback.md).