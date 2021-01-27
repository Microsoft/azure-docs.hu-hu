---
title: A virtuális gépek igény szerinti elérése a Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum azt mutatja be, hogy a Azure Security Center az Azure Virtual Machines szolgáltatáshoz való hozzáférés szabályozása révén Hogyan szabályozható a virtuális gépekhez való hozzáférés (JIT).
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 60ae36d80e34f27ed68c679f47edacf3e402417c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916150"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>A felügyeleti portok védelme az igény szerinti hozzáféréssel

Az Azure-Virtual Machines bejövő forgalmának zárolása Azure Security Center az igény szerinti (JIT) virtuálisgép-hozzáférési szolgáltatással. Ez csökkenti a támadásoknak való kitettséget, miközben egyszerű hozzáférést biztosít a virtuális géphez való csatlakozáshoz.

A JIT működésével és a mögöttes logikával kapcsolatos teljes körű magyarázatot a következő témakörben talál: [just-in-time](just-in-time-explained.md).

Ezen az oldalon megtudhatja, hogyan veheti fel a JIT-t a biztonsági programba. A következőket fogja megtanulni: 

- A **JIT engedélyezése a virtuális gépeken** – a JIT-t a Security Center, a PowerShell vagy a REST API használatával egy vagy több virtuális gép számára is engedélyezheti a saját egyéni beállításaival. Azt is megteheti, hogy az alapértelmezett, rögzített paraméterekkel rendelkező JIT-t az Azure Virtual Machines szolgáltatásban engedélyezte. Ha ez a beállítás engedélyezve van, a JIT leállítja a bejövő forgalmat az Azure-beli virtuális gépekre egy szabály létrehozásával a hálózati biztonsági csoportban.
- **Hozzáférés kérése egy olyan virtuális géphez, amelyen engedélyezve** van a JIT – a JIT célja annak biztosítása, hogy a bejövő forgalom zárolva legyen, Security Center továbbra is egyszerű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A Security Center, az Azure Virtual Machines, a PowerShell vagy a REST API használatával hozzáférés kérhető egy JIT-kompatibilis virtuális géphez.
- **A tevékenység naplózása** – annak érdekében, hogy a virtuális gépek megfelelően biztonságosak legyenek, tekintse át a hozzáférését a JIT-kompatibilis virtuális gépekhez a rendszeres biztonsági ellenőrzések részeként.   



## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|[Az Azure Defender for Servers](defender-for-servers-introduction.md) használatát igényli|
|Támogatott virtuális gépek:|![Igen, ](./media/icons/yes-icon.png) Azure Resource Manager használatával üzembe helyezett virtuális gépek.<br>![](./media/icons/no-icon.png)A klasszikus üzembe helyezési modellel rendelkező virtuális gépeket nem. [További információ ezekről a telepítési modellekről](../azure-resource-manager/management/deployment-models.md).<br>![Nem ](./media/icons/no-icon.png) [Azure Firewall Manager](../firewall-manager/overview.md) által vezérelt Azure-tűzfalak által védett virtuális gépek|
|Szükséges szerepkörök és engedélyek:|Az **olvasó** -és **SecurityReader** szerepkörök egyaránt megtekinthetik a JIT-állapotot és a paramétereket.<br>Az JIT-sel használható egyéni szerepkörök létrehozásához tekintse meg a [Mi szükséges engedélyeket a JIT konfigurálásához és használatához?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)című témakörben.<br>Ha olyan felhasználók számára szeretne legkevesebb jogosultsági szintű szerepkört létrehozni, akiknek JIT-hozzáférést kell kérniük egy virtuális géphez, és nem kell más JIT-műveletet végezniük, használja a Security Center GitHub közösségi oldalain található [set-JitLeastPrivilegedRole szkriptet](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) .|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="enable-jit-vm-access"></a>JIT virtuális gép hozzáférésének engedélyezése <a name="jit-configure"></a>

Egy vagy több virtuális gép számára engedélyezheti a JIT virtuális gépek hozzáférését a saját egyéni beállításaival Security Center vagy programozott módon. 

