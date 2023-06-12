# WordBlitzCheater
## Intro
Do you know the [WordBlitz Game](https://wordblitz.com/) ?, It's a battle of three rounds of 1 mn each where you need to find the maximum of words. It's really a good game, and with my son we love playing it. One day, my son asked me, hey dad, can you hack the game ? At least I can try...

## War Plan
Starting from the supposition the application relies on web services, the idea is to intercept the requests sent by the aplication. If we can programatically, get the the grid , find the bests words and returns theses words to the server then we are done.<br/>. So, is the plan step by step:
* Intercept Requests
* Analyse request, get the grid, find words, send the words to the server.
* Write a robot to play automatically.

## Intercept
The game runs on mobile phone, but obviously we want to intercept the requests on a laptop. Basically we wan to lead a (Man in the middle attack)[https://en.wikipedia.org/wiki/Man-in-the-middle_attack].<br>
For this, we can use the [Burp tool](https://portswigger.net/burp).  and configure it as described [here:](https://portswigger.net/burp/documentation/desktop/mobile/config-ios-device). Basically, we run burp on the laptop as proxy, the tool will intercept the request. On the mobile, we configured the proxy with the laptop IP address and the listening burp port. To make it work, we have a last thing to do. The mobile phone will detect the man of middle attack, because we intercepting the request, burp uses his own certificate to sign the requests, and the burp certificate is not valid for the mobile phone. So we need to trust the burp certificate

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
<img width="907" alt="image" src="https://user-images.githubusercontent.com/20342260/226115450-df9ce31f-297b-4b4f-ab2a-f5654fd545de.png"><br/>
This parameters authenticates our user on the server. If we reuse this parameter, we can run the requests on any device, it means we do not need burp anymore!</br>
As I have already the grid, and all words I wrote a first script to display the grid for the current round and return all words.
Then I improved the program to make it finding the best words !<br/>
<img width="562" alt="image" src="https://user-images.githubusercontent.com/20342260/226115836-00a5751d-ddfb-4762-9fab-f7f80e988b55.png">


And finally, I complete the program to make it playing automatically, up to you to decide how many words you wan to be found!
<br/>
<img width="619" alt="image" src="https://user-images.githubusercontent.com/20342260/226130307-80e2a0ff-0211-411b-b805-b56a4f3bb04e.png">
<br/>
<img width="375" alt="image" src="https://user-images.githubusercontent.com/20342260/226130422-479bdd6b-ca06-4c56-a37e-ef3fbb619606.png">
<br/> Enjoy ;)



## Conclusion
Cheating is bad... :)  Ironically, testing the app, I loose against another cheater, but now you know how it works.


## Make the program working
Install:

    $ pip3.10 install -r requirements.txt
    
You need to retrieve the signature, for exmple using burp and paste-it in the config.json file.

    $ python3.10 main
    


