---
title: Rövid útmutató – Csatlakozás Teams-értekezlethez iOS-alkalmazásból
description: Ez az oktatóanyag bemutatja, hogyan csatlakozhat Teams-értekezlethez az iOS-Azure Communication Services hívó SDK-val
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 363799cee5d66b718bb8ba06f4afd442add15148
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564715"
---
Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat Teams-értekezlethez az iOS-hez készült Azure Communication Services hívó SDK-val.

## <a name="prerequisites"></a>Előfeltételek

- Egy működő [Communication Services, amely iOS-alkalmazást hív meg.](../getting-started-with-calling.md)
- Egy [Teams-üzemelő példány.](/deployoffice/teams-install)


## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>A Teams felhasználói felület vezérlőinek hozzáadása és a Teams felhasználói felület vezérlőinek engedélyezése

Cserélje le a ContentView.swift kódját a következő kódrészletre. A szövegmezőben megadjuk a Teams-értekezlet környezetét, és a gomb használatával csatlakozunk a megadott értekezlethez:

```swift

import SwiftUI
import AzureCommunicationCalling
import AVFoundation

struct ContentView: View {
    @State var meetingLink: String = ""
    @State var callStatus: String = ""
    @State var message: String = ""
    @State var recordingStatus: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var callObserver: CallObserver?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Teams meeting link", text: $meetingLink)
                    Button(action: joinTeamsMeeting) {
                        Text("Join Teams Meeting")
                    }.disabled(callAgent == nil)
                    Button(action: leaveMeeting) {
                        Text("Leave Meeting")
                    }.disabled(call == nil)
                    Text(callStatus)
                    Text(message)
                    Text(recordingStatus)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
            var userCredential: CommunicationTokenCredential?
            do {
                userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
            } catch {
                print("ERROR: It was not possible to create user credential.")
                self.message = "Please enter your token in source code"
                return
            }

            self.callClient = CallClient()

            // Creates the call agent
            self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
                if error == nil {
                    guard let agent = agent else {
                        self.message = "Failed to create CallAgent"
                        return
                    }

                    self.callAgent = agent
                    self.message = "Call agent successfully created."
                } else {
                    self.message = "Failed to create CallAgent with error"
                }
            }
        }
    }

    func joinTeamsMeeting() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let joinCallOptions = JoinCallOptions()
                let teamsMeetingLinkLocator = TeamsMeetingLinkLocator(meetingLink: self.meetingLink);
                guard let call = self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) else {
                    self.message = "Failed to join Teams meeting"
                    return
                }

                self.call = call
                self.callObserver = CallObserver(self)
                self.call!.delegate = self.callObserver
                self.message = "Teams meeting joined successfully"
            }
        }
    }

    func leaveMeeting() {
        if let call = call {
            call.hangup(options: nil, completionHandler: { (error) in
                if error == nil {
                    self.message = "Leaving Teams meeting was successful"
                } else {
                    self.message = "Leaving Teams meeting failed"
                }
            })
        } else {
            self.message = "No active call to hanup"
        }
    }
}

class CallObserver : NSObject, CallDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func onCallStateChanged(_ call: Call!,
                                   args: PropertyChangedEventArgs!) {
        owner.callStatus = CallObserver.callStateToString(state: call.state)
        if call.state == .disconnected {
            owner.call = nil
            owner.message = "Left Meeting"
        } else if call.state == .inLobby {
            owner.message = "Waiting in lobby !!"
        } else if call.state == .connected {
            owner.message = "Joined Meeting !!"
        }
    }
    
    public func onIsRecordingActiveChanged(_ call: Call!, args: PropertyChangedEventArgs!) {
        if (call.isRecordingActive == true) {
            owner.recordingStatus = "This call is being recorded"
        }
        else {
            owner.recordingStatus = ""
        }
    }

    private static func callStateToString(state: CallState) -> String {
        switch state {
        case .connected: return "Connected"
        case .connecting: return "Connecting"
        case .disconnected: return "Disconnected"
        case .disconnecting: return "Disconnecting"
        case .earlyMedia: return "EarlyMedia"
        case .hold: return "Hold"
        case .incoming: return "Incoming"
        case .none: return "None"
        case .ringing: return "Ringing"
        case .inLobby: return "InLobby"
        default: return "Unknown"
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

## <a name="get-the-teams-meeting-link"></a>A Teams-értekezlet hivatkozásának lehívása

A Teams-értekezlet hivatkozása Graph API-k használatával olvasható be. Ennek részletes leírását a [Graph dokumentációjában találhatja](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)meg.
A Communication Services hívó SDK egy teljes Teams-értekezlethivatkozást fogad el. A hivatkozás az erőforrás részeként lesz `onlineMeeting` visszaadva, amely a tulajdonság alatt [ `joinWebUrl` érhető el.](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) A szükséges értekezletadatokat a Teams-értekezlet meghívásában található **Join Meeting** URL (Csatlakozás az értekezlet URL-címéhez) url-címből is le tudja szerezni.

## <a name="launch-the-app-and-join-teams-meeting"></a>Indítsa el az alkalmazást, és csatlakozzon a Teams-értekezlethez

Alkalmazását iOS-szimulátoron a Product Run (Termékfuttassa) vagy a  >   (&#8984;-R) billentyűparancs használatával is felépítheti és futtathatja.

:::image type="content" source="../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="Képernyőkép a kész alkalmazásról.":::

Szúrja be a Teams-környezetet a szövegmezőbe, majd nyomja le a *Join Teams Meeting* (Teams-értekezlethez való csatlakozás) gombot, hogy csatlakozzon a Teams-értekezlethez a Communication Services alkalmazásból.
