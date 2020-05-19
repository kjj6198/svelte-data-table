<script>
  import TableHeadColumn from "./TableHeadColumn.svelte";

  export let tableHeads = [];
  export let config;
  export let onSort;
  export let pinToTop = false;
  export let tableRef;

  let shouldPinToTop = false;
  let floatingTh;

  function pin(node, params) {
    if (params.pinToTop) {
      const { x, y } = node.getBoundingClientRect();
      const handlePinTrigger = () => {
        if (
          window.pageYOffset >= y &&
          window.pageYOffset <
            tableRef.clientHeight + tableRef.offsetTop - node.clientHeight &&
          !shouldPinToTop
        ) {
          const theadWidth = node.clientWidth;
          const ths = floatingTh.querySelectorAll("th");
          node
            .querySelectorAll("th")
            .forEach(
              (th, i) => (ths[i].style.minWidth = getComputedStyle(th).width)
            );

          floatingTh.style.top = `0px`; // TODO: top fixed header height?
          floatingTh.style.left = `${x}px`;
          floatingTh.style.width = `${theadWidth}px`;
          shouldPinToTop = true;
        } else if (
          window.pageYOffset < y ||
          window.pageYOffset >
            tableRef.clientHeight + tableRef.offsetTop - node.clientHeight
        ) {
          shouldPinToTop = false;
        }
      };

      window.addEventListener("scroll", handlePinTrigger, { passive: true });

      return {
        destory: () => window.removeEventListener("scroll", handlePinTrigger)
      };
    }
  }
</script>

<style>
  .thead {
    padding: 10px 0;
    background-color: #f2f2f2;
  }
  .floating {
    display: none;
  }

  .shouldPinToTop {
    display: block;
    position: fixed;
    /* /TODO: flexiable z-index */
    z-index: 256;
  }
</style>

<thead class="thead floating" bind:this={floatingTh} class:shouldPinToTop>
  <tr>
    {#each tableHeads as head}
      <TableHeadColumn
        hideMobile={config[head].hideMobile}
        on:sort={onSort}
        sortable={config[head].sortable}
        field={head}
        name={config[head].name}
        align={config[head].align} />
    {/each}
  </tr>
</thead>

<thead class="thead" use:pin={{ pinToTop }}>
  <tr>
    {#each tableHeads as head}
      <TableHeadColumn
        hideMobile={config[head].hideMobile}
        on:sort={onSort}
        sortable={config[head].sortable}
        field={head}
        name={config[head].name}
        align={config[head].align} />
    {/each}
  </tr>
</thead>
