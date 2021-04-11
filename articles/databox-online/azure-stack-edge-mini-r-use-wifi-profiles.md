---
title: Wi-Fi profilok használata Azure Stack Edge mini R-eszközökkel
description: Ismerteti, hogyan hozhat létre Wi-Fi profilokat Azure Stack Edge mini R-eszközökhöz nagy biztonságú vállalati hálózatokon és személyes hálózatokon.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050482"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Wi-Fi profilok használata Azure Stack Edge mini R-eszközökkel

Ez a cikk azt ismerteti, hogyan használható a vezeték nélküli hálózati (Wi-Fi) profilok a Azure Stack Edge mini R-eszközökkel.

A Wi-Fi profil előkészítésének módja a vezeték nélküli hálózat típusától függ:

- A Wi-Fi Protected Access 2 (WPA2)-Personal Network (például otthoni hálózat vagy Wi-Fi megnyitási pont) esetében előfordulhat, hogy egy meglévő vezeték nélküli profilt is le kell töltenie és használnia kell ugyanazzal a jelszóval, amelyet más eszközökkel is használ.

- Magas biztonsági szintű vállalati környezetben a rendszer a WPA2-Enterprise hálózaton keresztül fér hozzá az eszközhöz. Az ilyen típusú hálózatokon minden ügyfélszámítógép külön Wi-Fi profillal fog rendelkezni, és tanúsítványokon keresztül hitelesítve lesz. A szükséges konfiguráció meghatározásához működnie kell a hálózati rendszergazdával.

A profilra vonatkozó követelményeket később is tárgyaljuk mindkét típusú hálózatra.

Mindkét esetben nagyon fontos, hogy ellenőrizze, hogy a profil megfelel-e a szervezet biztonsági követelményeinek, mielőtt teszteli vagy használja a profilt az eszközzel.

## <a name="about-wi-fi-profiles"></a>Tudnivalók a Wi-Fi profiljairól

A Wi-Fi profil tartalmazza az SSID-t (a szolgáltatáskészlet azonosítóját vagy a **hálózat nevét**), a jelszó kulcsát és a Azure stack Edge mini R-eszköz vezeték nélküli hálózathoz való csatlakoztatásához szükséges biztonsági információkat.

Az alábbi kódrészlet egy tipikus vezeték nélküli hálózattal használható profil alapszintű beállításait mutatja be:

* `SSID` a hálózat neve.

* `name` a Wi-Fi-kapcsolatok felhasználóbarát neve. Ez a név a felhasználók számára jelenik meg, amikor megkeresik az elérhető kapcsolatokat az eszközön.

* A profil úgy van konfigurálva, hogy automatikusan összekösse a számítógépet a vezeték nélküli hálózattal, ha az a hálózat () tartományán belül van `connectionMode`  =  `auto` .

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

