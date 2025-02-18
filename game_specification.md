# SlowCTF (Game Design Document)

## Overview

A web-based, turn-based strategy game with a CTF-like objective. The goal is to reach the opponent's base, touch it, then return to your own base to score.

## Core Gameplay

- Players have a single controllable character
- The player can:
  - Move
  - Attack walls to break them down
  - Attack obstacles to slowly break them down
  - Build new wall blocks
- Objective:
  - Reach the enemy base, touch it, and return to your own base to score
- Turn-based:
  - Players issue orders within a set time limit; that is, the game will wait for the turn time limit for an order and if it receives none, it's a no-op for that player
  - Actions resolve sequentially based on timestamp order (e.g. if one player tries to build a wall in space x17, y38 and another player is trying to move to the same space, whomever has an older timestamp will win)
- Wall: has 3 hit points when built
- Obstacle: has 9 hit points when generated at the start of the game
- Player can use a turn to attack a wall or obstacle to decrease its hit points by 1
- Player can use a turn to build a wall
- Each team can only build a certain number of total wall segments. The number is \`(((game grid height) \* (game grid height)) / 2) / 4\` -- or, in other words, 1/4 of that team's grid squares
- Scoring: a player needs only to stand next to the enemy base to "pick up the flag"
- Building a wall: 'b' key followed by mouse click for where to place wall; 

## Map & Environment

- Regular grid layout
- Y axis squares shall be an odd total number because there will be a center line of squares with a tinted color to indicate the center of the grid
- Players start on opposite sides of the grid
- Obstacles (which act similarly to player-created walls but have much higher hit points) are randomly generated at game start

## Player Identity & Anti-Cheat Measures

- How the engine decides which team to put you on:
  - If there's a team with fewer points, put player on that team
  - If that's tied, then put them on the team with fewer players
  - If that's still tied, then randomly assign

* Players are identified by nickname + 10-character hash in parens, e.g. "PlayerGuy (f661c6fe2e)"
  - Hash is derived from IP address + screen resolution
* Banning is based on IP address + screen resolution

## UI Column Content

- Left:
  - Game intro and help text
- Center:
  - Game grid
- Right:
  - Red and Blue team scores
  - Player leaderboard: shows how many points each player has scored for their team
  - Wall build points remaining for team
  - Previous game Red and Blue team scores
  - Previous game player leaderboard

## Screen Refresh & turn Processing

- Polling mechanism: The client checks the server every 5 seconds for updated game state
- Game length is 4 Hours
- Turn time limit is 30 Seconds
- Turns execute strictly in order of the timestamp they were received on on the server

## Backend & Tech Stack

- Frontend: React + HTML5 Canvas
- Backend: Express.js REST API
- Hosting: EC2 instance
- Data Storage: In-memory structures only, no database

## Deployment

- Uses GitHub Actions to pull new code upon a branch push
- Uses pm2 to manage the server to initiate a restart upon new code
- appleboy/ssh-action@master for letting GitHub use SSH to get into the EC2 server (`pm2 restart your-app`)
- GitHub Secrets for managing EC2_HOST, EC2_SSH_KEY (`Settings → Secrets and variables → Actions`)

## Disclosure

- AI was used to help edit this document, though it did so badly. OpenAI's Canvas is a mess. It repeatedly failed to make edits correctly and had trouble understanding what I wanted.
