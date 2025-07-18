---
author: sloanster
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/20/2025
ms.author: micahvivion
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-windows.md)]

### Request access to the microphone

The app requires access to the camera. In Universal Windows Platform (UWP) apps, you need to declare the camera capability in the app manifest file.

1. Open the project in Visual Studio.
1. In the **Solution Explorer** panel, double click on the file with `.appxmanifest` extension.
1. Click on the **Capabilities** tab.
1. Select the `Camera` check box from the capabilities list.

### Create UI buttons to place and hang up the call

This sample app contains two buttons. One for placing the call and another to hang up a placed call.

1. In the **Solution Explorer** panel, double click on the file named `MainPage.xaml` for UWP, or `MainWindows.xaml` for WinUI 3.
2. In the central panel, look for the XAML code under the UI preview.
3. Modify the XAML code using the following excerpt:

```xml
<TextBox x:Name="CalleeTextBox" PlaceholderText="Who would you like to call?" />
<StackPanel>
    <Button x:Name="CallButton" Content="Start/Join call" Click="CallButton_Click" />
    <Button x:Name="HangupButton" Content="Hang up" Click="HangupButton_Click" />
</StackPanel>
```

### Setting up the app with Calling SDK APIs

The Calling SDK APIs are in two different namespaces.

Complete the following steps to inform the C# compiler about these namespaces, enabling Visual Studio's Intellisense to assist with code development.

1. In the **Solution Explorer** panel, click on the arrow on the left side of the file named `MainPage.xaml` for UWP, or `MainWindows.xaml` for WinUI 3.
2. Double click on file named `MainPage.xaml.cs` or `MainWindows.xaml.cs`.
3. Add the following commands at the bottom of the current `using` statements.

```csharp
using Azure.Communication.Calling.WindowsClient;
```

Keep `MainPage.xaml.cs` or `MainWindows.xaml.cs` open. The next step adds more code.

## Enable app interactions

The UI buttons we added need to operate on top of a placed `CommunicationCall`. It means that you must add a `CommunicationCall` data member to the `MainPage` or `MainWindow` class.
You also need to enable the asynchronous operation creating `CallAgent` to succeed. Add a `CallAgent` data member to the same class.

Add the following data members to the `MainPage` or `MainWindow` class:

```csharp
CallAgent callAgent;
CommunicationCall call;
```

## Create button handlers

Previously, we added two UI buttons to the XAML code. The following code adds the handlers to run when a user selects the button.

Add the following code after the data members from the previous section.

```csharp
private async void CallButton_Click(object sender, RoutedEventArgs e)
{
    // Start call
}

private async void HangupButton_Click(object sender, RoutedEventArgs e)
{
    // End the current call
}
```

## Object model

The following classes and interfaces handle some of the major features of the Azure Communication Services Calling client library for UWP.

| Name | Description |
| --- | --- |
| `CallClient` | The `CallClient` is the main entry point to the Calling client library. |
| `CallAgent` | The `CallAgent` is used to start and join calls. |
| `CommunicationCall` | The `CommunicationCall` is used to manage placed or joined calls. |
| `CommunicationTokenCredential` | The `CommunicationTokenCredential` is used as the token credential to instantiate the `CallAgent`.|
| `CallAgentOptions` | The `CallAgentOptions` contains information to identify the caller. |
| `HangupOptions` | The `HangupOptions` informs if a call should be terminated to all its participants. |

## Register video schema handler

A UI component, like XAML's `MediaElement` or `MediaPlayerElement`, require the app to register  a configuration for rendering local and remote video feeds.

Add the following content between the `Package` tags of the `Package.appxmanifest`:

```xml
<Extensions>
    <Extension Category="windows.activatableClass.inProcessServer">
        <InProcessServer>
            <Path>RtmMvrUap.dll</Path>
            <ActivatableClass ActivatableClassId="VideoN.VideoSchemeHandler" ThreadingModel="both" />
        </InProcessServer>
    </Extension>
</Extensions>
```

## Initialize the CallAgent

To create a `CallAgent` instance from `CallClient`, you must use `CallClient.CreateCallAgentAsync` method that asynchronously returns a `CallAgent` object once it's initialized.

To create `CallAgent`, you must pass a `CallTokenCredential` object and a `CallAgentOptions` object. Keep in mind that `CallTokenCredential` throws if a malformed token is passed.

