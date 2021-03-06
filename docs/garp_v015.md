
# GARP V0.1.5

## Impact
* We have introduced a protocol id handling for every call. This allows us to evolve the
SW without really breaking backwards compatibility.
    * v0.1.5 corresponds protocolId:2  
    If You use any of our modules like
        * Construct3
        * Javastript modules    
  
    You do not need to care for the procotolId as it will be handled by the underlaying layers.
      
* New message [GameInfoRequest](#GameInfoRequest)

Which SW modules are available can be
found [here](gamedev_modules/). 


## Overview
The following picture provides a functional relation of request and responses.

## Terminology
In the next paragraph we will use 

    <WHATEVER> as a place holder for WHATEVER    
## AuthenticatedConnectRequest
### Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>

### Functionality:  
This function is a mandatory start of all communication with the websocket.
It is invoked with a websocket address url constructed in the following way:

    url="wss://wss.atheios.org/ws/<GAMETOKEN>/<PROTOCOLID>"
where 

    <GAMETOKEN> Tokenid from portal.atheios.org
    <PROTOCOLID> Protocol id

Once that has happened there will be a a back and forth communication
ensuring that the game is authenticated. If the communication is successful
the wss protocol will reply a session ID which is stored as a state.

### Successful case:  
The case is successful when the GARP protocol returns a session ID

    {
        "@class": ".AuthenticatedConnectResponse",
        "sessionId": "2pfwp-ZXFp3-Krun0-rc17Y-NO5Jg",
        "requestId": "0"
    }

### Error cases:  
A single error case can occure, when the <GAMETOKENID> cannot be found.

    {
    	"@class": ".AuthenticatedConnectResponse",
    	"error": "Unknown GameID ",
    	"requestId ": "0"
    }

## GameInfoRequest
### Availablility:  
<button type="button" class="btn btn-primary">0.1.5</button>

### Functionality:
This function can be called after AuthenticatedConnectRequest and the 
response provides game related information which could be displayed at the 
login window.   

### Request
The websocket message sent has the following JSON format:

    {
    	"@class": ".GameInfoRequest",
        "protocolId": 2,
    	"apikey": "<GAMETOKENID>",
    	"requestId": "1590177530798_1"
    }
 

### Successful case:  
The case is successful when the GARP protocol returns game related data

    {
        "@class": ".GameInfoResponse",
        "sessionId": "2pfwp-ZXFp3-Krun0-rc17Y-NO5Jg",
        "gameId": "8",
        "wage": "1",
        "player1": "6",
        "player2": "3",
        "player3": "1",
        "player4": "0",
        "player5": "0",
        "scheme": "0.8",
        "description": "This is the description You added in the portal for the game",
        "requestId": "1590177530798_1"
    }

and also game related information like:

    wage:   Mandated wage value
    scheme: How much of the pot is distributed to the payers
    player1... player5: Distribution between players  

### Error cases:  
A single error case can occure, when the <GAMETOKENID> cannot be found.

    {
    	"@class": ".AuthenticatedConnectResponse",
    	"error": "Unknown GameID ",
    	"requestId ": "0"
    }

## AuthenticateUser 
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>

### Functionality:  
This request sends user credentials for validation by the portal.

### Request
The websocket message sent jas the following JSON format:

    {
    	"@class": ".AuthenticationRequest",
        "protocolId": 2,
    	"username": "<USERNAME>",
    	"userpass": "<USERPASSWORD>",
    	"apikey": "<GAMETOKENID>",
    	"requestId": "1590177530798_1"
    }
    
### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".AuthenticationResponse",
      "authToken" : "Qt4pzdX3JQtM8kZwFUjWoZ5d",
      "displayName" : "Lars",
      "newPlayer" : false,
      "userId" : "3",
      "gameId" : "27",
      "wage" : "1",
      "requestId" : "1590177530798_1"
    }
    
The response provides essential information for the game as the display name, userId, 
gameId and the wage to be used for the game play.

### Error cases:  
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

## AccountDetailsRequest
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>

### Functionality:  
After validation the user id is available and more information can be requested.

### Request
The websocket message sent jas the following JSON format:

    {
        "@class": ".AccountDetailsRequest",
        "protocolId": 2,
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"userId": "1",
    	"requestId": "1590221675618_2"
    }
    
### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".AccountDetailsResponse",
      "value" : 163,
      "currency" : "ATH",
      "requestId" : "1590221675618_2"
    }   
     
The response provides essential information for the game as value of available currency.
This amount needs to be checked when the user makes a wage.

### Error cases:  
In case the authentication is not matching:

    {
    	"@class": ".AccountDetailsResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Athentication not successful."
    	},
    	"requestId": "1590177530798_1"
    }
    
    
## SetWageRequest
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>

### Functionality:  
This request sets the wage and should be the trigger for game start. This will trigger
on the portal side to move the wage value from the user hot account to the gaming pot.

### Request
The websocket message sent jas the following JSON format:

    {
    	"@class": ".SetWageRequest",
        "protocolId": 2,
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"gameId": "27",
    	"userId": "1",
    	"apiKey": "KpcxJ-mkxhV-nunAs-KbItV-FbNTV",
    	"wage": "1",
    	"requestId": "1590221699730_3"
    }

### Successful case:  
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



### Error cases:  
In case the authentication is not matching:

    {
    	"@class": ".SetWageResponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Authentication is failing."
    	},
    	"requestId": "1590221699845_3"
    }

## FinishGameRequest
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>

### Functionality:  
This request ends a game and reposts the score. 

### Request
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
### Successful case:  
The successful response is rendered according to the following:

    {
      "@class" : ".FinishGameResponse",
      "authToken" : "CvZCaiXMcHVqk0uwh0b9VVOB",
      "status" : "OK",
      "requestId" : "1590304599414_8"
    }     
The response provides the reduced account value, but also the playId, which is the started 
game. Note that the portal tracks the time for the game.



### Error cases:  
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


## GameLadderRequest
Availablility:  
<button type="button" class="btn btn-primary">0.1.4</button>

### Functionality:  
This request triggers a game ladder request. 

### Request
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
        
### Successful case:  
The successful response is rendered according to the following:

    {
    	"@class": ".GameLadderResponse",
    	"authToken": "CvZCaiXMcHVqk0uwh0b9VVOB",
    	"requestId": "1590265383100_5",
    	"totalGames": 31,
    	"inPlay": 3,
    	"timeLeft": 43636,
    	"ladder": [{
    		"position": "3",
    		"score": "2759",
    		"displayname": "The big boss",
    		"amount": "1",
    		"date": "2020-05-23"
    	}, {
    		"position": "1",
    		"score": "4707",
    		"displayname": "DuckMyTruck",
    		"amount": "1",
    		"date": "2020-05-18"
    	}, {
    		"position": "2",
    		"score": "3541",
    		"displayname": "Lars",
    		"amount": "1",
    		"date": "2020-05-19"
    	}, {
    		"position": "3",
    		"score": "2759",
    		"displayname": "DuckMyTruck",
    		"amount": "1",
    		"date": "2020-05-18"
    	}]
    }
     


### Error cases:  
In case the authentication is not matching:

    {
    	"@class": ".GameLadderponse",
    	"authToken": "Qt4pzdX3JQtM8kZwFUjWoZ5d",
    	"error": {
    		"details": "Authentication is failing."
    	},
    	"requestId": "1590221699845_3"
    }





