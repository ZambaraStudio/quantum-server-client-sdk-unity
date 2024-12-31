# Quantum Client Unity

Quantum is a new networking solution created to simplify the creation of online multiplayer games.

## Features
* work in progress...

## Dependencies
work in progress...

## Installation
work in progress...

## Sample Code
```c#
using UnityEngine;
using Quantum;

public class ClientExample : MonoBehaviour
{
    private Client _client;
    
    private void Awake()
    {    
        var _clientConfig = new ClientConfig()
        {
            Url = "http://127.0.0.1:3005/gateway"
        }
        
        _client = new Client(_clientConfig);
        _client.OnGenericMessage += OnGenericMessage;
        _client.Connect(new Dictionary<string, string>
        {
            { "authToken", Convert.ToString(Random.Range(128, int.MaxValue)) }
        });
        _client.SendMessage(new GenericMessage()
        {
            type = "greetings-message",
            data = "Hello World"
        });
    }
    
    private void OnGenericMessage(GenericMessage message)
    {
       switch (message.type)
       {
           case "Ping":
           _client.SendMessage(new GenericMessage()
           {
               type = "ping-response-message",
               data = "Pong"
           });
           break;
       }
    }   
}
```

### ClientConfig
ClientConfig is a struct that stores data......
```C#
var _clientConfig = new ClientConfig()
{
    Url = "http://127.0.0.1:3005/gateway"
}
```
 #### Properties
- Url
- MessageDelay
- SerializationSettings
- EnableLogs

### Client
Client is a main class and is responsible for receiving and sending messages.

```c#
_client = new Client(_clientConfig);
_client.OnGenericMessage += OnGenericMessage;
_client.Connect(new Dictionary<string, string>
{
    { "authToken", Convert.ToString(Random.Range(128, int.MaxValue)) }
});
_client.SendMessage(new GenericMessage()
{
    type = "greetings-message",
    data = "Hello World"
});
```
 #### Callbacks
- OnConnected
- OnReconnected
- OnDisconnected
- OnAnyEvent\<QuantumEvent>
- OnGenericMessage\<GenericMessage>
- Log\<string>

 #### Methods
 - Connect(object Auth)
 - SendMessage(GenericMessage message)

 #### Properties
- Ping


## Helper Methods

### ExecuteOnMainThread

Quantum client runs on a separate thread, which makes it difficult to run Unity-specific methods, like Instantiate.
For cases like this, we have the ExecuteOnMainThread class.

```c#
 private void OnGenericMessage(GenericMessage message)
 {
    switch (message.type)
    {
        case "spawn-character-message":
        var characterInfo = message.GetData<CharacterInfo>();
        
        ExecuteOnMainThread.RunOnMainThread.Enqueue(() =>
        {                    
            var character = Instantiate(CharacterPrefab, characterInfo.Position, characterInfo.Rotation);
        });
        break;
    }
 }
```
<p>To use it, you need to add the ExecuteOnMainThread script to a gameobject in the scene.</p>
