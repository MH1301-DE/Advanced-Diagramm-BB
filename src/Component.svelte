<script>
  import { getContext } from "svelte"
  import { chart } from "svelte-apexcharts";

  export let dataProvider //
  export let chartWidth //
  export let chartHeight //

  $: rows = (dataProvider && dataProvider.rows)
    ? (Array.isArray(dataProvider.rows)
         ? dataProvider.rows
         : Object.values(dataProvider.rows))
    : [];

  // Beispiel: Alle eindeutigen NodeIds extrahieren und in das series-Array verpacken
  // $: series = rows.length > 0
  //   ? Array.from(new Set(rows.map(row => row.NodeId))).map(nodeId => ({
  //       name: nodeId,
  //       data: rows.filter(row => row.NodeId === nodeId).map(row => row.Value)
  //     }))
  //   : [];
    // ? dataProvider.rows
    // : [
    //     {
    //       NodeId: 'ns=3;s="PRG_HBW_Ablauf_DB"."li_Lights"',
    //       Value: 1,
    //       SourceTimestamp: "2025-02-20T17:44:33.3774154Z"
    //     },
    //     {
    //       NodeId: 'ns=3;s="PRG_HBW_Ablauf_DB"."ldi_PosRack_Vertical"',
    //       Value: 400,
    //       SourceTimestamp: "2025-02-20T17:44:33.3781391Z"
    //     },
    //     {
    //       NodeId: 'ns=3;s="PRG_HBW_Ablauf_DB"."li_Lights"',
    //       Value: 1,
    //       SourceTimestamp: "2025-02-20T17:44:41.2726343Z"
    //     },
    //     {
    //       NodeId: 'ns=3;s="PRG_HBW_Ablauf_DB"."ldi_PosRack_Vertical"',
    //       Value: 400,
    //       SourceTimestamp: "2025-02-20T17:44:41.273428Z"
    //     }
    //   ];

  // Extrahiere alle eindeutigen NodeIds
  $: uniqueName = Array.from(new Set(rows.map(row => row.Name)));

  // // Erzeuge das series-Array: Für jede NodeId wird ein Array von Datenpunkten erstellt,
  // // in dem x der Timestamp (als Number) und y der zugehörige Value ist.
  $: series = uniqueName.map(Name => {
    const dataPoints = rows
      .filter(row => row.Name === Name)
      .map(row => ({
        x: new Date(row.SourceTimestamp).getTime(),
        y: row.Value
      }));
    return { name: Name, data: dataPoints };
  });
  // // Options-Objekt im Format, das z. B. ApexCharts erwartet
  $: options = {
    chart: {
      type: "line",
      height: chartHeight
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
  <div use:chart={options} />
</div>
