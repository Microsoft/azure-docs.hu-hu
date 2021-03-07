---
title: Tanúsítványok létrehozása Microsoft Azure Stack hub Readiness-ellenőrző eszköz használatával | Microsoft Docs
description: Útmutatás a tanúsítványkérelmek létrehozásához, majd a tanúsítványok lekéréséhez és telepítéséhez a Azure Stack Edge Pro GPU-eszközön az Azure Stack hub Readiness-ellenőrző eszköz használatával.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: a04243093b89b6a2498efc48f80cbd7a47d57337
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437723"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Tanúsítványok létrehozása a Azure Stack Edge Pro-hoz Azure Stack hub Readiness-ellenőrző eszköz használatával 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre tanúsítványokat a Azure Stack Edge Pro-hoz a Azure Stack hub Readiness-ellenőrző eszköz használatával. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack hub Readiness-ellenőrző eszköz használata

Az Azure Stack hub Readiness-ellenőrző eszköz használatával tanúsítvány-aláírási kérelmeket (munkatársakat) hozhat létre egy Azure Stack Edge Pro-eszköz telepítéséhez. Ezeket a kéréseket a Azure Stack Edge Pro-eszköz megrendelése után hozhatja létre, és megvárhatja az eszköz érkezését.

> [!NOTE]
> Ezt az eszközt csak tesztelési vagy fejlesztési célokra, illetve éles eszközökre nem használhatja. 

Az Azure Stack hub Readiness-ellenőrző eszköz (AzsReadinessChecker) használatával a következő tanúsítványokat kérheti le:

- Tanúsítvány Azure Resource Manager
- Helyi felhasználói felületi tanúsítvány
- Csomópont-tanúsítvány
- BLOB-tanúsítvány
- VPN-tanúsítvány


## <a name="prerequisites"></a>Előfeltételek

Az ügyfélszolgálati munkatársak létrehozásához Azure Stack Edge Pro-eszköz telepítéséhez győződjön meg a következőket: 