Add the following code inside and helper function so that it runs during initialization.

```csharp
var callClient = new CallClient();
this.deviceManager = await callClient.GetDeviceManagerAsync();

var tokenCredential = new CallTokenCredential("<AUTHENTICATION_TOKEN>");
var callAgentOptions = new CallAgentOptions()
{
    DisplayName = "<DISPLAY_NAME>"
};

this.callAgent = await callClient.CreateCallAgentAsync(tokenCredential, callAgentOptions);
this.callAgent.CallsUpdated += Agent_OnCallsUpdatedAsync;
this.callAgent.IncomingCallReceived += Agent_OnIncomingCallAsync;
```

Change the `<AUTHENTICATION_TOKEN>` with a valid credential token for your resource. For more information about sourcing a credential token, see [user access token](../../../../quickstarts/identity/access-tokens.md).

## Place a 1:1 call with video camera

The objects needed for creating a `CallAgent` are now ready. Then asynchronously create `CallAgent` and place a video call.

```csharp
private async void CallButton_Click(object sender, RoutedEventArgs e)
{
    var callString = CalleeTextBox.Text.Trim();

    if (!string.IsNullOrEmpty(callString))
    {
        if (callString.StartsWith("8:")) // 1:1 Azure Communication Services call
        {
            this.call = await StartAcsCallAsync(callString);
        }
    }

    if (this.call != null)
    {
        this.call.RemoteParticipantsUpdated += OnRemoteParticipantsUpdatedAsync;
        this.call.StateChanged += OnStateChangedAsync;
    }
}

private async Task<CommunicationCall> StartAcsCallAsync(string acsCallee)
{
    var options = await GetStartCallOptionsAsync();
    var call = await this.callAgent.StartCallAsync( new [] { new UserCallIdentifier(acsCallee) }, options);
    return call;
}

var micStream = new LocalOutgoingAudioStream(); // Create a default local audio stream
var cameraStream = new LocalOutgoingVideoStream(this.viceManager.Cameras.FirstOrDefault() as VideoDeviceDetails); // Create a default video stream

private async Task<StartCallOptions> GetStartCallOptionsAsync()
{
    return new StartCallOptions() {
        OutgoingAudioOptions = new OutgoingAudioOptions() { IsMuted = true, Stream = micStream  },
        OutgoingVideoOptions = new OutgoingVideoOptions() { Streams = new OutgoingVideoStream[] { cameraStream } }
    };
}
```

## Local camera preview

We can optionally set up local camera preview. You can render the video through `MediaPlayerElement`:

```xml
<Grid>
    <MediaPlayerElement x:Name="LocalVideo" AutoPlay="True" />
    <MediaPlayerElement x:Name="RemoteVideo" AutoPlay="True" />
</Grid>
```
To initialize the local preview `MediaPlayerElement`:
```csharp
private async void CameraList_SelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (cameraStream != null)
    {
        await cameraStream?.StopPreviewAsync();
        if (this.call != null)
        {
            await this.call?.StopVideoAsync(cameraStream);
        }
    }
    var selectedCamera = CameraList.SelectedItem as VideoDeviceDetails;
    cameraStream = new LocalOutgoingVideoStream(selectedCamera);

    var localUri = await cameraStream.StartPreviewAsync();
    LocalVideo.Source = MediaSource.CreateFromUri(localUri);

    if (this.call != null) {
        await this.call?.StartVideoAsync(cameraStream);
    }
}
```

## Render remote camera stream

Set up even handler in response to `OnCallsUpdated` event:

