<script>
  import Overlay from './Overlay.svelte';
  import Row from './Row.svelte';
  import Pagination from './Pagination.svelte';
  import { fly } from 'svelte/transition';
  import fetchData from './data';

  /*
    https://svelte.dev/repl/1cb6b4324bc848a28ff6159299518f90?version=3.35.0
    https://stackoverflow.com/questions/17714705/how-to-use-checkbox-inside-select-option
    https://stackoverflow.com/questions/67600186/javascript-array-sort-strings-and-numbers
  */

  let rows = [];
  let path = '';
  let current_page = 1;
  let from = 1;
  let to = 1;
  let per_page = 1;
  let last_page = 1;
  let total = 0;

  let loading = true;


  function sort({ values}, col) {
    console.log(col);
    if (col.asc === true || col.asc == null) {
      columns[columns.indexOf(col)].asc = false;
      return col.type === 'number' 
        ? rows = [...rows.sort((a, b) => a[col.key] - b[col.key]),]
        : rows = [...rows.sort((a, b) => (a[col.key] > b[col.key] ? 1 : -1))];

    } else {
      columns[columns.indexOf(col)].asc = true;
      return col.type === 'number' 
        ? rows = [...rows.sort((a, b) => b[col.key] - a[col.key]),]
        : rows = [...rows.sort((a, b) => (a[col.key] > b[col.key] ? -1 : 1))];
      
    }
    console.log(rows)
  }

  let columns = [
    {
      key: 'id',
      name: 'ID',
      asc: null,
      type: 'string',
    },
    {
      key: 'name',
      name: 'Name',
      asc: null,
      type: 'string'

    },
    {
      key: 'surname',
      name: 'Surname',
      asc: null,
      type: 'number'
    }
  

  ];



  function changePage(params) {
    loading = true;

    fetchData(path, params)
    .then(function (response) {
      path = response.path;
      current_page = response.current_page;
      from = response.from;
      to = response.to;
      total = response.total;
      per_page = response.per_page;
      last_page = response.last_page;

      rows = response.data;
      console.log(rows);
    })
    .catch(error => {
      console.error(error);
    })
    .finally(() => {
      loading = false;
    });
  }

  changePage({ page: 1 });
</script>

<div style="position: relative;" class="section">
  {#if loading}
    <Overlay />
  {/if}

  <table class="table" transition:fly="{{ y: 100, duration: 1000 }}">
    <thead>
      <tr class="css">
        {#each columns as col} 
          <th class="sortable" class:asc={col.asc === null ? false : col.asc} class:desc={col.asc === null ? false : !col.asc} on:click={(e) => sort(e.target, col)}>
            {col.name} <span />
          </th>
        {/each}
       
        <th class="sortable" class:asc={false} class:desc={true}>Actions  <span /></th>
      </tr>
    </thead>
    <tbody>
      {#each rows as row, i}
        <Row {row} />
      {:else}
        <tr>
          <td colspan="100%">
            <h5 class="text-center">There are no Users here.</h5>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>

  {#if total > per_page}
    <Pagination
      {current_page}
      {from}
      {last_page}
      {per_page}
      {to}
      {total}
      on:change="{(ev) => changePage({page: ev.detail})}">
    ></Pagination>
  {/if}
</div>

<style type="text/css">

  .section { 
		position: -webkit-sticky;
		position: sticky;
		top: 0;
		left: 0;
		z-index: 6;
		background: inherit;
	}

  :global(button) {
    color: #fff;
    background-color: #007bff;
    border: 1px solid #007bff;
    padding: .5em;
    line-height: 1.25
  }

  .table {
    width: 100%;
    height: 550px;
    margin-bottom: 1rem;
    color: #212529;
    border-collapse: collapse;
  }

  .table thead th {
    vertical-align: bottom;
    border-bottom: 2px solid #dee2e6;
  }
  .table td, .table th {
    padding: .75rem;
    vertical-align: top;
    border-top: 1px solid #dee2e6;
    text-align: center;
  }
  .text-center {
    text-align: center;
  }


  .css th {
		padding: 8px 0px 8px 16px;
		text-align: center;
		border-bottom: 1px solid #eee;
		background: #fff;
    position: sticky;
    top: 0; /* Don't forget this, required for the stickiness */
	}
	.css th.sortable {
		cursor: pointer;
	}
	.css th.sortable span {
		padding-right: 16px;
		position: relative;
	}
	.css th.sortable span:before,
	.css th.sortable span:after {
		border: 4px solid transparent;
		content: '';
		display: block;
		height: 0;
		right: 0;
		top: 50%;
		position: absolute;
		width: 0;
	}
	.css th.sortable span:before {
		border-bottom-color: #e0e0e0;
		margin-top: -9px;
	}
	.css th.sortable span:after {
		border-top-color: #e0e0e0;
		margin-top: 1px;
	}
	.css th.sortable.asc span:before {
		border-bottom-color: #9e9e9e;
	}
	.css th.sortable.desc span:after {
		border-top-color: #9e9e9e;
	}
</style>