<script>
  import { onMount } from "svelte";
  import { DateTime } from "luxon";
  import "chartist-plugin-targetline";

  let chart;

  onMount(() => {
    console.log(Chartist.plugins);

    const days = [...Array(14).keys()]
      .map(index => {
        return DateTime.local()
          .minus({ days: 1 })
          .minus({ days: 7 * index });
      })
      .reverse();

    var data = {
      // A labels array that can contain any sort of values
      labels: days.map(day => day.toLocaleString()),
      // Our series array that contains series objects or in this case series data arrays
      series: [
        {
          value: [16, 11, 17, 15, 16, 14, 12, 12, 15, 12, 12, 15, 15, 17]
        },
        {
          value: [7, 8, 7, 10, 11, 11, 6, 11, 6, 9, 7, 6, 6, 11]
        },
        {
          value: [7, 8, 7, 10, 11, 11, 6, 11, 6, 9, 7, 6, 6, 11]
        }
      ]
    };

    // Create a new line chart object where as first parameter we pass in a selector
    // that is resolving to our chart container element. The Second parameter
    // is the actual data object.
    new Chartist.Line(
      chart,
      data,
      {
        high: 20,
        low: 0,
        axisX: {
          showGrid: true
        },
        axisY: {
          onlyInteger: true
        }
      },
      {
        plugins: [
          Chartist.plugins.ctTargetLine({
            value: 4
          })
        ]
      }
    );
  });
</script>

<style>
  .cont {
    margin-left: 10px;
    margin-right: 10px;
  }
</style>

<h1>Results Dashboard</h1>
<h2>Imperial College NHS</h2>
<h3>Burnout Scale Results</h3>
<div bind:this={chart} class="ct-chart ct-perfect-fourth" />
<p>
  <em>Data for illustrative purposes only</em>
</p>
