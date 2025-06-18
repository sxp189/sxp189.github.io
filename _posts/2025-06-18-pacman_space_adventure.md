---
title: "How I Built a Retro Game Using Amazon Q CLI"
date: 2025-06-18
---

Reading children's books with my son about building simple games in JavaScript has been a fun experience, but I've never actually created a game myself. So when an email from AWS landed in my inbox announcing the 'Build Games Challenge,' I saw it as the perfect opportunity to step outside my comfort zone and try something new.

### The Set Up ###
I've never used Amazon Q CLI before, so I followed the steps in [Ricardo Sueiras's blog](https://community.aws/content/2v5PptEEYT2y0lRmZbFQtECA66M/the-essential-guide-to-installing-amazon-q-developer-cli-on-windows?trk=e07eca93-fa2f-4351-b567-f293b83eb635&sc_channel=el_) and installed Amazon Q CLI. 

Once that was done, I explored the AWS Community forum, looking for some quick start videos. I came across another blog on [Getting started with Amazon Q Developer in Visual Studio Code](https://community.aws/content/2fVw1hN4VeTF3qtVSZHfQiQUS16/getting-started-with-amazon-q-developer-in-visual-studio-code) that showed me how to add the Amazon Q CLI extension in VS Code, so I gave this a try. The instructions were easy to follow, and I was ready to go in less than 1 minute.

### Chatting with Amazon Q ###
Without having any folders open in VS Code, I opened a new chat and entered the following prompt and pressed enter on the keyboard:
``Create a game that features Pacman flying a spaceship. Include multiplication problems that the player must solve to earn bonus points.``

As Amazon Q worked through this task, it required me to make approval decisions for situations like: allowing read-only tools outide the current workspace, creating a directory, etc. Amazon Q began by creating the main HTML for the game, followed by CSS before moving on to the JavaScript file. Upon encountering errors, Amazon Q undid changes and broke down the JavaScript file creation into smaller parts. 

Within just minutes, the game was created and ready to play. The only problem was that I could not locate the directory containing the game files. So, I asked Amazon Q the following question:
``In the “mkdir -p pacman_space_game” step, where did you create the directory?``

After locating and showing me the directory under which the game files were created, Amazon Q looked for a better location and created a new directory under my home directory. I had thought that Amazon Q would have moved the game files under my home directory. Instead, it seemed to have restarted the entire game development process. Even though it restarted the game development process, there were fewer errors, and the process went quicker.

Upon finishing the game development files a second time, Amazon Q informed me where the game files were located. I was able to locate it easily and test the game in my web browser.

![Screenshot of player having 1 life left in the Pacman Space Adventure Game with a multiplication problem to solve.](https://github.com/sxp189/pacman_space_game/blob/main/pacman_space_game.png)

### Generating Documentation ###
Using the /doc command, I asked Amazon Q to generate documentation for the Pacman Space Adventure game that was just built. This resulted in an error because I didn't have my directory open in VS Code. After opening the directory containing the game files, I was able to use the /doc command to generate a ReadMe file.
