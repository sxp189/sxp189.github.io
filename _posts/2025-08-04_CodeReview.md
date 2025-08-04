---
title: "Code Review of a Retro Game Built Using Amazon Q CLI"
date: 2025-08-04
---
Five days ago, I started this blog to document my thought process in reviewing my 328 lines of code for the [Pacman Space Adventure](https://github.com/sxp189/pacman_space_game/blob/main/game.js) game to see if there are any improvements I’d like to make.

### [Lines 1 through 9](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L1-L9) | Game Setup and Variables ###
````
// Game canvas setup
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');


// Game variables
let score = 0;
let lives = 3;
let gameActive = true;
let mathProblemActive = false;
````
In lines 1 through 9 above, we’re setting up the game canvas and initializing some variables. I wonder if the ‘2d’ in line 3 indicates whether or not this is a 2d vs 3d game. This is a question for Amazon Q. In line 7, I think it’s fair to start with 3 lives. I’m thinking of increasing it to 5 lives so an average user can get a little more play time. In line 9, I see that mathProblemActive is initially set to ‘false’; I wonder what condition or set of events trigger it to become ‘true’ during gameplay. I’m hopeful we’ll find the answer to this later on in the code.

### [Lines 12 through 22](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L12-L22) | Create Pacman and Spaceship ###
````
// Pacman spaceship
const pacman = {
    x: canvas.width / 2,
    y: canvas.height - 100,
    width: 50,
    height: 50,
    speed: 5,
    dx: 0,
    dy: 0,
    image: new Image()
};
pacman.image.src = 'data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><circle cx="50" cy="50" r="45" fill="yellow"/><polygon points="50,50 100,25 100,75" fill="black"/></svg>';
````
In lines 12 through 22, we’re constructing our Pacman. From test playing the game, I think the size of Pacman is decently visible to the player. Pacman really isn’t sitting inside a spaceship, it’s just Pacman itself.

### [Lines 24 through 35](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L24-L35) | Create Game Objects ###
````
// Enemies
const enemies = [];
const enemySpeed = 2;
const enemySpawnRate = 0.02;


// Bullets
const bullets = [];
const bulletSpeed = 7;


// Power-ups
const powerUps = [];
const powerUpSpawnRate = 0.005;
````
From lines 24 through 35, we’re constructing the enemies, bullets and power-ups. I’m considering decreasing the spawn rate by 0.01 and increasing the power-up spawn rate by 10 fold. As for bullet speed, I may increase it to 10 and see if there’s any noticeable difference in the player experience.

### [Lines 37 through 42](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L37-L42) | Set Up Math Problem Variables and Controls ###
````
// Math problem variables
let currentProblem = {};
let bonusPoints = 0;


// Controls
const keys = {};
````
In lines 37 through 42, we’re initializing 2 more variables and setting up the controls for the player. No changes needed here.

### [Lines 44 through 56](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L44-L56) | Setup Event Listeners ###
````
// Event listeners
document.addEventListener('keydown', (e) => {
    keys[e.key] = true;
    
    // Spacebar to shoot
    if (e.key === ' ' && gameActive && !mathProblemActive) {
        shootBullet();
    }
});


document.addEventListener('keyup', (e) => {
    keys[e.key] = false;
});
````
In lines 44 through 56, we’re creating a way for the game to collect player keystrokes from the keyboard. The Player can only shoot bullets if there is no active math problem displayed on the screen. Lines 54 through 56 are telling the game to stop listening when the key of a keyboard is no longer pressed down.

### [Lines 58 - 59](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L58-L59) | Player Response Submission ###
````
// Submit answer button
document.getElementById('submitAnswer').addEventListener('click', checkAnswer);
````
Lines 58 - 59 executes the functions to collect the response from the player along with the click when the player clicks on the ‘Submit’ button to submit their answer. The checkAnswer function is called to verify correctness of the answer to the math problem.

### [Lines 61 through 77](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L61-L77) | Create Math Problem ###
````
// Generate a random math problem
function generateMathProblem() {
    const num1 = Math.floor(Math.random() * 10) + 1;
    const num2 = Math.floor(Math.random() * 10) + 1;
    const answer = num1 * num2;
    
    currentProblem = {
        question: `${num1} × ${num2} = ?`,
        answer: answer,
        bonusPoints: num1 * num2 * 10
    };
    
    document.getElementById('question').textContent = currentProblem.question;
    document.getElementById('answer').value = '';
    document.getElementById('mathProblem').style.display = 'block';
    mathProblemActive = true;
}
````
Lines 61 through 77 create a function to generate a multiplication problem. It looks like num1 and num2 are set to be randomly generated numbers from 1 to 9. I can think of a few ways to make this a little more challenging:
- In lines 63 and 64, change the ‘10’ to ‘13’ to include numbers up through (and including) 12.
- Remove the ‘+1’ following the Math.floor() function to include 0s.
- In line 68, find a way to randomly generate the math operation symbol to alternate between multiplication and division. This would be a question for Amazon Q.
- In line 70, I think I’d set bonusPoints to ‘50’. It doesn’t make sense to me to have the amount of bonus points be dependent on the answer to the math problem.

It looks like the mathProblemActive variable gets changed from ‘false’ to ‘true’ once this generateMathProblem() function is called.

### [Lines 79 through 90](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L79-L90) | Check Player Response ###
````
// Check the player's answer
function checkAnswer() {
    const playerAnswer = parseInt(document.getElementById('answer').value);
    
    if (playerAnswer === currentProblem.answer) {
        score += currentProblem.bonusPoints;
        document.getElementById('score').textContent = score;
    }
    
    document.getElementById('mathProblem').style.display = 'none';
    mathProblemActive = false;
}
````
Lines 79 through 90 define the function for checking a player’s answer to the math problem. Line 81 records the integer of the answer in the playerAnswer variable. Lines 83 through 86 compare the player’s answer to the correct response, then adds bonus points to the player’s existing score if the player answered the math problem correctly. The player’s score is then updated. The mathProblemActive variable is switched back to ‘false’ to let the player return to the game.

### [Lines 92 through 102](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L92-L102) | Create an Enemy ###
````
// Create an enemy
function createEnemy() {
    const enemy = {
        x: Math.random() * (canvas.width - 30),
        y: -30,
        width: 30,
        height: 30,
        color: 'red'
    };
    enemies.push(enemy);
}
````
Lines 92 through 102 define the function to create the enemy. These appear as red circles, which can be difficult to distinguish from the green power-ups. We may want to consider acquiring an image for the enemies, but we can keep the red color.

### [Lines 104 through 114](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L104-L114) | Create Power-Up ###
````
// Create a power-up
function createPowerUp() {
    const powerUp = {
        x: Math.random() * (canvas.width - 20),
        y: -20,
        width: 20,
        height: 20,
        color: 'green'
    };
    powerUps.push(powerUp);
}
````
Lines 104 through 114 define the function to create the power-ups. I think I’d want to change the shape of the power-ups to a different image, perhaps a flask-shaped image.

### [Lines 116 through 126](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L116-L126) | Create Bullet Shooting Function ###
````
// Shoot a bullet
function shootBullet() {
    const bullet = {
        x: pacman.x + pacman.width / 2 - 2.5,
        y: pacman.y,
        width: 5,
        height: 10,
        color: 'yellow'
    };
    bullets.push(bullet);
}
````
Lines 116 through 126 define the function for the player to shoot bullets at the enemy and/or acquire power-ups. I think the size is good for now. No changes needed here.

### [Lines 128 - 130](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L128-L130) | Setting Up Update() Function ###
````
// Update game objects
function update() {
````
This next function ‘update()’ contains multiple conditions to update multiple game objects, which include:
- Move Pacman
- Move bullets
- Spawn enemies
- Move enemies
- Spawn power-ups
- Move power-ups

### [Lines 132 through 144](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L132-L144) | Moving Pacman ###
````
    // Move Pacman
    if (keys['ArrowLeft'] && pacman.x > 0) {
        pacman.x -= pacman.speed;
    }
    if (keys['ArrowRight'] && pacman.x < canvas.width - pacman.width) {
        pacman.x += pacman.speed;
    }
    if (keys['ArrowUp'] && pacman.y > 0) {
        pacman.y -= pacman.speed;
    }
    if (keys['ArrowDown'] && pacman.y < canvas.height - pacman.height) {
        pacman.y += pacman.speed;
    }
````
Lines 132 through 144 instruct the game to move the Pacman based on the x and y coordinates depending on which keys are pressed by the player. The “.speed” was defined back in line 17. Default speed was set to 5.

### [Lines 146 through 153](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L146-L153) | Moving the bullets ###
````
    // Move bullets
    bullets.forEach((bullet, index) => {
        bullet.y -= bulletSpeed;
        
        // Remove bullets that go off screen
        if (bullet.y < 0) {
            bullets.splice(index, 1);
        }
````
Lines 146 through 153 define the conditions for moving bullets. These bullets are shot vertically, as indicated in line 148. Perhaps this can also be modified to shoot bullets at a 45 degree angle, if desired. In lines 150 through 153, removing bullets that go off screen makes sense. No changes needed there.

### [Lines 156 through 159](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L156-L159) | Spawning the Enemies ###
````
    // Spawn enemies
    if (Math.random() < enemySpawnRate) {
        createEnemy();
    }
````
Lines 156 through 159 set up the condition to spawn enemies using the createEnemy() function. This looks good for now.

### [Lines 161 through 168](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L161-L168) | Moving the Enemies ###
````
    // Move enemies
    enemies.forEach((enemy, index) => {
        enemy.y += enemySpeed;
        
        // Remove enemies that go off screen
        if (enemy.y > canvas.height) {
            enemies.splice(index, 1);
        }
````
Lines 161 through 168 set up the equation to move enemies down the screen at the same rate as Pacman (moving 1 unit at a time on the x, y coordinates). We’ll keep this for now. In the future, we may want to change the rate based on the player advancing to a higher level in the game, provided that we introduce leveling up in this game.

### [Lines 171 through 174](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L171-L174) | Spawning the Power-Ups ###
````
    // Spawn power-ups
    if (Math.random() < powerUpSpawnRate) {
        createPowerUp();
    }
````
Lines 171 through 174 sets up the condition to generate power-ups using the createPowerUp() function, which I would assume is defined elsewhere in the game script.

### [Lines 176 through 183](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L176-L183) | Moving the Power-Ups ###
````
    // Move power-ups
    powerUps.forEach((powerUp, index) => {
        powerUp.y += enemySpeed;
        
        // Remove power-ups that go off screen
        if (powerUp.y > canvas.height) {
            powerUps.splice(index, 1);
        }
````
Lines 176 through 183 set up the condition to move the power-ups, similar to the previous sections where we move enemies and Pacman. It looks like, based on line 178, we’re moving the power-ups at the same rate as the enemySpeed (reference to line 163). Just like with all other moving game objects, we’re removing any power-ups that go off screen in lines 180 through 183.

### [Lines 186 through 188](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L186-L187) | Checking for Collisions ###
````
    // Check for collisions
    checkCollisions();
````
Finally, we check for any object collisions by calling the checkCollisions() function in lines 186 through 188. By now, we should have a collection of coordinates for each game object.

### [Lines 192 through 210](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L190-L210) | Checking for Collisions Between Bullet and Enemy ###
````
// Check for collisions between game objects
function checkCollisions() {
    // Check bullet-enemy collisions
    bullets.forEach((bullet, bulletIndex) => {
        enemies.forEach((enemy, enemyIndex) => {
            if (isColliding(bullet, enemy)) {
                // Remove bullet and enemy
                bullets.splice(bulletIndex, 1);
                enemies.splice(enemyIndex, 1);
                
                // Increase score
                score += 10;
                document.getElementById('score').textContent = score;
                
                // Trigger math problem with 10% chance
                if (Math.random() < 0.1) {
                    generateMathProblem();
                }
            }
        });
    });
````
This next block of code defines the checkCollisions() function to detect collisions between all game objects. In lines 192 through 210, we now set up loops and conditions to check for collections between the bullets and the enemies. For every bullet (a for loop), we’re checking every enemy (nested for loop) to see if there is a collision between the bullet and the enemy, by calling the isColliding() function. For every bullet that collides with an enemy, the player receives 10 points, and the score is updated. If the randomly generated percentage is less than 10%, a math problem will display on the screen, asking the player to answer the math problem. We could increase this to 30% in line 205 to give the player more opportunities to score bonus points.

### [Lines 212 through 228](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L212-L228) | Checking for Collisions Between Pacman and Enemies ###
````
    // Check pacman-enemy collisions
    enemies.forEach((enemy, index) => {
        if (isColliding(pacman, enemy)) {
            // Remove enemy
            enemies.splice(index, 1);
            
            // Decrease lives
            lives--;
            document.getElementById('lives').textContent = lives;
            
            // Check game over
            if (lives <= 0) {
                gameActive = false;
                alert('Game Over! Your score: ' + score);
            }
        }
    });
````
Lines 212 through 228 check for collisions between Pacman and any of the enemies. If a collision is detected based on the isColliding() function, the number of lives is decreased by 1 and updated in the lives count (lines 218 through 220). Once the player has 0 lives, the gameActive is switched to ‘false’, and a ‘Game Over’ alert is displayed on the screen along with the final score. No changes needed here at the moment.

### [Lines 230 through 239](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L230-L239) | Checking for Collisions between Pacman and Power-Ups ###
````
    // Check pacman-powerup collisions
    powerUps.forEach((powerUp, index) => {
        if (isColliding(pacman, powerUp)) {
            // Remove power-up
            powerUps.splice(index, 1);
            
            // Trigger math problem
            generateMathProblem();
        }
    });
````
Lines 230 through 239 of this last part of the checkCollisions() function checks for collisions between Pacman and power-ups. Like previously, any bullet, enemy, or power-up is removed in the collision (line 234). Then, the math problem is triggered. I think this is good for now. No changes here.

### [Lines 242 through 248](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L242-L248) | Defining isColliding() Function ###
````
// Check if two objects are colliding
function isColliding(obj1, obj2) {
    return obj1.x < obj2.x + obj2.width &&
           obj1.x + obj1.width > obj2.x &&
           obj1.y < obj2.y + obj2.height &&
           obj1.y + obj1.height > obj2.y;
}
````
Lines 242 through 248 define a very useful isColliding function that checks when any two game objects collide. For our purposes, we do not have situations where more than 2 objects would collide at once, so I think this is good for now.

### [Lines 250 through 271](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L250-L271) | Drawing Game Objects: Background ###
````
// Draw game objects
function draw() {
    // Clear canvas
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    
    // Draw background (space)
    ctx.fillStyle = 'black';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    
    // Draw stars
    ctx.fillStyle = 'white';
    for (let i = 0; i < 100; i++) {
        ctx.beginPath();
        ctx.arc(
            Math.random() * canvas.width,
            Math.random() * canvas.height,
            Math.random() * 2,
            0,
            Math.PI * 2
        );
        ctx.fill();
    }
````
This next section draws the game objects. Lines 252 - 253 clears the canvas. Lines 255 - 257 then fills the canvas with a black background. Then, stars are drawn in lines 259 through 271.

### [Lines 273 through 280](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L273-L280) | Drawing Game Objects: Pacman and Bullets ###
````
    // Draw Pacman spaceship
    ctx.drawImage(pacman.image, pacman.x, pacman.y, pacman.width, pacman.height);
    
    // Draw bullets
    ctx.fillStyle = 'yellow';
    bullets.forEach(bullet => {
        ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
    });
````
In lines 273 - 274, we draw Pacman. I am curious if I could custom draw an image, save it to a location in my github, and then reference it via url. This is lower in terms of priority, and I may revisit this later. Lines 276 through 280 draw the bullets that the player will use to blast the enemies in the game. I think the rectangular shape works for now. No changes needed here so far.

### [Lines 282 through 294](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L282-L294) | Drawing Game Objects: Enemies ###
````
    // Draw enemies
    ctx.fillStyle = 'red';
    enemies.forEach(enemy => {
        ctx.beginPath();
        ctx.arc(
            enemy.x + enemy.width / 2,
            enemy.y + enemy.height / 2,
            enemy.width / 2,
            0,
            Math.PI * 2
        );
        ctx.fill();
    });
````
Lines 282 through 295 draw the enemies. I think we can ask Amazon Q to add 2 eyeballs to each enemy.

### [Lines 296 through 309](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L296-L309) | Drawing Game Objects: Power-Ups ###
````
    // Draw power-ups
    ctx.fillStyle = 'green';
    powerUps.forEach(powerUp => {
        ctx.beginPath();
        ctx.arc(
            powerUp.x + powerUp.width / 2,
            powerUp.y + powerUp.height / 2,
            powerUp.width / 2,
            0,
            Math.PI * 2
        );
        ctx.fill();
    });
}
````
Lines 296 through 309 draw the power-ups. I’m thinking we can make these power-ups a different shape. I wonder if we can draw a hexagon or octagon.

### [Lines 311 through 318](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L311-L318) | Defining gameLoop() Function ###
````
// Game loop
function gameLoop() {
    if (gameActive) {
        update();
        draw();
    }
    requestAnimationFrame(gameLoop);
}
````
Lines 311 through 318 define the gameLoop() function that calls the update() and draw() functions as long as gameActive is set to ‘true’.

### [Lines 320 through 328](https://github.com/sxp189/pacman_space_game/blob/60e075792ababd0742e6019c20453e6551191d39/game.js#L320-L328) | Starting the Game ###
````
// Start the game when the page loads
window.onload = function() {
    // Initialize the game
    document.getElementById('score').textContent = score;
    document.getElementById('lives').textContent = lives;
    
    // Start the game loop
    gameLoop();
};
````
Finally, lines 320 through 328 set up the game once the user opens the game file. The ‘score’ and ‘lives’ variables are initiated, and the game loop starts in line 327.
