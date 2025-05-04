# CSC111 Project Proposal: Canadian Federal Election Polling and Seat Projections

**Authors**: Amin Behbudov, Fares Abdulmajeed Alabdulhadi, Tahmid Wasif Zaman, Dimural Murat  
**Date**: Monday, March 31st, 2025

## Problem Description and Research Question

Canada is approaching a critical federal election during a time of significant economic and geopolitical instability. The country faces challenges such as an unaffordable housing market, slow economic growth, and external trade pressures, particularly from the United States, which has recently threatened Canada with tariffs. Between 2012 and 2024, Canada’s average GDP per capita growth has been the third-lowest in the OECD, unlike the previous decade where it had roughly kept pace with the rest of the OECD (Palacios, Schembri, and Whalen, 2024). Affordability issues have become a pressing concern, with home prices outpacing wage growth (Statista, 2025). As a result, voters are increasingly focused on selecting the political party that they believe is best equipped to handle these economic hardships.

Understanding public opinion and election predictions is crucial for both voters and policymakers. Polling data provides a snapshot of voter preferences, but predicting election outcomes is complex due to Canada’s electoral system, which allocates seats based on regional results rather than national vote shares. Therefore, it is valuable to analyze and visualize polling data at different levels of granularity—national, provincial, and municipal—to provide a clearer picture of how the election might unfold.

**Research Question**:  
*How are political parties polling across Canada, and what are the projected seat distributions if an election were held today? What is the probability of each party winning based on current polling data?*

## Data Collection and Representation

We used two main sources of data for our project:

