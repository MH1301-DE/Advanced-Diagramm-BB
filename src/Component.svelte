<script>
  import { getContext } from "svelte"
  import { chart } from "svelte-apexcharts";

  export let dataProvider //
  export let chartWidth //
  export let chartHeight //
  export let seriesNames

  $: rows = (dataProvider && dataProvider.rows)
    ? (Array.isArray(dataProvider.rows)
         ? dataProvider.rows
         : Object.values(dataProvider.rows))
    : [];

  // Extrahiere alle eindeutigen NodeIds
  $: uniqueNodes = Array.from(new Set(rows.map(row => row.NodeId)));

  // Erzeuge das series-Array: Für jede NodeId wird ein Array von Datenpunkten erstellt,
  // in dem x der Timestamp (als Number) und y der zugehörige Value ist.
  $: series = uniqueNodes.map(nodeId => {
    const dataPoints = rows
      .filter(row => row.NodeId === nodeId)
      .map(row => ({
        x: new Date(row.SourceTimestamp).getTime(),
        y: row.Value
      }));
    return { name: nodeId, data: dataPoints };
  });
  // Options-Objekt im Format, das z. B. ApexCharts erwartet
  $: options = {
    chart: {
      type: "line",
      height: chartHeight
    },
    stroke: {
      curve: 'stepline'
    },
    series,
    xaxis: {
      type: "datetime"
    }
  };

  if (chartWidth != null){
    options.chart.width = chartWidth;
  };

  const { styleable } = getContext("sdk")
  const component = getContext("component")
</script>

<div use:styleable={$component.styles}>
  <div use:chart={options}/>
</div>
