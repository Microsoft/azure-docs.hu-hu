---
title: Az Azure IoT Hub Device Provisioning Service eszközök regisztrálásának kezelése a Azure Portal
description: Eszközök regisztrálásának kezelése az eszköz kiépítési szolgáltatásához (DPS) a Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 6ec146a05df1b896f8ca594d29cf13341b70765a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010962"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Az eszközök regisztrálásának kezelése a Azure Portal

Az *eszközök* regisztrálása létrehoz egy egyetlen eszközről vagy egy olyan eszközről szóló adatcsoportot, amely egy ponton regisztrálhat az Azure IoT hub Device Provisioning Service. A beléptetési rekord a beléptetés részeként tartalmazza az eszköz (ek) kezdeti konfigurációját. A konfigurációban a IoT hub egy eszköz lesz hozzárendelve, vagy egy elosztási házirend, amely a hubot egy hubok készletében konfigurálja. Ez a cikk bemutatja, hogyan kezelheti az eszközök regisztrációját a kiépítési szolgáltatáshoz.


## <a name="create-a-device-enrollment"></a>Eszközök regisztrálásának létrehozása

Az eszközöket kétféleképpen lehet regisztrálni a kiépítési szolgáltatással:

* A **beléptetési csoport** olyan eszközök csoportja, amelyek közös igazolási mechanizmussal rendelkeznek. Azt javasoljuk, hogy használjon egy regisztrációs csoportot nagy számú, kezdeti konfigurációt használó eszközhöz, vagy ha az összes eszköz ugyanahhoz a bérlőhöz fog csatlakozni. A [szimmetrikus kulcs](concepts-symmetric-key-attestation.md) vagy az [X. 509 tanúsítványokat](concepts-x509-attestation.md) használó eszközök támogatottak. 

    A szimmetrikus kulcsokkal rendelkező beléptetési csoportok létrehozásával és használatával kapcsolatos részletes útmutatásért lásd: [eszközök kiépítése szimmetrikus kulcsokkal](how-to-legacy-device-symm-key.md) oktatóanyag.

    A következő lépésekkel hozhat létre egy regisztrációs csoportot a portálon az eszközök csoportjához:

    1. Jelentkezzen be a Azure Portalba, és a bal oldali menüben kattintson az **összes erőforrás** elemre.  
    1. Kattintson arra az eszköz-kiépítési szolgáltatásra, amelyhez regisztrálni kívánja az eszközt az erőforrások listájából.  
    1. A kiépítési szolgáltatásban kattintson a **regisztrációk kezelése** elemre, majd kattintson a felül található **regisztrációs csoport hozzáadása** gombra.  
     
        ![Beléptetési csoport a portálon](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. Amikor megjelenik a "beléptetési csoport hozzáadása" panel, adja meg a regisztrációhoz szükséges adatokat, majd kattintson a **Mentés** gombra.  
     
        [![Regisztrációs csoport hozzáadása a portálon](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | Mező | Leírás |
        | :--- | :--- |
        | **Csoport neve** | Az eszközök csoportjának kötelező neve. |
        | **Igazolás típusa** | Kattintson az igazolási típus **tanúsítvány** vagy **szimmetrikus kulcs** elemére az eszközök által használt igazolási módszertől függően. |
        | **Tanúsítvány típusa** | Akkor érhető el, ha tanúsítvány-igazolást használ. Válassza ki a **hitelesítésszolgáltató tanúsítványát** vagy a **köztes** tanúsítványt az eszköz tanúsítványait aláíró tanúsítvány alapján. |
        | **Elsődleges tanúsítvány** | Ha a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal aláírja az eszköz tanúsítványait, akkor a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak rendelkeznie kell [a birtoklás igazolásával](how-to-verify-certificates.md) . Ezt követően kiválaszthatja az eszközök csoportjának **elsődleges tanúsítványát** .<br><br>Ha az eszköz tanúsítványait köztes tanúsítvánnyal regisztrálja, a feltöltés gomb elérhető lesz, amely lehetővé teszi a köztes tanúsítvány feltöltését. A közbenső tanúsítvány aláírásával [igazolni kell,](how-to-verify-certificates.md) hogy az a birtokában is megtörtént. |

        
    

* Az **Egyéni regisztráció** egy olyan eszköz bejegyzése, amely egy IoT hubhoz rendelhető. A [szimmetrikus kulcsú](concepts-symmetric-key-attestation.md), [X. 509 tanúsítványokat](concepts-x509-attestation.md)és a [TPM-igazolást](concepts-tpm-attestation.md) használó eszközök támogatottak. 

    Az alábbi lépéseket követve létrehozhat egy egyéni regisztrációt a portálon:

    1. Jelentkezzen be a Azure Portalba, és a bal oldali menüben kattintson az **összes erőforrás** elemre.
    1. Kattintson arra az eszköz-kiépítési szolgáltatásra, amelyhez regisztrálni kívánja az eszközt az erőforrások listájából.
    1. A kiépítési szolgáltatásban kattintson a **regisztrációk kezelése** lehetőségre, majd kattintson az **Egyéni regisztráció hozzáadása** gombra a felső részen.   

       [![Egyéni regisztráció hozzáadása a portálon](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. Amikor megjelenik a "regisztráció hozzáadása" panel, adja meg az egyes eszközök regisztrációjának adatait, majd kattintson a **Mentés** gombra. 
     
        [![Egyéni regisztráció a portálon](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | Mező | Leírás |
        | :--- | :--- |
        | **Mechanizmus** | Válassza az **X. 509**, **TPM** vagy **szimmetrikus kulcs** lehetőséget az igazolási mechanizmushoz, amely az eszközök által használt igazolási módszertől függően használható. |
        | Igazolási beállítások | A szimmetrikus kulcsokkal vagy X. 509 tanúsítványokkal rendelkező egyéni regisztrációk létrehozásával és használatával kapcsolatos részletes utasításokért tekintse meg a [szimmetrikus eszköz](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment) üzembe helyezésének egyikét vagy az [x. 509 tanúsítvány-eszköz](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry) kiépítését ismertető témakört.<br><br>A TPM-igazolást használó egyéni regisztrációk létrehozásával és használatával kapcsolatos részletes utasításokért tekintse meg a [szimulált TPM-eszközök egyikének kiépítése](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry) című témakört.|
        | **IoT Hub eszköz azonosítója** |  Ez az azonosító az eszközt fogja ábrázolni. Az eszköz AZONOSÍTÓjának szabályait kell követnie. További információ: [eszköz identitásának tulajdonságai](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).<br><br>X. 509 tanúsítványok használata esetén ennek a szövegnek a tulajdonos nevének kell lennie a beléptetéshez feltöltött eszköz tanúsítványában. A tulajdonos nevének meg kell felelnie az eszköz AZONOSÍTÓjának szabályainak.|
            


## <a name="update-an-enrollment-entry"></a>Beléptetési bejegyzés frissítése
A meglévő regisztrációs bejegyzéseket a következő lépésekkel frissítheti a portálon:

1. Nyissa meg az eszköz kiépítési szolgáltatását a Azure Portalon, és kattintson a **regisztrációk kezelése** lehetőségre. 
1. Navigáljon a módosítani kívánt beléptetési bejegyzéshez. Kattintson a bejegyzésre, amely megnyit egy összegző információt az eszköz regisztrálásáról. 
1. Ezen az oldalon a biztonsági típustól és a hitelesítő adatoktól eltérő elemeket is módosíthat, például az eszközhöz kapcsolódó IoT hub-t, valamint az eszköz AZONOSÍTÓját. Emellett módosíthatja a kezdeti eszköz kettős állapotát is. 
1. Ha elkészült, kattintson a **Save (Mentés** ) gombra az eszköz regisztrálásának módosításához. 

    ![Regisztráció frissítése a portálon](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Eszközök regisztrációjának eltávolítása
Azokban az esetekben, amikor az eszköz (ek) et nem kell IoT-hubhoz kiépíteni, a következő lépésekkel távolíthatja el a kapcsolódó beléptetési bejegyzést a portálon:

1. Nyissa meg az eszköz kiépítési szolgáltatását a Azure Portalon, és kattintson a **regisztrációk kezelése** lehetőségre. 
1. Navigáljon a elemre, és válassza ki az eltávolítani kívánt beléptetési bejegyzést. 
1. Kattintson a felül található **Törlés** gombra, majd válassza az **Igen** lehetőséget, amikor a rendszer felszólítja a megerősítésre. 
1. A művelet befejezése után a rendszer eltávolítja a bejegyzést az eszközök regisztrációjának listájáról. 
 
    ![Regisztráció eltávolítása a portálon](./media/how-to-manage-enrollments/remove-enrollment.png)