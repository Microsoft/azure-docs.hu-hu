---
title: Hozzáférés engedélyezése közös hozzáférésű aláírással az Azure-ban Event Hubs
description: Ez a cikk az Azure Event Hubs-erőforrásokhoz való hozzáférés megosztott hozzáférési aláírásokkal (SAS) történő engedélyezésével kapcsolatos információkat tartalmaz.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 6a2d7385f82864e8d378055333377fb9c3f73c19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85323114"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>A Event Hubs-erőforrásokhoz való hozzáférés engedélyezése közös hozzáférési aláírások használatával
A közös hozzáférésű aláírás (SAS) lehetővé teszi, hogy korlátozott hozzáférést biztosítson a Event Hubs-névtér erőforrásaihoz. Az SAS az engedélyezési szabályok alapján Event Hubs erőforrásokhoz való hozzáférést. Ezek a szabályok névtérben vagy entitáson (Event hub vagy témakör) vannak konfigurálva. Ez a cikk áttekintést nyújt az SAS-modellről, és a SAS ajánlott eljárásait ismerteti.

## <a name="what-are-shared-access-signatures"></a>Mik azok a közös hozzáférési aláírások?
A közös hozzáférésű aláírás (SAS) delegált hozzáférést biztosít a Event Hubs erőforrásokhoz az engedélyezési szabályok alapján. Az engedélyezési szabályok névvel rendelkeznek, bizonyos jogokkal vannak társítva, és egy pár titkosítási kulcsot is végrehajt. SAS-tokenek létrehozásához használja a szabály nevét és kulcsát a Event Hubs ügyfeleken vagy a saját kódjában. Az ügyfél ezután átadhatja a jogkivonatot a Event Hubsnak, hogy igazolja a kért művelet engedélyezését.

Az SAS egy egyszerű tokeneket használó jogcím-alapú engedélyezési mechanizmus. A SAS használatával a kulcsok soha nem lesznek átadva a huzalon. A kulcsok a szolgáltatás által később ellenőrizhető információk titkosítására szolgálnak. Az SAS a Felhasználónév és a jelszó sémához hasonló módon használható, ahol az ügyfél azonnal rendelkezik egy engedélyezési szabály nevével és egy hozzá tartozó kulccsal. Az SAS az összevont biztonsági modellhez hasonlóan használható, ahol az ügyfél időkorlátot és aláírt hozzáférési jogkivonatot kap a biztonsági jogkivonat szolgáltatástól anélkül, hogy az aláírási kulcs birtokában lenne.

