<script lang="ts" context="module">
  export enum Side {
    Word = 'word',
    Definition = 'definition',
  }
</script>

<script lang="ts">
  export let word: string;
  export let wordDetail = '';
  export let definition: string | string[];
  let initialSide: Side;
  export let scored: number | undefined = undefined;

  export { initialSide as side };

  let currentSide = initialSide;
  let currentWord = word;
  let flipped;

  $: displayDefinition = Array.isArray(definition) ? definition.slice(0, 4) : definition;
  $: wordFontSize = getFontSize(word?.length ?? 0);
  $: definitionFontSize = getFontSize(
    Array.isArray(displayDefinition)
      ? displayDefinition.join('    ').length
      : displayDefinition?.length ?? 0,
  );
  $: {
    if (word !== currentWord) {
      currentSide = initialSide;
    }
  }

  function getFontSize(textLength) {
    if (textLength > 100) return 'small';
    if (textLength > 30) return 'normal';
    return 'big';
  }

  function flipCard() {
    flipped = true;
    if (currentSide === Side.Definition) currentSide = Side.Word;
    else if (currentSide === Side.Word) currentSide = Side.Definition;
  }
</script>

<button
  on:click={flipCard}
  disabled={scored}
  class="card"
  class:flipped={currentSide === Side.Definition}
  class:scored
>
  <div class="card-front font-size-{wordFontSize}">
    <div class="card-content">
      <p class="word">{word}</p>
      {#if wordDetail}
        <p class="word-detail">{wordDetail}</p>
      {/if}
    </div>
  </div>

  <div class="card-back font-size-{definitionFontSize}">
    {#if Array.isArray(displayDefinition)}
      <ul class="card-content content-list">
        {#each displayDefinition as definition}
          <li>{definition}</li>
        {/each}
      </ul>
    {:else}
      <div class="card-content">{displayDefinition}</div>
    {/if}
  </div>
</button>

<style>
  .card {
    display: grid;
    max-width: 360px;
    min-height: 150px;
    max-height: 180px;
    width: 80vw;
    height: 40vh;

    margin: auto;

    text-align: left;

    transition: transform 0.8s;
    transform-style: preserve-3d;

    background-color: transparent;
    border: 1px solid #aaa;
    border-radius: 4px;
  }

  .card:focus {
    border: 2px solid #666;
  }

  .card:disabled {
    color: initial;
  }

  .card-content {
    padding: 20px;
  }

  .content-list {
    width: 100%;
    padding: 40px;
  }
  .card:focus .content-list {
    padding: 39px;
  }

  .flipped {
    transform: rotateY(180deg);
  }

  .card > * {
    display: flex;
    align-items: center;
    justify-content: center;

    box-shadow: rgba(0, 0, 0, 0.08) 0px 4px 12px;

    position: absolute;
    width: 100%;
    height: 100%;

    backface-visibility: hidden;
    -webkit-backface-visibility: hidden;
  }

  .card:not(.scored) {
    user-select: initial;
  }

  .card-front {
    text-align: center;
  }

  .card-front p {
    margin: 10px 0;
  }

  .word-detail {
    font-size: 0.7em;
  }

  .card-back {
    color: white;
    background-color: dodgerblue;
    transform: rotateY(180deg);
  }

  .font-size-small {
    font-size: 1.1em;
    font-weight: 200;
  }

  .font-size-normal {
    font-size: 1.5em;
    font-weight: 200;
  }

  .font-size-big {
    font-size: 2em;
    font-weight: 100;
  }

  /*
  .card.scored.been-flipped .card-front::after {
    content: '';
    position: absolute;
    top: 0;
    right: 0;
    border-color: transparent;
    border-style: solid;
  }

  .card.scored.been-flipped .card-front::after {
    border-width: 0.45em;
    border-right-color: #f00;
    border-top-color: #f00;
    backface-visibility: hidden;
    -webkit-backface-visibility: hidden;
  }
  */
</style>
