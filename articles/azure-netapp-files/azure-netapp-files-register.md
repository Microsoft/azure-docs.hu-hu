---
title: Regisztráljon a Azure NetApp-fájlok |} A Microsoft Docs
description: Ismerteti, hogyan lehet regisztrálni az Azure Files-NetApp szolgáltatásban kérelmet küldeni.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 86c016a5dbcc0d78378e59bc6b3606ddf2c54f64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452771"
---
# <a name="register-for-azure-netapp-files"></a>Regisztrálás az Azure NetApp Filesra
Mielőtt Azure NetApp fájlokat használ, a NetApp fájlokat az Azure szolgáltatásban regisztrálni kérelmet kell benyújtania.  A regisztráció után, majd regisztrálja a szolgáltatás használatához.

## <a name="request-to-enroll-in-the-service"></a>A szolgáltatás regisztráció kérelem
Kell tartoznia a nyilvános előzetes program és az engedélyezési listához hozzáadni kívánt Microsoft.NetApp erőforrás-szolgáltató elérésére. A nyilvános előzetes verzió programjához való csatlakozással kapcsolatos részletekért lásd az [Azure NetApp Files nyilvános előzetes verzió regisztrációs lapját](https://aka.ms/nfspublicpreview). 


## <a name="register-the-netapp-resource-provider"></a>A NetApp erőforrás-szolgáltató regisztrálása

A szolgáltatás használatához regisztrálnia kell az Azure erőforrás-szolgáltató Azure NetApp fájlok. 

1. Az Azure Portalon kattintson az Azure Cloud Shell ikonra a jobb felső sarokban található:

      ![Az Azure Cloud Shell ikon](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Ha több előfizetéssel rendelkezik az Azure-fiókjával, válassza ki azt, amelyik már szerepel az engedélyezési listán NetApp Azure-fájlok:
    
        az account set --subscription <subscriptionId>

3. Az Azure Cloud Shell-konzolon adja meg annak ellenőrzésére, hogy az előfizetés lett szerepel az engedélyezési listán a következő parancsot:
    
        az feature list | grep NetApp

   A parancs kimenete a következőképpen jelenik meg:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` az előfizetés-azonosítója.

4. Az Azure Cloud Shell-konzolon adja meg az Azure erőforrás-szolgáltató regisztrálásához a következő parancsot: 
    
        az provider register --namespace Microsoft.NetApp --wait

   A `--wait` paraméter arra utasítja a konzolt, hogy Várjon, amíg a regisztráció befejezéséhez. A regisztrációs folyamat befejezése hosszabb időt is igénybe vehet.

5. Az Azure Cloud Shell-konzolon adja meg, győződjön meg arról, hogy az Azure erőforrás-szolgáltató regisztrálva van-e a következő parancsot: 
    
        az provider show --namespace Microsoft.NetApp

   A parancs kimenete a következőképpen jelenik meg:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` az előfizetés-azonosítója.  A `state` paraméter értéke azt jelzi, hogy `Registered`.

6. Az Azure Portalon kattintson a **előfizetések** panelen.
7. Az előfizetések panelen kattintson az előfizetési azonosítóját. 
8. Kattintson a beállítások az előfizetés **erőforrás-szolgáltatók** , győződjön meg arról, hogy Microsoft.NetApp szolgáltató azt jelzi, hogy a regisztrált állapota: 

      ![Registered Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>További lépések  

[NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)