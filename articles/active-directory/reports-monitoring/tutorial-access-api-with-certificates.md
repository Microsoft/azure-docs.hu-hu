---
title: Oktatóanyag az AD Reporting API-hoz tanúsítványokkal és | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan használhatja az Azure AD Reporting API-t tanúsítvány-hitelesítő adatokkal a könyvtárakból felhasználói beavatkozás nélkül történő adatbetekintéshez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: c4f4ac6ab3825c82ac0cb8ef5c31f9396ef7b41f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533736"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Oktatóanyag: Adatok lekérte az Azure Active Directory Reporting API és tanúsítványok használatával

Az [Azure Active Directory (Azure AD) Reporting API-k](concept-reporting-api.md) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat. Ha felhasználói beavatkozás nélkül szeretné elérni az Azure AD Reporting API-t, konfigurálnia kell a hozzáférést a tanúsítványok használatára.

Ez az oktatóanyag bemutatja, hogyan férhet hozzá az MS Graph API jelentéskészítéshez egy teszt tanúsítvánnyal. Éles környezetben nem javasoljuk a teszttanúsítványok használatát. 

## <a name="prerequisites"></a>Előfeltételek

1. A bejelentkezési adatok eléréséhez győződjön meg arról, hogy prémium Azure Active Directory (P1/P2) licenccel rendelkező bérlővel rendelkezik. A [kiadás frissítését](../fundamentals/active-directory-get-started-premium.md) prémium szintű Azure Active Directory első Azure Active Directory lásd: Getting started with prémium szintű Azure Active Directory (Ismerkedés a Azure Active Directory kiadással. Vegye figyelembe, hogy ha a frissítés előtt nem rendelkezik tevékenységadatokkal, akkor a prémium szintű licencre való frissítés után néhány napba is eltelik, hogy az adatok a jelentésekben adatokat mutassanak. 

2. Hozzon létre egy felhasználói fiókot a bérlő globális **rendszergazdai,** biztonsági rendszergazdai,  **biztonsági** olvasói vagy jelentésolvasói szerepkörében, vagy váltson rá.  

3. A [jelentéskészítési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md)szükséges Azure Active Directory meg. 

4. Töltse le és telepítse az [Azure AD PowerShell V2-t.](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)

5. Telepítse az [MSCloudIdUtils eszközt.](https://www.powershellgallery.com/packages/MSCloudIdUtils/) Ez a modul számos segédprogramként használható parancsmagot biztosít, többek között:
    - A hitelesítéshez szükséges ADAL-kódtárak
    - a felhasználó, alkalmazáskulcsok és tanúsítványok jogkivonatainak elérését az ADAL használatával,
    - a lapokra bontott eredményeket kezelő Graph API-t.

6. Ha most használja először az **Install-MSCloudIdUtilsModule** modult, akkor importálja azt az **Import-Module PowerShell-paranccsal.** A munkamenetnek a következő képernyőhöz hasonlóan kell kinéznie: ![ Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Teszt tanúsítvány létrehozásához használja a **New-SelfSignedCertificate** PowerShell-parancsmagot.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Az **Export-Certificate** parancsmag használatával exportálja azt egy tanúsítványfájlba.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával

1. Lépjen a [Azure Portal,](https://portal.azure.com)válassza **a Azure Active Directory** lehetőséget,  majd Alkalmazásregisztrációk az alkalmazást a listából. 

2. Válassza **a Tanúsítványok & titkos kulcsokhoz** **lehetőséget** az Alkalmazásregisztráció panel Kezelés szakaszában, majd válassza a **Tanúsítvány feltöltése lehetőséget.**

3. Válassza ki az előző lépésben kiválasztott tanúsítványfájlt, majd válassza a **Hozzáadás lehetőséget.** 

4. Jegyezze fel az alkalmazásazonosítót és az alkalmazáshoz éppen regisztrált tanúsítvány ujjlenyomatát. Az ujjlenyomat megkereséhez a portál alkalmazáslapján, a Kezelés szakaszban válassza a Tanúsítványok & **titkos** **kulcsok szakaszt.** Az ujjlenyomat a Tanúsítványok listában **lesz.**

5. Nyissa meg az alkalmazásjegyzéket a beágyazott jegyzékszerkesztőben, és ellenőrizze, hogy a *keyCredentials* tulajdonság frissült-e az új tanúsítvány adataival az alább látható módon – 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ``` 
6. Most lekérhet egy hozzáférési jogkivonatot az MS-Graph API ezzel a tanúsítvánnyal. Használja az **MSCloudIdUtils PowerShell-modul Get-MSCloudIdMSGraphAccessTokenFromCert** parancsmagját az alkalmazásazonosító és az előző lépésben kapott ujjlenyomat megírásával. 

   ![Képernyőkép egy PowerShell-ablakról egy olyan paranccsal, amely létrehoz egy hozzáférési jogkivonatot.](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. A PowerShell-szkript hozzáférési jogkivonatának használatával lekérdezheti a Graph API. Az **MSCloudIDUtils Invoke-MSCloudIdMSGraphQuery** parancsmagjának használatával számba veszi a signins és directoryAudits végpontot. Ez a parancsmag kezeli a többoldalas eredményeket, és elküldi azokat a PowerShell-folyamatnak.

8. A directoryAudits végpont lekérdezése az auditnaplók lekéréséhez. 

   ![Képernyőkép egy PowerShell-ablakról, amely egy paranccsal lekérdezi a directoryAudits végpontot az eljárás korábbi, hozzáférési jogkivonatával.](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. A bejelentkezési naplók lekéréséhez le kellkérdezni a bejelentkezési végpontot.

    ![Képernyőkép egy PowerShell-ablakról, amely a bejelentkezési végpont lekérdezésére vonatkozó parancsot tartalmazza az eljárás korábbi, hozzáférési jogkivonatával.](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Most már exportálhatja az adatokat egy CSV-fájlba, és mentheti egy SIEM-rendszerbe. A szkriptet be is csomagolhatja egy ütemezett feladatba az Azure AD-adatok bérlőtől való időszakos lekérésére úgy is, hogy nem kell a forráskódban tárolnia az alkalmazáskulcsokat. 

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a Reporting API-k működésével](concept-reporting-api.md)
* [Api-referencia naplózása](/graph/api/resources/directoryaudit) 
* [Bejelentkezési tevékenység jelentésének API-referenciája](/graph/api/resources/signin)