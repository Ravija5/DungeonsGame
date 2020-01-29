## Overview

A user friendly single player game based on playing dungeon style puzzles. With a partner from my lab class, I followed an agile development process to design and implement a desktop Java application that satisfies the requirements of the client (see below). 


## Preliminary client requirements

The client desires an application that lets the user move a player around a dungeon and try to overcome various challenges in order to "complete" the dungeon by reaching some goal. The simplest form of such a puzzle is a maze, where the player must find their way from the starting point to the exit.

![Maze][maze]

More advanced puzzles may contain things like boulders that need to be pushed onto floor switches,

![Boulders][boulders]

or enemies that need to be fought with weapons, potions, or treasure.

![Advanced dungeon][advanced]

### Dungeon layout

To be specific, the layout of each dungeon is defined by a grid of squares, each of which may contain one or more entities. The different types of entities are as follows:

| Entity               | Example | Description                             |
| ------               | ------- | --------------------------------------- |
| Player               | ![Player][player] | The player can be moved either up, down, left, or right into adjacent squares, as long as another entity does not stop them (e.g. a wall). |
| Wall                 | ![Wall][wall] | Blocks the movement of the player, enemies, boulders and arrows. |
| Exit                 | ![Exit][exit] | If the player goes through an exit the puzzle is complete.  |
| Treasure             | ![Treasure][treasure] | Can be collected by the player. |
| Door                 | ![Door][door_open] ![Door][door_closed] | Exists in conjunction with a single key that can open it. If the player holds the key, they can open the door by moving through it. After opening it remains open. The client will be satisfied if dungeons can be made with up to 3 doors. |
| Key                  | ![Key][key] | Can be picked up by the player when they move into the containing square. The player can carry only one key at a time, and only one door has a lock that fits the key. The key disappears once it is used to open its corresponding door. |
| Boulder              | ![Boulder][boulder] | Acts like a wall in most cases. The only differences are that it can be pushed by the player into adjacent squares and can be destroyed by a bomb. The player is only strong enough to push **one** boulder at a time. |
| Floor switch         | ![Floor switch][switch] | Switches behave like empty squares so other entities can appear on top of them. When a boulder is pushed onto a floor switch, it is triggered. Pushing a boulder off the floor switch untriggers it. |
| Unlit bomb           | ![Unlit bomb][bomb_unlit] | The bomb is picked up by the player when they move into the square containing it. |
| Lit bomb             | ![Lit bomb][bomb_lit_1] ![Lit bomb][bomb_lit_2] ![Lit bomb][bomb_lit_3] ![Lit bomb][bomb_lit_4] | The player can light the bombs they have collected and drop them. The fuse burns down for a short fixed period of time before the bomb explodes. Upon explosion, any boulders or enemies in the squares immediately to the left, right, above or below are destroyed. If the player is in one of these squares they die. |
| Enemy                | ![Enemy][enemy] | Constantly moves toward the player, stopping if it cannot move any closer. The player dies upon collision with an enemy. |
| Sword                | ![Sword][sword] | This can be picked up the player and used to kill enemies. Only one sword can be carried at once. Each sword is only capable of 5 hits and disappears after that. One hit of the sword is sufficient to destroy any enemy. |
| Invincibility potion | ![Invincibility][invincibility] | If the player picks this up they become invincible to all bombs and enemies. Colliding with an enemy should result in their immediate destruction. Because of this, all enemies will run away from the player when they are invincible. The effect of the potion only lasts a limited time. |

### Goals

In addition to its layout, each dungeon also has a goal that defines what must be achieved by the player for the dungeon to be considered complete. Basic goals are:

* Getting to an exit.
* Destroying all enemies.
* Having a boulder on all floor switches.
* Collecting all treasure.

More complex goals can be built by logically composing basic goals. For example,

* Destroying all enemies AND getting to an exit
* Collecting all treasure OR having a boulder on all floor switches
* Getting to an exit AND (destroying all enemies OR collecting all treasure)

If getting to an exit is one of a conjunction of conditions, it must be done last. For example, if the condition is to destroy all enemies AND get to an exit, the player must destroy the enemies *then* get to the exit.

### Input

Your application will read from a JSON file containing a complete specification of the dungeon (the initial position of entities, goal, etc.). Example dungeons are included in the `dungeons` directory and the starter code contains an incomplete dungeon loader.

The dungeon files have the following format:

> { "width": *width in squares*, "height": *height in squares*, "entities": *list of entities*, "goal-condition": *goal condition* }

Each entity in the list of entities is structured as:

> { "type": *type*, "x": *x-position*, "y": *y-position* }

where *type* is one of

> ["player", "wall", "exit", "treasure", "door", "key", "boulder", "switch", "bomb", "enemy", "sword", "invincibility"]

The `wall` and `key` entities include an additional field `id` containing a number. Keys open the door with the same `id` (e.g. the key with `id` 0 opens the door with `id` 0).

