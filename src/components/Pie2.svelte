<script>
  import * as d3 from "d3";
  // our interactive data
  // export let migration_medium_data = [];
  let migration_medium_data = [
    { index: 0, size: 893, name: "Yes, you reside in the destination country" },
    { index: 1, size: 338, name: "Not yet, in transit to destination country" },
    { index: 2, size: 115, name: "Passed away / disappeared" },
    { index: 3, size: 95, name: "Returned to country of origin" },
  ];

  let arcGenerator = d3
    .arc()
    .innerRadius(0)
    .outerRadius(100)
    .padAngle(0)
    .cornerRadius(0);

  let pieAngleGenerator = d3.pie().value((d) => d[0]);
  let arc_data = [];

  const arc_color = d3
    .scaleOrdinal()
    .range(["#67923D", "#ECDC8F", "#B22234", "#F4AD6A"])
    .domain([0, 1, 2, 3]);

  let hovered = -1;

  let recorded_mouse_position = {
    x: 0,
    y: 0,
  };

  $: {
    // interactive data here
    let migration_medium_counts = d3.rollups(
      migration_medium_data,
      (v) => v.length,
      (d) => d.size
    );

    arc_data = pieAngleGenerator(migration_medium_counts);
    console.log(arc_data);
  }
</script>

<h3 style="margin-top: 15px">Migration Succeeded or Failed?</h3>
<div class="visualization">
  <svg class="pie-svg" viewBox="0 0 400 300" transform="translate(100, 0)">
    <g transform="translate(250, 120)">
      {#each arc_data as data, index}
        <path
          d={arcGenerator({
            startAngle: data.startAngle,
            endAngle: data.endAngle,
          })}
          fill={index === hovered ? "#3C3B6E" : arc_color(data.data[0])}
          on:mouseover={(event) => {
            hovered = index;
            recorded_mouse_position = {
              x: event.pageX,
              y: event.pageY,
            };
          }}
          on:mouseout={(event) => {
            hovered = -1;
          }}
        />
      {/each}
    </g>
  </svg>
  <div
    class={hovered === -1 ? "tooltip-hidden" : "tooltip-visible"}
    style="left: {recorded_mouse_position.x +
      40}px; top: {recorded_mouse_position.y + 40}px"
  >
    {#if hovered !== -1}
      {arc_data[hovered].data[0]} people responded:
      {migration_medium_data[arc_data[hovered].index].name}
    {/if}
  </div>
</div>

<style>
  .visualization {
    font: 25px sans-serif;
    margin: auto;
    margin-top: 1px;
    text-align: middle;
  }

  /* dynamic classes for the tooltip */
  .tooltip-hidden {
    visibility: hidden;
    font-family: "Nunito", sans-serif;
    width: 200px;
    position: absolute;
  }

  .tooltip-visible {
    font: 25px sans-serif;
    font-family: "Nunito", sans-serif;
    visibility: visible;
    background-color: #f0dba8;
    border-radius: 10px;
    width: 200px;
    color: black;
    position: absolute;
    padding: 10px;
  }
</style>
