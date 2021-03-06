# Battleship

#### <img src="https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/resources/cloud.svg" width="35" height="35"/> The application is deployed in Heroku and can be viewed [here](https://battlefield-spring.herokuapp.com/#/battleboard).

A web-based **multiplayer** battleship game that uses Spring in the backend and Angular in the front. MongoDB was used for persisting the data. Players communicate with each other using Websockets. The project follows Model-View-Control Design Pattern. Technologies used at a glance:

- Spring Boot 2.1.4
- Angular 7.2.12
- MongoDB 4.0.8
- Websockets used for web-app conversations.
- RESTful API used for client-server conversations.
- Bootstrap for the design.
- Spring dependencies can be checked [here](https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/battleship/pom.xml).
- Angular dependencies can be checked [here](https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/battleship-front/package.json).

## Documentation
- :newspaper: The documentation for the Spring Back-End can be seen [here](https://battlefield-spring.herokuapp.com/doc/backend/index.html).
- :newspaper: The documentation for the Angular Front-End can be seen [here](https://battlefield-spring.herokuapp.com/doc/frontend/index.html).

## Play the game
To play the game, open your broser at http://localhost:4200/battleboard after compiling the game.

## Compiling the game ![Build Status](https://travis-ci.org/google/gson.svg?branch=master)
The folder `battleship` contains the spring boot server, and `battleship-front` has the angular front-end files. The spring project uses `maven` as it's package manager, were as the angular project uses `yarn` as it's package manager.

### To run the server

- In the project folder, run:
    1. `mvn clean install`
    2. `mvn spring-boot:run`

The Spring server should now be listening at http://localhost:8080

### To run the application
- In the project folder, run:
    1. `yarn upgrade`
    2. `yarn run start`

This will start the angular server. The game can be played at http://localhost:4200/battleboard

#### Prerequisits
- Make sure `Angular`, `node`, `npm`, `maven` is installed.
- As the angular project uses `yarn` as a package manager, make sure it is [installed](https://yarnpkg.com/lang/en/docs/install).


## Time Taken
Total time taken to build the game was 14-15 hours.

---
## Spring Boot Backend
The project follows MVC Design Pattern. The `model` package contains all the models. Cookies or Sessions are not used. The server persists the game data in it's database, and remembers the players via the websocket messages.
The models that are persisted in the Mongo Database are:

- **GameInstance** Contains all the data regarding a particular player, such as player id, current game situation, number of ships attacked in the current situation, number of times won, etc. Also contains other models such as `Ships`, `Battleboard`, which in turn contains 64 instances of `Battlecell`.
- **PlayerMatches** Contains a unique id and two player id's of the players playing or played currently. This unique id is later used for unique socket connections between both the players via the server.

The controller is responsible for communicating with the angular application at the front-end via websockets and REST calls. There are two controllers in this project:

- **BattleController** Responsible for communicating with the client via REST api and JSON Objects. The API's developed are:
    1.  **getAllItems()**: To get all the player data for making a score-table.
    2.  **initPlayer1()**: To initialize the first player board and generate random ship positions. It takes `username` as the parameter.
    3.  **getUserName()**: To get the username based on `userID`.
    4.  **getPlayerTwoName()**: This command returns the player 2 name to be displayed in the player 1 board, via the unique `socket-id` from the `PlayerMatches` model.
    5.  **initPlayer2()**: To initialize the second player. It takes `username` and the `socket-id`, which was generated when `player1` was initialized. Note that this, although a REST api, currently resides in the `WebSocketController` class, which will be discussed. This will be updated in future versions.

- **WebSocketController** Responsible for communication between the web-apps via websocket. It primarily has just one method, the `processMessageFromClient()`. It is responsible for all the communication between the clients. Both the clients are subscribed to this socket and send all the information, such as whether the player board is initialized or if the player has taken her turn, etc. Both the client subscribe to the same socket.

Further, the `Repository` package contains repositories for the models persisted in MongoDB that were discussed above. The `config` class contains other configurations for websocket. The other class `Security Config` will contain security configurations, which have not been implemented fully yet. The `application.properties` file in the `resources` folder contains other configurations, such as MongoDB location, etc. The `logics` package contains program logic. Although a lot of processing happens in the `GameInstance` model itself, the `logics` package is responsible for converting the `GameInstance` into a more consise format that can be easily read when sent to the clients.

### The Class Diagram

![BattleShip-Server Class Diagram](https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/resources/class.svg)
The yellow nodes represent packages and blue nodes represent classes.

### MongoDB Relationship Diagram
![MongoDB Diagram](https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/resources/database.png)

---
## Angular Front-End

### Overview
![Overview](https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/resources/overview.png)

### Routes
![Routes](https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/resources/routes.svg)

The front-end was built in angular as it makes it easier and organized for bigger applications. Although Battleship is a small game, dividing various parts into components will make it easy to upgrade in future. There are three components in this applicaton:

1. BattleBoard,
2. BattleBoard2, and
3. ScoreTable


Both `BattleBoard` and `BattleBoard2` have the same design and almost same functions. They are the main web-app for player1 and player2 respectively. The game always works in pairs; when the first component for player1 is instantiated, it contains a link, which when clicked, instantiates the second component for player2. `ScoreTable` is a component used to display scores of the players.

### GUI and Services
- The application is built with a minimal design where the left part contains the player's board with her ship positions that were initialized randomly. Further, the right half contains an empty board through which the other player can be attacked by clicking a particular cell. ![Game-View](https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/resources/gameView.png)

- The applications initially requests a username, which is then used as a unique-id. If a user uses the same name in another instance, their win-count will be updated in the repository. ![Welcome-Page](https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/resources/welcomePage.png)

- The third component displays the scorecard. It contains three columns displaying the user-name, the number of times they have lost the game and won the game. For a user to have their name in the score-card, they should have, at least once completed the game fully. ![Score-Table](https://github.com/prasannjeet/springboot-angular-mongo-battleship/blob/master/resources/scoreTable.png)

- Additionally, the application has one service, `BattleService` that handles all the REST api calls. Websocket communications are handled by component themselves. The application uses `Routing` to toggle between different components in the application using pseudo URL's generated.

---
## Features
- Apart from the enemy's board, users can also see their own board and the ship positions. 
- The enemy attacks on a player's board are also marked. 
- If the enemy attacks correctly on a ship, a "fire" symbol appears in that positions.
- A scorecard, as mentioned above, is also implemented.
- Users can also see an error message if, for any reason, the websockets connection is lost with the server.

## Game Workflow
Following are the sequence of events in the perspective of an end user that take place in one instance of the game: Assuming the players are John and Jane,
1. John lands on the first page where he enters his username and submits.
2. John can then see two boards on his screen. The left one represents his own board with randomly placed ships, and the right one represents Jane's (note that Jane is yet to join the game) empty board, as he cannot se the her ship positions.
3. John can then see a *link* at the bottom of the screen that he can share over the internet with Jane who can play against him.
4. The link can then be opend by Jane in her own browser. The entry screen will be similar to what John sees: A form where she can enter her username, followed by the screen where two boards are present, as discussed in point 2 above. Further, she can also see the username of John at the bottom of the screen, as it's John against whom she is playing.
5. As soon as Jane enters her username, it will then be visible to John at the bottom of his screen, mentioning that she hs joined the game.
6. Jane gets the chance to play first. She is allowed to attack at any of the 64 blocks ofn the *right* side of the screen. Any block attacked will then be visible in the *left* side of the John's screen as well, marked in *red*. However, if a she is successful in attacking a block where John's ship is present, a *fire* icon can be seen instead of *red* color, and this will be reflected to both the players.
7. The first player to successfully attack all the ships at all positions wins the game.
8. The link to view the Score Table can be seen at the bottom of the screen for both the players.

## Limitations
One or many of these limitations could have been resolved with more time in hand.

- The board is set randomly for all the players currently.
- No validations have been done in the text boxes. As a result, the game might fail if username contains characters other than alphabets and numerals.
- ~~Currently, the game will run only in localhost.~~
- The game is now deployed [here](https://battlefield-spring.herokuapp.com/#/battleboard) after a few tweaks in server routing configurations.
- No tests have been implemented yet.
- There is no AI opponent currently.
- The design of the application can be improved.
- If the browser is closed, or the user gets disconnected from the server, there is no way to go back to the game.
- If the second player loggs in with the same username as the first player, the game might not function correctly. At the moment, there is no validation built for this.
- Welcome message, instructions to play the game are not yet available.

## Improvements
- The two components for playing a game can be clubbed into one, as most of the functionalities, and design are identical.

## Design Inspirations
- The battleship board design was inspired from the chessboard design discussed [here](https://stackoverflow.com/questions/39008253/draw-chessboard-with-frame-with-pure-html-and-css) by `caco` and `pol`. This was later extensively modified, and was also adapted fro Angular.
- The idea for two-columned game layout was taken from [here](https://codepen.io/lukemeyrick/pen/gppveo), made by `Luke Meyrick`.
- The design for scorecard table was taken from [here](https://codepen.io/ivillamil/pen/jWjgzE), made by `Ivan Villamil`.
- Websites such as `angular.io`, `spring.io`, `stackoverflow.com` were extensively referenced.