---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ee92e145acd981bb8c84b43986b90306b994c262
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554332"
---
#### <a name="to-configure-and-register-the-device"></a>Az eszköz konfigurálása és regisztrálása

1. Nyissa meg a Windows PowerShell felületet a StorSimple-eszköz soros konzoljában. Az erre vonatkozó utasítások [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console) című szakaszban találhatók. **Pontosan kövesse az ismertetett eljárást, különben nem fog tudni hozzáférni a konzolhoz.**

2. A megnyitott munkamenetben nyomja le egyszer az **Enter** billentyűt a parancssor megjelenítéséhez.

3. Ekkor a rendszer arra kéri, hogy válassza ki az eszközhöz beállítani kívánt nyelvet. Válassza ki a nyelvet, majd nyomja le az **Enter** billentyűt.

4. A soros konzol megjelenő menüjében válassza az 1. lehetőséget a **teljes körű hozzáféréssel való bejelentkezéshez**.
     Végezze le az 5–12. lépést az eszköz minimálisan szükséges hálózati beállításainak konfigurálásához. **Ezeket a konfigurálási lépéseket az eszköz aktív vezérlőjén kell elvégezni.** A vezérlő állapota a soros konzol menüjének címsorában látható. Ha nincs kapcsolat az aktív vezérlővel, akkor bontsa a kapcsolatot, és csatlakozzon újra az aktív vezérlőhöz.

5. Írja be a jelszót a parancssorba. Az eszköz alapértelmezett jelszava: **Password1**.

6. Írja be a következő parancsot: `Invoke-HcsSetupWizard`.

7. Ekkor megjelenik egy telepítővarázsló az eszköz hálózati beállításainak konfigurálásához. Adja meg az alábbi adatokat:
   
   * A DATA 0 hálózati adapter IP-címe
   * Alhálózati maszk
   * Átjáró
   * Az elsődleges DNS-kiszolgáló IP-címe

   Az alábbiakban egy példa látható a kimenetre.

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    Az előző kimeneti példánál megfigyelhető, hogy a rendszer a folyamat minden lépése után érvényesíti a hálózati beállításokat.

     > [!NOTE]
     > Az alhálózati maszk és a DNS-beállítások alkalmazása néhány percig is eltarthat. Ha az alábbi hibaüzenetet kapja: „Ellenőrizze a Data 0 hálózati kapcsolatát”, akkor ellenőrizze a fizikai hálózati kapcsolatot az aktív vezérlő DATA 0 hálózati adapterén.

8. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, **vegye figyelembe, hogy ha webproxyt használ, azt csak itt tudja beállítani**. További információ: [Configure web proxy for your device](../articles/storsimple/storsimple-8000-configure-web-proxy.md) (Webproxy beállítása az eszközhöz).
9. Állítson be egy elsődleges NTP-kiszolgálót az eszközhöz. Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy a felhőszolgáltatókkal hitelesítést végezhessen. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat. Ha ez nem lehetséges, akkor adjon meg egy belső NTP-kiszolgálót.

    Az alábbiakban egy példa látható a kimenetre.

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. Biztonsági okokból az eszköz rendszergazdai jelszava az első munkamenet végeztével lejár, így ezt most meg kell változtatnia. Amikor a rendszer erre kéri, adjon meg az eszközhöz egy rendszergazdai jelszót. Az eszköz rendszergazdai jelszavának 8–15 karakter hosszúságúnak kell lennie. A jelszónak az alábbiak közül hármat tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek.

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. A telepítővarázsló utolsó lépéseként regisztrálja az eszközt a StorSimple-eszközkezelő szolgáltatásban. Ehhez szükség van a 2. lépésben kapott szolgáltatásregisztrációs kulcsra. Előfordulhat, hogy a regisztrációs kulcs megadása után 2–3 percet várni kell az eszköz regisztrálására.
    
    > [!NOTE]
    > A Ctrl + C billentyűkombinációval bármikor kiléphet a varázslóból. Ha minden hálózati beállítást megadott (a Data 0 IP-címe, alhálózati maszk és átjáró), akkor a bejegyzéseket megőrzi a rendszer.
    
    Az alábbiakban egy példa látható a kimenetre.

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. Az eszköz regisztrálása után megjelenik egy szolgáltatásadat-titkosítási kulcs. Másolja ki ezt a kulcsot, és mentse egy biztonságos helyre. **Szüksége lesz erre a kulcsra a szolgáltatás regisztrációs kulcsával együtt, ha további eszközöket szeretne regisztrálni a StorSimple-eszközkezelő szolgáltatásban.** További információk a kulccsal kapcsolatban: [A StorSimple biztonsági megoldásai](../articles/storsimple/storsimple-8000-security.md).
    
    ![StorSimple-eszköz regisztrálása, 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > Ha ki szeretné másolni a szöveget a soros konzol ablakából, egyszerűen jelölje ki azt. A szöveget ezután a vágólapra vagy bármilyen szövegszerkesztőbe beillesztheti. NE használja a Ctrl + C billentyűparancsot a szolgáltatás adattitkosítási kulcsának másolásához. A Ctrl + C billentyűkombinációval kilép a varázslóból. Ennek eredményeképpen az eszköz rendszergazdai jelszava nem változik meg, és az eszköz visszaállítja az alapértelmezett jelszót.
    
13. Lépjen ki a soros konzolból.
14. Térjen vissza az Azure Portalra, és hajtsa végre a következő lépéseket:
    
    1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást.
    2. Kattintson az **Eszközök** elemre.
    3. Az eszközök táblázatos listájában tekintse meg az eszközök állapotát, és ellenőrizze, hogy az eszköz sikeresen csatlakozott-e a szolgáltatáshoz. Az eszköznek **Beállításra kész** állapotúnak kell lennie.
       
        ![A StorSimple Eszközök lapja](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        Előfordulhat, hogy várnia kell néhány percet, amíg az eszköz **Beállításra kész** állapotú nem lesz.
       
        Ha az eszköz nem jelenik meg a listában, akkor ellenőrizze, hogy a tűzfallal védett hálózat [a StorSimple-eszköz hálózati követelményeiben](../articles/storsimple/storsimple-8000-system-requirements.md) leírtak szerint van-e beállítva. Ellenőrizze, hogy a 9354-es port kimenő kommunikációra nyitva van-e, mivel az a StorSimple-eszközkezelő szolgáltatás és az eszköz közötti kommunikációt lebonyolító Service Bus működéséhez szükséges.