- **Live Polling Data**:  
  Scraped using Selenium from the CBC Poll Tracker website ([link](https://newsinteractives.cbc.ca/elections/poll-tracker/canada/)). This dataset contains provincial polling percentages for the major parties (Liberal, Conservative, NDP, Green, Bloc Québécois, People's Party) and a catch-all category called "Others". However, the site does not include data for Yukon, Northwest Territories, or Nunavut. It also aggregates data for the Atlantic provinces and for Saskatchewan and Manitoba.

- **Historical Election Data**:  
  CSV files downloaded from the Elections Canada website containing results from the 2015, 2019, and 2021 federal elections. These datasets contain vote percentages by political party for each province. For consistency with the CBC Poll Tracker format, we manually averaged the percentages of the four Atlantic provinces (Newfoundland and Labrador, Nova Scotia, New Brunswick, and Prince Edward Island) and also grouped Saskatchewan and Manitoba together. We excluded data from Yukon, Nunavut, and NWT as they were not included in the polling data.

Each CSV used:
- **Columns**: Province-specific vote shares
- **Rows**: Political party names
- **Format**: Percentages of votes, normalized to sum to 100% per province

### Computational Overview

Our final project is a simulation and visualization tool that estimates the probability of different outcomes in a Canadian federal election using current polling data and historical voter behavior. It is implemented in Python and modularized into multiple files, each handling a different phase of computation.

#### Data Representation
We represent Canada’s election system hierarchically using a tree structure implemented in `election_model.py`. The tree has the following structure:
- The root node represents the national election.
- Each child node represents a province (e.g., Ontario, Alberta).
- Each province node holds a number of seats (electoral districts), which are allocated based on first-past-the-post results in the simulation.

#### Core Computations

1. **Data Collection and Cleaning**:
   - `scraper.py`: Uses the **Selenium** library to scrape real-time polling data from the CBC Poll Tracker.
   - `data_loader.py`: Uses **pandas** to load and clean CSV files containing historical federal election results from Elections Canada.

2. **Tree Simulation**:
   - `build_election_tree()`: Builds a tree based on seat allocation per province.
   - `simulate_single_province()`: Assigns each seat to a party based on current polling, using **random.choices()** for probabilistic outcomes.

3. **Voter Graph Construction and Adjustment**:
   - `graph.py`: Builds a voter transition graph using historical CSVs by analyzing vote share changes between elections.
   - `adjust_polling_with_voter_graph()`: Adjusts current polling data based on historical swing patterns before running simulations.

4. **Monte Carlo Simulation**:
   - `run_simulation()`: Runs 1,000 trials of the election simulation to generate distributions of seat counts per party. It also computes the probability of a party winning a majority, a minority, or not winning at all.

#### Visualization and Interactivity

Results are displayed through a web-based interface using the **Dash** framework, implemented in `dashboard.py`. The interface includes:
- A button that triggers the simulation using Dash callbacks.
- Four tabs:
  - **Map**: A color-coded choropleth map of Canada showing projected winning party per province, implemented using `choroplethmapbox()` from **plotly.express**.
  - **Seat Distribution**: A bar chart showing average seat counts for each party, using `plotly.graph_objects.Bar()`.
  - **Voter Transition Graph**: A curved, directed graph showing voter swings between parties, created using **networkx** and visualized using `plotly.graph_objects`.
  - **Compare Predictions**: A side-by-side comparison of simulation results with and without accounting for historical swing behavior.

Simulation data is stored and shared between components using `dcc.Store()`.

#### Python Libraries Used
- **pandas** – For loading and cleaning CSV data.
- **Selenium** – For automated web scraping from the CBC Poll Tracker.
- **networkx** – To build and process the directed graph representing voter transitions.
- **plotly.graph_objects** – For rendering bar charts, scatter plots, and the voter transition graph.
- **plotly.express** – For rendering the interactive choropleth map.
- **dash** – For building the web app interface, tabs, and callback functions.
- **random** – For simulating election outcomes using `random.choices()`.
- **json** – To serialize and deserialize simulation data between callbacks.

#### Running the Program

To run the simulation:
1. Run `main.py` from your Python console.
2. A message will display: `Dash is running on http://127.0.0.1:8050/`. Click the link.
3. Press “Run Simulation” on the web interface.
4. Selenium will open a Chrome window twice to scrape live polling data. Do not close them manually.

**Outputs**:
- A summary of the probability of each party winning a majority, minority, or not winning.
- A colour-coded map of projected winning party per province.
- A bar chart of predicted seat counts.
- A voter transition graph showing swing behavior between elections.
- A “Compare Predictions” tab to toggle between simulation results with vs. without historical voter behavior modeling.

### Changes from Proposal

Our final project closely followed the structure and goals outlined in our original proposal, but several refinements were made based on TA feedback and practical considerations:

- We incorporated historical voter behavior by downloading federal election results from the Elections Canada website and modeling voter transitions as a directed graph.
- Due to computational limitations, we reduced the number of Monte Carlo simulations from 10,000 to 1,000 to maintain interactivity.
- We enhanced visualizations beyond the proposal by adding a voter transition graph and a “Compare Predictions” tab to show the influence of historical swing behavior.

### Discussion and Interpretation

Our simulation successfully addressed the research question, offering insights into how current polling data and historical voter trends could shape the outcome of the upcoming Canadian federal election. The results are presented visually through a map, seat distribution chart, and voter transition graph. Despite some limitations (e.g., polling data granularity, computational constraints), our tool offers a meaningful way to explore and compare election forecasts.

#### Future Directions
- Incorporating confidence intervals into polling input.
- Using more granular regional polling data.
- Adding animation or temporal projections to simulate how forecasts change over time.
- Improving the voter transition model using more rigorous statistical methods or machine learning techniques.

### References

1. A. Whalen, M. Palacios, and L. Schembri, “We’re Getting Poorer: GDP per Capita in Canada and the OECD, 2002-2060,” *Fraser Institute*, Jul. 2024, Accessed: Mar. 04, 2025. Available: [Fraser Institute](https://www.fraserinstitute.org/studies/were-getting-poorer-gdp-per-capita-in-canada-and-oecd-2002-2060)
2. S. R. Department, “House Price to Income Ratio Index in Canada from 1st Quarter 2012 to 3rd Quarter 2024,” *Statista*, Jan. 28, 2025. Available: [Statista](https://www.statista.com/statistics/591782/house-price-to-income-ratio-canada/)
3. É. Grenier, “CBC News Canada Poll Tracker,” *CBC News*, Sep. 19, 2019. Available: [CBC News](https://newsinteractives.cbc.ca/elections/poll-tracker/canada/)
4. Sources of historical data:
   - [2021 Federal Election Results](https://www.elections.ca/res/rep/off/ovr2021app/53/data_donnees/table_tableau09.csv)
   - [2019 Federal Election Results](https://www.elections.ca/res/rep/off/ovr2019app/51/data_donnees/table_tableau09.csv)
   - [2015 Federal Election Results](https://www.elections.ca/res/rep/off/ovr2015app/41/data_donnees/table_tableau09.csv)
