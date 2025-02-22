<script>
  import { getContext } from "svelte"
  import { chart } from "svelte-apexcharts";

  export let dataProvider //
  export let serieField //
  export let selectionMethod ////

  $: rows = (dataProvider && dataProvider.rows && dataProvider.rows.length > 0)
    ? dataProvider.rows
    : [
        {
          NodeId: 'ns=3;s="PRG_HBW_Ablauf_DB"."li_Lights"',
          Value: 1,
          SourceTimestamp: "2025-02-20T17:44:33.3774154Z"
        },
        {
          NodeId: 'ns=3;s="PRG_HBW_Ablauf_DB"."ldi_PosRack_Vertical"',
          Value: 400,
          SourceTimestamp: "2025-02-20T17:44:33.3781391Z"
        },
        {
          NodeId: 'ns=3;s="PRG_HBW_Ablauf_DB"."li_Lights"',
          Value: 1,
          SourceTimestamp: "2025-02-20T17:44:41.2726343Z"
        },
        {
          NodeId: 'ns=3;s="PRG_HBW_Ablauf_DB"."ldi_PosRack_Vertical"',
          Value: 400,
          SourceTimestamp: "2025-02-20T17:44:41.273428Z"
        }
      ];

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
      width: "100%"
    },
    series,
    xaxis: {
      type: "datetime"
    }
  };



  const { styleable } = getContext("sdk")
  const component = getContext("component")
</script>

<div use:styleable={$component.styles}>
  <div use:chart={options} />
</div>
