---
title: Gyors útmutató – csapatokhoz való csatlakozás egy iOS-alkalmazásból
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat egy Teams-értekezlethez az iOS-hez készült ügyféloldali kódtárat hívó Azure kommunikációs szolgáltatásokkal
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 68f1abb5335386634874d9696738a8e552ebbe9c
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487993"
---
Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat a Teams Meeting szolgáltatáshoz az iOS-hez készült ügyféloldali kódtárat hívó Azure kommunikációs szolgáltatások használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy működő [kommunikációs szolgáltatás iOS-alkalmazást hív](../getting-started-with-calling.md)meg.
- [Csapatok üzembe helyezése](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>A csapatok felhasználói FELÜLETének vezérlése és a csapatok felhasználói felületének engedélyezése

Cserélje le a ContentView. Swift kódot a következő kódrészletre. A rendszer a Teams Meeting-környezet megadására fogja használni a szövegmezőt, és a gomb a megadott értekezlethez való csatlakozáshoz használható:

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

## <a name="get-the-teams-meeting-link"></a>A Teams Meeting-hivatkozás beszerzése

A Teams Meeting hivatkozás a Graph API-k használatával kérhető le. Ez részletesen szerepel a [Graph dokumentációjában](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
A kommunikációs szolgáltatások meghívójának ügyféloldali könyvtára fogadja a teljes Teams Meeting-hivatkozást. Ezt a hivatkozást a `onlineMeeting` [ `joinWebUrl` tulajdonság](/graph/api/resources/onlinemeeting?view=graph-rest-beta)alatt elérhető erőforrás részeként adja vissza a rendszer. A szükséges értekezlet-információkat a **JOIN Meeting** URL-címében is lekérheti a csapatok Értekezletének meghívásához.

## <a name="launch-the-app-and-join-teams-meeting"></a>Indítsa el az alkalmazást, és csatlakozzon a csapatok Értekezletéhez

Az alkalmazást az iOS-szimulátorban a **termék**  >  **futtatása** vagy a (&#8984;-R) billentyűparancs használatával hozhatja létre és futtathatja.

:::image type="content" source="../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="A befejezett alkalmazást bemutató képernyőkép.":::

Szúrja be a csapatok kontextusát a szövegmezőbe, majd nyomja meg a *Csatlakozás* a csapatok között lehetőséget a Teams Meeting szolgáltatáshoz való csatlakozáshoz a kommunikációs szolgáltatások alkalmazásából.
