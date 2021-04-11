---
title: Az OPC-tár tanúsítvány-szolgáltatásának kezelése – Azure | Microsoft Docs
description: Az OPC-tároló legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak és felhasználói engedélyeinek kezelése.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0829d4b3fca068ddb0db2df53dd635ab7ad80bed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646162"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Az OPC-tár tanúsítvány-szolgáltatásának kezelése

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Ez a cikk az Azure-beli OPC Vault Certificate Management szolgáltatás felügyeleti feladatait ismerteti. A kiállító HITELESÍTÉSSZOLGÁLTATÓ tanúsítványainak megújításával, a visszavont tanúsítványok listájának megújításával, valamint a felhasználói hozzáférés engedélyezésével és visszavonásával kapcsolatos információkat tartalmaz.

## <a name="create-or-renew-the-root-ca-certificate"></a>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozása vagy megújítása

Az OPC-tároló üzembe helyezése után létre kell hoznia a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt. Érvényes kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nélkül nem lehet aláírni vagy kiadni az alkalmazás tanúsítványait. A tanúsítványokat [a tanúsítványok](howto-opc-vault-secure-ca.md#certificates) kezeléséhez ésszerű, biztonságos élettartammal kezelheti. A kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújítása az élettartamának fele után. A megújításakor azt is vegye figyelembe, hogy egy újonnan aláírt alkalmazás tanúsítványának beállított élettartama nem haladhatja meg a kiállító HITELESÍTÉSSZOLGÁLTATÓ tanúsítványának élettartamát.
> [!IMPORTANT]
> A kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozásához vagy megújításához rendszergazdai szerepkör szükséges.

1. Nyissa meg a tanúsítványszolgáltatásokat a (z) helyen `https://myResourceGroup-app.azurewebsites.net` , és jelentkezzen be.
2. Nyissa meg a **Tanúsítványsablonok csoportot**.
3. Egy alapértelmezett tanúsítványsablon van felsorolva. Válassza a **Szerkesztés** elemet.
4. A **tanúsítványkezelő részleteinek szerkesztése** lehetőséggel módosíthatja a hitelesítésszolgáltató és az alkalmazások tulajdonosának nevét és élettartamát. A tulajdonos és az élettartamok csak egyszer állíthatók be az első HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány kiállítása előtt. A műveletek élettartama változása inkonzisztens élettartamot eredményezhet a kiállított tanúsítványokban és a CRL-ben.
5. Adjon meg egy érvényes tárgyat (például: `CN=My CA Root, O=MyCompany, OU=MyDepartment` ).<br>
   > [!IMPORTANT]
   > Ha módosítja a tárgyat, meg kell újítania a kiállítói tanúsítványt, vagy a szolgáltatás nem fogja aláírni az alkalmazás tanúsítványait. A konfiguráció tárgya az aktív kiállítói tanúsítvány tárgyában van bejelölve. Ha a tulajdonosok nem egyeznek, a rendszer elutasítja a tanúsítvány aláírását.
6. Kattintson a **Mentés** gombra.
7. Ha ezen a ponton "tiltott" hibaüzenet jelenik meg, a felhasználói hitelesítő adatai nem rendelkeznek rendszergazdai jogosultsággal az új főtanúsítvány módosításához vagy létrehozásához. Alapértelmezés szerint a szolgáltatást telepítő felhasználó rendelkezik rendszergazdai és aláírási szerepkörökkel a szolgáltatással. Más felhasználókat hozzá kell adni a jóváhagyó, az író vagy a rendszergazdai szerepkörhöz a Azure Active Directory (Azure AD) alkalmazás regisztrációjának megfelelően.
8. Válassza a **részletek** lehetőséget. Ez a frissített információkat jeleníti meg.
9. Válassza a **hitelesítésszolgáltatói tanúsítvány megújítása** lehetőséget az első kiállító hitelesítésszolgáltatói tanúsítvány kiállításához, vagy a kiállítói tanúsítvány megújításához. Ez után válassza az **OK** gombot.
10. Néhány másodperc elteltével látni fogja a **tanúsítvány részleteit**. Ha le szeretné tölteni a legújabb HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és a CRL-t az OPC UA-alkalmazásokba való terjesztéshez, válassza a **kiállító** vagy a **CRL** lehetőséget.

Az OPC UA tanúsítványkezelő szolgáltatás most már készen áll az OPC UA-alkalmazások tanúsítványainak kiállítására.

## <a name="renew-the-crl"></a>A CRL megújítása

A CRL megújítása egy frissítés, amelyet rendszeres időközönként el kell osztani az alkalmazásokban. Az OPC UA-eszközök, amelyek támogatják a CRL terjesztési pont X509 bővítményét, közvetlenül frissíthetik a CRL-t a Service-végpontról. Más OPC UA-eszközökhöz manuális frissítésre lehet szükség, vagy a GDS Server push Extensions (*) használatával frissítheti a megbízhatósági listákat a tanúsítványokkal és a CRL-sel.

A következő munkafolyamatban a törölt állapotokban lévő összes tanúsítványkérelem Visszavonva lesz a CRL-ben, amelyek megfelelnek a kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak, amelynek ki lettek adva. A CRL verziószáma eggyel nő. <br>
> [!NOTE]
> Az összes kiadott CRL érvényes a kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának lejárta előtt. Ennek az az oka, hogy az OPC UA-specifikáció nem igényel kötelező, determinisztikus terjesztési modellt a CRL-hez.

> [!IMPORTANT]
> A kiállítói CRL megújításához rendszergazdai szerepkör szükséges.

1. Nyissa meg a tanúsítványszolgáltatásokat a (z) helyen `https://myResourceGroup.azurewebsites.net` , és jelentkezzen be.
2. Nyissa meg a **Tanúsítványsablonok** lapot.
3. Válassza a **részletek** lehetőséget. Ez a tanúsítvány és a CRL aktuális információit jeleníti meg.
4. Válassza a CRL-visszavonási **lista (CRL) frissítése** lehetőséget, hogy egy frissített CRL-t bocsásson ki az OPC-tároló tárterületén lévő összes aktív kiállító tanúsítványhoz.
5. Néhány másodperc elteltével látni fogja a **tanúsítvány részleteit**. Ha le szeretné tölteni a legújabb HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és a CRL-t az OPC UA-alkalmazásokba való terjesztéshez, válassza a **kiállító** vagy a **CRL** lehetőséget.

## <a name="manage-user-roles"></a>Felhasználó szerepkörök kezelése

Az OPC Vault-szolgáltatáshoz tartozó felhasználói szerepköröket az Azure AD Enterprise alkalmazásban kezelheti. A szerepkör-definíciók részletes ismertetését lásd: [szerepkörök](howto-opc-vault-secure-ca.md#roles).

Alapértelmezés szerint a bérlő hitelesített felhasználója olvasóként is bejelentkezhet a szolgáltatásba. Magasabb jogosultsági szintű szerepkörökhöz manuális felügyelet szükséges a Azure Portalben vagy a PowerShell használatával.

### <a name="add-user"></a>Felhasználó hozzáadása

1. Nyissa meg az Azure Portalt.
2. Nyissa meg **Azure Active Directory**  >  **vállalati alkalmazásokat**.
3. Válassza ki az OPC Vault-szolgáltatás regisztrációját (alapértelmezés szerint a `resourceGroupName-service` ).
4. Nyissa meg a **felhasználók és csoportok** lehetőséget.
5. Válassza a **felhasználó hozzáadása** elemet.
6. Válassza ki vagy hívja meg a felhasználót egy adott szerepkörhöz való hozzárendeléshez.
7. Válassza ki a felhasználók szerepkörét.
8. Válassza a **Hozzárendelés** elemet.
9. A rendszergazdai vagy jóváhagyói szerepkörben lévő felhasználók számára folytassa a Azure Key Vault hozzáférési szabályzatok hozzáadását.

### <a name="remove-user"></a>Felhasználó eltávolítása

1. Nyissa meg az Azure Portalt.
2. Nyissa meg **Azure Active Directory**  >  **vállalati alkalmazásokat**.
3. Válassza ki az OPC Vault-szolgáltatás regisztrációját (alapértelmezés szerint a `resourceGroupName-service` ).
4. Nyissa meg a **felhasználók és csoportok** lehetőséget.
5. Válasszon ki egy felhasználót az eltávolítandó szerepkörrel, majd válassza az **Eltávolítás** lehetőséget.
6. Az eltávolított felhasználók a rendszergazda vagy a jóváhagyó szerepkörben a Azure Key Vault házirendek közül is törlődnek.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Felhasználói hozzáférési házirend hozzáadása Azure Key Vaulthoz

A jóváhagyóknak és a rendszergazdáknak további hozzáférési szabályzatokra van szükségük.

Alapértelmezés szerint a szolgáltatás identitása csak korlátozott engedélyekkel rendelkezik a Key Vaulthoz való hozzáféréshez, hogy megakadályozza a emelt szintű műveleteket vagy a módosításokat a felhasználó megszemélyesítése nélkül. Az alapszintű szolgáltatási engedélyek beolvasása és listázása a titkos kulcsokhoz és a tanúsítványokhoz. A titkok esetében csak egyetlen kivétel van: a szolgáltatás a titkos kulcsokat a felhasználó által elfogadott módon törölheti a titkos tárolóból. Minden egyéb művelethez a felhasználó által megszemélyesített engedélyek szükségesek.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>A jóváhagyó szerepkörhöz a következő engedélyeket kell hozzáadni Key Vault

1. Nyissa meg az Azure Portalt.
2. Nyissa meg az OPC `resourceGroupName` -tárolót, amelyet az üzembe helyezés során használ.
3. Lépjen a Key Vault `resourceGroupName-xxxxx` .
4. Nyissa meg a **hozzáférési házirendeket**.
5. Válassza az **új hozzáadása** elemet.
6. A sablon kihagyása. Nincs olyan sablon, amely megfelel a követelményeknek.
7. Válassza a **résztvevő kiválasztása** lehetőséget, majd válassza ki a hozzáadni kívánt felhasználót, vagy hívjon fel egy új felhasználót a Bérlőnek.
8. Válassza ki a következő **fő engedélyeket**: **lekérés**, **Listázás** és **aláírás**.
9. Válassza ki a következő **titkos engedélyeket**: **beolvasás**, **Listázás**, **beállítás** és **Törlés**.
10. Válassza ki a következő **tanúsítvány-engedélyeket**: **lekérés** és **Listázás**.
11. Válassza **az OK**, majd a **Mentés** lehetőséget.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Rendszergazdai szerepkör esetén a következő engedélyeket kell hozzáadni a Key Vault

1. Nyissa meg az Azure Portalt.
2. Nyissa meg az OPC `resourceGroupName` -tárolót, amelyet az üzembe helyezés során használ.
3. Lépjen a Key Vault `resourceGroupName-xxxxx` .
4. Nyissa meg a **hozzáférési házirendeket**.
5. Válassza az **új hozzáadása** elemet.
6. A sablon kihagyása. Nincs olyan sablon, amely megfelel a követelményeknek.
7. Válassza a **résztvevő kiválasztása** lehetőséget, majd válassza ki a hozzáadni kívánt felhasználót, vagy hívjon fel egy új felhasználót a Bérlőnek.
8. Válassza ki a következő **fő engedélyeket**: **lekérés**, **Listázás** és **aláírás**.
9. Válassza ki a következő **titkos engedélyeket**: **beolvasás**, **Listázás**, **beállítás** és **Törlés**.
10. Válassza ki a **következő tanúsítvány-engedélyeket**: **beolvasás**, **Listázás**, **frissítés**, **Létrehozás** és **Importálás**.
11. Válassza **az OK**, majd a **Mentés** lehetőséget.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Felhasználói hozzáférési házirend eltávolítása Azure Key Vault

1. Nyissa meg az Azure Portalt.
2. Nyissa meg az OPC `resourceGroupName` -tárolót, amelyet az üzembe helyezés során használ.
3. Lépjen a Key Vault `resourceGroupName-xxxxx` .
4. Nyissa meg a **hozzáférési házirendeket**.
5. Keresse meg az eltávolítandó felhasználót, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az OPC-tároló tanúsítványainak és a felhasználók felügyeletét, a következőket teheti:

> [!div class="nextstepaction"]
> [Az OPC-eszközök biztonságos kommunikációja](howto-opc-vault-secure.md)
