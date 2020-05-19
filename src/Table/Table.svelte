<script>
  import TableCell from "./TableCell.svelte";
  import TableHead from "./TableHead.svelte";
  import { tick } from "svelte";
  export let config;
  export let pinToTop = false;
  export let data;
  export let title;
  let sortType = null;
  let tableRef;
  $: tableHeads = Object.keys(config);
  $: originalData = data.slice();

  let sortedData = null;

  async function handleSort(e) {
    const field = e.detail.field;
    sortType = e.detail.sortType;
    await tick();
    const comparator = config[field].comparator;

    if (comparator) {
      const accessor =
        config[field].accessor ||
        function accessor(d) {
          return d[field];
        };

      if (sortType === "asc") {
        sortedData = originalData.sort((a, b) =>
          comparator(accessor(a), accessor(b))
        );
      } else if (sortType === "desc") {
        sortedData = originalData.sort((a, b) =>
          comparator(accessor(b), accessor(a))
        );
      } else {
        sortType = null;
        sortedData = null;
      }
    }
  }
</script>

<style>
  .caption {
    caption-side: top;
    padding: 12px;
    text-align: center;
    background-color: #fff;
  }

  @media screen and (max-width: 680px) {
    .table {
      font-size: 14px !important;
    }
  }
  .table {
    table-layout: auto;
    position: relative;
    min-width: 100%;
    margin-left: auto;
    margin-right: auto;
    font-variant-numeric: lining-nums tabular-nums;
    border-collapse: collapse;
    background-color: #fff;
    font-size: 16px;
  }

  .tr:hover {
    background-color: #ddd;
  }

  .tr:nth-child(even) {
    background-color: #efefef;
  }
</style>

<table class="table" bind:this={tableRef}>
  {#if title}
    <caption class="caption">
      <slot name="caption">
        <h2>{title}</h2>
      </slot>
    </caption>
  {/if}
  <TableHead {tableRef} {tableHeads} {config} onSort={handleSort} {pinToTop} />
  <tbody>
    {#each sortedData || data as item, i}
      <tr class="tr">
        {#each Object.keys(config) as itemKey}
          <TableCell
            hideMobile={config[itemKey].hideMobile}
            type={config[itemKey].type}
            data={item}
            formatter={config[itemKey].formatter}
            accessor={config[itemKey].accessor}
            config={config[itemKey]}
            props={config[itemKey].props || function() {
                return {};
              }}
            component={config[itemKey].component}
            align={config[itemKey].align || 'left'}
            field={itemKey} />
        {/each}
      </tr>
    {/each}
  </tbody>
</table>
