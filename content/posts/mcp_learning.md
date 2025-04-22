---
author: "Abhay Vashist"
title: "MCP Learning"
date: 2025-04-21T13:47:03-06:00
description: "Building a simple MCP with LiChess"
tags: ["MCP", "Anthropic", "AI", "LLM"]
series: [ ]
aliases: ["/mcp_learning"]
weight: 1
ShowToc: true
TocOpen: true
cover:
    image: https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F3aabd8804251c0364cbde9d2e4be6dc8e8c2faec-2880x1620.png&w=1080&q=75
---


Last year, the Anthropic team introduced the MCP API protocol, an open standard for connecting external tools to Large Language Models. The major contribution of this protocol is that it converts an N (Number of different LLMs) × N (Number of different services) problem into a 1 × N problem. I am currently working in the Agentic Solution Space and became curious about the protocol. I would like to share my journey and experience.


## What Technologies I used
- Python (Programming Language)
- MCP (Python MCP SDK)
- LiChess (Open Source API)
- Github (Version Tracking)



## How did I build It

### Codebase
[Github Repo](https://github.com/Avashist1998/map_learning)

### Design

- Important Files
    - `lichess/main.py`: Contains all of the application node
    - `example_flows`: Contains the step taken by the llm to complete the task


### Code

The function flow basically wrapper around the httpx, and makes sure that no error are raised when making an api call. If the api fails `None` is return, and failure can be handled gracefully.

```python
async def make_lichess_request(url: str) -> dict[str, Any] | None:
    """Make a request to the LiChess API with proper error handling."""
    headers = {"User-Agent": USER_AGENT, "Accept": "application/json"}
    async with httpx.AsyncClient() as client:
        try:
            response = await client.get(url, headers=headers, timeout=30.0)
            response.raise_for_status()
            return response.json()
        except Exception:
            return None

```

`get_top_lichess_players`: Return the top 20 players for each chess mode

```python
@mcp.tool()
async def get_top_lichess_players() -> str:
    """Get the top 10 player in the lichess."""
    points_url = f"{LICHESS_URL}/player"
    player_data = await make_lichess_request(points_url)

    if not player_data:
        return "Unable to fetch top 10 players for different game modes"

    top_player_of_different_modes = []
    for mode, players in player_data.items():
        top_player_str = f" {mode} \n"
        for player in players:
            top_player_str += f"""userId: {player["id"]} Username: {player["username"]}  Rating: {player["perfs"][mode]["rating"]} \n"""
        top_player_of_different_modes.append(top_player_str)
    return "\n---\n".join(top_player_of_different_modes)

```

`get_player_stats`: Return the status of a player from its username and the chess game type

```python
@mcp.tool()
async def get_player_stats(username: str, mode: str) -> str:
    """Get the player stats such as rank, game count from lichess.

    Args:
        username: username of the player of interest
        mode: Game mode the stats you want for the player
    
    """
    player_stats = f"{LICHESS_URL}/user/{username}/perf/{mode}"
    player_data = await make_lichess_request(player_stats)

    if not player_data:
        return "Unable to fetch the player stats from this game mode"
    
    res = ""

    if "user" in player_data:
        res += f"Username: {player_data['user']['name']} \n"

    if "perf" in player_data:
        res += json.dumps(player_data["perf"]) + "\n"

    res += f"Rank: {player_data['rank']} Percentile: {player_data['percentile']}\n"

    if "stat" in player_data:
        count_stats = []
        for key, value in player_data["stat"]["count"].items():
            count_stats.append(f"{key}: {value}")
        res += " \n".join(count_stats)
    
    return res

```

## Examples

- [Single Step Task](https://github.com/Avashist1998/map_learning/blob/main/example_flows/single_step.md)
- [Multi Step Task](https://github.com/Avashist1998/map_learning/blob/main/example_flows/multi_step.md)


## Final Thoughts
I was extremely surprised by how efficiently the model can work with the API. I am also surprised that the model is able to chain different LLM calls and multiple function tools in the right order. However, I do see some problems with the MCP model and the overall agentic solutions:

- As the reasoning power of LLMs improves, more weight is given to language. Language may not be the ideal channel for communication, and it is also not a gift shared by everyone.
- Authentication is not included in the MCP by default. This is concerning and may become mission critical as we depend more and more on agents.
- Will reasoning keep up with the number of Ns? In my example, there were only two function calls for the model to choose from. So the agent had a 25% chance of choosing the right action (not calling any function, calling function A, calling function B, calling both functions). As the number of MCPs grows, there will be overlap between tools, and the model is more likely to get confused between them.