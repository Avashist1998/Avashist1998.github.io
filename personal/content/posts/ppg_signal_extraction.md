---
author: "Abhay Vashist"
title: "PPG Singal Extraction"
date: "2017-12-15"
description: "Showcasing the PPG Singal Extraction"
tags: ["python", "signal-processing", "bioelectronics"]
series: ["SchoolProjects"]
aliases: ["/ppg_signal_extraction"]
weight: 2
ShowToc: true
TocOpen: true
---

## PPG singal Extraction

Used Matlab to extract the RPG matrix for a single frame from a 720p video file with a duration of 10 to 15 seconds. The RPG matrix was converted into grayscale single value matrix to simplify the calculation. The dimension of the grayscale matrix was changed to focus on the area of interest to increase the computation time of the program. The grayscale signal was extracted from the video and was studies in the frequency domain. An IIR filter was applied to the single to extract the PPG signal. 