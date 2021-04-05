---
title: A vállalati biztonsági általános irányelvek az Azure HDInsight
description: Néhány ajánlott eljárás, amely megkönnyíti a Enterprise Security Package üzembe helyezését és felügyeletét.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933570"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>A vállalati biztonsági általános információk és irányelvek az Azure HDInsight

Ha biztonságos HDInsight-fürtöt helyez üzembe, néhány ajánlott eljárással egyszerűbben elvégezhető az üzembe helyezés és a fürt kezelése. Néhány általános információt és útmutatást itt talál.

## <a name="use-of-secure-cluster"></a>Biztonságos fürt használata

### <a name="recommended"></a>Ajánlott

* A fürtöt több felhasználó fogja használni egyszerre.
* A felhasználók eltérő szintű hozzáféréssel rendelkeznek ugyanahhoz az adatelérési szinthez.

### <a name="not-necessary"></a>Nem szükséges

* Csak az automatikus feladatokat (például egy felhasználói fiókot) fogja futtatni, a standard szintű fürt elég jó.
* Az adatok importálását szabványos fürt használatával végezheti el, és ugyanazt a Storage-fiókot használhatja egy másik biztonságos fürtön, ahol a felhasználók elemzési feladatokat futtathatnak.

## <a name="use-of-local-account"></a>Helyi fiók használata

* Ha megosztott felhasználói fiókot vagy helyi fiókot használ, nehéz lesz azonosítani, hogy ki használta a fiókot a konfiguráció vagy a szolgáltatás módosításához.
* A helyi fiókok használata nem jelent problémát, ha a felhasználók már nem részei a szervezetnek.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Házirendek

* Alapértelmezés szerint a Ranger a **Megtagadás** házirendet használja.

* Az adathozzáférés olyan szolgáltatáson keresztül történik, amelyen engedélyezve van az engedélyezés:
  * A rendszer meghívja a Ranger engedélyezési beépülő modulját, és a kérelem kontextusát adja meg.
  * A Ranger a szolgáltatáshoz konfigurált szabályzatokat alkalmazza. Ha a Ranger-szabályzatok sikertelenek, a hozzáférés-ellenőrzését a fájlrendszerre késlelteti a rendszer. Néhány szolgáltatás, például a MapReduce, csak azt vizsgálja, hogy a fájlt vagy mappát a kérést küldő felhasználó birtokolja-e. Olyan szolgáltatások, mint a kaptár, keresse meg a tulajdonosi egyeztetés vagy a megfelelő fájlrendszer-engedélyeket ( `rwx` ).

* A kaptár esetében, a létrehozási/frissítési/törlési engedélyekhez szükséges engedélyek mellett a felhasználónak `rwx` engedélyekkel kell rendelkeznie a tárolón és az összes alkönyvtáron.

* A szabályzatok az egyéni felhasználók helyett csoportokba (lehetőleg) is alkalmazhatók.

* A Ranger-engedélyező minden egyes kérelem esetében kiértékeli az adott szolgáltatáshoz tartozó összes Ranger-házirendet. Ez a kiértékelés hatással lehet a feladatok vagy lekérdezések elfogadásának idejére.

### <a name="storage-access"></a>Tárterület-hozzáférés

* Ha a tárolási típus WASB, akkor a rendszer nem vesz fel OAuth tokent.
* Ha a Ranger végrehajtotta az engedélyt, a tárterület-hozzáférés a felügyelt identitás használatával történik.
* Ha a Ranger nem hajtott végre semmilyen engedélyt, a tárterület-hozzáférés a felhasználó OAuth-jogkivonatával történik.

### <a name="hierarchical-name-space"></a>Hierarchikus névtér

Ha a hierarchikus névtér nincs engedélyezve:

* Nincsenek örökölt engedélyek.
* Csak a **Storage-adat XXXX** Azure-szerepkört használó fájlrendszerbeli engedélyek használhatók közvetlenül a Azure Portalhoz való hozzárendeléshez.

### <a name="default-hdfs-permissions"></a>Alapértelmezett HDFS engedélyek

