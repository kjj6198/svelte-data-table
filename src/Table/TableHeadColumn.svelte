<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();
  export let name;
  export let align;
  export let sortable;
  export let field;
  export let hideMobile;

  let currentSortType = "none";

  function nextState(sortType) {
    if (sortType === "none") {
      return "asc";
    } else if (sortType === "asc") {
      return "desc";
    } else if (sortType === "desc") {
      return "none";
    }
  }

  function handleSortClick(sortType) {
    if (currentSortType === sortType) {
      return;
    }

    currentSortType = sortType;
    dispatch("sort", {
      sortType: currentSortType,
      field
    });
  }

  function getClassNames() {
    if (sortable) {
      return `sortable align-${align}`;
    } else if (align) {
      return `align-${align}`;
    }

    return "";
  }

  function getSortName(sortType) {
    if (sortType === "none") {
      return "none";
    } else if (sortType === "desc") {
      return "descending";
    } else {
      return "ascending";
    }
  }

  function handleKeydown(e) {
    e.stopPropagation();
    if (e.keyCode === 13) {
      handleSortClick(nextState(currentSortType));
    }
  }

  $: className = getClassNames();
</script>

<style>
  th {
    font-size: 18px;
    color: #424242;
    position: relative;
    padding: 8px;
    line-height: 1.67;
  }

  @media (max-width: 680px) {
    .hideMobile {
      display: none;
    }

    th {
      font-size: 12px;
    }
  }
  .align-right {
    text-align: right;
  }

  .align-left {
    text-align: left;
  }

  .align-center {
    text-align: center;
  }

  .sortable {
    position: relative;
    cursor: pointer;
  }

  .sortable > span {
    position: relative;
    display: inline-block;
    padding-right: 1em;
    user-select: none;
  }

  .sortable > span:before,
  .sortable > span:after {
    display: none;
    content: "";
    right: 0;
    position: absolute;
    border-style: solid;
  }

  .desc > span:before {
    display: block;
    bottom: 30%;
    border-width: 5px 5px 0px 5px;
    border-color: #999 transparent transparent transparent;
  }

  .asc > span:before {
    display: block;
    top: 30%;
    border-width: 0 5px 5px 5px;
    border-color: transparent transparent #999;
  }

  .none > span:after {
    display: block;
    content: "";
    bottom: 30%;
    border-width: 5px 5px 0px 5px;
    border-color: #999 transparent transparent transparent;
  }
  .none > span:before {
    display: block;
    top: 30%;
    border-width: 0 5px 5px 5px;
    border-color: transparent transparent #999;
  }
</style>

<th
  scope="col"
  data-field={field}
  aria-sort={sortable && currentSortType}
  tabindex={sortable ? '0' : null}
  aria-label={`${name}: activate to sort column ${getSortName(currentSortType)}`}
  class={className}
  class:hideMobile
  class:desc={sortable && currentSortType === 'desc'}
  class:asc={sortable && currentSortType === 'asc'}
  class:none={sortable && currentSortType === 'none'}
  on:keydown={sortable ? handleKeydown : null}
  on:click={() => handleSortClick(nextState(currentSortType))}>
  <span>{name}</span>
</th>
