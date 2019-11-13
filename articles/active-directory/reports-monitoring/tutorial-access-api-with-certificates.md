---
title: Oktatóanyag az AD Reporting API-hoz tanúsítványok használatával | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogy az Azure AD Reporting API és a tanúsítvány hitelesítő adatai segítségével hogyan kérhet le adatokat a címtárakból felhasználói beavatkozás nélkül.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d723af5d994006c4ae4f90905ede73fa87326bf
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014264"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Oktatóanyag: az adatlekérdezés a Azure Active Directory Reporting API és a tanúsítványok használatával

Az [Azure Active Directory (Azure AD) Reporting API-k](concept-reporting-api.md) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat. Ha felhasználói beavatkozás nélkül szeretné elérni az Azure AD Reporting API-t, konfigurálnia kell a hozzáférést a tanúsítványok használatához.

Ebből az oktatóanyagból megtudhatja, hogyan használható egy tesztelési tanúsítvány az MS Graph API jelentésekhez való eléréséhez. Nem ajánlott éles környezetben használni a teszt tanúsítványokat. 

## <a name="prerequisites"></a>Előfeltételek

1. A bejelentkezési információ eléréséhez győződjön meg arról, hogy rendelkezik egy prémium szintű (P1/P2) licenccel rendelkező Azure Active Directory Bérlővel. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört. Vegye figyelembe, hogy ha a frissítés előtt nem rendelkezik tevékenységi adataival, a rendszer több napot is igénybe vesz, hogy az adatai megjelenjenek a jelentésekben a prémium szintű licencre való frissítés után. 

2. Hozzon létre vagy váltson át egy felhasználói fiókra a bérlő **globális rendszergazdája**, a **biztonsági rendszergazda**, a **biztonsági olvasó** vagy a **jelentéskészítő olvasó** szerepkörben. 

3. Fejezze be a [Azure Active Directory jelentési API eléréséhez szükséges előfeltételeket](howto-configure-prerequisites-for-reporting-api.md). 

4. Töltse le és telepítse az [Azure ad PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)-t.

5. Telepítse a [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Ez a modul számos segédprogramként használható parancsmagot biztosít, többek között:
    - A hitelesítéshez szükséges ADAL-kódtárak
    - a felhasználó, alkalmazáskulcsok és tanúsítványok jogkivonatainak elérését az ADAL használatával,
    - a lapokra bontott eredményeket kezelő Graph API-t.

6. Ha először használja a modult a **install-MSCloudIdUtilsModule**futtatásával, akkor az **importálási modul PowerShell-** paranccsal importálhatja azt. A munkamenetnek a következő képernyőhöz hasonlóan kell kinéznie: ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Tesztelési tanúsítvány létrehozásához használja a **New-SelfSignedCertificate PowerShell-** parancsmagot.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Az **export-Certificate** parancsmagot exportálja egy tanúsítványfájl-fájlba.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával

1. Navigáljon a [Azure Portal](https://portal.azure.com), válassza a **Azure Active Directory**lehetőséget, majd válassza a **Alkalmazásregisztrációk** lehetőséget, és válassza ki az alkalmazást a listából. 

2. Válassza a **beállítások** > **kulcsok** lehetőséget, majd válassza a **nyilvános kulcs feltöltése**lehetőséget.

3. Válassza ki az előző lépésben megadott tanúsítványfájl, majd válassza a **Mentés**lehetőséget. 

4. Jegyezze fel az alkalmazás AZONOSÍTÓját és az imént regisztrált tanúsítvány ujjlenyomatát. Az ujjlenyomat megtalálásához a portál alkalmazás lapján lépjen a **Beállítások** lapra, és kattintson a **kulcsok**elemre. Az ujjlenyomat a **nyilvános kulcsok** listájában jelenik meg.

5. Nyissa meg az alkalmazás jegyzékfájlját a beágyazott jegyzékfájl-szerkesztőben, és cserélje le a *hitelesítő adatok* tulajdonságot az új tanúsítvány adataira a következő séma használatával. 

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

6. Mentse a jegyzékfájlt. 
  
7. Ezzel a tanúsítvánnyal lekérheti az MS Graph API hozzáférési jogkivonatát. Használja a **Get-MSCloudIdMSGraphAccessTokenFromCert** parancsmagot a MSCloudIdUtils PowerShell-modulból, és adja át az alkalmazás azonosítóját és az előző lépésben beszerzett ujjlenyomatot. 

   ![Azure Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. A Graph API lekérdezéséhez használja a PowerShell-parancsfájl hozzáférési tokenjét. A bejelentkezések és a directoryAudits végpont enumerálásához használja a MSCloudIDUtils **meghívása-MSCloudIdMSGraphQuery** parancsmagot. Ez a parancsmag több lapozható eredményt kezel, és elküldi ezeket az eredményeket a PowerShell-folyamatnak.

9. A naplók lekéréséhez kérdezze le a directoryAudits-végpontot. 
   ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. A bejelentkezési naplók lekéréséhez kérdezze le a bejelentkezések-végpontot.
    ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Most már dönthet úgy is, hogy exportálja ezeket az adatfájlokat egy CSV-be, és ment egy SIEM rendszerbe. A szkriptet be is csomagolhatja egy ütemezett feladatba az Azure AD-adatok bérlőtől való időszakos lekérésére úgy is, hogy nem kell a forráskódban tárolnia az alkalmazáskulcsokat. 

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a Reporting API-k működésével](concept-reporting-api.md)
* [Naplózási API-referenciák](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [A bejelentkezési tevékenység jelentésének API-referenciája](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
