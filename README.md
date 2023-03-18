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
