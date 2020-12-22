# GARP V0.3.0

This version is not yet released and corresponds protocol id 4

## Impact
* Introduction of multiplayer functionality into the GARP framework. Multiplayers are 
brought together via rooms. 
      
* New message [RoomCreateRequest](#RoomCreateRequest)
* New message [RoomJoinRequest](#RoomJoinRequest)
* New message [LeaveRoomRequest](#LeaveRoomRequest)
* New message [QueryRoomRequest](#QueryRoomRequest)
* New message [ResolveRoomRequest](#ResolveRoomRequest)

* New message [MessageGameRequest](#MessageGameRequest)

Which SW modules are available can be
found [here](gamedev_modules/). 


## Overview
The following picture provides a functional relation of request and responses.

## Terminology
In the next paragraph we will use 

    <WHATEVER> as a place holder for WHATEVER    
    
The availability paragraph indicated when a message has been introduced (first entry) and when it has been
updated (2nd and following entries).

<button type="button" class="btn btn-primary">0.1.4</button>
<button type="button" class="btn btn-primary">0.2.0</button>

means then introduced in 0.1.4 and updated in 0.2.0.

## General request
### AuthenticatedConnectRequest
#### Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>

#### Functionality:  
This function is a mandatory start of all communication with the websocket.
It is invoked with a websocket address url constructed in the following way:

    url="wss://wss.atheios.org/ws/<GAMETOKEN>/<PROTOCOLID>"
where 

    <GAMETOKEN> Tokenid from portal.atheios.org
    <PROTOCOLID> Protocol id

Once that has happened there will be a a back and forth communication
ensuring that the game is authenticated. If the communication is successful
the wss protocol will reply a session ID which is stored as a state.

#### Successful case:  
The case is successful when the GARP protocol returns a session ID

    {
        "@class": ".AuthenticatedConnectResponse",
        "sessionId": "2pfwp-ZXFp3-Krun0-rc17Y-NO5Jg",
        "requestId": "0"
    }

#### Error cases:  
A single error case can occure, when the <GAMETOKENID> cannot be found.

    {
    	"@class": ".AuthenticatedConnectResponse",
    	"error": "Unknown GameID ",
    	"requestId ": "0"
    }

### GameInfoRequest
#### Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>
<button type="button" class="btn btn-secondary">0.2.0</button>

#### Functionality:
This function can be called after AuthenticatedConnectRequest and the 
response provides game related information which could be displayed at the 
login window. It contains game data, including how many games are currently ongoing, 
which is a valuable teaser for Your login / registeration screen.   

#### Request
The websocket message sent has the following JSON format:

    {
    	"@class": ".GameInfoRequest",
        "protocolId": 2,
    	"apikey": "<GAMETOKENID>",
    	"requestId": "1590177530798_1"
    }
 

#### Successful case:  
The case is successful when the GARP protocol returns game related data

    {
        "@class": ".GameInfoResponse",
        "sessionId": "2pfwp-ZXFp3-Krun0-rc17Y-NO5Jg",
        "gameId": "8",
        "wage": "1",
        "currency": "ATH|ETHO",
        "player1": "6",
        "player2": "3",
        "player3": "1",
        "player4": "0",
        "player5": "0",
        "scheme": "0.8",
        "description": "This is the description You added in the portal for the game",
        "ongoinggames": "3",
        "requestId": "1590177530798_1"
    }

and also game related information like:

    wage:   Mandated wage value
    currency: String of supported currencies seperated by '|'
    player1... player5: Distribution between players  
    scheme: How much of the pot is distributed to the payers
    

#### Error cases:  
A single error case can occure, when the <GAMETOKENID> cannot be found.

    {
    	"@class": ".AuthenticatedConnectResponse",
    	"error": "Unknown GameID ",
    	"requestId ": "0"
    }

### AuthenticateUser 
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>
<button type="button" class="btn btn-primary">0.2.0</button>
<button type="button" class="btn btn-primary">0.3.0</button>

#### Functionality:  
This request sends user credentials for validation by the portal.

#### Request
The websocket message sent jas the following JSON format:

    {
    	"@class": ".AuthenticationRequest",
        "protocolId": 2,
    	"username": "<USERNAME>",
    	"userpass": "<USERPASSWORD>",
    	"apikey": "<GAMETOKENID>",
    	"requestId": "1590177530798_1"
    }
    
#### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".AuthenticationResponse",
      "authToken" : "Qt4pzdX3JQtM8kZwFUjWoZ5d",
      "displayName" : "Lars",
      "newPlayer" : false,
      "userId" : "3",
      "gameId" : "27",
      "wage" : "1",
      "currency": "ATH",
      "room": 
      [
        {
          "roomid": "30",      
          "roomname": "Crypto chess",
          "roomserial": "zMiEH3CSqYsxLH5wv62r88Om4c4A0Ekq6RqK5ySOlnV7tGhrl5DpOgSNW9EVY9pE"
        },
        {
          "roomid": "31",
          "roomname": "Crypto chess",
          "roomserial": "piG1k2JTNqim0Mp64VxpU7H5AHy1M0DPS6AgvRGBlz77EWEjl4tfhgz9kHt0iueb"
         }
      ],
      "requestId" : "1590177530798_1"
    }
    
    
The response provides essential information for the game as the display name, userId, 
gameId and the wage to be used for the game play.

The room statement contains a json with 0 up to max number of rooms associated with the game.
If there are rooms allicated, then it contains the roomid, the roomname, and a 64bit serial 
called roomserial.


#### Error cases:  
In case the api key is not matching:

    {
    	"@class": ".AuthenticationResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "API key not matching."
    	},
    	"requestId": "1590177530798_1"
    }
    
In case of a user / password mismatch: 

    {
        "@class": ".AuthenticationResponse",
        "authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
        "error": {
            "details": "User password unrecognized ."
        },
        "requestId": "1590177530798_1"
    }

### AccountDetailsRequest
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>
<button type="button" class="btn btn-secondary">0.2.0</button>

#### Functionality:  
After validation the user id is available and more information can be requested.

#### Request
The websocket message sent jas the following JSON format:

    {
        "@class": ".AccountDetailsRequest",
        "protocolId": 2,
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"userId": "1",
    	"requestId": "1590221675618_2"
    }
    
#### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".AccountDetailsResponse",
      "value" : ["163", "25"],
      "currency" : ["ATH","ETHO"],
      "requestId" : "1590221675618_2"
    }   
     
The response provides essential information how much value of currencies is available.
This amount needs to be checked when the user makes a wage.

#### Error cases:  
In case the authentication is not matching:

    {
    	"@class": ".AccountDetailsResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Athentication not successful."
    	},
    	"requestId": "1590177530798_1"
    }


    
### SetWageRequest
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>
<button type="button" class="btn btn-secondary">0.2.0</button>

#### Functionality:  
This request sets the wage and should be the trigger for game start. This will trigger
on the portal side to move the wage value from the user hot account to the gaming pot.

#### Request
The websocket message sent jas the following JSON format:

    {
    	"@class": ".SetWageRequest",
        "protocolId": 2,
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"gameId": "27",
    	"userId": "1",
    	"apiKey": "KpcxJ-mkxhV-nunAs-KbItV-FbNTV",
    	"wage": "1",
        "currency": "ATH",
    	"requestId": "1590221699730_3"
    }

#### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".SetWageResponse",
      "authToken" : "CvZCaiXMcHVqk0uwh0b9VVOB",
      "value" : 162,
      "playid" : 353,
      "gameid" : 27,
      "currency" : "ATH",
      "status" : "OK",
      "reason" : "",
      "requestId" : "1590221699730_3"
    }
     
The response provides the reduced account value, but also the playId, which is the started 
game. Note that the portal tracks the time for the game.



#### Error cases:  
In case the authentication is not matching:

    {
    	"@class": ".SetWageResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Authentication is failing."
    	},
    	"requestId": "1590221699845_3"
    }

