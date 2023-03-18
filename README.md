# WordBlitzCheater
## Intro
Do you know this game ?, It's a battle of three rounds of 1 mn where you need to find the maximum of words. It's really a good game, and with my son we love playing this game. One day, my son asked me, hey dad, can you hack the game ? At least I can try...

## Methodology
What I have in mind, it to intercept the request containing the grid, compute the bests words and returns theses words to the server.<br/>
The first thing is to understand how the game works. I suppose the game send some http requests, so, what I need is to intercept these las ones.But how to intercept these requests?, The game runs on my mobile phone, and I want to read thes requests on my laptop. So, I need to lead a man of middle attack.
For this, I used the excellent [Burp tool](https://portswigger.net/burp) and configure it as described [here:](https://portswigger.net/burp/documentation/desktop/mobile/config-ios-device). Basically, we run burp on the laptop as proxy, the tool will intercept the request. On the mobile, we configured the proxy with the laptop IP address and the listening burp port. To make it work, we have a last thing to do. The mobile phone will detect the man of middle attack, because we intercepting the request, burp uses his own certificate to sign the requests, and the burp certificate is not valid for the mobile phone. So we need to trust the burp certificate

## Request analysis
After the proxy configured, and the burp proxy listening the request, I started a game with wordblitz to analyze how it works.
We got the following result:
<img width="1498" alt="image" src="https://user-images.githubusercontent.com/20342260/226113936-08f2d0a3-d10b-4484-a30b-11495490324c.png">
What a bunch o requests! After sorting them, three are quite intersting:
* *v2/game/list* returns all the games
* *v2/game/start* starts a round
* *v2/game/play* closes a round

#### Analysing more deeper the *v2/game/list*, I discovered the request is really juicy !
* First, when a new game is created, the game contains already the three rounds, meaning we have all grids in advanced.<br/><img width="698" alt="image" src="https://user-images.githubusercontent.com/20342260/226114816-89e2ae22-bf2e-44b3-a68e-36e755b5d068.png">
* Moreover, we have all available words in advance !<br/><img width="538" alt="image" src="https://user-images.githubusercontent.com/20342260/226114929-19bd1cb6-cd7c-4fb9-b259-d0a0a621c9c3.png">

#### The *v2/game/start* request, just starts a game with the the round id and a timer.
<img width="906" alt="image" src="https://user-images.githubusercontent.com/20342260/226115296-35291aed-c990-4607-b936-4ee38432ad37.png">


#### And finally the request *v2/game/play* sends all path found in json form with an url encoding:<br/>
<img width="914" alt="image" src="https://user-images.githubusercontent.com/20342260/226115088-fa3d1f9e-3e64-4ab5-ad41-b4436c033454.png"><br/>
<img width="1011" alt="image" src="https://user-images.githubusercontent.com/20342260/226115150-7e2cc3bb-75e1-4ad6-aa45-a6b72568817d.png">


## Exploiting the requests
Before exploiting the program, we can get the signature parameter provided as url parameter for each request.<br/>
<img width="907" alt="image" src="https://user-images.githubusercontent.com/20342260/226115450-df9ce31f-297b-4b4f-ab2a-f5654fd545de.png">
This parameters authenticates our user on the server. If we reuse this parameter, we can run the requests on any device, it means we do not need burp anymore!</br>
As I have already the grid, and all words I wrote a first script to display the grid for the current round and return all words.
Then I improved the program to make it computing what are the best words !
<img width="562" alt="image" src="https://user-images.githubusercontent.com/20342260/226115836-00a5751d-ddfb-4762-9fab-f7f80e988b55.png">


And finally, I complete the program to make it playing automatically, up to you to decide how many words you wan to be found!




