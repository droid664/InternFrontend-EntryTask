<script lang="ts">
  import { onMount } from 'svelte'
  import { fetchGetCodes, codes } from '../model/codes.store'
  import { get } from 'svelte/store'
  import { Select } from '../../../shared'
  import { parseCurrency } from '../model/parseCurrency'
  import { fetchGetPairConversion, pairData } from '../model/pariConversion.store'

  let codesList: string[] = []
  let selectedFirst: string = ''
  let selectedSecond: string = ''
  let sumFirst: number = 1
  let sumSecond: number = 0

  const updateSelectedFirst = async (event: CustomEvent<string>) => {
    selectedFirst = event.detail

    await changeSelectedCurrency()
    updateSecondValue()
  }

  const updateSelectedSecond = async (event: CustomEvent<string>) => {
    selectedSecond = event.detail

    await changeSelectedCurrency()
    updateFirstValue()
  }

  const updateFirstValue = () => {
    sumFirst = calcCurrencyValue(sumSecond)
  }

  const updateSecondValue = () => {
    sumSecond = calcCurrencyValue(sumFirst)
  }

  const calcCurrencyValue = (value: number) => {
    return get(pairData).conversion_rate * value
  }

  const changeSelectedCurrency = async () => {
    const first = parseCurrency(selectedFirst)
    const second = parseCurrency(selectedSecond)

    await fetchGetPairConversion(first, second)
  }

  onMount(async () => {
    await fetchGetCodes()

    codesList = get(codes)

    selectedFirst = codesList[0]
    selectedSecond = codesList[1]

    await changeSelectedCurrency()

    updateSecondValue()
  })

  $: filteredListFirst = codesList.filter((item) => item !== selectedSecond)
  $: filteredListSecond = codesList.filter((item) => item !== selectedFirst)
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
        <input
          type="text"
          class="form-control"
          bind:value={sumFirst}
          on:input={() => updateSecondValue()}
        />
      </div>
      <div class="converter__grid-item">
        <h2>{selectedSecond}</h2>
        <Select
          selected={selectedSecond}
          options={filteredListSecond}
          on:change={updateSelectedSecond}
        />
        <input
          type="text"
          class="form-control"
          bind:value={sumSecond}
          on:input={() => updateFirstValue()}
        />
      </div>
    </div>
  {/if}
</div>
