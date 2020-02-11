<script>
export let pattern
export let instruments
export let show
let height = 0

$: height = show ? 0 : pattern.instruments.filter(({key}) => key !== 'AC').length * 48 - 1
</script>
<div class="border all" style="min-height: {height}px;">
    {#if show}
        {#each pattern.instruments.filter(({key}) => key !== 'AC') as instrument, index (index)}
          <div style="display: flex; ">
            <div class="instrument-wrap">
              <div class="cell border">{instruments[instrument.key].name}</div>
            </div>
            <div style="display: flex; flex-grow: 1;">
                {#each {length: 16} as number, index (index)}
                  <div
                    class="{'cell border ' + (Math.floor(index / 4) % 2 ? 'even' : 'odd') + (instrument.hits.includes(index + 1) ? ' active' : '')}"
                    style={`display: flex; flex-basis: 100%; text-align: center; background-color: ${instrument.hits.includes(index + 1) ? instruments[instrument.key].color : null}`}>
                      {#if instrument.hits.includes(index + 1)}
                        <span class="digit" style="flex-grow: 1;">{index + 1}</span>
                      {/if}
                      {#if instrument.hits.includes(`${index + 1}.1`) || instrument.hits.includes(`${index + 1}.2`)}
                        <div
                          style="border-radius: 2px; flex-grow: 1; margin: 4px 3px 4px 5px; background-color: {instrument.hits.includes(`${index + 1}.1`) ? instruments[instrument.key].color : 'transparent'}"/>
                        <div
                          style="border-radius: 2px; flex-grow: 1; margin: 4px 4px 4px 4px; background-color: {instrument.hits.includes(`${index + 1}.2`) ? instruments[instrument.key].color : 'transparent'}"/>
                      {/if}
                  </div>
                {/each}
            </div>
          </div>
        {/each}
    {/if}
</div>
{#if show}
    {#each pattern.instruments.filter(({key}) => key === 'AC') as instrument, index (index)}
      <div style="display: flex; ">
        <div class="cell accent" style="width: 144px;"/>
        <div style="display: flex; flex-grow: 1;">
            {#each {length: 16} as number, index (index)}
              <div
                class="cell accent"
                style="flex-basis: 100%; text-align: center;">
                  {#if instrument.hits.includes(index + 1)}
                    <span class="dot"/>
                  {/if}
              </div>
            {/each}
        </div>
      </div>
    {/each}
{/if}
<style>
.border {
  border: 1px solid rgba(255, 255, 255, 0.333);
  border-left-width: 0;
  border-top-width: 0;
}

*:last-child > *:last-child > .border {
  border-bottom-width: 0;
}

.border:last-child {
  border-right-width: 0;
}

.border.all {
  border-radius: 12px;
  overflow: hidden;
  border-bottom-width: 0;
  border-right-width: 0;
  box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.75);
}

.border:first-child:last-child {
  border-width: 0 0 0 0;
  font-size: 16px;
  background: #383838;
}

.even {
  background: #5c5c5c;
}

.odd {
  background: #747474;
}

.cell {
  min-height: 47px;
  line-height: 47px;
}

.cell.accent {
  padding: 0;
  min-height: 20px;
  line-height: 0;
  margin-bottom: -20px;
}

.active {
  background-color: rgba(224, 182, 26, 0.94);
  color: #000;
  font-size: 24px;
  font-weight: bold;
}

.digit {
  display: none;
}

.dot {
  display: inline-block;
  background-color: #fff;
  width: 8px;
  height: 8px;
  border-radius: 4px;
  color: rgba(0, 0, 0, 0.87);
  margin: 4px;
}

.instrument-wrap .border {
  width: 144px;
  text-align: center;
}

@media all and (min-width: 880px) {
  .digit {
    display: inline;
  }

  .dot {
    width: 12px;
    height: 12px;
    border-radius: 6px;
  }
}

@media all and (max-width: 360px) {
  .instrument-wrap .border {
    width: 64px !important;
    text-align: left;
    padding: 0 4px;
    text-overflow: ellipsis;
    font-size: 14px !important;
    white-space: nowrap;
    overflow: hidden;
  }

  .cell {
    min-height: 23px;
    line-height: 23px;
  }

  .border.all {
    border-radius: 6px;
  }
}
</style>
