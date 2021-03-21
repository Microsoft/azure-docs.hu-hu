---
title: StorSimple 8000 sorozatú biztonság | Microsoft Docs
description: Ismerteti azokat a biztonsági és adatvédelmi funkciókat, amelyek védik a StorSimple szolgáltatást, az eszközt és az adatokat a helyszínen és a felhőben.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 72bf252488878c8b6fabb2ea2d61658a2cd87e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966207"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple biztonság és adatvédelem

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A biztonság fontos szempont mindazok számára, akik új technológiákat fogadnak el, különösen akkor, ha a technológiát bizalmas vagy tulajdonosi adatokkal használják. A különböző technológiák kiértékelése során nagyobb kockázatokat és költségeket kell figyelembe vennie az adatvédelem terén. A Microsoft Azure StorSimple biztonsági és adatvédelmi megoldást is biztosít az adatvédelem érdekében, így biztosítva a következőket:

* **Bizalmas** – csak a jogosultsággal rendelkező entitások tekinthetik meg az adatait.
* **Integritás** – csak az illetékes entitások módosíthatják vagy törölhetik az adataikat.

A Microsoft Azure StorSimple megoldás négy fő összetevőből áll, amelyek egymással együttműködnek:

* **StorSimple Eszközkezelő a Microsoft Azure üzemeltetett szolgáltatás** – a StorSimple-eszköz konfigurálásához és üzembe helyezéséhez használt felügyeleti szolgáltatás.
* **StorSimple-eszköz** – az adatközpontba telepített fizikai eszköz. Minden olyan gazdagép és ügyfél, amely a StorSimple-eszközhöz csatlakozik, és az eszköz felügyeli az adatátvitelt, és szükség szerint áthelyezi az Azure-felhőbe.
* **Az eszközhöz csatlakozó ügyfelek/gazdagépek** – az infrastruktúrában található azon ügyfelek, amelyek a StorSimple-eszközhöz csatlakoznak, és amelyek a védeni kívánt adatszolgáltatásokat eredményezik.
* **Felhőalapú tároló** – a hely az Azure-felhőben, ahol az adatok tárolása történik.

A következő szakaszok ismertetik a StorSimple biztonsági funkcióit, amelyek segítenek az egyes összetevők és a rajtuk tárolt adataik védelmében. Emellett tartalmazza a Microsoft Azure StorSimple biztonsággal kapcsolatos kérdések listáját, valamint a hozzájuk tartozó válaszokat.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Eszközkezelő szolgáltatás védelme

A StorSimple Eszközkezelő szolgáltatás egy Microsoft Azure üzemeltetett felügyeleti szolgáltatás, amely a szervezet által beszerzett összes StorSimple kezelésére szolgál. A StorSimple Eszközkezelő szolgáltatást a szervezeti hitelesítő adataival érheti el, hogy webböngészőn keresztül jelentkezzen be a Azure Portalba.

A StorSimple Eszközkezelő szolgáltatáshoz való hozzáféréshez a szervezetnek rendelkeznie kell egy Azure-előfizetéssel, amely tartalmazza a StorSimple. Az előfizetés szabályozza, hogy az Azure Portal mely szolgáltatásai érhetők el. Ha a szervezet nem rendelkezik Azure-előfizetéssel, és többet szeretne megtudni róluk, tekintse meg [a regisztráció az Azure-ra szervezetként](../active-directory/fundamentals/sign-up-organization.md)című témakört.

Mivel a StorSimple Eszközkezelő szolgáltatás az Azure-ban fut, az Azure biztonsági funkciói védik. A Microsoft Azure által biztosított biztonsági funkciókról a [Microsoft Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/security/) talál további információt.

## <a name="storsimple-device-protection"></a>StorSimple-eszközök védelme

A StorSimple-eszköz egy helyszíni hibrid tárolóeszköz, amely SSD-ket és merevlemez-meghajtókat (HDD-ket) tartalmaz, redundáns vezérlőkkel és automatikus feladatátvételi képességekkel együtt. A vezérlők a tárolási rétegek kezelését, a jelenleg használt (vagy forró) adatok helyi tárolón való elhelyezését (a StorSimple-eszközön vagy a helyszíni kiszolgálókon) a ritkábban használt adatok felhőbe való áthelyezésével kezelik.

