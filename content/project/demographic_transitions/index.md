---
title: Visualizing Demographic Transitions
summary: "[*Demographic Transitions Across Time and Space* (Delventhal, Fernández-Villaverde & Guner, 2021)](../../publication/demographic_transitions/ /"accompanying research paper/")."
tags:
- 
date: "2022-04-03T00:00:00Z"

# Optional external URL for project (replaces project detail page).
external_link: ""

image:
  caption: Photo by rawpixel on Unsplash
  focal_point: Smart
  preview_only: true

links:
- name: Research paper
  url: "publication/demographic_transitions/"
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: example
---

With this app you can visualize the historical transition in birth and death rates for 186 countries. Just select the countries you would like to plot from the dropdown menu. Crude Birth and Death Rates (CBR/CDR) are the raw number of births/deaths per 1,000 population in one year. The modeled birth and death rates represent the 3-phase transition structure which best fits the underlying data. Details can be found in the research paper [*Demographic Transitions Across Time and Space* (Delventhal, Fernández-Villaverde & Guner, 2021)](../../publication/demographic_transitions/ "accompanying research paper").

The year range can be adjusted using a slider below the chart. Clicking on the ">" arrow will open a sidebar, where you can set the y-axis limits and select which variables to graph. You can also adjust the chart view by using the mouse wheel to zoom and clicking and dragging on the chart directly.

Below the chart a table will populate summarizing the start year, end year, initial level, and final level for the selected birth rate and death rate transitions. Some values are blank because the data do not allow us to observe either the start or the end of a transition.

Buttons below the chart and the summary table allow you to download either the data for the visualization you have selected, or the complete underlying dataset.

<iframe height="1100" width="95%" frameborder="no" src="https://share.streamlit.io/mdelventhal/dt_visualize/main/DT_visualize.py"> </iframe>
