---
title: HSM által védett kulcsok létrehozása és átvitele Azure Key Vault – Azure Key Vault
description: Ez a cikk segítséget nyújt a HSM által védett kulcsok tervezésében, létrehozásában és átvitelében a Azure Key Vault. Más néven BYOK vagy saját kulcs.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: e8230404d71b0fe46e8a7cc536d2b5f3a4c5004f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588394"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>HSM által védett kulcsok importálása Key Vault (nCipher)

> [!WARNING]
> A jelen dokumentumban leírt HSM-kulcs importálási metódus elavult, és 2021. június 30. után nem lesz támogatott.  Csak az nCipher nShield HSM-családokkal működik, a 12.40.2-es vagy 12.50-es belső vezérlőprogrammal és gyorsjavítással. Erősen [ajánlott új módszerrel importálni a HSM-kulcsokat.](hsm-protected-keys-byok.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A biztonság érdekében az Azure Key Vault hardveres biztonsági modulokban (HSM-ekben) importálhat vagy hozhat létre kulcsokat, amelyek soha nem hagyják el a HSM határait. Ennek a megoldásnak a neve *saját kulcs használata*, angol betűszóval BYOK. Azure Key Vault nCipher nShield HSM-család (FIPS 140-2 2. szint érvényesítve) használatával védi a kulcsokat.


Az ebben a témakörben található információk segítségével tervezheti meg, hozhatja létre és továbbíthat saját HSM által védett kulcsokat a Azure Key Vault. 

Ez a funkció nem érhető el a Azure China 21Vianet.

> [!NOTE]
> További információ a Azure Key Vault: [Mi a Azure Key Vault?](../general/overview.md)  
> A HSM által védett kulcsok kulcstartóját tartalmazó első lépésekről a [What is Azure Key Vault? (Mi](../general/overview.md)a HSM által védett kulcsokhoz? ) Azure Key Vault.

További információ a HSM által védett kulcsok létrehozásáról és átviteléről az interneten keresztül:

* A kulcsot egy offline munkaállomásról hozza létre, ami csökkenti a támadási felületet.
* A kulcs titkosítása kulcscsere-kulccsal (KEK) történik, amely addig titkosítva marad, amíg át nem kerül a Azure Key Vault HSM-re. Csak a kulcs titkosított verziója hagyja el az eredeti munkaállomást.
* Az eszközkészlet olyan tulajdonságokat állít be a bérlőkulcson, amelyek a kulcsot a Azure Key Vault világhoz kötik. Így miután a Azure Key Vault HSM-ek megkapják és visszafejtik a kulcsot, csak ezek a HSM-ek használhatják azt. A kulcs nem exportálható. Ezt a kötést az nCipher HSM-ek kényszerítik.
* A kulcs titkosításához használt kulcscserekulcs (KEK) a HSM-Azure Key Vault jön létre, és nem exportálható. A HSM-ek biztosítják, hogy a KEK-nek nem létezhet titkosítatlan verziója a HSM-eken kívül. Emellett az eszközkészlet igazolást is tartalmaz az nCiphertől, amely szerint a KEK nem exportálható, és egy eredeti HSM-en jött létre, amelyet az nCipher készített.
* Az eszközkészlet igazolást tartalmaz az nCiphertől, hogy Azure Key Vault biztonsági világ egy eredeti, nCipher által gyártott HSM-en is létre lett hozva. Ez az igazolás igazolja, hogy a Microsoft eredeti hardvert használ.
* A Microsoft minden földrajzi régióban külön KEK-eket és különálló biztonsági világokat használ. Ez az elkülönítés biztosítja, hogy a kulcs csak abban a régióban található adatközpontokban használható, ahol titkosította. Egy európai ügyfél kulcsát például nem lehet észak-amerikai vagy ázsiai adatközpontokban használni.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>További információ az nCipher HSM-ről és Microsoft-szolgáltatások

Az nCipher Security, egy Entrust Datacard-vállalat vezető az általános célú HSM-piacon, és a bizalom, az integritás és az irányítás biztosítása által a világvezető szervezeteket teszi lehetővé az üzleti kritikus információk és alkalmazások felé. Az nCipher kriptográfiai megoldásai biztonságossá tévő új technológiákat ( felhő, IoT, blokklánc, digitális kifizetések) és segítenek az új megfelelőségi előírások betartásában ugyanazokkal a bevált technológiákkal, amelyekre a globális szervezetek napjainkban támaszkodnak a bizalmas adataikat, a hálózati kommunikációt és a vállalati infrastruktúrát érintő fenyegetések elleni védelemhez. Az nCipher megbízhatónak számít az üzleti kritikus fontosságú alkalmazások számára, biztosítja az adatok integritását, és teljes körű vezérlést biztosít az ügyfelek számára – napjainkban, holnap, mindig.

A Microsoft együttműködött az nCipher Securityvel a HSM-ek fejlett állapotának javítása érdekében. Ezeknek a fejlesztéseknek köszönhetően Ön úgy élvezheti az üzemeltetett szolgáltatások szokásos előnyeit, hogy közben nem kell feláldoznia a kulcsai felügyeletét. Egészen pontosan a fejlesztéseknek köszönhetően a Microsoft képes felügyelni a HSM-eket, hogy Önnek ne kelljen. Felhőszolgáltatásként a Azure Key Vault a szervezet használati csúcsának megfelelő méretezést biztosít. A kulcs ugyanakkor a Microsoft HSM-jein belül van védve: Ön megtartja az irányítást a kulcs életciklusa felett, mivel Ön hozza létre a kulcsot, és továbbítja azt a Microsoft HSM-ire.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Saját kulcs használatának (BYOK) megvalósítása az Azure Key Vaulthoz

A következő információkat és eljárásokat akkor használhatja, ha saját HSM által védett kulcsot fog létrehozni, majd át fogja vinni a Azure Key Vault – a saját kulcs használata (BYOK) forgatókönyvbe.

## <a name="prerequisites-for-byok"></a>A BYOK előfeltételei

A saját kulcs (BYOK) használatára vonatkozó előfeltételek listáját az alábbi táblázatban Azure Key Vault.

| Követelmény | További információ |
| --- | --- |
| Azure-előfizetés |Előfizetés létrehozásához Azure Key Vault Azure-előfizetés szükséges: [Regisztráljon az ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/) |
| A Azure Key Vault prémium szolgáltatási szint a HSM által védett kulcsok támogatásához |A szolgáltatásszintekkel és a szolgáltatás képességeivel kapcsolatos további Azure Key Vault az Azure Key Vault [webhelyén.](https://azure.microsoft.com/pricing/details/key-vault/) |
| nCipher nShield HSM-ek, intelligens kártyák és támogatási szoftverek |Hozzá kell férnie egy nCipher hardveres biztonsági modulhoz, és az nCipher nShield HSM-ekkel kapcsolatos alapvető működési ismeretekkel kell rendelkezik. A kompatibilis modellek listájáért lásd: [nCipher nShield Hardware Security Module ( nCipher nShield](https://go.ncipher.com/rs/104-QOX-775/images/nCipher_nShield_Family_Brochure.pdf?_ga=2.106120835.1607422418.1590478092-577009923.1587131206) hardveres biztonsági modul) vagy HSM vásárlása, ha még nincs ilyen. |
| A következő hardverek és szoftverek:<ol><li>Offline x64 munkaállomás, legalább Windows 7 operációs rendszerrel és nCipher nShield szoftverrel, amely legalább 11.50-es verziójú.<br/><br/>Ha ez a munkaállomás Windows 7 rendszert futtat, telepítenie kell [a Microsoft .NET Framework 4.5 keretrendszert.](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)</li><li>Olyan munkaállomás, amely csatlakozik az internethez, és legalább [](/powershell/azure/) Windows 7 operációs rendszerrel és Azure PowerShell **1.1.0-s** verzióval rendelkezik.</li><li>Usb-meghajtó vagy egyéb hordozható tárolóeszköz, amely legalább 16 MB szabad tárhellyel rendelkezik.</li></ol> |Biztonsági okokból javasoljuk, hogy az első munkaállomás ne csatlakozzon hálózathoz. Ez a javaslat azonban nem programozott módon van kikényszerülve.<br/><br/>A következő utasításokban ezt a munkaállomást leválasztott munkaállomásnak nevezzük.</p></blockquote><br/>Továbbá, ha a bérlőkulcs éles hálózathoz készült, javasoljuk, hogy használjon egy második, különálló munkaállomást az eszközkészlet letöltéséhez és a bérlőkulcs feltöltéséhez. Tesztelési célokra azonban használhatja az első munkaállomást is.<br/><br/>A következő utasításokban ezt a második munkaállomást internetkapcsolattal rendelkező munkaállomásnak nevezzük.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Kulcs létrehozása és átvitele Azure Key Vault HSM-be

A következő öt lépéssel hozza létre és továbbítja a kulcsot egy Azure Key Vault HSM-be:

* [1. lépés: Az internetkapcsolattal rendelkező munkaállomás előkészítése](#step-1-prepare-your-internet-connected-workstation)
* [2. lépés: A kapcsolat nélküli munkaállomás előkészítése](#step-2-prepare-your-disconnected-workstation)
* [3. lépés: A kulcs létrehozása](#step-3-generate-your-key)
* [4. lépés: A kulcs előkészítése az átvitelre](#step-4-prepare-your-key-for-transfer)
* [5. lépés: A kulcs átvitele az Azure Key Vaultba](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>1. lépés: Az internetkapcsolattal rendelkező munkaállomás előkészítése

Ehhez az első lépéshez tegye a következő eljárásokat az internethez csatlakozó munkaállomáson.

### <a name="step-11-install-azure-powershell"></a>1.1. lépés: A Azure PowerShell

Az internetkapcsolattal rendelkező munkaállomásról töltse le és telepítse a Azure PowerShell modult, amely tartalmazza a parancsmagokat a Azure Key Vault. A telepítési utasításokért [lásd: How to install and configure Azure PowerShell](/powershell/azure/).

### <a name="step-12-get-your-azure-subscription-id"></a>1.2. lépés: Az Azure-előfizetés azonosítójának lekért száma

Indítson Azure PowerShell munkamenetet, és jelentkezzen be Azure-fiókjába a következő paranccsal:

```Powershell
   Connect-AzAccount
```
Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Ezután használja a [Get-AzSubscription parancsot:](/powershell/module/az.accounts/get-azsubscription)

```powershell
   Get-AzSubscription
```
A kimenetben keresse meg annak az előfizetésnek az azonosítóját, Azure Key Vault. Erre az előfizetés-azonosítóra később szüksége lesz.

Ne zárja be a Azure PowerShell ablakot.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>1.3. lépés: A BYOK eszközkészlet letöltése a Azure Key Vault

A Microsoft letöltőközpontból töltse le a [Azure Key Vault BYOK eszközkészletet](https://www.microsoft.com/download/details.aspx?id=45345) az Ön földrajzi régiójához vagy Azure-példányához. A következő információk alapján azonosíthatja a letölteni és a hozzá tartozó SHA-256 csomag kivonatát:

---
**Egyesült Államok:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Európa:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Ázsia:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latin-Amerika:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D0375642F25A538922DD1B01A4FACB619

---
**Japán:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDDA05344ED136F

---
**Dél-Afrika:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**Uae:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Ausztrália:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**US Government DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Németország:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Németországi nyilvános:**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**India:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Franciaország:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Egyesült Királyság:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Svájc:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


A letöltött BYOK-eszközkészlet integritásának ellenőrzéséhez a Azure PowerShell munkamenetből használja a [Get-FileHash](/powershell/module/microsoft.powershell.utility/get-filehash) parancsmagot.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Az eszközkészlet a következőket tartalmazza:

* Kulcscserekulcs- (KEK-) csomag, amely **BYOK-KEK-pkg-kezdetű névvel rendelkezik.**
* Egy biztonságivilág-csomag, amely a **BYOK-SecurityWorld-pkg-kezdetű névvel rendelkezik.**
* Egy python-szkript **verifykeypackage.py.**
* Egy parancssori végrehajtható fájl **KeyTransferRemote.exe** és társított DLL-ekkel.
* Egy Visual C++ terjeszthető csomag, **vcredist_x64.exe.**

Másolja a csomagot egy USB-meghajtóra vagy más hordozható tárolóeszközre.

## <a name="step-2-prepare-your-disconnected-workstation"></a>2. lépés: A kapcsolat nélküli munkaállomás előkészítése

Ebben a második lépésben a következő eljárásokat kell követnie a nem hálózathoz csatlakozó munkaállomáson (sem az internethez, sem a belső hálózathoz).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>2.1. lépés: A leválasztott munkaállomás előkészítése nCipher nShield HSM-el

Telepítse az nCipher támogatási szoftvert egy Windows rendszerű számítógépre, majd csatoljon hozzá egy nCipher nShield HSM-et.

Győződjön meg arról, hogy az nCipher eszközök az elérési úton vannak (**%nfast_home%\bin**). Adja meg például a következőt:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

További információért tekintse meg az nShield HSM felhasználói útmutatóját.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>2.2. lépés: A BYOK eszközkészlet telepítése a kapcsolat nélküli munkaállomáson

Másolja át a BYOK eszközkészletcsomagot az USB-meghajtóról vagy egyéb hordozható tárolóeszközről, majd tegye a következőket:

1. Csomagolja ki a letöltött csomagban található fájlokat egy tetszőleges mappába.
2. Ebből a könyvtárból futtassa a vcredist_x64.exe fájlt.
3. Kövesse az utasításokat a Visual C++ runtime components for Visual Studio 2013 telepítéséhez.

## <a name="step-3-generate-your-key"></a>3. lépés: A kulcs létrehozása

Ebben a harmadik lépésben a következő eljárásokat kell követnie a kapcsolat nélküli munkaállomáson. A lépés befejezéséhez a HSM-nek inicializálási módban kell lennie. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>3.1. lépés: A HSM mód módosítása "I" módra

Ha nCipher nShield Edge-et használ, módosítsa a módot: 1. A Szükséges mód kiemeléséhez használja a Mode (Mód) gombot. 2. Néhány másodpercen belül tartsa lenyomva a Clear gombot. Ha a mód megváltozik, az új mód LED-ének villogása megszűnik, és begyújtva marad. Az Állapot LED néhány másodpercig szabálytalanul villoghat, majd rendszeresen villog, amikor az eszköz készen áll. Ellenkező esetben az eszköz az aktuális módban marad, és a megfelelő módú LED világítani fog.

### <a name="step-32-create-a-security-world"></a>3.2. lépés: Biztonsági világ létrehozása

Indítson el egy parancssort, és futtassa az nCipher new-world programot.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Ez a program létrehoz egy **biztonságivilág**-fájlt az %NFAST_KMDATA%\local\world címen, amely a C:\ProgramData\nCipher\Key Management Data\local mappának felel meg. A kvórumhoz különböző értékeket is használhat, de ebben a példában három üres kártyát és mindegyikhez pin-kódot kell megadnia. Ezután bármelyik két kártya teljes hozzáférést biztosít a biztonsági világhoz. Ezek a kártyák lesznek az új biztonsági világ **rendszergazdai kártyakészlete**.

> [!NOTE]
> Ha a HSM nem támogatja az újabb DLf3072s256mRijndael titkosítási csomagot, a --cipher-suite= DLf3072s256mRijndael helyett használhatja a --cipher-suite=DLf1024s160mRijndael helyére
> 
> Az nCipher new-world.exe 12.50-es verziójával létrehozott biztonsági világ nem kompatibilis ezzel a BYOK-eljárással. Két lehetőség érhető el:
> 1) Az nCipher szoftververziót a 12.40.2-es verzióra visszaminősítésével új biztonsági világot hozhat létre.
> 2) Lépjen kapcsolatba az nCipher támogatási szolgálatával, és kérje meg, hogy adja meg a 12.50 szoftververzió gyorsjavítását, amely lehetővé teszi az new-world.exe 12.40.2-es verziójának használatát, amely kompatibilis ezzel a BYOK-eljárással.

Ezután tegye a következőket:

* Készítsen biztonsági másolatot a világfájlról. Helyezze biztonságba és biztosítson védelmet a világfájl, valamint a rendszergazdai kártyák és PIN-kódjaik számára, és gondoskodjon róla, hogy senki ne férhessen hozzá egynél több kártyához.

### <a name="step-33-change-the-hsm-mode-to-o"></a>3.3. lépés: A HSM mód módosítása "O" módra

Ha nCipher nShield Edge-et használ, módosítsa a módot: 1. A Szükséges mód kiemeléséhez használja a Mode (Mód) gombot. 2. Néhány másodpercen belül nyomja le és tartsa lenyomva a Clear gombot. Ha a mód megváltozik, az új mód LED-ének villogása megszűnik, és begyújtva marad. Az Állapot LED néhány másodpercig szabálytalanul villoghat, majd rendszeresen villog, amikor az eszköz készen áll. Ellenkező esetben az eszköz az aktuális módban marad, és a megfelelő módú LED világítani fog.

### <a name="step-34-validate-the-downloaded-package"></a>3.4. lépés: A letöltött csomag ellenőrzése

Ezen lépés végrehajtása nem kötelező, de ajánlott a következők ellenőrzéséhez:

* Az eszközkészletben található kulcscserekulcs egy eredeti nCipher nShield HSM-ről lett létrehozva.
* Az eszközkészletben található biztonsági világ kivonata egy eredeti nCipher nShield HSM-ben lett létrehozva.
* A kulcscserekulcs nem exportálható.

> [!NOTE]
> A letöltött csomag ellenőrzéséhez a HSM-nek csatlakoztatva és bekapcsolva kell lennie, és egy biztonsági világgal (például az újonnan létrehozottval) kell lennie.

A letöltött csomag ellenőrzése:

1. Futtassa verifykeypackage.py szkriptet a következők egyikének beírásával a földrajzi régiótól vagy Azure-példánytól függően:

   * Észak-Amerika esetében:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
      ```
   * Európa esetében:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
      ```
   * Ázsia esetében:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
        ```
   * További Latin-Amerika:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
        ```
   * Japánban:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
        ```
   * Dél-Korea esetében:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
        ```
   * Dél-Afrika:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
        ```
   * Egyesült Arab Emírségekhez:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
        ```
   * Ausztrália esetében:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
        ```
   * A [Azure Government,](https://azure.microsoft.com/features/gov/)amely az Usa kormányzati Azure-példányát használja:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * Az Usa kormányzati dod-ja:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
        ```
   * Kanada esetében:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
        ```
   * Németország esetében:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Németországi nyilvánosak esetében:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * India esetében:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
      ```
   * Franciaország esetében:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
        ```
   * Egyesült Királyság esetében:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
        ```
   * Svájc esetében:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1
        ```

     > [!TIP]
     > Az nCipher nShield szoftver a következő pythont tartalmazza: %NFAST_HOME%\python\bin
     >
     >
2. Ellenőrizze, hogy a következő látható-e, ami sikeres érvényesítést jelez: **Eredmény: SUCCESS**

Ez a szkript ellenőrzi az aláíró láncot az nShield gyökérkulcsban. Ennek a gyökérkulcsnak a kivonata be van ágyazva a parancsfájlba, az értékének pedig a következőnek kell lennie: **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Ezt az értéket külön is megerősítheti az [nCipher webhelyen.](https://www.ncipher.com)

Most már készen áll egy új kulcs létrehozására.

### <a name="step-35-create-a-new-key"></a>3.5. lépés: Új kulcs létrehozása

Hozzon létre egy kulcsot az nCipher nShield **generatekey** program használatával.

A kulcs létrehozásához futtassa a következő parancsot:

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

A parancs futtatásakor használja a következő utasításokat:

* A *protect* paramétert a **module** értékre kell állítani, ahogyan az ábrán látható. Ez egy modul által védett kulcsot hoz létre. A BYOK eszközkészlet nem támogatja az OCS által védett kulcsokat.
* Cserélje le a *contosokey* értéket az **ident** és a **plainname** esetében bármilyen tetszőleges sztringre. Az adminisztratív terhelés minimalizálása és a hibák kockázatának csökkentése érdekében javasoljuk, hogy mindkettőnél ugyanazt az értéket használja. Az **ident érték** csak számokat, kötőjeleket és kisbetűket tartalmazhat.
* A példában a pubexp üresen maradt (alapértelmezett érték), de megadhat specifikus értékeket is. További információt az [nCipher dokumentációjában talál.](https://www.entrust.com/-/media/documentation/brochures/entrust-nshield-general-purpose-hsms-br-a4.pdf)

Ez a parancs létrehoz egy tokenizált kulcsfájlt a %NFAST_KMDATA%\local mappában egy **key_simple_-val** kezdődő névvel, amelyet a parancsban megadott **ident** követ. Például: **key_simple_contosokey**. Ez a fájl egy titkosított kulcsot tartalmaz.

Készítsen erről a tokenekre bontott kulcsfájlról egy biztonsági másolatot egy biztonságos helyre.

> [!IMPORTANT]
> Amikor később átküldi a kulcsot a Azure Key Vault, a Microsoft nem tudja visszaexportolni a kulcsot, ezért rendkívül fontos, hogy biztonságosan biztonsági adatokat és biztonsági adatokat is ellássunk róla. Lépjen [kapcsolatba az nCipherrel,](https://www.ncipher.com/about-us/contact-us) ha útmutatásra és ajánlott eljárásokra van szüksége a kulcs biztonságiolásának biztonsági írásával kapcsolatban.
>


Most már készen áll a kulcs átvitelére a Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>4. lépés: A kulcs előkészítése az átvitelre

Ebben a negyedik lépésben a következő eljárásokat kell követnie a kapcsolat nélküli munkaállomáson.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>4.1. lépés: Másolat létrehozása a kulcsról csökkentett engedélyekkel

Nyisson meg egy új parancssort, és módosítsa az aktuális könyvtárat arra a helyre, ahol kicsomagolta a BYOK zip-fájlt. A kulcsra vonatkozó engedélyek csökkentéséhez egy parancssorból futtassa a következők egyikét a földrajzi régiótól vagy Azure-példánytól függően:

* Észak-Amerika esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-
   ```
* Európa esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
   ```
* Ázsia esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
   ```
* További Latin-Amerika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
   ```
* Japánban:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
   ```
* Dél-Korea esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
   ```
* Dél-Afrika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
   ```
* Egyesült Arab Emírségekhez:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
   ```
* Ausztrália esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
   ```
* A [Azure Government,](https://azure.microsoft.com/features/gov/)amely az Usa kormányzati Azure-példányát használja:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* Az USA kormányzati dod-ja:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
   ```
* Kanada esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
   ```
* Németország esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Németországi nyilvánosak esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* India esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
   ```
* Franciaország esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
   ```
* Egyesült Királyság esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
   ```
* Svájc esetében:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1
   ```

A parancs futtatásakor cserélje le a *contosokey* értéket **a 3.5. lépés:** Új kulcs létrehozása a Kulcs létrehozása lépésből lépésben [megadott értékre.](#step-3-generate-your-key)

A rendszer arra kéri, hogy csatlakoztassa a biztonsági világ rendszergazdai kártyáit.

Amikor a parancs befejeződik, a **Result: SUCCESS (Eredmény: SIKERES)** szöveg látható, és a kulcs korlátozott engedélyekkel rendelkező másolata a következő nevű fájlban található: \<contosokey> key_xferacId_.

Az ACL-eket az nCipher nShield segédprogramokkal a következő parancsokkal lehet átlátni:

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  A parancsok futtatásakor cserélje le a contosokey értéket **a 3.5. lépés:** Új kulcs létrehozása a Kulcs létrehozása lépésből lépésben megadott [értékre.](#step-3-generate-your-key)

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>4.2. lépés: A kulcs titkosítása a Microsoft kulcscserekulcsával

Futtassa a következő parancsok egyikét az Azure földrajzi régiójától vagy példányától függően:

* Észak-Amerika esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Európa esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Ázsia esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* További Latin-Amerika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Japánban:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dél-Korea esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dél-Afrika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Egyesült Arab Emírségekhez:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Ausztrália esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* A [Azure Government,](https://azure.microsoft.com/features/gov/)amely az Usa kormányzati Azure-példányát használja:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Az USA kormányzati dod-ja:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Kanada esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Németország esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Németországi nyilvánosak esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* India esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Franciaország esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Egyesült Királyság esetében:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Svájc esetében:

  ```azurepowershell
  KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
  ```


A parancs futtatásakor használja a következő utasításokat:

* Cserélje le a *contosokey* helyére azt az azonosítót, amely a kulcs létrehozásához használt **3.5. lépés:** Új kulcs létrehozása a Kulcs [létrehozása lépésből.](#step-3-generate-your-key)
* Cserélje *le a SubscriptionID* helyére a kulcstartót tartalmazó Azure-előfizetés azonosítóját. Ezt az értéket korábban, az **1.2: Get your Azure subscription ID (1.2: Az Azure-előfizetés** azonosítójának lekérése) lépésben, az internetkapcsolattal rendelkező munkaállomás előkészítése lépésből [kérte](#step-1-prepare-your-internet-connected-workstation) le.
* Cserélje le a *ContosoFirstHSMKey* helyére a kimeneti fájl nevéhez használt címkét.

Ha ez sikeresen befejeződött, a **Result: SUCCESS** eredményt jeleníti meg, és az aktuális mappában található egy új fájl, amely a következő névvel rendelkezik: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>4.3. lépés: A kulcsátviteli csomag másolása az internetre kapcsolódó munkaállomásra

USB-meghajtóval vagy más hordozható tárolóval másolja az előző lépésből származó kimeneti fájlt (KeyTransferPackage-ContosoFirstHSMkey.byok) az internetkapcsolattal rendelkező munkaállomásra.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>5. lépés: A kulcs átvitele az Azure Key Vaultba

Ehhez az utolsó lépéshez az internetkapcsolattal rendelkező munkaállomáson az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmaggal töltse fel a kapcsolat nélküli munkaállomásról a HSM-re Azure Key Vault kulcsátviteli csomagot:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Ha a feltöltés sikeres, megjelennek az előbb hozzáadott kulcs tulajdonságai.

## <a name="next-steps"></a>Következő lépések

Most már használhatja ezt a HSM által védett kulcsot a kulcstartóban. További információért tekintse meg ezt az árat és a funkciók [összehasonlítását.](https://azure.microsoft.com/pricing/details/key-vault/)