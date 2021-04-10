---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007892"
---
| Platform          | Projekt bekapcsolva<br/>GitHub                                                                          | Csomag                                                                               | Szerzés<br/>lépések                    | Bejelentkezési felhasználók                                         | Hozzáférés a webes API-khoz                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes verzió<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android rendszerhez](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Gyors útmutató](../articles/active-directory/develop/quickstart-v2-android.md) | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| Android (Kotlin)  | [MSAL Android rendszerhez](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| iOS (Swift/obj-C) | [MSAL iOS és macOS rendszerekre](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> a Microsoft Azure előzetes verziókra [vonatkozó kiegészítő használati feltételek][preview-tos] a *nyilvános előzetes* verzióban lévő tárakra vonatkoznak.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/