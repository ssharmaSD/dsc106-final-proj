<script>
  import { scaleBand, scaleLinear } from "d3-scale";
  import { select } from "d3-selection";
  import { csv } from "d3-fetch";
  import * as d3 from "d3";

  let height = 500;
  let width = 500;
  const mobile = window.innerWidth <= 700;
  const margin = {
    top: mobile ? 40 : 50,
    bottom: mobile ? 10 : 25,
    left: mobile ? 0 : 80,
    right: mobile ? 0 : 10,
  };

  let data = [];
  let selectedCountry = 'Albania'; // Default selected country

  // Load the CSV data
  csv('/data/drinks.csv').then(loadedData => {
    data = loadedData.map(d => ({
      country: d.country,
      beer_servings: +d.beer_servings,
      spirit_servings: +d.spirit_servings,
      wine_servings: +d.wine_servings,
      total_litres_of_pure_alcohol: +d.total_litres_of_pure_alcohol
    }));
    drawBarChart(selectedCountry);
  });

  // Function to draw bar chart
  function drawBarChart(country) {
    const svg = select("#visualization");
    svg.selectAll("*").remove();

    const countryData = data.filter(d => d.country === country)[0];

    if (!countryData) return;

    const barData = [
      { label: 'Beer Servings', value: countryData.beer_servings },
      { label: 'Spirit Servings', value: countryData.spirit_servings },
      { label: 'Wine Servings', value: countryData.wine_servings },
      { label: 'Total Litres of Pure Alcohol', value: countryData.total_litres_of_pure_alcohol }
    ];

    const xScale = scaleBand()
      .domain(barData.map(d => d.label))
      .range([margin.left, width - margin.right])
      .padding(0.1);

    const yScale = scaleLinear()
      .domain([0, d3.max(barData, d => d.value)])
      .nice()
      .range([height - margin.bottom, margin.top]);

    // Create bars
    svg.selectAll(".bar")
      .data(barData)
      .enter()
      .append("rect")
      .attr("class", "bar")
      .attr("x", d => xScale(d.label))
      .attr("y", d => yScale(d.value))
      .attr("width", xScale.bandwidth())
      .attr("height", d => height - margin.bottom - yScale(d.value))
      .attr("fill", "steelblue");

    // Add axes
    const xAxis = g => g
      .attr("transform", `translate(0,${height - margin.bottom})`)
      .call(d3.axisBottom(xScale));

    const yAxis = g => g
      .attr("transform", `translate(${margin.left},0)`)
      .call(d3.axisLeft(yScale).ticks(5));

    svg.append("g").call(xAxis);
    svg.append("g").call(yAxis);
  }
</script>

<h1 class="body-header">Alcohol Type Consumption by Country</h1>

<p class="body-text">
  With an understanding of how alcohol consumption began in ancient times 
  let us explore how it looks in modern day.
</p>

<p class="body-text">
  <strong>Now it's your turn to explore!</strong>
</p>
  
<p class="body-text">
  <strong>Choose a country from the dropdown/type it in the box**</strong> 
  to generate a bar chart describing different consumptions rates of different 
  alcohol types of your chosen country.
</p>

<p class="body-text">
  The original data set can be found from this 
  <a href="https://github.com/fivethirtyeight/data/tree/master/alcohol-consumption">fivethirtyeight</a> link. 
</p>

<p class="body-text">
  <strong>**</strong>For this prototype our bar chart only shows information for one hard-coded
  country and we hope to improve the interaction of this in the final model.  
</p>

<div id="error-chart" bind:offsetWidth={width} bind:offsetHeight={height}>
  <svg id="visualization" width={width + margin.left + margin.right} height={height + margin.top + margin.bottom}></svg>
</div>

<style>
  #error-chart {
    margin: auto;
    max-height: 55vh;
    width: 58%;
    margin: 1rem auto;
  }

  .error-axis-text {
    font-size: 0.9rem;
  }

  .bar {
    transition: fill 0.3s ease;
  }

  .y-axis-line {
    opacity: 0.2;
  }

  .bar:hover {
    fill: orange;
  }

  /* ipad */
  @media screen and (max-width: 950px) {
    #error-chart {
      max-height: 55vh;
      width: 85%;
      margin: 1rem auto;
    }
    .error-axis-label {
      font-size: 0.8rem;
    }
    .error-axis-text {
      font-size: 0.8rem;
    }
  }

  /* mobile */
  @media screen and (max-width: 750px) {
    #error-chart {
      max-height: 55vh;
      width: 95%;
      margin: 1rem auto;
    }

    .error-axis-label {
      font-size: 0.75rem;
    }
    .error-axis-text {
      font-size: 0.7rem;
    }
  }
</style>
