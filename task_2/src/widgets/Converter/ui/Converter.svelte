<script lang="ts">
  import { onMount } from 'svelte'
  import { fetchGetCodes, codes } from '../model/codes.store'
  import { get } from 'svelte/store'
  import { Select } from '../../../shared'
  import { parseCurrency } from '../model/parseCurrency'
  import { fetchGetPairConversion } from '../model/pariConversion.store'

  let codesList: string[] = []
  let selectedFirst: string = ''
  let selectedSecond: string = ''
  let currencyFirst: string = ''
  let currencySecond: string = ''
  let sumFirst: number = 1
  let sumSecond: number = 0

  const updateSelectedFirst = (event: CustomEvent<string>) => {
    selectedFirst = event.detail
  }

  const updateSelectedSecond = (event: CustomEvent<string>) => {
    selectedSecond = event.detail
  }

  onMount(async () => {
    await fetchGetCodes()

    codesList = get(codes)

    selectedFirst = codesList[0]
    selectedSecond = codesList[1]
    currencyFirst = parseCurrency(selectedFirst)
    currencySecond = parseCurrency(selectedSecond)

    await fetchGetPairConversion(currencyFirst, currencySecond)
  })

  $: filteredListFirst = codesList.filter((item) => item !== selectedSecond)
  $: filteredListSecond = codesList.filter((item) => item !== selectedFirst)
  $: currencyFirst = parseCurrency(selectedFirst)
  $: currencySecond = parseCurrency(selectedSecond)
</script>

<div class="converter">
  <h1>Converter</h1>
  {#if codesList.length}
    <div class="converter__grid">
      <div class="converter__grid-item">
        <h2>{selectedFirst}</h2>
        <Select
          selected={selectedFirst}
          options={filteredListFirst}
          on:change={updateSelectedFirst}
        />
        <input type="text" class="form-control" bind:value={sumFirst} />
      </div>
      <div class="converter__grid-item">
        <h2>{selectedSecond}</h2>
        <Select
          selected={selectedSecond}
          options={filteredListSecond}
          on:change={updateSelectedSecond}
        />
        <input type="text" class="form-control" bind:value={sumSecond} />
      </div>
    </div>
  {/if}
</div>