### FinishGameRequest
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>

#### Functionality:  
This request ends a game and reposts the score. 

#### Request
The websocket message sent jas the following JSON format:

    {
        "@class": ".FinishGameRequest",
        "protocolId": 2,
        "authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
        "userId": "1",
        "playId": "364",
        "scoreValue": "1237",
        "requestId": "1590304599414_8"
    }
#### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".FinishGameResponse",
      "authToken" : "CvZCaiXMcHVqk0uwh0b9VVOB",
      "status" : "OK",
      "requestId" : "1590304599414_8"
    }     
The response provides the reduced account value, but also the playId, which is the started 
game. Note that the portal tracks the time for the game.



#### Error cases:  
In case the authentication is not matching:

    {
    	"@class": ".FinishGameResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Authentication is failing."
    	},
    	"requestId": "1590221699845_3"
    }

In case the playId is not set:

    {
    	"@class": ".FinishGameResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "PlayId not properly set."
    	},
    	"requestId": "1590221699845_3"
    }


### GameLadderRequest
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>
<button type="button" class="btn btn-secondary">0.2.0</button>

#### Functionality:  
This request triggers a game ladder request. 

#### Request
The websocket message sent jas the following JSON format:

    {
        "@class": ".GameLadderRequest",
        "protocolId": 2,
        "authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
        "userId": "1",
        "playId": "353",
        "gameId": "27",
        "requestId": "1590265383100_5"
    }        
        