Azt is megteheti, hogy az alapértelmezett, rögzített paraméterekkel rendelkező JIT-t az Azure Virtual Machines szolgáltatásban engedélyezte.

Ezeket a beállításokat az alábbi külön lapon ismertetjük.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>A JIT engedélyezése a virtuális gépeken Azure Security Center <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="JIT virtuális gép hozzáférésének konfigurálása Azure Security Center":::

A Security Centerból engedélyezheti és konfigurálhatja a JIT VM-hozzáférést.

1. Nyissa meg az Azure Defender irányítópultot, és a speciális védelem területen válassza az igény szerinti virtuális gépekhez **való hozzáférést**.

    Megnyílik az igény szerinti virtuálisgép **-hozzáférés** lap a virtuális gépekkel a következő lapokon csoportosítva:

    - **Konfigurálva** – a virtuális gépek igény szerinti elérésének támogatásához már konfigurált virtuális gépek. Minden egyes virtuális gép esetében a konfigurált lap a következőket jeleníti meg:
        - a jóváhagyott JIT-kérelmek száma az elmúlt hét napban
        - az utolsó hozzáférés dátuma és időpontja
        - a kapcsolati adatok konfigurálva
        - az utolsó felhasználó
    - **Nincs konfigurálva** – a JIT-t nem engedélyező virtuális gépek, de támogatni tudják a JIT-t. Javasoljuk, hogy ezekhez a virtuális gépekhez engedélyezze a JIT-t.
    - Nem **támogatott** – a JIT-t nem támogató virtuális gépek, amelyek nem támogatják a szolgáltatást. Előfordulhat, hogy a virtuális gép a következő okok miatt van jelen a lapon:
      - Hiányzó hálózati biztonsági csoport (NSG) – a JIT megköveteli egy NSG konfigurálását
      - A klasszikus VM-JIT támogatja a Azure Resource Manageron központilag telepített virtuális gépeket, nem a "klasszikus üzembe helyezést". [További információ a klasszikus vs Azure Resource Manager üzembe helyezési modellekről](../azure-resource-manager/management/deployment-models.md).
      - Egyéb – előfordulhat, hogy a virtuális gép ezen a lapon található, ha a JIT-megoldás le van tiltva az előfizetés vagy az erőforráscsoport biztonsági házirendjében.

1. A **nincs konfigurálva** lapon jelölje ki a virtuális gépeket a JIT-mel, és válassza a **JIT engedélyezése a virtuális gépeken** lehetőséget. 

    Megnyílik a JIT VM-hozzáférés lap, amely a Security Center által javasolt portok listáját tartalmazza:
    - 22 – SSH
    - 3389 – RDP
    - 5985 – WinRM 
    - 5986 – WinRM

    Az alapértelmezett beállítások elfogadásához válassza a **Mentés** lehetőséget.

1. A JIT-beállítások testreszabása:

    - Egyéni portok hozzáadása a **Hozzáadás** gombbal. 
    - Módosítsa az egyik alapértelmezett portot a listából kiválasztva.

    Minden porthoz (egyéni és alapértelmezett) a **port hozzáadása konfiguráció** ablaktábla a következő lehetőségeket kínálja:

    - **Protokoll**– a kérelem jóváhagyásakor a porton engedélyezett protokoll
    - **Engedélyezett forrás IP**-címek – a kérés jóváhagyásakor a porton engedélyezett IP-címtartományok
    - **Kérelmek maximális ideje**– az a maximális időtartam, ameddig egy adott portot meg lehet nyitni

     1. Állítsa be a port biztonságát az igényeinek megfelelően.

     1. Kattintson az **OK** gombra.

1. Kattintson a **Mentés** gombra.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>A JIT-konfiguráció szerkesztése egy JIT-kompatibilis virtuális gépen Security Center használatával <a name="jit-modify"></a>

