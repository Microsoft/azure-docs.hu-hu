---
ms.openlocfilehash: 60023f9b97a2ce68cf5689c800080454768ba40d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007965"
---
| Nyelv/keretrendszer | Projekt bekapcsolva<br/>GitHub                                                                                     | Csomag                                                                               | Szerzés<br/>lépések                        | Bejelentkezési felhasználók                                         | Hozzáférés a webes API-khoz                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes verzió<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| macOS (Swift/obj-C)  | [MSAL iOS és macOS rendszerekre](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> a Microsoft Azure előzetes verziókra [vonatkozó kiegészítő használati feltételek][preview-tos] a *nyilvános előzetes* verzióban lévő tárakra vonatkoznak.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/