- Windows 10 vagy Windows Server 2016 vagy újabb rendszert futtató ügyfél. 
- Letöltötte a Microsoft Azure Stack hub Readiness-ellenőrző eszközét [a rendszer PowerShell-Galéria](https://aka.ms/AzsReadinessChecker) .
- A tanúsítványokkal kapcsolatban a következő információk szerepelnek:
  - Eszköz neve
  - Csomópont sorozatszáma
  - Külső teljesen minősített tartománynév (FQDN)

## <a name="generate-certificate-signing-requests"></a>Tanúsítvány-aláírási kérelmek előállítása

Az alábbi lépések segítségével készítheti elő az Azure Stack Edge Pro-eszköz tanúsítványait:

1. Futtassa a PowerShellt rendszergazdaként (5,1 vagy újabb).
2. Telepítse az Azure Stack hub Readiness-ellenőrző eszközt. A PowerShell parancssorába írja be a következőt: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    A telepített verzió beszerzéséhez írja be a következőt:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Hozzon létre egy könyvtárat az összes tanúsítványhoz, ha még nem rendelkezik ilyennel. Típus: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Tanúsítványkérelem létrehozásához adja meg a következő információkat. Ha VPN-tanúsítványt hoz létre, néhány ilyen bemenet nem érvényes.
    
    |Bevitel |Description  |
    |---------|---------|
    |`OutputRequestPath`|A fájl elérési útja a helyi ügyfélszámítógépen, amelyen létre szeretné hozni a tanúsítványokat.        |
    |`DeviceName`|Az eszköz helyi webes felhasználói felületének **eszközök** lapján található név. <br> Ez a mező nem szükséges a VPN-tanúsítványokhoz.         |
    |`NodeSerialNumber`|Az eszköz csomópontjának sorozatszáma a **hálózat** lapon az eszköz helyi webes felületén. <br> Ez a mező nem szükséges a VPN-tanúsítványokhoz.       |
    |`ExternalFQDN`|Az eszköz helyi webes FELÜLETének **eszközök** lapján található DNSDomain érték.         |
    |`RequestType`|A kérelem típusa a `MultipleCSR` különböző végpontokhoz tartozó tanúsítványok esetében lehet, vagy `SingleCSR` – egyetlen tanúsítvány az összes végponthoz. <br> Ez a mező nem szükséges a VPN-tanúsítványokhoz.     |

    A VPN-tanúsítvány kivételével az összes tanúsítvány esetében írja be a következőt: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Ha VPN-tanúsítványt hoz létre, írja be a következőt: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. A tanúsítványkérelem fájljait a fenti OutputRequestPath paraméterben megadott könyvtárba fogja megtalálni. A paraméter használatakor `MultipleCSR` a következő négy fájl jelenik meg a `.req` kiterjesztéssel:

    
    |Fájlnevek  |Tanúsítványkérelem típusa  |
    |---------|---------|
    |Kezdve a `DeviceName`     |Helyi webes FELHASZNÁLÓIFELÜLET-tanúsítvány kérése      |
    |Kezdve a `NodeSerialNumber`     |Csomópont-tanúsítványkérelem         |
    |Kezdés `login`     |Azure Resource Manager Endpoint Certificate kérelem       |
    |Kezdés `wildcard`     |BLOB Storage-tanúsítvány kérése. Helyettesítő karaktert tartalmaz, mert az az eszközön létrehozott összes Storage-fiókot tartalmazza.          |
    |Kezdés `AzureStackEdgeVPNCertificate`     |VPN-ügyféltanúsítvány iránti kérelem.         |

    Megjelenik egy INF-mappa is. Ez egy felügyeleti. <Edge-DeviceName> információs fájlt tartalmaz a tanúsítvány részleteit magyarázó szövegben.  


6. Küldje el ezeket a fájlokat a hitelesítésszolgáltatótól (belső vagy nyilvános). Győződjön meg arról, hogy a HITELESÍTÉSSZOLGÁLTATÓ olyan tanúsítványokat hoz létre a generált kérelem alapján, amelyek megfelelnek a csomópont- [tanúsítványok](azure-stack-edge-gpu-manage-certificates.md#node-certificates), a [végponti tanúsítványok](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)és a [helyi felhasználói felületi tanúsítványok](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates)Azure stack Edge Pro-tanúsítványra vonatkozó követelményeinek.

## <a name="prepare-certificates-for-deployment"></a>Tanúsítványok előkészítése központi telepítéshez

A HITELESÍTÉSSZOLGÁLTATÓTÓL beolvasott tanúsítványfájl-fájlokat importálni és exportálni kell az Azure Stack Edge Pro-eszköz tanúsítványra vonatkozó követelményeinek megfelelő tulajdonságokkal. Hajtsa végre a következő lépéseket ugyanazon a rendszeren, amelyen a tanúsítvány-aláírási kéréseket létrehozta.

- A tanúsítványok importálásához kövesse a [tanúsítványok importálása az Azure stack Edge Pro-eszközt elérő ügyfeleken](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)című témakör lépéseit.

- A tanúsítványok exportálásához kövesse az [Azure stack Edge Pro-eszközhöz hozzáférő ügyfél tanúsítványának exportálása](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)című témakör lépéseit.


## <a name="validate-certificates"></a>Tanúsítványok ellenőrzése

Először létrehoz egy megfelelő mappastruktúrát, és elhelyezi a tanúsítványokat a megfelelő mappákban. Csak ezután érvényesíti a tanúsítványokat az eszköz használatával.

1. Futtassa a PowerShellt rendszergazdaként.

2. A mappa megfelelő struktúrájának létrehozásához a parancssorba írja be a következőt:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. A PFX-jelszó konvertálása biztonságos karakterlánccá. Típus:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Ezután érvényesítse a tanúsítványokat. Típus:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Következő lépések

[Az Azure Stack Edge Pro-eszköz üzembe helyezése](azure-stack-edge-gpu-deploy-prep.md)
