# WordBlitzCheater
## Intro
Do you know this game ?, It's a battle of three rounds of 1 mn where you need to find the maximum of words. It's really a good game, and with my son we love playing this game. One day, my son asked me, hey dad, can you hack the game ? At least I can try...

## Methodology
What I have in mind, it to intercept the request containing the grid, compute the bet words and returns theses words to the server.
The first thing is to understand how the game works. I suppose the game send some http requests, so, what I need is to intercept these las ones.But how to intercept these requests?, The game runs on my mobile phone, and I want to read thes requests on my laptop. So, I need to lead a man of middle attack.
For this, I used the excellent [Burp tool](https://portswigger.net/burp) and configure it as described [here:](https://portswigger.net/burp/documentation/desktop/mobile/config-ios-device)
