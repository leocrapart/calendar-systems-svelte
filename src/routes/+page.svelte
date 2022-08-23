<script>
  let feedback = "";

  // converter algorithm

  let bisextile = false;

  function generate_bion_days() {
    const alphabet = "abcdefghijklmnopqrstuvwxyz";
    let days = [];
    // first half
    for (const letter of alphabet) {
      days.push(letter + "ion");
    }

    // second half
    for (const letter of alphabet) {
      days.push(letter + "ion bis");
    }
    return days;
  }

  function generate_bion_calendar(bisextile) {
    let calendar = [];
    const bion_cycles = [
      "1er cycle",
      "2e cycle",
      "3e cycle",
      "4e cycle",
      "5e cycle",
      "6e cycle",
      "7e cycle",
    ];
    const bion_days = generate_bion_days();

    for (const cycle of bion_cycles) {
      for (const day of bion_days) {
        calendar.push(day + " " + cycle);
      }
    }

    calendar.push("reset");
    if (bisextile) {
      calendar.push("super reset");
    }

    return calendar;
  }

  function generate_normal_calendar(bisextile) {
    let calendar = [];
    let days_in_fevrier = 28;
    if (bisextile) {
      days_in_fevrier = days_in_fevrier + 1;
    }
    const days_in_months = {
      Janvier: 31,
      Février: days_in_fevrier,
      Mars: 31,
      Avril: 30,
      Mai: 31,
      Juin: 30,
      Juillet: 31,
      Août: 31,
      Septembre: 30,
      Novembre: 31,
      Octobre: 30,
      Décembre: 31,
    };
    const months = [
      "Janvier",
      "Février",
      "Mars",
      "Avril",
      "Mai",
      "Juin",
      "Juillet",
      "Août",
      "Septembre",
      "Novembre",
      "Octobre",
      "Décembre",
    ];

    for (const month of months) {
      const days_in_month = days_in_months[month];
      for (let i = 1; i <= days_in_month; i++) {
        calendar.push("" + i + " " + month);
      }
    }
    return calendar;
  }

  // api
  function normal_day_to_bion_day(normal_day) {
    let day_number = 0;
    const normal_calendar = generate_normal_calendar(bisextile);
    const bion_calendar = generate_bion_calendar(bisextile);
    // find day_number
    for (const [i, day] of normal_calendar.entries()) {
      if (day == normal_day) {
        day_number = i;
      }
    }
    const day_not_found = day_number == 0;
    if (day_not_found) {
      console.log("normal day not found in normal_calendar");
    }
    return bion_calendar[day_number];
  }

  // api
  function bion_day_to_normal_day(bion_day) {
    let day_number = 0;
    const normal_calendar = generate_normal_calendar(bisextile);
    const bion_calendar = generate_bion_calendar(bisextile);
    // find day_number
    for (const [i, day] of bion_calendar.entries()) {
      if (day == bion_day) {
        day_number = i;
      }
    }
    const day_not_found = day_number == 0;
    if (day_not_found) {
      console.log("bion day not found in bion_calendar");
    }
    return normal_calendar[day_number];
  }

  // end of converter algorithm

  function bion_to_normal_date(bion_date) {
    return bion_day_to_normal_day(bion_date);
  }

  function normal_to_bion_date(normal_date) {
    return normal_day_to_bion_day(normal_date);
  }

  let normal_date_1 = "1 Janvier";
  $: bion_date_1 = normal_to_bion_date(normal_date_1);

  let bion_date_2 = "aion 1er cycle";
  $: normal_date_2 = bion_to_normal_date(bion_date_2);

  let left = { calendar: "Normal", date: "1 Janvier" };
  let right = { calendar: "Bion", date: "aion 1er cycle" };

  let left_calendar = "Normal";
  let left_date = "1 Janvier";
  let right_calendar = "Bion";
  let right_date = "aion 1er cycle";

  const convert_fn_for = {
    normal: {
      bion: normal_to_bion_date,
    },
    bion: {
      normal: bion_to_normal_date,
    },
  };

  $: convert_fn =
    convert_fn_for[left_calendar.toLowerCase()][right_calendar.toLowerCase()];

  $: right_date = convert_fn(left_date);

  function switch_converter() {
    const temp_calendar = left_calendar;
    const temp_date = left_date;

    left_calendar = right_calendar;
    left_date = right_date;
    right_calendar = temp_calendar;
    right_date = temp_date;
  }
</script>

<!-- bisextile year checkbox -->
<div class="flex justify-center">
  <input type="checkbox" bind:checked={bisextile} />
  <div class="px-1" />
  {#if bisextile}
    <label for="">bisextile year</label>
  {:else}
    <label for="" class="line-through">bisextile year</label>
  {/if}
</div>

<div class="py-3" />

<!-- converter -->
<div class="width-screen flex flex-col">
  <!-- top -->
  <div class="bg-white flex justify-between p-2 border rounded-t-lg">
    <div class="w-14 self-center">{left_calendar}</div>
    <!-- converter icon -->
    <button>
      <svg
        class="h-8 w-8 fill-gray-600 hover:fill-blue-600"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 20 20"
        fill="currentColor"
        on:click={switch_converter}
      >
        <path
          d="M8 5a1 1 0 100 2h5.586l-1.293 1.293a1 1 0 001.414 1.414l3-3a1 1 0 000-1.414l-3-3a1 1 0 10-1.414 1.414L13.586 5H8zM12 15a1 1 0 100-2H6.414l1.293-1.293a1 1 0 10-1.414-1.414l-3 3a1 1 0 000 1.414l3 3a1 1 0 001.414-1.414L6.414 15H12z"
        />
      </svg>
    </button>

    <div class="w-14 text-right self-center">{right_calendar}</div>
  </div>

  <!-- bot -->
  <div class="inline-flex">
    <input
      class="border grow p-2 rounded-bl-lg"
      type="text"
      bind:value={left_date}
    />
    <input
      class="border grow p-2 focus:outline-none rounded-br-lg"
      type="text"
      readonly
      value={right_date}
    />
  </div>
</div>