A virtuális gép igény szerinti konfigurálását úgy módosíthatja, hogy egy új portot ad hozzá a virtuális gép védelméhez, vagy ha módosítja a már védett porthoz kapcsolódó egyéb beállításokat.

Egy virtuális gép meglévő JIT-szabályainak szerkesztése:

1. Nyissa meg az Azure Defender irányítópultját, és a speciális védelem területen válassza az **adaptív alkalmazás-vezérlőelemek** lehetőséget.

1. A **konfigurált** lapon kattintson a jobb gombbal arra a virtuális gépre, amelyhez portot kíván hozzáadni, majd válassza a szerkesztés lehetőséget. 

    ![JIT VM-hozzáférési konfiguráció szerkesztése a Azure Security Centerban](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. A **JIT VM-hozzáférés konfigurálása** területen szerkesztheti a már védett portok meglévő beállításait, vagy hozzáadhat egy új egyéni portot is.

1. Ha befejezte a portok szerkesztését, válassza a **Mentés** lehetőséget.
 


### <a name="azure-virtual-machines"></a>[**Azure-beli virtuális gépek**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>A JIT engedélyezése virtuális gépeken az Azure Virtual Machines szolgáltatásban

A JIT-t a Azure Portal Azure Virtual Machines oldalain engedélyezheti egy virtuális gépen.

![A JIT virtuális gépekhez való hozzáférés konfigurálása az Azure Virtual Machines szolgáltatásban](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Ha egy virtuális géphez már van engedélyezve az igény szerinti hozzáférés, a konfiguráció oldalán láthatja, hogy az igény szerint engedélyezve van, és a hivatkozásra kattintva megnyithatja az igény szerinti virtuálisgép-hozzáférési lapot Security Centerban, és megtekintheti és módosíthatja a beállításokat.

1. A [Azure Portal](https://ms.portal.azure.com)keresse meg és válassza ki a **virtuális gépek** elemet. 

1. Válassza ki azt a virtuális gépet, amelyet a JIT használatával szeretne védelemmel ellátni.

1. A menüben válassza a **Konfigurálás** lehetőséget.

1. Az igény szerinti **hozzáférés** területen válassza az igény szerinti **Engedélyezés** lehetőséget. 

    Ez a virtuális gép igény szerinti elérését teszi lehetővé a következő alapértelmezett beállításokkal:

    - Windows rendszerű gépek:
        - RDP-port 3389
        - Legfeljebb három órányi hozzáférés engedélyezett
        - Az engedélyezett forrás IP-címek beállítása bármelyik
    - Linux rendszerű gépek:
        - SSH-port – 22
        - Legfeljebb három órányi hozzáférés engedélyezett
        - Az engedélyezett forrás IP-címek beállítása bármelyik

1. Ezen értékek bármelyikének szerkesztéséhez, vagy további portok hozzáadásához a JIT-konfigurációhoz használja a Azure Security Center igény szerinti oldalát:

    1. A Security Center menüjében válassza az igény szerinti virtuális gépekhez **való hozzáférést**.

    1. A **konfigurált** lapon kattintson a jobb gombbal arra a virtuális gépre, amelyhez portot kíván hozzáadni, majd válassza a szerkesztés lehetőséget. 

        ![JIT VM-hozzáférési konfiguráció szerkesztése a Azure Security Centerban](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. A **JIT VM-hozzáférés konfigurálása** területen szerkesztheti a már védett portok meglévő beállításait, vagy hozzáadhat egy új egyéni portot is.

    1. Ha befejezte a portok szerkesztését, válassza a **Mentés** lehetőséget.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>A JIT engedélyezése virtuális gépeken a PowerShell-lel

Az igény szerinti virtuális gépek PowerShell-lel való elérésének engedélyezéséhez használja a hivatalos Azure Security Center PowerShell-parancsmagot `Set-AzJitNetworkAccessPolicy` .

**Példa** – a virtuális gépek igény szerinti elérésének engedélyezése egy adott virtuális gépen a következő szabályokkal:

* A 22-es és a 3389-es port lezárása
* A maximális időablak 3 órán belül megadható, hogy az egyes jóváhagyott kérések alapján megnyitható legyen.
* A forrás IP-címek vezérléséhez hozzáférést kérő felhasználó engedélyezése
* Annak engedélyezése, hogy a hozzáférést kérő felhasználó sikeres munkamenetet hozzon létre egy jóváhagyott igény szerinti hozzáférési kérelem alapján

A következő PowerShell-parancsok hozza létre ezt a JIT-konfigurációt:

1. Rendeljen hozzá egy olyan változót, amely a virtuális gép igény szerinti virtuálisgép-hozzáférési szabályait tartalmazza:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. Helyezze be a virtuális gép igény szerinti virtuálisgép-hozzáférési szabályait egy tömbbe:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. A virtuális gépek igény szerinti hozzáférési szabályainak konfigurálása a kiválasztott virtuális gépen:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    A virtuális gép megadásához használja a-name paramétert. Ha például két különböző virtuális gép, a VM1 és a VM2 JIT-konfigurációját szeretné létrehozni, használja a következőt: ```Set-AzJitNetworkAccessPolicy -Name VM1``` és ```Set-AzJitNetworkAccessPolicy -Name VM2``` .


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>A JIT engedélyezése a virtuális gépeken a REST API használatával

Az igény szerinti virtuálisgép-hozzáférési szolgáltatás a Azure Security Center API-n keresztül is használható. Ezzel az API-val lekérheti a konfigurált virtuális gépekre vonatkozó információkat, újakat adhat hozzá, hozzáférést kérhet egy virtuális géphez, és egyéb műveleteket is elérhet. 

További információ: [JIT hálózati hozzáférési házirendek](/rest/api/securitycenter/jitnetworkaccesspolicies).


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>Hozzáférés kérése gy JIT-kompatibilis virtuális géphez

A Azure Portal (Security Center vagy Azure-beli virtuális gépeken) vagy programozott módon hozzáférést igényelhet egy JIT-kompatibilis virtuális géphez.

Ezeket a beállításokat az alábbi külön lapon ismertetjük.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Hozzáférés kérése egy JIT-kompatibilis virtuális géphez Azure Security Center 

Ha egy virtuális gépnek engedélyezve van a JIT, hozzáférést kell kérnie a csatlakozáshoz. Bármilyen támogatott módon kérhet hozzáférést, függetlenül attól, hogy hogyan engedélyezte a JIT-t.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="JIT-hozzáférés kérése Security Center":::

1. A **virtuális gépek igény szerinti elérése** lapon válassza a **konfigurált** lapot.

1. Megjelölheti az elérni kívánt virtuális gépeket.

    - A **kapcsolat részletei** oszlopban látható ikon jelzi, hogy a JIT engedélyezve van-e a hálózati biztonsági csoportban vagy a tűzfalon. Ha mindkettőn engedélyezve van, csak a tűzfal ikon jelenik meg.

    - A **kapcsolat részletei** oszlop a virtuális gép csatlakoztatásához és a nyitott portok eléréséhez szükséges információkat tartalmazza.

1. Válassza a **hozzáférés kérése** lehetőséget. Megnyílik a **kérelem hozzáférése** ablak.

1. A **kérelmek hozzáférése** területen minden egyes virtuális gép esetében konfigurálja a megnyitni kívánt portokat, valamint a port megnyitásának forrás IP-címeit, valamint azt az időablakot, amelyre a port megnyílik. Csak a konfigurált portokhoz lehet hozzáférést kérni. Minden porton engedélyezett a létrehozott JIT-konfigurációból származó maximális idő.

1. Válassza a **portok megnyitása** lehetőséget.

> [!NOTE]
> Ha egy hozzáférést kérő felhasználó proxy mögött van, akkor az **IP-cím** nem működik. Előfordulhat, hogy meg kell határoznia a szervezet teljes IP-címtartományt.



### <a name="azure-virtual-machines"></a>[**Azure-beli virtuális gépek**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Hozzáférés kérése egy JIT-kompatibilis virtuális géphez az Azure-beli virtuális gép kapcsolódási oldaláról

Ha egy virtuális gépnek engedélyezve van a JIT, hozzáférést kell kérnie a csatlakozáshoz. Bármilyen támogatott módon kérhet hozzáférést, függetlenül attól, hogy hogyan engedélyezte a JIT-t.

  >![JIT igény szerinti kérése](./media/security-center-just-in-time/jit-request-vm.png)


Hozzáférés kérése Azure-beli virtuális gépekről:

1. A Azure Portal nyissa meg a virtuális gépek lapjait.

1. Válassza ki azt a virtuális gépet, amelyhez csatlakozni szeretne, majd nyissa meg a **Kapcsolódás** lapot.

    Az Azure ellenőrzi, hogy a JIT engedélyezve van-e a virtuális gépen.

    - Ha az JIT nincs engedélyezve a virtuális gépen, a rendszer felszólítja, hogy engedélyezze.

    - Ha a JIT engedélyezve van, válassza a hozzáférés **kérése** lehetőséget az adott virtuális géphez konfigurált IP-címet, időtartományt és portokat kérő hozzáférési kérelem továbbításához.

> [!NOTE]
> A Azure Firewall által védett virtuális gépekre vonatkozó kérések jóváhagyása után Security Center biztosítja a felhasználó számára a megfelelő kapcsolódási adatokat (a port hozzárendelését a DNAT táblából) a virtuális géphez való csatlakozáshoz.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Hozzáférés kérése egy JIT-kompatibilis virtuális géphez a PowerShell használatával

A következő példában egy igény szerinti virtuálisgép-hozzáférési kérést láthat egy adott virtuális géphez, amelyben a 22-es portot egy adott IP-cím és egy adott időtartam esetében kérik le:

Futtassa a következőt a PowerShellben:

1. A virtuálisgép-kérelem hozzáférési tulajdonságainak konfigurálása:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. Helyezze be a virtuális gép hozzáférési kérelmének paramétereit egy tömbbe:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. A kérelem elérésének elküldése (az 1. lépésben szereplő erőforrás-azonosító használata)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

További információt a [PowerShell-parancsmag dokumentációjában talál](/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Hozzáférés kérése egy JIT-kompatibilis virtuális gépekhez a REST API használatával

Az igény szerinti virtuálisgép-hozzáférési szolgáltatás a Azure Security Center API-n keresztül is használható. Ezzel az API-val lekérheti a konfigurált virtuális gépekre vonatkozó információkat, újakat adhat hozzá, hozzáférést kérhet egy virtuális géphez, és egyéb műveleteket is elérhet. 

További információ: [JIT hálózati hozzáférési házirendek](/rest/api/securitycenter/jitnetworkaccesspolicies).

---








## <a name="audit-jit-access-activity-in-security-center"></a>A JIT hozzáférési tevékenység naplózása Security Center

A naplóbeli kereséssel betekintést nyerhet a virtuális gépek tevékenységeibe. A naplók megtekintése:

1. A **virtuális gépek igény szerinti elérése lehetőségnél** válassza a **konfigurált** lapot.

1. A naplózni kívánt virtuális gép esetében nyissa meg a sor végén található három pont menüt.
 
1. Válassza ki a **tevékenység naplóját** a menüből.

   ![Just-in-time JIT-műveletnapló kiválasztása](./media/security-center-just-in-time/jit-select-activity-log.png)

   A műveletnapló az adott virtuális géphez tartozó korábbi műveletek szűrt nézetét, valamint időt, dátumot és előfizetést biztosít.

1. A napló adatainak letöltéséhez válassza a **Letöltés CSV-ként** lehetőséget.








## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan állíthatja be és használhatja a virtuális gépek igény szerinti elérését. A JIT használatának okának megismeréséhez olvassa el a következő témakört:

> [!div class="nextstepaction"]
> [JIT magyarázata](just-in-time-explained.md)