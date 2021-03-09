---
title: Gyakori kérdések az Azure Marketplace-en futó virtuális gépekről
description: A virtuális gépek az Azure Marketplace-en való létrehozásakor felmerülő gyakori kérdések.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: d045af3b170d585b4bf1f8c57b7ba924c6b30695
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489786"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Gyakori kérdések az Azure Marketplace-en futó virtuális gépekről

Ezek a gyakori kérdések (GYIK) olyan gyakori problémákkal foglalkoznak, amelyekkel a virtuális gépek (VM) ajánlatának létrehozásakor találkozhat az Azure piactéren.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hogyan a virtuális magánhálózat (VPN) konfigurálását a virtuális gépekkel való munkavégzéshez?

Ha a Azure Resource Manager üzembe helyezési modellt használja, három lehetőség közül választhat:

- [Route-alapú VPN-átjáró létrehozása a Azure Portal használatával](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Route-alapú VPN-átjáró létrehozása Azure PowerShell használatával](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Route-alapú VPN-átjáró létrehozása a parancssori felület használatával](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Mik a Microsoft-kiszolgálói szoftverek Azure-alapú virtuális gépeken való futtatására vonatkozó Microsoft-támogatási szabályzatok?

[Az Microsoft Azure Virtual Machines szolgáltatással kapcsolatos](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)további részletekért tekintse meg a Microsoft Server szoftver támogatását.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Egy virtuális gépen Hogyan kezelhetem az egyéni szkriptek bővítményét az indítási feladatban?

További információ az egyéni szkriptek bővítmény használatáról a Azure PowerShell modullal, Azure Resource Manager sablonokkal és a Windows rendszerekkel kapcsolatos hibaelhárítási lépésekkel kapcsolatban: [egyéni parancsfájl-bővítmény a Windowshoz](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Támogatottak-e a 32 bites alkalmazások vagy szolgáltatások az Azure Marketplace-en?

Nem. Az Azure-beli virtuális gépekhez támogatott operációs rendszerek és standard szolgáltatások mind 64 bitesek. Bár a legtöbb 64 bites operációs rendszer támogatja az alkalmazások 32 bites verzióit a visszamenőleges kompatibilitás érdekében, az 32 bites alkalmazások a virtuálisgép-megoldás részeként való használata nem támogatott és nem ajánlott. Hozza létre újra az alkalmazást 64 bites projektként.

További információért lásd a következő cikkeket:

- [32 bites alkalmazások futtatása](/windows/desktop/WinProg64/running-32-bit-applications)
- [32 bites operációs rendszerek támogatása az Azure-beli virtuális gépeken](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft kiszolgálószoftveres támogatás a Microsoft Azure Virtual Machines szolgáltatáshoz](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Hiba: a virtuális merevlemez már regisztrálva van a rendszerkép-tárházban erőforrásként

Minden alkalommal, amikor megpróbálok létrehozni egy rendszerképet a virtuális merevlemezekről, a következő hibaüzenet jelenik meg: "a VHD már regisztrálva van a rendszerkép-tárházban erőforrásként" a Azure PowerShellban. Nem hoztam létre a képet, és nem találtam ilyen nevű képet az Azure-ban. Hogyan oldhatom meg ezt?

Ez a probléma általában akkor jelenik meg, ha olyan virtuális merevlemezről hozta létre a virtuális gépet, amelynek van zárolása. Győződjön meg arról, hogy nincs lefoglalva virtuális gép a virtuális merevlemezről, majd próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, nyisson meg egy támogatási jegyet. Lásd: [a partner Center támogatása](support.md).

## <a name="how-do-i-test-a-hidden-preview-image"></a>Hogyan tesztelni egy rejtett előnézeti képet?

A Gyorsindítás sablonok használatával a rejtett előnézeti képeket is üzembe helyezheti.
A Linux előzetes lemezképének üzembe helyezéséhez 
1. Ugrás ehhez a [gyors üzembe helyezési sablonhoz](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)válassza az "üzembe helyezés az Azure-ban" lehetőséget. Ennek elvégzéséhez Azure Portal
2. A Azure Portal területen válassza a "sablon szerkesztése" lehetőséget.
3. A JSON-sablonban keressen rá a imageReference kifejezésre, és frissítse a publisherid, a OfferID, a SkuID és a rendszerkép verzióját. Az előzetes rendszerkép teszteléséhez fűzze a "-PREVIEW" hozzáfűzést a OfferID.
 ![kép](https://user-images.githubusercontent.com/79274470/110191995-71c7d500-7de0-11eb-9f3c-6a42f55d8f03.png)
4. Kattintson a Save (Mentés) gombra.
5. Töltse ki a további részleteket. Áttekintés és létrehozás



## <a name="next-steps"></a>Következő lépések

- [Virtuális gépek tanúsításával kapcsolatos hibaelhárítás](azure-vm-create-certification-faq.md)