Csak a jogosult StorSimple-eszközök csatlakozhatnak az Azure-előfizetésében létrehozott StorSimple Eszközkezelő szolgáltatáshoz. Az eszköz engedélyezéséhez regisztrálnia kell a StorSimple Eszközkezelő szolgáltatással a szolgáltatás regisztrációs kulcsának megadásával. A szolgáltatás regisztrációs kulcsa a Azure Portal generált, 128 bites véletlenszerű kulcs.

![Szolgáltatás regisztrációs kulcsa](./media/storsimple-security/ServiceRegistrationKey.png)

A szolgáltatás regisztrációs kulcsának beszerzéséről a 2. [lépés: a szolgáltatás regisztrációs kulcsának beszerzése című témakörben](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)tájékozódhat.

A szolgáltatás regisztrációs kulcsa egy olyan hosszú kulcs, amely 100 karakterből álló karaktert tartalmaz. Másolja a kulcsot, és mentse egy szövegfájlba egy biztonságos helyen, így szükség esetén további eszközöket is engedélyezhet. Ha a szolgáltatás regisztrációs kulcsa elveszett az első eszköz regisztrálása után, létrehozhat egy új kulcsot a StorSimple Eszközkezelő szolgáltatásból. Ez nem befolyásolja a meglévő eszközök működését.

Az eszközök regisztrálását követően a tokenek használatával kommunikál a Microsoft Azureokkal. A szolgáltatás regisztrációs kulcsát a rendszer nem használja az eszköz regisztrálása után.

> [!NOTE]
> Javasoljuk, hogy minden használat után újra létrehozza a szolgáltatás regisztrációs kulcsát.


## <a name="protect-your-storsimple-solution-via-passwords"></a>A StorSimple-megoldás jelszavas védelemmel való ellátása

A jelszavak fontos részét képezik a számítógép biztonságának, és széles körben használhatók a StorSimple-megoldásban, így biztosítva, hogy az adatai csak a jogosult felhasználók számára legyenek elérhetők. A StorSimple lehetővé teszi a következő jelszavak konfigurálását:

* StorSimple-eszköz rendszergazdai jelszava
* Challenge Handshake Authentication Protocol (CHAP) kezdeményező és megcélzott jelszavak
* StorSimple Snapshot Manager jelszava

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell StorSimple-bővítménye és a StorSimple-eszköz rendszergazdai jelszava

A Windows PowerShell StorSimple-bővítménye egy parancssori felület, amely a StorSimple-eszköz felügyeletére használható. Windows PowerShell StorSimple-bővítménye rendelkezik olyan funkciókkal, amelyek lehetővé teszik az eszköz regisztrálását, a hálózati adapter konfigurálását az eszközön, bizonyos típusú frissítések telepítését, az eszköz hibakeresését a támogatási munkamenet elérésével, valamint az eszköz állapotának módosítását. Windows PowerShell StorSimple-bővítménye eléréséhez csatlakozzon az eszköz soros konzolján vagy a Windows PowerShell távelérési szolgáltatásával.

A PowerShell-távelérés HTTPS-vagy HTTP-kapcsolaton keresztül is elvégezhető. Ha engedélyezve van a Távoli felügyelet a HTTPS protokollon keresztül, akkor le kell töltenie a távfelügyeleti tanúsítványt az eszközről, és telepítenie kell a távoli ügyfélre. A PowerShell távelérési szolgáltatásával kapcsolatos további információkért nyissa meg a [távoli kapcsolódás a StorSimple-eszközhöz](storsimple-8000-remote-connect.md)című témakört.

Miután a Windows PowerShell StorSimple-bővítménye használatával csatlakozik az eszközhöz, meg kell adnia az eszköz rendszergazdai jelszavát az eszközre való bejelentkezéshez.

![Az eszköz rendszergazdai jelszava](./media/storsimple-security/DeviceAdminPW.png)

Tartsa szem előtt az alábbi ajánlott eljárásokat:

* A távfelügyelet alapértelmezés szerint ki van kapcsolva. A StorSimple Eszközkezelő szolgáltatás használatával engedélyezheti. Ajánlott biztonsági eljárásként a távelérést csak a ténylegesen szükséges időszakban kell engedélyezni.
* Ha megváltoztatja a jelszót, ügyeljen arra, hogy tájékoztassa az összes távelérési felhasználót, hogy ne tapasztaljon váratlan kapcsolati adatvesztést.
* A StorSimple Eszközkezelő szolgáltatás nem tudja beolvasni a meglévő jelszavakat: csak azokat állíthatja alaphelyzetbe. Javasoljuk, hogy az összes jelszót biztonságos helyen tárolja, hogy ne kelljen alaphelyzetbe állítani a jelszót, ha elfelejtik. Ha a jelszó alaphelyzetbe állítására van szükség, mindenképpen az Alaphelyzetbe állítás előtt értesítse az összes felhasználót.

A Windows PowerShell felületét soros kapcsolaton keresztül érheti el az eszközhöz. A távoli elérést HTTP vagy HTTPS használatával is elérheti, amely további biztonságot nyújt. A HTTPS magasabb szintű biztonságot nyújt, mint akár a soros, akár a HTTP-kapcsolat. A HTTPS használatához azonban először telepítenie kell egy olyan tanúsítványt az ügyfélszámítógépen, amely hozzáfér az eszközhöz. A távelérési tanúsítványt a StorSimple Eszközkezelő szolgáltatás eszköz konfigurációja lapjáról töltheti le. Ha a távelérési tanúsítvány elvész, le kell töltenie egy új tanúsítványt, és propagálni kell az összes olyan ügyfél számára, amely jogosult a Távoli felügyelet használatára.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Challenge Handshake Authentication Protocol (CHAP) kezdeményező és megcélzott jelszavak

A CHAP egy hitelesítési séma, amelyet a StorSimple-eszköz használ a távoli ügyfelek identitásának ellenőrzéséhez. Az ellenőrzés egy megosztott jelszón alapul. A CHAP egyirányú (egyirányú) vagy kölcsönös (kétirányú) lehet. Egyirányú CHAP esetén a cél (a StorSimple-eszköz) hitelesíti a kezdeményezőt (gazdagépet). A kölcsönös vagy fordított irányú CHAP megköveteli, hogy a cél hitelesítse a kezdeményezőt, majd a kezdeményező hitelesítse a célt. A StorSimple bármely metódus használatára konfigurálható.

A CHAP konfigurálásakor ügyeljen a következőkre:

* A CHAP-Felhasználónév 233 karakternél rövidebbnek kell lennie.
* A CHAP-jelszónak 12 és 16 karakter közöttinek kell lennie. Ha egy hosszú felhasználónevet vagy jelszót próbál használni, a rendszer hitelesítési hibát okoz a Windows-gazdagépen.
* Nem használhatja ugyanazt a jelszót a CHAP-kezdeményező és a CHAP-cél esetében is.
* A jelszó beállítása után módosítható, de nem kérhető le. Ha a jelszó módosul, ügyeljen arra, hogy minden távelérési felhasználót értesítse, hogy sikeresen csatlakozhassanak a StorSimple-eszközhöz.

A CHAP és a StorSimple-megoldás konfigurálásával kapcsolatos további információkért lépjen a [CHAP konfigurálása a StorSimple-eszközhöz](storsimple-8000-configure-chap.md)című témakörre.

### <a name="storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager jelszava

A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amely mennyiségi csoportokat és a Windows Kötet árnyékmásolata szolgáltatást használja az alkalmazással konzisztens biztonsági másolatok létrehozásához. Emellett a StorSimple Snapshot Manager használatával biztonsági mentési ütemterveket hozhat létre, illetve klónozott vagy helyreállíthatja a köteteket.

Ha a StorSimple Snapshot Manager használatára konfigurálja az eszközt, meg kell adnia a StorSimple Snapshot Manager jelszavát. A regisztráció során a rendszer először a jelszót Windows PowerShell StorSimple-bővítménye állítja be. A jelszó beállítható és módosítható a StorSimple Eszközkezelő szolgáltatásból is. Ez a jelszó hitelesíti az eszközt az StorSimple Snapshot Manager használatával.

