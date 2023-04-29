---
author: "Abhay Vashist"
title: "Maze Game"
date: "2023-02-10"
description: "Visualization of different sorting algorithms"
tags: ["pygame", "python", "game"]
series: ["Personal Project"]
aliases: ["/maze_game"]
weight: 1
ShowToc: true
TocOpen: true
cover:
    image: https://images.nightcafe.studio/jobs/JIODE2KiPFTlX8mPB4Ry/JIODE2KiPFTlX8mPB4Ry--1--r2fxo_2x.jpg
---


I build maze solving game using `Pygame`: [Check it out](https://avashist1998.github.io/maze_game/)

## What Technologies I used
- Python (Programming Language)
- Pytest (Unit testing)
- Pygame (Visualization & Keyboard Events)
- Pygbag (Python to wasm tool for Web Distribution)
- Github Pages (Hosting) 
- Github Action (CI pipeline)
- Github (Version Tracking)

## How did I build It

### Codebase
[Github Repo](https://github.com/avashist1998/maze_game)

### Design
The game follows an MVC(Model View Controller) architecture and is based on Wesleywerner’s [repo](https://github.com/wesleywerner/mvc-game-design). I would highly recommend checking his repo out for clarification.

- Important Files
    - `main.py`: Entry point for the game application
    - `src` : Containing all of the python source code
        - `logger.py`: Defines the logger of the game
        - `event.py`: Defines different type of event in the game environment 
        - `model.py`: Defines the Game Engine of game
        - `view.py`: Defines the Visual of the game
        - `maze_game`: Contains the logic and maze generation code.
        - `maze_visualization`: Contains helper function for game visuals 
    - `test`: Contains the test written for the game

### Maze Generation

For the maze generation I used the [Randomized Prim's algorithm](https://en.wikipedia.org/wiki/Maze_generation_algorithm#Randomized_Prim's_algorithm). The algorithm is a very popular choose when generating mazes, and I followed this [article](https://medium.com/swlh/fun-with-python-1-maze-generator-931639b4fb7e) by Orestis Zekai. The article was helpful for the basic setup, but I had difficulty following along in the final parts, so I will be presenting my implementation here. Check out the [file](https://github.com/Avashist1998/maze_game/blob/develop/src/maze_game/maze_generation.py) on Github for the final version.

#### Maze Board Value Enum
The MazeGameObject enum help with the readability of code, and creates a layer of abstraction between the values of the board and the maze generation code.
```python
class MazeGameObject(Enum):
    PATH = 0
    WALL = 1
    GOAL = 2
    EMPTY = -1
    PLAYER_TILE = 7
    VISITED_TILE = 3
```
### Maze Generation Code breakdown

- Initialize the maze with Empty object values (Exactly same as Zekai)
```python
def init_maze(height, width):
    maze = []
    for _ in range(height):
        maze_row = [MazeGameObject.EMPTY.value for _ in range(width)]
        maze.append(maze_row)
    return maze
```
- Pick a start position by randomly selecting a location in the maze that does not lie on the edge. (Exactly same as Zekai)
```python
from random import random
def get_start_pos(row, col):
    start_row, start_col = int(random() * (row-2))+1, int(random() * (col-2))+1
    return start_row, start_col
```
- Function fills the remains cells with wall value (Exactly same as Zekai)
```python
def fill_walls(maze)
    for i, _ in enumerate(maze):
        for j in range(len(maze[0])):
            if maze[i][j] == MazeGameObject.EMPTY.value:
                maze[i][j] = MazeGameObject.WALL.value
```
- To get the number of paths surrounding a given cell in the maze, we check the surrounding cells using `if statements`. If the cells inside the bounds of the grid and cell has the same value as the path, the count is incremented.
```python
def get_surrounding_cell_count(cell, maze):
    s_cell_count = 0
    if (cell[0] > 0 and 
        maze[cell[0] - 1][cell[1]] ==  MazeGameObject.PATH.value):
        s_cell_count += 1
    if (cell[0] < len(maze) - 1 and 
        maze[cell[0] + 1][cell[1]] ==  MazeGameObject.PATH.value):
        s_cell_count += 1
    if (cell[1] > 0 and 
        maze[cell[0]][cell[1] - 1] ==  MazeGameObject.PATH.value):
        s_cell_count += 1
    if (cell[1] < len(maze[0]) - 1 and 
        maze[cell[0]][cell[1] + 1] ==  MazeGameObject.PATH.value):
        s_cell_count += 1
    return s_cell_count
```
- To create a exits and entry for the maze once the maze is generated. We break make the row and bottom row walls into paths which are adjacent to a path. This creates a long and complex path.
```python
def create_entry_exit(maze):
    row, col = len(maze), len(maze[0])
    start_point, exit_point = (0, 0), (row - 1, col - 1)
    # Set entrance and exit
    for i in range(col):
        if maze[1][i] == MazeGameObject.PATH.value:
            maze[0][i] = MazeGameObject.PLAYER_TILE.value
            start_point = (0, i)
            break

    for i in range(col - 1, 0, -1):
        if maze[row - 2][i] == MazeGameObject.PATH.value:
            maze[row - 1][i] = MazeGameObject.GOAL.value
            exit_point = (row - 1, i)
            break
    return start_point, exit_point, maze
```
- Generation the maze 
    - The first step to generating the maze is initializing the board using `init_maze` function
        ```python
            maze = init_maze(n_row, n_col)
        ```
    - Then we define the seed position of the maze using the `get_start_pos` function, and set the `start_pos` to the value of a path cell.
        ```python
            start_pos = get_start_pos(maze)
            maze[start_pos[0]][start_pos[1]] = MazeGameObject.PATH.value
        ```
    - We assign the surrounding cells to the `start_pos` to the value of a `WALL`.
        ```python
            maze[start_pos[0] - 1][start_pos[1]] = MazeGameObject.WALL.value
            maze[start_pos[0]][start_pos[1] - 1] = MazeGameObject.WALL.value
            maze[start_pos[0]][start_pos[1] + 1] = MazeGameObject.WALL.value
            maze[start_pos[0] + 1][start_pos[1]] = MazeGameObject.WALL.value
        ```
    - Then we add the defined walls the our queue, in my case a set named `wall_list`
        ```python
            wall_list = set()
            wall_list.add((start_pos[0] - 1, start_pos[1]))
            wall_list.add((start_pos[0], start_pos[1] - 1))
            wall_list.add((start_pos[0], start_pos[1] + 1))
            wall_list.add((start_pos[0] + 1, start_pos[1]))
        ```
    - After which we want to repeated the following steps unit we run out of walls to process. The repetition is implemented with a `while` loop which runs till the `wall_list` is empty. We pick a wall from the list at random, then we count the total number of cells surrounding the selected wall using the ` get_surrounding_cell_count` function. We check if there are more than one surround path to the wall, if there is we remove the wall from the list since the wall is process. Once all the cells are processed we fill the remain cells of the grid using the `fill_walls` function. Then we create an entry and exit in the maze using `create_entry_exit` function.
        ```python
        from random import choices
        while wall_list:
            rand_wall = choices(list(wall_list), k=1)[0]
            s_cell_count = get_surrounding_cell_count(rand_wall, maze)
            if s_cell_count < 2:
                ### Wall Processing block
            wall_list.remove(rand_wall)
        fill_walls(maze)
        entry_point, exit_point, maze = create_entry_exit(maze)
        return entry_point, exit_point, maze
        ```
    - Inside the wall processing block there is a row case and a col case, and they are checked using if statements. Inside each of the two cases we have two sub cases, as shown below. Please read the maze wiki for clerification.
        ```markdown
        Row Cases:
            Case 1:         Case 2:
                Path               Empty
                |                  |
                Wall               Wall
                |                  |
                Empty              Path   
        Col Cases:
            Case 1: Path - Wall - Empty
            Case 2: Empty - Wall - Path
        ```
    - According to the algorithm we break the wall when it does not connect two paths. After a wall is identified that fits the condition we make the wall a path. We also make the 3 new surrounding empty cells into walls and add them to the `wall_list` to be processed.
        ```python
        if (rand_wall[0] > 0 and rand_wall[0] + 1 < n_row):
            # row case
            if (maze[rand_wall[0] - 1][rand_wall[1]] == MazeGameObject.WALL.value 
                and maze[rand_wall[0] + 1][rand_wall[1]] == MazeGameObject.PATH.value):
                # Case 2
                maze[rand_wall[0]][rand_wall[1]] = MazeGameObject.PATH.value

                maze[rand_wall[0] - 1][rand_wall[1]] = MazeGameObject.WALL.value
                maze[rand_wall[0]][rand_wall[1] - 1] = MazeGameObject.WALL.value
                maze[rand_wall[0]][rand_wall[1] + 1] = MazeGameObject.WALL.value

                wall_list.add((rand_wall[0] - 1, rand_wall[1]))
                wall_list.add((rand_wall[0], rand_wall[1] + 1))
                wall_list.add((rand_wall[0], rand_wall[1] - 1))
            if (maze[rand_wall[0] + 1][rand_wall[1]] == MazeGameObject.WALL.value 
                and maze[rand_wall[0] + 1][rand_wall[1]] == MazeGameObject.PATH.value):
                # Case 1
                    # Similar logic as case 2 block
        if (rand_wall[1] > 0 and rand_wall[1] + 1 < n_col):
            ### similar logic as above row cases
        ```

### Maze Visualization

Visualizing the maze requires two main functions, `draw_maze` and `draw_tile` which are defined [here](https://github.com/Avashist1998/maze_game/blob/07cb8f2943dea72f322c4f02b75301312be9b7a6/src/maze_visualization/maze_game_visualization.py#L186-L234). The function communicate using the two data classes called  `Tile` and `ScreenSize`. This allows the visualization to be agnotic to the value of the tile, and treat each tile the same.

```python
from dataclasses import dataclass
@dataclass
class Tile:
    """Maze tile data object definition."""
    tile_color: Color
    border_color: Color
    tile_space: ScreenSize

@dataclass
class ScreenSize:
    """Screen size dataclass"""
    width: int
    height: int
    top_left_x: int
    top_left_y: int
```
In the `draw_maze` function, the first step to get the total number of dimension and define the total screen size of the maze in pixels. The maze will take up all of the space on the screen except 50 px on the right and left and 75 px and 25 px on the top and bottom respectively. Once we have the maze size in pixels we need to compute the size of the inidivials tiles using integer divisions. Then iterate throught the grid and set the appropirate tile color based on the grid cell value. After which the tile top left corredictate is computed by mutiplying the index with tile size and adding the maze board top right point corrdinates respectively. We call the `draw_tile` function to render the tile on the screen.

```python
board = maze.get_board() # maze generated previously
row, col = len(board), len(board[0])
maze_board_top, maze_board_left = 75, 50
maze_screen_width = self.screen_width - 100 
maze_screen_height = self.screen_height - 100
tile_width, tile_height = maze_screen_width // col, maze_screen_height // row

for i in range(row):
    for j in range(col):
        if board[i][j] == MazeGameObject.WALL.value:
            tile_color = WALL_COLOR
        elif board[i][j] == MazeGameObject.GOAL.value:
            tile_color = GOAL_COLOR
        elif board[i][j] == MazeGameObject.VISITED_TILE.value:
            tile_color = VISITED_COLOR
        elif board[i][j] == MazeGameObject.PLAYER_TILE.value:
            tile_color = PLAYER_COLOR
        else:
            tile_color = PATH_COLOR
        tile_space = ScreenSize(tile_width, tile_height,
                                maze_board_left + j * tile_width,
                                maze_board_top + i * tile_height)
        tile = Tile(tile_color, TILE_BORDER_COLOR, tile_space)
        self.draw_tile(tile)


def draw_tile(self, tile):
    boarder_thickness = int(tile.tile_space.width * 0.1) 
    pygame.draw.rect(
        self.screen, tile.tile_color,
        pygame.Rect(tile.tile_space.top_left_x, tile.tile_space.top_left_y,
                    tile.tile_space.width, tile.tile_space.height), 0)
    if boarder_thickness > 0:
        pygame.draw.rect(
            self.screen, tile.border_color,
            pygame.Rect(tile.tile_space.top_left_x,
                        tile.tile_space.top_left_y, tile.tile_space.width,
                        tile.tile_space.height), boarder_thickness)
```

### Maze Animation 
![](https://raw.githubusercontent.com/Avashist1998/Avashist1998.github.io/main/static/images/maze_solving.gif)

### Final Thoughts
The maze game was a great project to learn pygame, and would recommned you to try building games using pygames. It is a great place to protype your ideas, and fantstic for indiviuals development to build something small and simple. I hope you guys enjoyed reading about my project, as much I enjoyed build the game.