```csharp
private async void OnCallsUpdatedAsync(object sender, CallsUpdatedEventArgs args)
{
    var removedParticipants = new List<RemoteParticipant>();
    var addedParticipants = new List<RemoteParticipant>();

    foreach(var call in args.RemovedCalls)
    {
        removedParticipants.AddRange(call.RemoteParticipants.ToList<RemoteParticipant>());
    }

    foreach (var call in args.AddedCalls)
    {
        addedParticipants.AddRange(call.RemoteParticipants.ToList<RemoteParticipant>());
    }

    await OnParticipantChangedAsync(removedParticipants, addedParticipants);
}

private async void OnRemoteParticipantsUpdatedAsync(object sender, ParticipantsUpdatedEventArgs args)
{
    await OnParticipantChangedAsync(
        args.RemovedParticipants.ToList<RemoteParticipant>(),
        args.AddedParticipants.ToList<RemoteParticipant>());
}

private async Task OnParticipantChangedAsync(IEnumerable<RemoteParticipant> removedParticipants, IEnumerable<RemoteParticipant> addedParticipants)
{
    foreach (var participant in removedParticipants)
    {
        foreach(var incomingVideoStream in  participant.IncomingVideoStreams)
        {
            var remoteVideoStream = incomingVideoStream as RemoteIncomingVideoStream;
            if (remoteVideoStream != null)
            {
                await remoteVideoStream.StopPreviewAsync();
            }
        }
        participant.VideoStreamStateChanged -= OnVideoStreamStateChanged;
    }

    foreach (var participant in addedParticipants)
    {
        participant.VideoStreamStateChanged += OnVideoStreamStateChanged;
    }
}

private void OnVideoStreamStateChanged(object sender, VideoStreamStateChangedEventArgs e)
{
    CallVideoStream callVideoStream = e.CallVideoStream;

    switch (callVideoStream.StreamDirection)
    {
        case StreamDirection.Outgoing:
            OnOutgoingVideoStreamStateChanged(callVideoStream as OutgoingVideoStream);
            break;
        case StreamDirection.Incoming:
            OnIncomingVideoStreamStateChanged(callVideoStream as IncomingVideoStream);
            break;
    }
}
```

Start rendering remote video stream on `MediaPlayerElement`:

```csharp
private async void OnIncomingVideoStreamStateChanged(IncomingVideoStream incomingVideoStream)
{
    switch (incomingVideoStream.State)
    {
        case VideoStreamState.Available:
            {
                switch (incomingVideoStream.Kind)
                {
                    case VideoStreamKind.RemoteIncoming:
                        var remoteVideoStream = incomingVideoStream as RemoteIncomingVideoStream;
                        var uri = await remoteVideoStream.StartPreviewAsync();

                        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                        {
                            RemoteVideo.Source = MediaSource.CreateFromUri(uri);
                        });

                        /* Or WinUI 3
                        this.DispatcherQueue.TryEnqueue(() => {
                            RemoteVideo.Source = MediaSource.CreateFromUri(uri);
                            RemoteVideo.MediaPlayer.Play();
                        });
                        */

                        break;

                    case VideoStreamKind.RawIncoming:
                        break;
                }

                break;
            }
        case VideoStreamState.Started:
            break;
        case VideoStreamState.Stopping:
            break;
        case VideoStreamState.Stopped:
            if (incomingVideoStream.Kind == VideoStreamKind.RemoteIncoming)
            {
                var remoteVideoStream = incomingVideoStream as RemoteIncomingVideoStream;
                await remoteVideoStream.StopPreviewAsync();
            }
            break;
        case VideoStreamState.NotAvailable:
            break;
    }
}
```

## End a call

Once a call is placed, use the `HangupAsync` method of the `CommunicationCall` object to hang up the call.

Use an instance of `HangupOptions` to inform participants if the call must be terminated.

Add the following code inside `HangupButton_Click`.

```csharp
var call = this.callAgent?.Calls?.FirstOrDefault();
if (call != null)
{
    var call = this.callAgent?.Calls?.FirstOrDefault();
    if (call != null)
    {
        foreach (var localVideoStream in call.OutgoingVideoStreams)
        {
            await call.StopVideoAsync(localVideoStream);
        }

        try
        {
            if (cameraStream != null)
            {
                await cameraStream.StopPreviewAsync();
            }

            await call.HangUpAsync(new HangUpOptions() { ForEveryone = false });
        }
        catch(Exception ex) 
        { 
            var errorCode = unchecked((int)(0x0000FFFFU & ex.HResult));
            if (errorCode != 98) // Sample error code, sam_status_failed_to_hangup_for_everyone (98)
            {
                throw;
            }
        }
    }
}
```

## Run the code

1. Make sure Visual Studio builds the app for `x64`, `x86`, or `ARM64`.
1. Press **F5** to start running the app.
1. Click the **CommunicationCall** button to place a call to the defined recipient.

The first time the app runs, the system prompts the user to grant access to the microphone.
