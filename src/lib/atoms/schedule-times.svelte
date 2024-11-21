<!--!
  For every time with 1 hour it shows the time,
  except for the last it shows 23:59!
-->
<div class="schedule-times">
  {#each Array(24) as _, i}
    <div
      class="schedule-time-outer"
      style="--start: {i * 50}; --end: {i * 50 + 50};"
    >
      <div class="schedule-times__time">
        {i === 23 ? "23:59" : i < 10 ? `0${i}:00` : `${i}:00`}
      </div>

      {#if i < 23}
        <div
          class="dotted-line"
          style="--start: {i * 50 * 2}; --end: {i * 50 + 50};"
        ></div>
      {/if}
    </div>
  {/each}
</div>

<style>
  .schedule-times {
    position: relative;
    margin-left: calc(100px + 20px);
    display: grid;
    grid-template-columns: repeat(calc(2359 / 2.001953), 3px);
  }

  .schedule-time-outer {
    position: relative;
    grid-column-start: calc(var(--start) + 1);
    grid-column-end: calc(var(--end) + 1);
    /* width: fit-content; */
    display: flex;
    align-items: center;
  }

  .schedule-times__time {
    position: relative;
  }

  .schedule-time-outer:last-child {
    align-items: flex-end;
    width: fit-content;
  }

  .dotted-line {
    top: 50%;

    height: 3px;
    background: none;
    border-top: 3px dotted black;
    height: 3px;
    width: -webkit-fill-available;
    margin: 0 8px;
  }
</style>