#### Successful case:  
The successful response is rendered according to the following:

    {
    	"@class": ".GameLadderResponse",
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"requestId": "1590265383100_5",
    	"totalGames": 31,
    	"inPlayATH": 2,
    	"inPlayETHO": 1,
        "currency": "ATH",
    	"timeLeft": 43636,
    	"ladder": [{
    		"position": "3",
    		"score": "2759",
    		"displayname": "DuckMyTruck",
    		"amount": "1",
    		"currency": "ATH",
    		"date": "2020-05-23"
    	}, {
    		"position": "1",
    		"score": "4707",
    		"displayname": "DuckMyTruck",
    		"amount": "1",
            "currency": "ATH",
    		"date": "2020-05-18"
    	}, {
    		"position": "2",
    		"score": "3541",
    		"displayname": "Lars",
    		"amount": "1",
            "currency": "ATH",
    		"date": "2020-05-19"
    	}, {
    		"position": "3",
    		"score": "2759",
    		"displayname": "DuckMyTruck",
    		"amount": "1",
            "currency": "ETHO",
    		"date": "2020-05-18"
    	}]
    }
     


#### Error cases:  
In case the authentication is not matching:

    {
    	"@class": ".GameLadderponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Authentication is failing."
    	},
    	"requestId": "1590221699845_3"
    }

## Room management  
The room management makes really sense only for multiuser games. A room can be created
and functionality is provided so that rooms can created, joined, left, closed, locked or 
queried. The functions only provide IDs and names, You need to provide the logic for a 
chat room for instance, or when a game is started that different IDs will then really
play with each other.
  
### CreateRoomRequest
Availablility:  
<button type="button" class="btn btn-primary">0.3.0</button>

#### Functionality:  
After validation the user id is available and a room can be created. This is used for
games requiring a room with a chat functionality. The request delivers handles to
access the room and associated chat.

#### Request
The websocket message is sent with the following JSON format:

    {
        "@class": ".RoomCreateRequest",
        "protocolId": 4,
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"userId": "1",
    	"gameId": "4",
    	"requestId": "1590221675618_2"
    }
    
#### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".RoomCreateRequestResponse",
      "roomName" : "CryptoChess",
      "roomId" : "25",
      "roomState": "1",
      "roomSeats": "2",
      "requestId" : "1590221675618_2"
    }   
     
The response provides the created room handle and the number of seats provided.
This is being set by the game configuration in the portal, like it wouldn't make 
sense to have for a chess game more users tied up then 2.  

#### Error cases:  
In case no room can be created:

    {
    	"@class": ".RoomCreateRequestResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Room cannot be created."
    	},
    	"requestId": "1590177530798_1"
    }

### JoinRoomRequest
Availablility:  
<button type="button" class="btn btn-primary">0.3.0</button>

#### Functionality:  
After validation the user id is available and a room can be joined. This is 
used for games requiring a room with a chat functionality. The request joins an 
existing room. The k

#### Request
The websocket message is sent with the following JSON format:

    {
        "@class": ".JoinRoomRequest",
        "protocolId": 4,
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"userId": "1",
    	"roomId": "4",
    	"requestId": "1590221675618_2"
    }
    
#### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".JoinRoomRequestResponse",
      "roomName" : "CryptoChess",
      "roomId" : "25",
      "roomState": "1",
      "requestId" : "1590221675618_2"
    }   
     
The response provides the created room handle.

#### Error cases:  
In case no room can be created:

    {
    	"@class": ".JoinRoomRequestResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Room cannot be joined."
    	},
    	"requestId": "1590177530798_1"
    }

### LockRoomRequest
Availablility:  
<button type="button" class="btn btn-primary">0.3.0</button>

#### Functionality:  
The user can close a room to limit the number of participants.
This message does just that.

#### Request
The websocket message is sent with the following JSON format:

    {
        "@class": ".LockRoomRequest",
        "protocolId": 4,
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"userId": "1",
    	"roomId": "4",
    	"requestId": "1590221675618_2"
    }
    
#### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".LockRoomRequestResponse",
      "roomName" : "CryptoChess",
      "roomId" : "25",
      "roomState": "2",
      "requestId" : "1590221675618_2"
    }   
     
The response provides the created room handle.

#### Error cases:  
In case no room can be created:

    {
    	"@class": ".LockRoomRequestResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Room cannot be joined."
    	},
    	"requestId": "1590177530798_1"
    }
    
### CloseRoomRequest
Availablility:  
<button type="button" class="btn btn-primary">0.3.0</button>

#### Functionality:  
After room creation or joining a room the user can send a message 
which is then sent to all participants in the room.

#### Request
The websocket message is sent with the following JSON format:

    {
        "@class": ".SendChatRequest",
        "protocolId": 4,
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"userId": "1",
    	"roomId": "25"
    	"msg": "4",
    	"requestId": "1590221675618_2"
    }
    
#### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".SendChatRequestResponse",
      "requestId" : "1590221675618_2"
    }   
     
The response provides the created room handle.

#### Error cases:  
In case no room can be created:

    {
    	"@class": ".SendChatRequestResponse",
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVO",
    	"error": {
    		"details": "Message cannot be sent."
    	},
    	"requestId": "1590221675618_2"
    }
    