További információ a Wi-Fi profil beállításairól: a [Windows 10 és újabb rendszerű eszközök Wi-Fi beállításainak hozzáadása](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)a **vállalati profilban** , valamint a [Cisco Wi-Fi-profil konfigurálása](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Ha a vezeték nélküli kapcsolatokat egy Azure Stack Edge mini R-eszközön szeretné engedélyezni, konfigurálja a Wi-Fi portot az eszközön, majd adja hozzá a Wi-Fi profilt (ka) t az eszközhöz. Vállalati hálózatokon a tanúsítványokat is feltöltheti az eszközre. Ezután csatlakozhat egy vezeték nélküli hálózathoz az eszköz helyi webes FELÜLETén. További információ: a [vezeték nélküli kapcsolat kezelése az Azure stack Edge mini R](./azure-stack-edge-mini-r-manage-wifi.md)-ben.

## <a name="profile-for-wpa2---personal-network"></a>A WPA2-Personal Network profilja

A Wi-Fi Protected Access 2 (WPA2)-Personal Network (például otthoni hálózat vagy Wi-Fi nyitott hozzáférési pont) esetén több eszköz is használhatja ugyanazt a profilt és jelszót. Az otthoni hálózaton a mobiltelefon és a laptop ugyanazt a vezeték nélküli profilt és jelszót használja a hálózathoz való kapcsolódáshoz.

Egy Windows 10 rendszerű ügyfél például létrehozhat egy futásidejű profilt. Amikor bejelentkezik a vezeték nélküli hálózatba, a rendszer kéri a Wi-Fi jelszavát, és ha megadja ezt a jelszót, csatlakozik. Ebben a környezetben nem szükséges tanúsítvány.

Ilyen típusú hálózat esetén előfordulhat, hogy a laptopról exportálhat egy Wi-Fi profilt, majd hozzáadhatja az Azure Stack Edge mini R-eszközhöz. Útmutatásért lásd az alábbi [Wi-Fi-profil exportálása](#export-a-wi-fi-profile)című szakaszt.

> [!IMPORTANT]
> Mielőtt létrehoz egy Wi-Fi profilt az Azure Stack Edge mini R-eszközhöz, forduljon a hálózati rendszergazdához, és keresse meg a szervezet biztonsági követelményeit a vezeték nélküli hálózatkezeléshez. Ha nem tudja, hogy a vezeték nélküli hálózat megfelel a követelményeknek, ne tesztelje vagy ne használjon Wi-Fi profilt az eszközön.

## <a name="profiles-for-wpa2---enterprise-network"></a>A WPA2-Enterprise hálózat profiljai

A vezeték nélküli védett hozzáférés 2 (WPA2)-Enterprise hálózaton a hálózati rendszergazdával kell együttműködni a szükséges Wi-Fi profil és tanúsítvány beszerzéséhez, hogy az Azure Stack Edge mini R-eszközt a hálózathoz lehessen kapcsolni.

A fokozottan biztonságos hálózatok esetében az Azure-eszköz a PEAP protokollt is használhatja bővíthető hitelesítéssel Protocol-Transport Layer Security (EAP-TLS) használatával. A PEAP és az EAP-TLS számítógép-hitelesítést használ: az ügyfél és a kiszolgáló tanúsítványokat használ az identitásuk ellenőrzéséhez.

> [!NOTE]
> * A PEAP Microsoft Challenge Handshake Authentication Protocol 2-es verziójú (PEAP MSCHAPv2) használatával történő felhasználói hitelesítés nem támogatott Azure Stack Edge mini R-eszközökön.
> * Az EAP-TLS hitelesítés szükséges ahhoz, hogy hozzáférjen Azure Stack Edge mini R-funkcióihoz. A Active Directory használatával beállított vezeték nélküli kapcsolat nem fog működni.

A hálózati rendszergazda egyedi Wi-Fi profilt és ügyféltanúsítványt hoz majd az egyes számítógépekhez. A hálózati rendszergazda eldönti, hogy minden eszközhöz vagy megosztott tanúsítványhoz külön tanúsítványt kíván-e használni.

Ha a munkahelyen több fizikai helyen dolgozik, előfordulhat, hogy a hálózati rendszergazdának egynél több helyspecifikus Wi-Fi profilt és tanúsítványt kell megadnia a vezeték nélküli kapcsolatokhoz.

Vállalati hálózaton azt javasoljuk, hogy ne változtassa meg a hálózati rendszergazda által biztosított Wi-Fi-profilok beállításait. Csak az automatikus kapcsolódási beállításokra lehet szükség. További információ: [alapszintű profil](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) Wi-Fi beállítások Windows 10 és újabb rendszerű eszközökhöz.

Magas biztonsági szintű vállalati környezetben a meglévő vezeték nélküli hálózati profilt sablonként is használhatja:

* A vállalati vezeték nélküli hálózati profilt a munkahelyi számítógépről is letöltheti. Útmutatásért lásd az alábbi [Wi-Fi-profil exportálása](#export-a-wi-fi-profile)című szakaszt.

* Ha a szervezet többi tagja már csatlakozik a Azure Stack Edge mini R-eszközökhöz vezeték nélküli hálózaton keresztül, letöltheti a Wi-Fi profilt az eszközéről. Útmutatásért lásd: [Wi-Fi profil letöltése](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Wi-Fi-profil exportálása

A számítógép Wi-Fi felületének profiljának exportálásához hajtsa végre a következő lépéseket:

1. A számítógép vezeték nélküli profiljainak megtekintéséhez a **Start** menüben nyissa meg a **parancssort** (cmd.exe), és írja be a következő parancsot:

   `netsh wlan show profiles`

   A kimenet a következőhöz hasonlóan fog kinézni:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. A profil exportálásához írja be a következő parancsot:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   A következő parancs például XML formátumban menti a ContosoFTINET-profilt a nevű felhasználó letöltések mappájába `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Tanúsítvány hozzáadása, Wi-Fi profil az eszközhöz

Ha rendelkezik a szükséges Wi-Fi profilokkal és tanúsítványokkal, hajtsa végre az alábbi lépéseket a Azure Stack Edge mini R-eszköz vezeték nélküli kapcsolatokhoz való konfigurálásához:

1. A WPA2-Enterprise hálózat esetében töltse fel a szükséges tanúsítványokat az eszközre a [tanúsítványok feltöltése](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates)című témakör útmutatását követve.

1. Töltse fel a Wi-Fi profil (oka) t a mini R-eszközre, majd kapcsolódjon hozzá a [Hozzáadás, kapcsolódás Wi-Fi profilhoz című](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile)témakör útmutatását követve.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [konfigurálhatja a hálózatot Azure stack Edge mini R-hez](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Megtudhatja, hogyan [kezelheti Wi-Fi az Azure stack Edge mini R](azure-stack-edge-mini-r-manage-wifi.md)-ben.
