<script>
  // General imports
  import { onMount } from "svelte";
  import Clock from "$lib/atoms/icon.svelte";

  // data import
  export let status = "";
  export let jdImgSrc = "";
  export let thumbnailImgSrc = "";
  export let programName = "Unnamed Program";
  export let description;

  export let time = "00:00 - 00:00";
  export let programLink = "";

  // Time splitting
  let parts = time.split(" - ");
  let tempstart = parseInt(parts[0].replace(":", ""));
  let tempend = parseInt(parts[1].replace(":", ""));

  // Calc zodat de grid niet te groot wordt
  const start = tempstart / 2.02;
  const end = tempend / 2.02;

  const updateStatus = () => {
    const date = new Date();
    const currentTime = date.getHours() * 100 + date.getMinutes();
    status = tempstart <= currentTime && currentTime <= tempend ? "active" : "";
  };

  onMount(() => {
    updateStatus();
    setInterval(updateStatus, 60000);
  });

  onMount(() => {
    const popoverButton = document.querySelector(
      `[popovertarget="popover-${programLink}"]`,
    );
    const popover = document.querySelector(`#popover-${programLink}`);
    const closePopoverButton = popover?.querySelector(".close");

    function togglePopover() {
      popover.togglePopover();
    }

    if (popoverButton && popover) {
      popoverButton.addEventListener("click", (e) => {
        if (document.startViewTransition) {
          console.log("startViewTransition open");
          document.startViewTransition(togglePopover);
        } else {
          togglePopover();
        }
        e.preventDefault();
      });

      // close button
      closePopoverButton.addEventListener("click", (e) => {
        if (document.startViewTransition) {
          console.log("startViewTransition close");
          document.startViewTransition(togglePopover);
        } else {
          togglePopover();
        }
        e.preventDefault();
      });
    }
  });
</script>

<button
  class="card {status}"
  popovertarget="popover-{programLink}"
  style="--start:{start}; --end:{end}; --vt:{programLink}"
>
  <picture class="image">
    <source
      srcset={jdImgSrc
        ? `https://fdnd-agency.directus.app/assets/${jdImgSrc}?format=webp&height=96&width=96`
        : "/path/to/default/image.jpg"}
      alt={programName}
    />
    <source
      srcset={jdImgSrc
        ? `https://fdnd-agency.directus.app/assets/${jdImgSrc}?format=jpg&height=96&width=96`
        : "/path/to/default/image.jpg"}
      alt={programName}
    />
    <img
      loading="lazy"
      src={jdImgSrc
        ? `https://fdnd-agency.directus.app/assets/${jdImgSrc}?format=jpeg&height=96&width=96`
        : "/path/to/default/image.jpg"}
      alt={programName}
    />
  </picture>

  <article class="content">
    <div class="title-wrapper">
      <h2>{programName}</h2>
    </div>

    <div class="events">
      <div class="time-stamp">
        <Clock data={tempstart} />
        <span>{time}</span>
      </div>

      <div class="visit-url">
        <svg
          width="16"
          height="16"
          viewBox="0 0 16 16"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <g clip-path="url(#clip0_208_1034)">
            <path
              d="M5.13683 2.47016C4.87729 2.7297 4.87715 3.15045 5.13651 3.41016L8.82085 7.09956C9.21092 7.49016 9.2107 8.12295 8.82037 8.51329L5.13713 12.1965C4.87748 12.4562 4.87748 12.8772 5.13713 13.1368C5.39667 13.3963 5.81744 13.3965 6.07713 13.1371L10.7057 8.51377C11.0966 8.12331 11.0968 7.48984 10.7061 7.09915L6.07715 2.47016C5.81749 2.2105 5.39649 2.2105 5.13683 2.47016Z"
              fill="white"
            />
          </g>
          <defs>
            <clipPath id="clip0_208_1034">
              <rect
                width="6.74667"
                height="11.6067"
                fill="white"
                transform="translate(4.66699 2)"
              />
            </clipPath>
          </defs>
        </svg>
      </div>
    </div>
  </article>
</button>

<div
  data-sveltekit-preload-data="hover"
  class="popover"
  popover
  id="popover-{programLink}"
  style="--vt:{programLink}"
>
  <div class="popover-card">
    <picture class="image">
      <source
        srcset={thumbnailImgSrc
          ? `https://fdnd-agency.directus.app/assets/${thumbnailImgSrc}?format=webp`
          : "/path/to/default/image.jpg"}
        alt={programName}
      />
      <source
        srcset={thumbnailImgSrc
          ? `https://fdnd-agency.directus.app/assets/${thumbnailImgSrc}?format=jpg`
          : "/path/to/default/image.jpg"}
        alt={programName}
      />
      <img
        loading="lazy"
        src={thumbnailImgSrc
          ? `https://fdnd-agency.directus.app/assets/${thumbnailImgSrc}?format=jpeg`
          : "/path/to/default/image.jpg"}
        alt={programName}
      />
    </picture>

    <input
      type="button"
      value="Close"
      class="close"
      popovertarget="popover-{programLink}"
    />

    <article class="content">
      <div class="title-wrapper">
        <h2>{programName}</h2>
        <p>{@html description}</p>
      </div>

      <div class="events">
        <div class="time-stamp">
          <Clock data={tempstart} />
          <span>{time}</span>
        </div>
      </div>
    </article>
  </div>
</div>