The goal condition is a JSON object representing the logical statement that defines the goal. Basic goals are:

> { "goal": *goal* }

where *goal* is one of

> ["exit", "enemies", "boulders", "treasure"]

In the case of a more complex goal, *goal* is the logical operator and the additional *subgoals* field is a JSON array containing subgoals, which themselves are goal conditions. For example,

```JSON
{ "goal": "AND", "subgoals":
  [ { "goal": "exit" },
    { "goal": "OR", "subgoals":
      [ {"goal": "enemies" },
        {"goal": "treasure" }
      ]
    }
  ]
}
```

Note that the same basic goal *can* appear more than once in a statement.

You can extend this format to include additional information if you wish, but your application should still work with files in the original format.

### User interface

The UI component of this project will be implemented in JavaFX. The starter code contains a very basic UI showing how a player can be moved around with the arrow keys, but it is missing many features (the player can walk through walls for one).

The client has given you free reign over the visual design of the program. Included in the starter code are some example assets, but you are free to use different ones. You can find them elsewhere or even create your own. The examples above came from [here](http://opengameart.org).

## Requirement analysis (Milestone 1)

For this initial milestone, you are to model the requirements of the client as user stories on the issue board in GitLab. You will show these user stories to your tutor in the Week 6 lab, where they will give feedback.

Prior to your lab session, you and your partner must schedule collaboration sessions, to have initial high-level visioning discussions during which you will brainstorm to identify epic stories from the client's requirements and their key features, break-down high-level user stories to smaller user-stories and detail each user story to identify key conditions of satisfaction. Each epic story and user story should have its own card. Your team is expected to produce:

1. High-level epic stories from the problem statement. Each epic should have its own card/issue and a corresponding tag used to mark user stories that fall under it.
2. User stories, each containing:
   * a short description of the feature based on Role-Goal-Benefit template (Refer to the RGB model described in the lectures)
   * an estimate for the implementation of the user story in user story points (e.g. 4 points, where each point takes 2.5 hours).
   * a tag indicating the priority
   * acceptance criteria for each user story as a checklist in the issue (Refer to the 3 C's model described in the lectures)

As you progress through the rest of the project, you will keep your board and issues up to date: checking off acceptance criteria that have been satisfied and moving stories from **To Do** into **Doing** and finally into **Closed**.

## Domain modelling and backend implementation (Milestone 2)

Based on your requirements analysis and taking into account the feedback from your tutor, you will produce a domain model for the backend component of your project in the form of a UML class diagram, implement it in Java and write JUnit tests to test its functionality.

In deciding on your design and writing your implementation you should follow the practices and design principles covered in the course. You are expected to apply at least 3 of the design patterns learnt in the course. It is up to you where they are applied, but you will be expected to justify how and why you used them to your tutor during demonstration.

Your JUnit tests should be rigorous to ensure your backend functions as expected. In addition to basic functionality tests, you need to have tests based on your acceptance criteria.

In the week 8 lab, your tutor will ask you questions about your design and implementation and give feedback you can use as you continue to work on it.

## UI design and extensions (Milestone 3)

For this milestone you are to design and implement the user interface component of the application. A very basic UI can be built with minimal changes to the starter code, so that is where you should start. Fancier UI features can be added once you have a usable UI. You should apply the ideas from user-centric design and consider the usability heuristics covered in the lectures.

Additionally, for this milestone you also have the chance to extend the project with your own ideas. Note that to get high marks for extensions you will need to consider how they impact the user. Extensions that are technically complex, but do not provide the user with any real benefit are not considered good extensions. You can, and should, create additional user stories to represent the requirements of these extensions. Possible extensions include but are not limited to, multiplayer, different sorts of enemies, new weapons, and animated movement.

This final milestone will be a culmination of all the work done in the previous milestones. You have the opportunity to improve on your design based on feedback from your tutor. Marking of the design will be harsher for the final milestone as you have already had the opportunity to receive feedback.


[player]:        images/human_new.png
[wall]:          images/brick_brown_0.png
[exit]:          images/exit.png
[door_open]:     images/open_door.png
[door_closed]:   images/closed_door.png
[key]:           images/key.png
[boulder]:       images/boulder.png
[switch]:        images/pressure_plate.png
[bomb_unlit]:    images/bomb_unlit.png
[bomb_lit_1]:    images/bomb_lit_1.png
[bomb_lit_2]:    images/bomb_lit_2.png
[bomb_lit_3]:    images/bomb_lit_3.png
[bomb_lit_4]:    images/bomb_lit_4.png
[enemy]:        images/deep_elf_master_archer.png
[sword]:         images/greatsword_1_new.png
[invincibility]: images/brilliant_blue_new.png
[treasure]:      images/gold_pile.png

[maze]:          examples/maze.png
[boulders]:      examples/boulders.png
[advanced]:      examples/advanced.png
