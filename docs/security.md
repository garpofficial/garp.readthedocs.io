## Security aspects
### Clients can't be trusted
To be upfront, hackers can re-engineer any client. So if You have a game which is running purely 
on a client only, any score being submitted from a client can be faked.

We have taken actions in oder to minimise possible fraud, but when the code is running only
on a client there is no way to fully trust anyone. That is different to if You run the game on a 
server. In that case the score is generated there and cannot be compromised (unless You hacl into the server ofcourse).

In any case the security measures we have taken are the following:

- Scores can only be submitted by registered users
- Each submission needs to be waged
- Sequential state machine for score submission
- Blacklisting mechanism for potential offenders
- Whitelisting check for submission

### Servers can be trusted
Thinks are different when You have a server implementation of a gave with a JS front end.
Then You can ensure that the submission of the score calculation and submission happens from the server side 
and tampering is not possible