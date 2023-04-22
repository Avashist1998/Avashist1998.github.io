---
author: "Abhay Vashist"
title: "Sorting Visualization"
date: "2022-12-29"
description: "Visualization of different sorting algorithms"
tags: ["visualization", "typescript"]
series: ["Personal Project"]
aliases: ["/sorting_visualization"]
weight: 1
ShowToc: true
TocOpen: true
---

I build a sorting visualization tool: [Check it out](https://avashist1998.github.io/sortingVisualization/)

## What Technologies I used
- Github Pages (Hosting & Deployment) 
- D3.js (Visualization)
- Javascript (UI & Sorting Logic)
- HTML (UI)
- CSS (Styling)

## How did I build It

### Code

All of the code available on [Github](https://github.com/Avashist1998/sortingVisualization).

### Data Object
The `SortData`type wraps all the information into a bundle that is needed for visualization.

```typescript
type SortData = {
    init_index: number,
    value: number,
    color: string
}
```

### Data Validation and Object Creation
#### Helper functions
- `validateData` (Validates user input data)
- `convertData` (Converts the input string in to array of numbers)
- `arrayToSortData` (Converts a number array to SortData array)

```typescript

function validateData(userInput) {
    if (userInput.length === 0) {
        return false
    } 
    else if(!(/ ?([0-9]* ?,)/.test(userInput))){
        return false
    } else {
        const data = convertData(userInput)
        for (let val of data) {
            if (val === undefined) {
                return false
            }
            return true
        }
    }
}

function convertData(userInput) {
    return  userInput.split(',').map(element => {
        return Number(element) ? Number(element) !== undefined : 0;
    })
}

function arrayToSortData (nums: number[], color: string) {
    let data: SortData[] = []
    nums.map( (num, index) => {
        data.push({init_index: index, value: num, color: color} as SortData);
    })
    return data;
}

```

### Visualization  

[D3](https://d3js.org) is a javascript visualization library build on top of [Web Components](https://developer.mozilla.org/en-US/docs/Web/API/Web_components). The library provides a lot of helpful components for generating different graphics.
I would highly recommend you check them out for some next level interactive visualizations.

For my project I used the `rect` shape to render a histogram which represents the different values provided by the users and colors are used to indicate the state of the different values. The colors of the histogram is used to communicate the transition of each step.



#### Color Map
- Blue: Base/Unsorted
- Green: Sorted
- RED: Selected
- Yellow: Pointer location
- Tile: Pivot

```javascript
const svg = d3.select("#"+ id)
    .append("svg")
    .attr("width", width)
    .attr("height", height)
    .attr("viewBox", [0, 0, width, height])
    .attr("style", "max-width: 100%; height: auto; height: intrinsic;");


var bar = svg.append("g")
.selectAll("rect"+id)
.data(I)
.join("rect")
    .attr("x", i => xScale(X[i]))
    .attr("y", i => yScale(Y[i]))
    .attr("height", i => yScale(0) - yScale(Y[i]))
    .attr("width", xScale.bandwidth())
    .attr("fill", i => data[i].color);
```

### Animation

Set `setInterval` is used to call the `sortStep` with a certain amount of delay which is specified by the user.

```javascript

function playSort(dataObject) {
    if (dataObject.sorted) {
        return 
    }
    dataObject.timer = setInterval(function() {
        dataObject.sortStep(dataObject);
        updateRender(dataObject);
        if (dataObject.sorted) {
            clearInterval(dataObject.timer);
            dataObject.sortedUpdateUI();
        }
    }, dataObject.freeRunTime)
}

function toggleInsertPlay(dataObject) {
    if (!dataObject.paused) {
        dataObject.paused = true;
        clearInterval(dataObject.timer);
        document.getElementById("nextButton").className = "button";
        document.getElementById("playPauseButton").innerHTML = "Play"
        document.getElementById("playPauseButton").className = "playButton"
    } else {
        playSort(dataObject)
        paused = false
        document.getElementById("nextButton").className = "disabled";
        document.getElementById("playPauseButton").innerHTML = "Pause"
        document.getElementById("playPauseButton").className = "pauseButton"
    }
}
```

### Final Animation
![](https://raw.githubusercontent.com/Avashist1998/Avashist1998.github.io/main/static/images/insert_sort.gif)


## Final Thoughts

I had a great time building the visualization, but looking back at the whole processes, I should have separated the sorting logic and visual elements. My recommendation to fellow coders would be to implement a MVC design for the visualization, making the code reusable and expandable. `D3.js` is a great tool for data visualization, and will use it in the future. I hope you enjoyed looking at the different sorting algorithms, and comparing them.
