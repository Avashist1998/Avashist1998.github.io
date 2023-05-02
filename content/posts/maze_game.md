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
The game is built using the MVC (Model-View-Controller) architecture and is based on Wesley Werner's [repository]((https://github.com/wesleywerner/mvc-game-design)). I highly recommend checking out his repository for better understanding.

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

I used the [Randomized Prim's algorithm](https://en.wikipedia.org/wiki/Maze_generation_algorithm#Randomized_Prim's_algorithm) for generating the maze, which is a popular choice for maze generation. I followed Orestis Zekai's article on maze generation using Python, which was helpful for the basic setup. However, I had some difficulty following the final parts of the [article](https://medium.com/swlh/fun-with-python-1-maze-generator-931639b4fb7e), so I implemented my own version. You can find the final version of my implementation in this [file](https://github.com/Avashist1998/maze_game/blob/develop/src/maze_game/maze_generation.py) on Github.

#### Maze Board Value Enum

The MazeGameObject enum improves the readability of the code and creates a layer of abstraction between the values on the game board and the maze generation code.
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
- Function fills the empty cells with wall value (Exactly same as Zekai)
```python
def fill_walls(maze)
    for i, _ in enumerate(maze):
        for j in range(len(maze[0])):
            if maze[i][j] == MazeGameObject.EMPTY.value:
                maze[i][j] = MazeGameObject.WALL.value
```
- To determine the number of paths surrounding a given cell in the maze, we examine the adjacent cells using `if` statements. If the cells are within the grid's boundaries and have the same value as the path, the `s_cell_count` is incremented.
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
- To create entry and exit points for the maze after it's generated, we break the top and bottom walls of the maze into paths that are adjacent to existing paths. This creates a long and winding path that leads from the entrance to the exit of the maze. This step is important because it ensures that there is a clear path from the entrance to the exit, and that the maze is solvable.
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
    - The first step in generating the maze is to initialize the board using the `init_maze` function.
        ```python
            maze = init_maze(n_row, n_col)
        ```
    - We need to determine the starting position of the maze, which we achieve using the get_start_pos function. This function ensures that the starting position is always a path cell and returns its coordinates. We then set the start_pos variable to these coordinates.
        ```python
            start_pos = get_start_pos(maze)
            maze[start_pos[0]][start_pos[1]] = MazeGameObject.PATH.value
        ```
    - We assign the surrounding cells to the `start_pos` to the value of a `WALL`. Then we add the defined walls the our queue, in my case a set named `wall_list`
        ```python
            maze[start_pos[0] - 1][start_pos[1]] = MazeGameObject.WALL.value
            maze[start_pos[0]][start_pos[1] - 1] = MazeGameObject.WALL.value
            maze[start_pos[0]][start_pos[1] + 1] = MazeGameObject.WALL.value
            maze[start_pos[0] + 1][start_pos[1]] = MazeGameObject.WALL.value

            wall_list = set()
            wall_list.add((start_pos[0] - 1, start_pos[1]))
            wall_list.add((start_pos[0], start_pos[1] - 1))
            wall_list.add((start_pos[0], start_pos[1] + 1))
            wall_list.add((start_pos[0] + 1, start_pos[1]))
        ```
    - After the initialization steps, the maze generation algorithm repeats the following steps until all the walls in the maze have been processed:
        - Pick a random wall from the `wall_list`.
        - Count the number of surrounding cells of the wall that are paths using the `get_surrounding_cell_count` function.
        - If the count is greater than 1, remove the wall from the `wall_list` since it has been processed.
        - If the count is 1, we process the wall and remove the wall from the `wall_list`.
        - We continue this process until the `wall_list` is empty.
    - After all the cells are processed, we fill the remaining cells with walls using the fill_walls function.
    - Finally, we create an entry and exit in the maze using the `create_entry_exit` function.
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
    - The "Row case" and "Col case" refer to the two possibilities for which direction the wall being processed is oriented: either horizontally (a "row") or vertically (a "column").
        - For each of these cases, there are two sub-cases, based on whether the wall is at the edge of the grid or not.
        - If the wall being processed is not at the edge of the grid, the code checks the cells on either side of the wall to see if one of them is a path and the other empty (i.e. possible connected). If so, the wall is removed from the wall list, meaning it is no longer available to be broken down.
        - If the wall being processed is at the edge of the grid, the code only checks the cells on the side that is not at the edge. This is because the cells on the other side are already known to be walls, since they lie outside the bounds of the grid.

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

Visualizing the maze requires two main functions, `draw_maze` and `draw_tile` which are defined [here](https://github.com/Avashist1998/maze_game/blob/07cb8f2943dea72f322c4f02b75301312be9b7a6/src/maze_visualization/maze_game_visualization.py#L186-L234). The function communicates using two data classes called `Tile` and `ScreenSize`. This approach makes the visualization independent of the value of the tile and treats each tile equally. In other words, the function doesn't need to know what the tile represents in terms of the maze structure, it only needs to know the dimensions of the tile and the screen size to properly display it on the screen. This separation of concerns makes the code easier to maintain and modify in the future, as changes to the tile value won't affect the visualization code.

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

In the `draw_maze` function, the first step is to calculate the total dimensions and define the screen size of the maze in pixels. The maze will occupy all of the space on the screen except for 50 pixels on the left and right sides, and 75 pixels on the top and 25 pixels on the bottom. Once we have the maze size in pixels, we need to compute the size of individual tiles using integer division.

Next, we iterate through the grid and set the appropriate tile color based on the grid cell value. We calculate the tile's top-left coordinates by multiplying the index with the tile size and adding the maze board's top-left coordinates. Then, we call the `draw_tile` function to render the tile on the screen.

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
This project to build a maze game using pygame was a valuable experience for me, and I highly recommend it as a way to learn pygame and prototype game ideas. Pygame provides a great environment for individuals to develop simple and small games, and the process can be very enjoyable. I hope you found my explanation of the project informative and enjoyable.