* Alapértelmezés szerint a felhasználók nem férhetnek hozzá a **/** mappához a HDFS (a sikeres elérés érdekében a Storage blob tulajdonosi szerepkörben kell lenniük).
* A MapReduce és mások előkészítési könyvtárához a rendszer létrehoz egy felhasználó-specifikus könyvtárat, és megadja az `sticky _wx` engedélyeket. A felhasználók az alatta lévő fájlokat és mappákat is létrehozhatnak, de nem nézhetnek meg más elemeket.

### <a name="url-auth"></a>URL-hitelesítés

Ha engedélyezve van az URL-cím hitelesítése:

* A konfiguráció tartalmazni fogja, hogy mely előtagokat tartalmazza az URL-hitelesítés (például `adl://` ).
* Ha a hozzáférés ehhez az URL-címhez van, akkor a Ranger azt vizsgálja, hogy a felhasználó szerepel-e az engedélyezési listán.
* A Ranger nem vizsgálja meg a részletes szabályzatok egyikét sem.

## <a name="resource-groups"></a>Erőforráscsoportok

Minden fürthöz használjon egy új erőforráscsoportot, hogy a fürt erőforrásai megkülönböztethetők legyenek.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG, tűzfalak és belső átjáró

* A virtuális hálózatok zárolásához használjon hálózati biztonsági csoportokat (NSG).
* A tűzfal használatával kezelheti a kimenő hozzáférési házirendeket.
* Olyan belső átjárót használjon, amely nincs nyitva a nyilvános interneten.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása.

### <a name="policies"></a>Házirendek

* A feltételes hozzáférési szabályzat letiltása az IP-cím alapú házirend használatával. Ehhez engedélyezni kell a szolgáltatási végpontokat azon a virtuális hálózatok, amelyen a fürtök telepítve vannak. Ha olyan külső szolgáltatást használ az MFA-hoz (amely nem HRE), az IP-cím alapú házirend nem fog működni.

* `AllowCloudPasswordValidation` az összevont felhasználók házirendje szükséges. Mivel a HDInsight a felhasználónevet és a jelszót használja közvetlenül az Azure AD-ből származó jogkivonatok beszerzéséhez, ezt a házirendet minden összevont felhasználó számára engedélyezni kell.

* Engedélyezze a szolgáltatás-végpontokat, ha a megbízható IP-címek használatával feltételes hozzáférést szeretne megkerülni.

### <a name="groups"></a>Csoportok

* A fürtöket mindig egy csoporttal telepítse.
* Használja az Azure AD-t a csoporttagságok kezeléséhez (egyszerűbb, mint a fürtben lévő egyes szolgáltatások kezelése).

### <a name="user-accounts"></a>Felhasználói fiókok

* Minden forgatókönyvhöz használjon egyedi felhasználói fiókot. Használjon például egy fiókot az importáláshoz, egy másikat a lekérdezésekhez vagy más feldolgozási feladatokhoz.
* Egyéni házirendek helyett használjon csoportos Ranger-házirendeket.
* Tervezze meg, hogyan kezelheti a fürtöket többé nem rendelkező felhasználók kezeléséhez.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory tartományi szolgáltatások

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, a Lightweight Directory Access Protocol (LDAP) és a KERBEROS/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel.

Az Azure AD DS szükséges a biztonságos fürtök tartományhoz való csatlakoztatásához.
A HDInsight nem függhet a helyszíni tartományvezérlőkön vagy az egyéni tartományvezérlőkön, mert túl sok hibát mutat be, a hitelesítő adatok megosztását, a DNS-engedélyeket és így tovább. További információ: [Azure AD DS GYIK](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Azure AD DS-példány

* Hozza létre a példányt a paranccsal `.onmicrosoft.com domain` . Így nem lesz több DNS-kiszolgáló, amely a tartományt szolgálja ki.
* Hozzon létre egy önaláírt tanúsítványt az LDAPs szolgáltatáshoz, és töltse fel az Azure AD DSba.
* A fürtök üzembe helyezéséhez használjon egy összevont virtuális hálózatot (ha több csapat is telepít HDInsight ESP-fürtöket, ez hasznos lesz). Ez biztosítja, hogy a tartományvezérlővel nem kell megnyitnia a portokat (NSG) a virtuális hálózaton.
* Konfigurálja megfelelően a virtuális hálózat DNS-t (az Azure AD DS tartománynevet a gazdagépek bejegyzései nélkül kell feloldania).
* Ha korlátozza a kimenő forgalmat, ellenőrizze, hogy elolvasta-e a [tűzfal támogatását a HDInsight-ben](../hdinsight-restrict-outbound-traffic.md) .

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Az Azure AD-ből az Azure-ba szinkronizált tulajdonságok AD DS

* Az Azure AD-összekötő szinkronizál a helyszíni rendszerből az Azure AD-be.
* Az Azure AD DS szinkronizál az Azure AD-ből.

Az Azure AD DS rendszeresen szinkronizálja az objektumokat az Azure AD-ből. Az Azure AD DS panel a Azure Portal megjeleníti a szinkronizálási állapotot. A szinkronizálás egyes szakaszaiban az egyedi tulajdonságok ütköznek és átnevezve lehetnek. Ügyeljen az Azure AD-ből az Azure AD DS-ra való hozzárendelésre.

További információ: [Azure ad userPrincipalName-populáció](../../active-directory/hybrid/plan-connect-userprincipalname.md), és [Hogyan működik az Azure AD DS szinkronizálása](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Jelszó kivonatának szinkronizálása

* A jelszavak szinkronizálása más objektumtípusoktól eltérően történik. Az Azure AD-ben és az Azure-ban csak a nem visszafejthető jelszó-kivonatok vannak szinkronizálva AD DS
* Az AD-kapcsolaton keresztül engedélyezni kell a helyszíni Azure AD-t
* Az Azure AD és az Azure AD DS Sync automatikus (a késések 20 perc alatt vannak).
* A jelszó-kivonatok szinkronizálása csak akkor történik meg, ha módosult a jelszó. Ha engedélyezi a jelszó-kivonatolási szinkronizálást, a rendszer az összes meglévő jelszót automatikusan nem szinkronizálja, mert a rendszer visszafordíthatatlan módon tárolja őket. A jelszó módosításakor a jelszó-kivonatok szinkronizálva vannak.

### <a name="computer-objects-location"></a>Számítógép-objektumok helye

Minden fürt egyetlen szervezeti egységhez van társítva. Belső felhasználó van kiépítve a szervezeti egységben. Az összes csomópont ugyanahhoz a szervezeti egységhez van csatlakoztatva.

### <a name="active-directory-administrative-tools"></a>Active Directory felügyeleti eszközök

A Active Directory felügyeleti eszközök Windows Server rendszerű virtuális gépen való telepítésének lépéseiért lásd: [felügyeleti eszközök telepítése](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="cluster-creation-fails-repeatedly"></a>A fürt létrehozása nem sikerül ismételten

Leggyakoribb okok:

* A DNS-konfiguráció nem megfelelő, a fürtcsomópontok tartományhoz való csatlakoztatása meghiúsul.
* A NSG túlságosan korlátozóak, megakadályozva a tartományhoz való csatlakozást.
* A felügyelt identitás nem rendelkezik megfelelő engedélyekkel.
* A fürt neve nem egyedi az első hat karakternél (vagy egy másik élő fürttel, vagy egy törölt fürttel).

## <a name="authentication-setup-and-configuration"></a>Hitelesítés beállítása és konfigurálása

### <a name="user-principal-name-upn"></a>Egyszerű felhasználónév (UPN)

* Használjon kisbetűset az összes szolgáltatáshoz – az egyszerű felhasználónevek nem érzékenyek az ESP-fürtökben, de
* Az UPN-előtagnak egyeznie kell mindkét SAMAccountName az Azure AD-DS-ben. Nincs szükség az e-mail mezőhöz való megfeleltetésre.

### <a name="ldap-properties-in-ambari-configuration"></a>LDAP-tulajdonságok a Ambari-konfigurációban

A HDInsight-Ambari befolyásoló tulajdonságok teljes listáját lásd: [AMBARI LDAP-hitelesítés beállítása](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html).

## <a name="next-steps"></a>Következő lépések

* [Konfigurációk Enterprise Security Package Azure Active Directory Domain Services a HDInsight-ben](./apache-domain-joined-configure-using-azure-adds.md)

* [Azure Active Directory felhasználók szinkronizálása egy HDInsight-fürttel](../hdinsight-sync-aad-users-to-cluster.md).