<style>
  :root {
    --calc: calc(2rem - 1rem);
  }

  /* Popover */

  [popover] {
    position: absolute;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(0, 0, 0, 0.5);
    z-index: 1000;
    pointer-events: none;
  }

  [popover] .popover-card {
    pointer-events: all;
    border: none;
    min-height: 100vh;
    height: fit-content;
    width: 100%;
    overflow: hidden;
    border-color: #fff;
    background-color: #fafafa;

    @media (min-width: 40em) {
      min-height: auto;
      border-style: solid;
      border-width: 2px;
      border-radius: 8px;
      box-shadow: 0px 8px 16px rgba(30, 30, 30, 0.08);
      min-height: 50vh;
      width: 50vw;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
    }
  }

  [popover] .popover-card .image {
    overflow: hidden;
    align-items: center;
    display: flex;
    justify-content: center;
    width: 100%;

    @media (min-width: 40em) {
      border-radius: 8px 8px 0 0;
    }
  }

  [popover] .popover-card .image img {
    width: 100%;
    height: 30vh;
    object-fit: cover;
    background: #f8f8f8;
  }

  [popover] .popover-card .content {
    padding: 1rem;
  }

  [popover] .popover-card .close {
    position: absolute;
    right: 1rem;
    top: 1rem;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 10px;
    font-size: 1.1em;
    font-weight: bold;
    border: none;
    padding: 0.5em 1.5em 0.5em 1.5em;
    cursor: pointer;
    transition: 0.1s ease;
  }

  [popover] .events {
    margin-top: 0.5rem;
  }

  [popover] h2 {
    color: #1e1e1e;
    font-size: 1.4rem;
    font-weight: 700;
    line-height: 1.1;
    margin-bottom: 0.5rem;
  }

  [popover] p {
    color: #5a5a5a;
    font-size: 1rem;
    line-height: 1.2;
  }

  /* Card */

  .card {
    --vt: "";
    cursor: pointer;
    align-items: center;
    text-align: left;
    font-size: inherit;
    background-color: #ffffff;
    border-color: #fff;
    border-radius: 8px;
    border-style: solid;
    border-width: 2px;
    box-shadow: 0px 8px 16px rgba(30, 30, 30, 0.08);
    color: #1e1e1e;
    /* Add this to make @Container Queries work */
    container-type: inline-size;
    display: block;
    display: flex;
    grid-column-end: calc(var(--end) + 1);
    grid-column-start: calc(var(--start) + 1);
    height: auto;
    margin-right: 8px;
  }

  @media (prefers-reduced-motion) {
    .card {
      border-radius: 0;
      border-right: 2px solid #e8e8e8;
      box-shadow: none;
      overflow: hidden;
      text-overflow: ellipsis;
      width: 100%;
    }

    .card:hover,
    .card.active:hover {
      background-color: #e8e8e8;
      border-color: #e8e8e8;
      box-shadow: none !important;
    }

    .card.active:hover {
      border-color: #fe0170;
    }

    h2 {
      animation: none !important;
    }
  }

  .card.active {
    border-color: #fe0170;
    box-shadow: 0px 0px 0px 2px inset transparent;
  }

  .card:hover,
  .card.active:hover {
    box-shadow: 0px 8px 16px rgba(30, 30, 30, 0.16);
  }

  .card picture {
    align-items: center;
    display: flex;
    justify-content: center;
    max-width: 100px;
    padding: 0 var(--calc);
    width: 100px;

    @container (width < 400px) {
      display: none;
    }
  }

  .content:after {
    background-color: #e8e8e8;
    content: "";
    display: none;
    height: calc(100% - 4px);
    inset: 0;
    position: absolute;
    top: 2px;
    width: 2px;

    @container (width < 400px) {
      display: none;
    }
  }

  .card img {
    border-radius: 9999px;
    height: 48px;
    object-fit: cover;
    width: 48px;
  }

  .card .content {
    display: flex;
    flex-direction: column;
    gap: 0.75rem;
    justify-content: space-between;
    left: -10px;
    padding: var(--calc);
    position: relative;
    width: 100%;
  }

  .card .title-wrapper {
    max-width: 100%;
    overflow: hidden;

    @container (width > 400px) {
      left: 0;
      max-width: fit-content;
      position: sticky;
    }
  }

  .card h2 {
    color: inherit;
    display: inline-block;
    font-size: 1.4rem;
    font-weight: 700;
    line-height: 1.1;
    white-space: nowrap;
    width: 100%;

    @container (width < 200px) {
      animation:
        scrolling-left 20s linear infinite,
        teleport-back 20s linear infinite;
    }
  }

  .events {
    align-items: center;
    display: flex;
    gap: 16px;
    justify-content: space-between;
    position: relative;
    width: 100%;

    @container (width > 400px) {
      left: 0;
      max-width: fit-content;
      position: sticky;
    }
  }

  .time-stamp {
    align-items: center;
    color: color-mix(inherit 50%, #8b8b8b 50%);
    display: flex;
    gap: 8px;

    @container (width < 200px) {
      display: none;
    }
  }

  :global(.time-stamp .icon) {
    @container (width < 400px) {
      display: none;
    }
  }

  .time-stamp path {
    fill: #5a5a5a;
  }

  .visit-url {
    align-items: center;
    background-color: #fe0170;
    border-radius: 999px;
    display: flex;
    display: none;
    height: 24px;
    justify-content: center;
    width: 24px;

    @container (width < 200px) {
      display: none;
    }
  }

  @keyframes scrolling-left {
    0% {
      transform: translateX(0%);
    }
    70% {
      transform: translateX(-100%);
    }
    100% {
      transform: translateX(-100%);
    }
  }

  @keyframes teleport-back {
    0%,
    20% {
      transform: translateX(0%);
    }
    70% {
      transform: translateX(-100%);
    }
    80% {
      transform: translateX(100%);
    }
    100% {
      transform: translateX(0%);
    }
  }

  @media (min-width: 40em) {
    :root {
      --calc: calc(2rem - 0rem);
    }

    .visit-url {
      display: flex;
    }
  }
</style>
