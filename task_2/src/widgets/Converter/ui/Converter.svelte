<script lang="ts">
  import { onMount } from 'svelte'
  import { fetchGetCodes, codes } from '../model/codes.store'
  import { get } from 'svelte/store'
  import { Select } from '../../../shared'

  let codesList: string[] = []
  let selectedFirst: string = ''
  let selectedSecond: string = ''

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
  })

  $: filteredListFirst = codesList.filter((item) => item !== selectedSecond)
  $: filteredListSecond = codesList.filter((item) => item !== selectedFirst)
</script>

<div class="converter">
  <h1>Converter</h1>
  {#if codesList.length}
    <div class="converter__grid">
      <Select
        selected={selectedFirst}
        options={filteredListFirst}
        on:change={updateSelectedFirst}
      />
      <Select
        selected={selectedSecond}
        options={filteredListSecond}
        on:change={updateSelectedSecond}
      />
    </div>
  {/if}
</div>
