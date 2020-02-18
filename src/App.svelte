<script>
  import { onMount } from "svelte";
  import { DateTime } from "luxon";

  let chart;

  onMount(() => {
    Chartist.plugins = {
      ctTargetLine: function(options) {
        const defaultOptions = {
          class: "ct-target-line",
          color: "blue",
          label: "",
          value: null
        };

        options = Chartist.extend({}, defaultOptions, options);

        return function ctTargetLine(chart) {
          function projectY(chartRect, bounds, value) {
            return chartRect.y1 - (chartRect.height() / bounds.max) * value;
          }

          chart.on("created", function(context) {
            var targetLineY = projectY(
              context.chartRect,
              context.bounds,
              options.value
            );

            if (options.showOverlay) {
              context.svg.elem(
                "rect",
                {
                  width: context.chartRect.x2 - context.chartRect.x1 + 5,
                  height: targetLineY - context.chartRect.y2,
                  x: context.chartRect.x1,
                  y: context.chartRect.y2,
                  style: `fill: ${options.color}; opacity: 0.075; z-index: -5;`
                },
                options.class
              );
            }

            context.svg.elem(
              "line",
              {
                x1: context.chartRect.x1,
                x2: context.chartRect.x2,
                y1: targetLineY,
                y2: targetLineY,
                style: `stroke: ${options.color}`
              },
              options.class
            );

            context.svg
              .elem(
                "text",
                {
                  x: context.chartRect.x2,
                  y: targetLineY + 20,
                  style: `text-anchor: end; fill: ${options.color};`
                },
                ""
              )
              .text(options.label);
          });
        };
      }
    };

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
        }
      ]
    };

    // Create a new line chart object where as first parameter we pass in a selector
    // that is resolving to our chart container element. The Second parameter
    // is the actual data object.
    new Chartist.Line(chart, data, {
      high: 20,
      low: 0,
      axisX: {
        showGrid: true
      },
      axisY: {
        onlyInteger: true
      },
      plugins: [
        Chartist.plugins.ctTargetLine({
          color: "#e84747",
          label: "Standardised Definition of Burnout",
          value: 13,
          showOverlay: true
        }),
        Chartist.plugins.ctTargetLine({
          color: "#E847E8",
          label: "National Average",
          value: 15,
          showOverlay: false
        })
      ]
    });
  });
</script>

<style>
  .cont {
    margin-left: 10px;
    margin-right: 10px;
  }
</style>

<h1>Burnout Scale Results (Weekly)</h1>
<h2>Imperial College NHS</h2>
<div bind:this={chart} class="ct-chart ct-perfect-fourth" />
<p>
  <em>Data for illustrative purposes only</em>
</p>
<h3>About this data</h3>
<p>
  Etiam aliquam cubilia fringilla lacus turpis nisi rhoncus tempus, senectus
  ridiculus accumsan molestie malesuada magna potenti justo tincidunt, facilisi
  sed consequat sem taciti ante leo. Tempor ultricies placerat dui neque
  volutpat vehicula morbi augue porta ut lectus, a sagittis eros mus consequat
  ac nulla aenean leo vivamus. Augue quam lacinia pharetra enim donec dictum in,
  habitant molestie tempus dignissim urna fusce imperdiet, tellus aliquam ac
  bibendum vehicula blandit.
</p>