> [!NOTE]
> Az Azure Event Hubs támogatja az Azure Active Directory (Azure AD) használatával Event Hubs erőforrások engedélyezését. Az Azure AD által visszaadott OAuth 2,0 tokent használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a közös hozzáférésű aláírások (SAS) számára. Az Azure AD-ben nincs szükség a jogkivonatok tárolására a kódban, és kockázatos biztonsági réseket.
>
> A Microsoft azt javasolja, hogy ha lehetséges, az Azure AD-t az Azure Event Hubs alkalmazásaival együtt használja. További információ: az [Azure Event Hubs-erőforráshoz való hozzáférés engedélyezése Azure Active Directory használatával](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Az SAS (közös hozzáférésű aláírások) tokenek kritikus fontosságúak az erőforrások megóvása érdekében. A részletesség biztosítása mellett az SAS hozzáférést biztosít az ügyfeleknek a Event Hubs erőforrásaihoz. A felhasználók nem oszthatók meg nyilvánosan. Ha a megosztásra szükség van a hibaelhárításhoz, érdemes lehet a naplófájlok csökkentett verzióját használni, vagy törölni az SAS-jogkivonatokat (ha vannak) a naplófájlokban, és győződjön meg arról, hogy a képernyőképek nem tartalmazzák az SAS-adatokat sem.

## <a name="shared-access-authorization-policies"></a>Megosztott hozzáférés engedélyezési házirendjei
Minden Event Hubs névtérnek és minden Event Hubs entitásnak (egy Event hub-példánynak vagy egy Kafka-témakörnek) közös hozzáférési engedélyezési szabályzata van. A névtér szintjén lévő szabályzat a névtéren belüli összes entitásra vonatkozik, függetlenül az egyes házirend-konfigurációtól.
Minden egyes engedélyezési házirend-szabályhoz három információt kell eldöntenie: név, hatókör és jogosultságok. A név a hatókörben található egyedi név. A hatókör a kérdéses erőforrás URI-ja. Event Hubs névtér esetében a hatókör a teljes tartománynév (FQDN), például: `https://<yournamespace>.servicebus.windows.net/` .

A házirend-szabály által biztosított jogok a következőket foglalhatják magukban:
- **Küldés** – megadja a jogot, hogy üzeneteket küldjön az entitásnak
- **Figyelés** – feljogosítja az entitás figyelésére vagy fogadására
- **Kezelés** – jogot biztosít a névtér topológiájának kezelésére, beleértve az entitások létrehozását és törlését.

> [!NOTE]
> A **kezelés** jogosultság magában foglalja a **küldési** és **figyelési** jogosultságokat.

A névtér vagy az entitások házirendje legfeljebb 12 megosztott hozzáférési engedélyezési szabályt tud tárolni, amelyek a három szabály számára biztosítanak helyet, amelyek mindegyike rendelkezik az alapszintű jogokkal, valamint a Küldés és a figyelés kombinációjával. Ez a korlát azt hangsúlyozza, hogy a SAS-szabályzat tárolója nem a felhasználó-vagy szolgáltatásfiók-tároló. Ha az alkalmazásnak hozzáférést kell biztosítania Event Hubs erőforrásokhoz felhasználói vagy szolgáltatásbeli identitások alapján, akkor olyan biztonsági jogkivonat-szolgáltatást kell létrehoznia, amely a hitelesítés és a hozzáférés-ellenőrzések után az SAS-jogkivonatokat bocsát ki.

Az engedélyezési szabályokhoz egy **elsődleges kulcs** és egy **másodlagos kulcs** van rendelve. Ezek a kulcsok titkosítási szempontból erős kulcsok. Ne veszítse el vagy ne szivárogjon. Mindig a Azure Portal lesznek elérhetők. A generált kulcsok bármelyikét használhatja, és bármikor újragenerálhatja őket. Ha újra létrehoz vagy módosít egy kulcsot a házirendben, az adott kulcson alapuló, korábban kiadott jogkivonatok azonnal érvénytelenné válnak. Az ilyen jogkivonatok alapján létrehozott folyamatban lévő kapcsolatok azonban továbbra is működni fognak, amíg a jogkivonat le nem jár.

Event Hubs névtér létrehozásakor a rendszer automatikusan létrehoz egy **RootManageSharedAccessKey** nevű házirend-szabályt a névtérhez. Ez a szabályzat a teljes névtérre vonatkozó engedélyeket **kezeli** . Javasoljuk, hogy ezt a szabályt rendszergazdai főfiókként kezelje, és ne használja az alkalmazásban. A portálon, a PowerShell vagy az Azure CLI használatával további szabályzatokat hozhat létre a névtér **configure (Konfigurálás** ) lapján.

## <a name="best-practices-when-using-sas"></a>A SAS használatával kapcsolatos ajánlott eljárások
Ha közös hozzáférési aláírásokat használ az alkalmazásokban, a következő két lehetséges kockázattal kell tisztában lennie:

- Ha egy SAS kiszivárgott, azt bárki felhasználhatja, aki beolvassa azt, ami potenciálisan veszélyeztetheti a Event Hubs erőforrásait.
- Ha egy ügyfélalkalmazás számára megadott SAS lejár, és az alkalmazás nem tud beolvasni egy új SAS-t a szolgáltatásból, akkor az alkalmazás funkciói akadályozva lehetnek.

A közös hozzáférési aláírások használatára vonatkozó alábbi javaslatok segíthetnek a kockázatok enyhítésében:

- **Ha szükséges, az ügyfelek automatikusan megújítják az SAS**-t, ha szükséges: az ügyfeleknek a lejárat előtt is meg kell újítaniuk a sas-t, hogy az újrapróbálkozások ideje ne legyen elérhető. Ha a SAS-t kis számú azonnali, rövid életű művelethez kívánja használni, amelyeket a lejárati időszakon belül el kell végezni, akkor előfordulhat, hogy az SAS-t nem kell megújítani. Ha azonban olyan ügyfele van, amely az SAS-n keresztül rutinul kéri a kérelmeket, akkor a lejárati lehetőség a lejátszásra kerül. A legfontosabb szempont, hogy az SAS-nek rövid életűnek kell lennie (ahogy azt korábban már említettük) annak biztosításához, hogy az ügyfél kellő időben megújítsa a megújítást (a sikeres megújítás előtt lejáró SAS miatti megszakítás elkerülése érdekében).
- Ügyeljen **a sas indítási időpontjára**: Ha **most** beállítja az SAS kezdési idejét, majd az óra eldöntése miatt (a különböző gépektől függően a jelenlegi idő eltérése), a hibák időnként megfigyelhetők az első pár percben. Általában úgy állítsa be a kezdési időpontot, hogy legalább 15 perccel korábbi legyen. Vagy ne állítsa be egyáltalán, hogy minden esetben azonnal érvényes lesz. Ugyanez általában a lejárati időre is vonatkozik. Ne feledje, hogy akár 15 perces órajelet is megvizsgálhat a kérések bármelyik irányában. 
- **Legyen egyedi az elérni kívánt erőforrással**: biztonsági szempontból ajánlott a minimálisan szükséges jogosultságokat biztosítani a felhasználónak. Ha egy felhasználónak csak olvasási hozzáférésre van szüksége egyetlen entitáshoz, akkor olvasási hozzáféréssel kell rendelkeznie az adott entitáshoz, és nem kell olvasási/írási/törlési hozzáférést adni az összes entitáshoz. Emellett segít csökkenteni a károkat abban az esetben, ha egy SAS biztonsága sérül, mert az SAS a támadó kezében kevesebb árammal rendelkezik.
- **Ne mindig az SAS-t használja**: időnként az adott művelethez kapcsolódó kockázatok meghaladják az SAS előnyeit Event Hubs. Ilyen műveletekhez hozzon létre egy olyan középszintű szolgáltatást, amely az üzleti szabályok érvényesítése, hitelesítése és naplózása után a Event Hubs ír.
- **Mindig https használata**: sas létrehozása vagy terjesztése mindig HTTPS használatával. Ha a SAS átadása HTTP-n keresztül történik, és a rendszer elvégzi a lehallgatást, az ember által a középső csatolást végző támadó elolvashatja az SAS-t, majd ugyanúgy használhatja azt, mint amennyire a kívánt felhasználó rendelkezhet, esetleg veszélyezteti a bizalmas adatokat, vagy engedélyezheti az adatsérülést a rosszindulatú felhasználó számára.

## <a name="conclusion"></a>Összegzés
A hozzáférési aláírások megosztása hasznos lehet a korlátozott engedélyek biztosításához, hogy Event Hubs erőforrásokat az ügyfeleknek. Az Azure Event Hubst használó alkalmazások biztonsági modelljének létfontosságú részét képezik. Ha követi az ebben a cikkben felsorolt ajánlott eljárásokat, az SAS használatával nagyobb rugalmasságot biztosíthat az erőforrásokhoz való hozzáféréshez, az alkalmazás biztonságának veszélyeztetése nélkül.

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő kapcsolódó cikkeket: 

- [Kérelmek hitelesítése az Azure Event Hubs alkalmazásból Azure Active Directory használatával](authenticate-application.md)
- [Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md)
- [Kérelmek hitelesítése az Azure Event Hubs megosztott hozzáférési aláírások használatával](authenticate-shared-access-signature.md)
- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](authorize-access-azure-active-directory.md)