![StorSimple Snapshot Manager jelszava](./media/storsimple-security/SnapshotMgrPassword.png)

A StorSimple Snapshot Manager jelszavának 14 és 15 karakter közöttinek kell lennie, és legalább 3 nagybetűt, kisbetűt, számot és speciális karaktert kell tartalmaznia. A StorSimple Snapshot Manager jelszavának beállítása után módosítható, de nem kérhető le. Ha megváltoztatja a jelszót, ügyeljen arra, hogy értesítse az összes távoli felhasználót.

További információ a StorSimple Snapshot Managerről: [What is StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Ajánlott eljárások a jelszavakhoz

Javasoljuk, hogy az alábbi irányelvek segítségével biztosítsa, hogy a StorSimple-jelszavak erősek és jól védettek legyenek:

* Változtassa meg a jelszavait háromhavonta. A jelszavak módosítása évente érvénybe lép.
* Használjon erős jelszavakat. További információért látogasson el [erősebb jelszavak létrehozására és a védelemre](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Mindig használjon különböző jelszavakat a különböző hozzáférési mechanizmusokhoz; a megadott jelszavak mindegyikének egyedinek kell lennie.
* Ne ossza meg a jelszavakat bárkivel, aki nem jogosult a StorSimple-eszköz elérésére.
* Ne beszéljen más jelszóról a mások előtt, vagy a jelszó formátuma.
* Ha azt gyanítja, hogy a fiók vagy a jelszó biztonsága sérült, jelentse az incidenst az informatikai részlegnek.
* Az összes jelszót bizalmasként, bizalmas információként kezelheti. 

## <a name="storsimple-data-protection"></a>StorSimple-adatvédelem

Ez a szakasz azokat a StorSimple biztonsági funkciókat ismerteti, amelyek védik az adatátviteli és tárolt adataikat.

A más szakaszokban leírtaknak megfelelően a jelszavak segítségével engedélyezheti és hitelesítheti a felhasználókat, mielőtt hozzá tudnak férni a StorSimple-megoldáshoz. Egy másik biztonsági szempont a jogosulatlan felhasználóktól érkező adatok védelme, miközben a tárolási rendszerek és a tárolás közben is átkerülnek. A következő szakaszok ismertetik a StorSimple által biztosított adatvédelmi funkciókat.

> [!NOTE]
> A deduplikálás további védelmet biztosít a StorSimple-eszközön és a Microsoft Azure tárolóban tárolt információk számára. Az adatok deduplikálása esetén az adatobjektumok a leképezéshez és a hozzáféréshez használt metaadatoktól függetlenül tárolódnak: nincs rendelkezésre álló tárolási szintű környezet, amely a kötet struktúrája, a fájlrendszer vagy a fájlnév alapján újraépíti az adatokat.


## <a name="protect-data-flowing-through-the-service"></a>A szolgáltatáson keresztül áramló forgalom védelme

A StorSimple Eszközkezelő szolgáltatás elsődleges célja a StorSimple-eszköz kezelése és konfigurálása. A StorSimple Eszközkezelő szolgáltatás Microsoft Azure fut. A Azure Portal használatával adhatja meg az eszköz konfigurációs beállításait, majd a Microsoft Azure a StorSimple Eszközkezelő szolgáltatás segítségével küldi el az adott eszközt az eszköznek. A StorSimple aszimmetrikus kulcspár rendszerét használja annak biztosítására, hogy az Azure-szolgáltatás sérülése ne veszélyeztesse a tárolt adatokat.

![Adattitkosítás a Flight szolgáltatásban](./media/storsimple-security/DataEncryption.png)

Az aszimmetrikus kulcsrendszer a következő módon segíti a szolgáltatáson keresztül átáramló adatvédelmet:

1. Az eszközön egy aszimmetrikus nyilvános és titkos kulcspárt használó adattitkosítási tanúsítvány jön létre, és az adatvédelemre szolgál. A kulcsok akkor jönnek létre, amikor az első eszköz regisztrálva van.
2. Az adattitkosítási tanúsítvány kulcsait a szolgáltatás adattitkosítási kulcsa által védett személyes információcsere (. pfx) fájlba exportálja, amely egy erős 128 bites kulcs, amelyet az első eszköz véletlenszerűen generál a regisztráció során.
3. A tanúsítvány nyilvános kulcsa biztonságosan elérhetővé válik a StorSimple Eszközkezelő szolgáltatás számára, és a titkos kulcs az eszközön marad.
4. A szolgáltatásba beérkező adatokat a rendszer a nyilvános kulccsal titkosítja, és visszafejti az eszközön tárolt titkos kulccsal, így biztosítva, hogy az Azure szolgáltatás nem tudja visszafejteni az eszközre áramló adatokat.

A szolgáltatásban tárolt adattitkosítási kulcs csak a szolgáltatásban regisztrált első eszközön jön létre. A szolgáltatásban regisztrált összes további eszköznek ugyanazt a szolgáltatási adattitkosítási kulcsot kell használnia.

> [!IMPORTANT]
> Nagyon fontos, hogy készítsen másolatot a szolgáltatásban tárolt adattitkosítási kulcsról, és mentse biztonságos helyre. A szolgáltatásban tárolt adattitkosítási kulcs másolatát úgy kell tárolni, hogy egy jogosult személy hozzáférhessen, és könnyen kommunikálni tudjon az eszköz rendszergazdájával.
> 
> Ha a szolgáltatás adattitkosítási kulcsa elvész, a Microsoft támogatási szolgálata segítséget nyújthat annak lekéréséhez, hogy legalább egy eszköz online állapotban van. Azt javasoljuk, hogy a lekérést követően módosítsa a szolgáltatás adattitkosítási kulcsát.

A szolgáltatás adattitkosítási kulcsának és a megfelelő adattitkosítási tanúsítványnak a módosításához kövesse a [StorSimple Eszközkezelő szolgáltatás adattitkosítási kulcsának módosítása című szakasz](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)lépéseit. A titkosítási kulcsok módosítása megköveteli, hogy minden eszköz frissítve legyen az új kulccsal. Ezért azt javasoljuk, hogy módosítsa a kulcsot, ha az összes eszköz online állapotban van. Ha az eszközök offline állapotban vannak, a kulcsaik más időpontban is megváltoztathatók. Az elavult kulccsal rendelkező eszközök továbbra is futtathatják a biztonsági mentéseket, de nem tudják visszaállítani az adatokat, amíg a kulcs nem frissül.

A szolgáltatásban tárolt adattitkosítási kulcs és az adattitkosítási tanúsítvány nem jár le. Azt javasoljuk azonban, hogy évente módosítsa a szolgáltatás adattitkosítási kulcsát, hogy elkerülje a kulcsfontosságú adatsérülést.

## <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

A StorSimple-eszköz a használat gyakorisága alapján helyi és Felhőbeli szinten tárolja az adatkezelést. Az eszközhöz csatlakozó összes gazdagép az eszközre küldi az adatküldést, majd szükség szerint áthelyezi az adatátvitelt a felhőbe. Az adatok átvitele az eszközről a felhőbe biztonságos módon történik az interneten keresztül. Minden eszköz egyetlen iSCSI-tárolóval rendelkezik, amely az adott eszközön lévő összes megosztott kötetet felfedi. A rendszer minden adattitkosítást elküld a Felhőbeli tárolóba. 

![Felhőbeli tárolás titkosítási kulcsa](./media/storsimple-security/CloudStorageEncryption.png)

A felhőbe áthelyezett adatok biztonságának és integritásának biztosításához a StorSimple lehetővé teszi a Felhőbeli tárolók titkosítási kulcsainak definiálását a következőképpen:

* Mennyiségi tároló létrehozásakor a Felhőbeli tároló titkosítási kulcsát kell megadnia. A kulcs később nem módosítható és nem adható hozzá.
* A kötet tárolójában lévő összes kötet ugyanazt a titkosítási kulcsot használja. Ha egy adott kötethez eltérő titkosítási formát szeretne használni, javasoljuk, hogy hozzon létre egy új mennyiségi tárolót a kötet üzemeltetéséhez.
* Amikor megadja a Felhőbeli tároló titkosítási kulcsát a StorSimple Eszközkezelő szolgáltatásban, a kulcs titkosítása a szolgáltatás adattitkosítási kulcsának nyilvános részével történik, majd a rendszer elküldi az eszköznek.
* A Felhőbeli tároló titkosítási kulcsát a szolgáltatás nem tárolja bárhol, és csak az eszköz ismeri.
* A Felhőbeli tároló titkosítási kulcsának megadása nem kötelező. A gazdagépen titkosított olyan adatküldés, amely az eszközön található.

### <a name="additional-security-best-practices"></a>További ajánlott biztonsági eljárások

* Felosztott forgalom: az iSCSI SAN-t a vállalati helyi hálózaton lévő felhasználói forgalom elkülönítésével egy teljesen elkülönített hálózat üzembe helyezésével és VLAN-ok használatával, ahol a fizikai elkülönítés nem választható. Az iSCSI-tárolók dedikált hálózata biztosítja az üzleti szempontból kritikus fontosságú adat biztonságát és teljesítményét. A tárolók és a felhasználói forgalom a vállalati helyi hálózaton keresztül történő keverése nem ajánlott, és növelheti a késést, és hálózati hibákat okozhat.
* A gazdagép-oldali hálózati biztonság esetében használja a TCP/IP-kiszervezési motort (TOE) támogató hálózati adaptereket. A TOE csökkenti a CPU-terhelést a hálózati adapter TCP protokolljának feldolgozásával.

## <a name="protect-data-via-storage-accounts"></a>Az adatvédelem a Storage-fiókok használatával

Mindegyik Microsoft Azure-előfizetés létrehozhat egy vagy több Storage-fiókot. (A Storage-fiók egy egyedi névteret biztosít az Azure-felhőben tárolt adatkezeléshez.) A Storage-fiókhoz való hozzáférést az ehhez a Storage-fiókhoz társított előfizetés és hozzáférési kulcsok vezérlik.

A Storage-fiók létrehozásakor a Microsoft Azure 2 512 bites tárterület-hozzáférési kulcsokat hoz létre, amelyek közül az egyik a hitelesítéshez használatos, amikor a StorSimple-eszköz hozzáfér a Storage-fiókhoz. Vegye figyelembe, hogy a kulcsok közül csak az egyik használatban van. A másik kulcs a tartalékban van tárolva, így rendszeresen elforgathatja a kulcsokat. A kulcsok elforgatásához hajtsa végre a másodlagos kulcs aktív értékeit, majd törölje az elsődleges kulcsot. Ezután létrehozhat egy új kulcsot, amelyet a következő forgatás során használhat fel. (Biztonsági okokból számos adatközponthoz szükséges a kulcs elforgatása.)

Javasoljuk, hogy kövesse az alábbi ajánlott eljárásokat a Key rotációhoz:

* A Storage-fiók kulcsainak rendszeres elforgatásával biztosíthatja, hogy a Storage-fiók nem érhető el jogosulatlan felhasználók számára.
* Az Azure-rendszergazdának rendszeresen módosítania kell vagy újra kell generálnia az elsődleges vagy másodlagos kulcsot a Azure Portal Storage szakaszának használatával, hogy közvetlenül hozzáférhessen a Storage-fiókhoz.

## <a name="protect-data-via-encryption"></a>Az adatvédelem titkosítás útján

A StorSimple a következő titkosítási algoritmusokat használja a StorSimple-megoldás összetevői között tárolt vagy oda irányuló adatvédelemhez.

| Algoritmus | A kulcs hossza | Protokollok/alkalmazások/megjegyzések |
| --- | --- | --- |
| RSA |2048 |Az RSA PKCS 1 v 1.5 eszközt a Azure Portal az eszközre továbbított konfigurációs adatok titkosítására használja: például a Storage-fiók hitelesítő adatait, a StorSimple és a Felhőbeli tárolás titkosítási kulcsait. |
| AES |256 |Az AES és a CBC használatával titkosíthatja a szolgáltatás adattitkosítási kulcsának nyilvános részét, mielőtt elküldi a Azure Portalnak a StorSimple-eszközről. Azt is használja, hogy a StorSimple-eszköz titkosítsa az adatvédelmet, mielőtt a rendszer elküldte az adatátvitelt a felhőalapú Storage-fiókba. |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple Cloud Appliance biztonság

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Személyes adatok kezelése

A fizikai és a virtuális adatsorozatok StorSimple Eszközkezelő a következő fő példányokban gyűjt személyes adatokat:

- A riasztás felhasználói beállításai, ha a felhasználók e-mail-címe konfigurálva van. Ezeket az információkat a rendszergazda megtekintheti és törölheti. Ez a StorSimple 8000 sorozatú eszközökre és a StorSimple virtuális tömbökre is vonatkozik.
  * A StorSimple 8000 sorozat beállításainak megtekintéséhez és törléséhez kövesse a [StorSimple-riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md#configure-alert-settings) című témakör lépéseit.
  * A StorSimple virtuális tömb beállításainak megtekintéséhez és törléséhez kövesse a [StorSimple-riasztások megtekintése és kezelése](storsimple-virtual-array-manage-alerts.md#configure-alert-settings) című témakör lépéseit.
- Azok a felhasználók, akik hozzáférhetnek a megosztásokon található adataihoz. Megjelenik a megjelenő megosztási adatelérést elérő felhasználók listája, és megtekinthető. Ezt a listát a megosztások törlésekor is törli a rendszer. Ez csak a StorSimple virtuális tömbökre vonatkozik.
  * A megosztást elérő vagy törölni kívánt felhasználó listájának megtekintéséhez kövesse a [megosztások kezelése a StorSimple virtuális tömbben](storsimple-virtual-array-manage-shares.md) című témakör lépéseit.

További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

A következő kérdések és válaszok vannak a biztonsággal és a Microsoft Azure StorSimpleokkal kapcsolatban.

**K:** A szolgáltatás biztonsága sérült. Mi a következő lépés?

**A:** Azonnal módosítania kell a szolgáltatás adattitkosítási kulcsát és a Storage-fiók kulcsait, amelyeket a rendszer a rétegek adatfeldolgozására használ. Útmutatásért keresse fel a következőt:

* [A szolgáltatásban tárolt adattitkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Storage-fiókok kulcsának rotációja](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**K:** Van egy új StorSimple-eszköz, amely a szolgáltatás regisztrációs kulcsát kéri. Hogyan beolvasni?

**A:** Ez a kulcs akkor jött létre, amikor először hozta létre a StorSimple Eszközkezelő szolgáltatást. Ha a StorSimple Eszközkezelő szolgáltatást használja az eszközhöz való csatlakozáshoz, a szolgáltatás gyors üzembe helyezés lapján megtekintheti vagy újralétrehozhatja a szolgáltatás regisztrációs kulcsát. Az új szolgáltatás-regisztrációs kulcs létrehozása nem érinti a meglévő regisztrált eszközöket. Útmutatásért keresse fel a következőt:

* [A szolgáltatás regisztrációs kulcsának megtekintése vagy újbóli előállítása](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**K:** Elveszítettem a szolgáltatás adattitkosítási kulcsát. Mit tegyek?

**A:** Lépjen kapcsolatba Microsoft ügyfélszolgálata. Bejelentkezhetnek egy támogatási munkamenetbe az eszközön, és segítséget nyújthatnak a kulcs lekéréséhez (legalább egy eszköz online állapotban van). A szolgáltatás adattitkosítási kulcsának beszerzése után azonnal meg kell változtatnia annak érdekében, hogy az új kulcs csak Ön számára legyen ismert. Útmutatásért keresse fel a következőt:

* [A szolgáltatásban tárolt adattitkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**K:**  A szolgáltatás adattitkosítási kulcsának módosítására engedélyt kapott egy eszköznek, de nem indította el a kulcs módosításának folyamatát. Mit tegyek?

**A:** Ha az időkorlát lejárt, akkor újra kell engedélyeznie az eszközt a szolgáltatás adattitkosítási kulcsának módosításához, és újra kell indítania a folyamatot.

**K:**  Módosítottam a szolgáltatás adattitkosítási kulcsát, de 4 órán belül nem sikerült frissíteni a többi eszközt. Újra kell kezdeni?

**A:** A 4 órás időszak csak a módosítás kezdeményezésére szolgál. Miután elindította a frissítési folyamatot a jogosult StorSimple-eszközön, az engedély érvényes, amíg az összes eszköz nem frissül.

**K:** A StorSimple rendszergazdája elhagyta a vállalatot. Mit tegyek?

**A:** Módosítsa és állítsa alaphelyzetbe a StorSimple eszköz elérését engedélyező jelszavakat, és módosítsa a szolgáltatás adatainak titkosítási kulcsát, hogy az új információk nem ismertek legyenek a jogosulatlan személyek számára. Útmutatásért keresse fel a következőt:

* [A StorSimple Eszközkezelő szolgáltatás használata a StorSimple jelszavának módosítására](storsimple-8000-change-passwords.md)
* [A szolgáltatásban tárolt adattitkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [A CHAP konfigurálása a StorSimple-eszközhöz](storsimple-8000-configure-chap.md)

**K:** Meg szeretném adni a StorSimple Snapshot Manager jelszót egy olyan gazdagéphez, amely csatlakozik a StorSimple-eszközhöz, de a jelszó nem érhető el. Mit tehetek?

**A:** Ha elfelejtette a jelszót, hozzon létre egy újat. Ezt követően ügyeljen arra, hogy tájékoztassa az összes meglévő felhasználót, hogy a jelszó meg lett változtatva, és hogy frissítse az ügyfeleit az új jelszó használatára. Útmutatásért keresse fel a következőt:

* [A StorSimple Snapshot Manager jelszavának módosítása](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Eszköz hitelesítése](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**K:** Az eszközön a Windows PowerShell StorSimple-bővítménye távoli elérésére vonatkozó tanúsítvány megváltozott. Hogyan frissíteni a távelérési ügyfelet?

**A:** Az új tanúsítványt letöltheti a StorSimple Eszközkezelő szolgáltatásból, majd megadhatja, hogy a a távelérési ügyfelek tanúsítványtárolójában legyen telepítve. Útmutatásért keresse fel a következőt:

* [Import-Certificate parancsmag](/powershell/module/pkiclient/import-certificate)

**K:** Védettek az adataim, ha a StorSimple Eszközkezelő szolgáltatás biztonsága sérül?

**A:** A szolgáltatás konfigurációs adatait a rendszer mindig titkosítja a nyilvános kulccsal, amikor megtekinti azt egy böngészőben. Mivel a szolgáltatás nem rendelkezik hozzáféréssel a titkos kulcshoz, a szolgáltatás nem fog tudni semmilyen információt megjeleníteni. Ha a StorSimple Eszközkezelő szolgáltatás biztonsága sérül, nincs hatása, mivel nem találhatók kulcsok a StorSimple Eszközkezelő szolgáltatásban.

**K:** Ha valaki hozzáfér az adattitkosítási tanúsítványhoz, az adataim biztonságban lesznek?

**A:** A Microsoft Azure titkosított formátumban tárolja az ügyfél adattitkosítási kulcsát (. pfx-fájl). Mivel a. pfx-fájl titkosítva van, és a StorSimple szolgáltatás nem rendelkezik a szolgáltatás adattitkosítási kulcsával a. pfx fájl visszafejtéséhez, egyszerűen csak a. pfx fájlhoz férhet hozzá.

**K:** Mi történik, ha egy kormányzati szerv megkéri a Microsofttól az adataimat?

**A:** Mivel az összes adat titkosítva van a szolgáltatásban, és a titkos kulcs az eszközön marad, a kormányzati szervnek fel kell kérnie az ügyfelet az adatra.



## <a name="next-steps"></a>Következő lépések

[Telepítse a StorSimple-eszközt](storsimple-8000-deployment-walkthrough-u2.md).