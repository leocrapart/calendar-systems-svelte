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
</script>

<div class="flex justify-center">
  <input type="checkbox" bind:checked={bisextile} />
  <div class="px-1" />
  {#if bisextile}
    <label for="">bisextile year</label>
  {:else}
    <label for="" class="line-through">bisextile year</label>
  {/if}
</div>
<div class="flex justify-center">
  <!-- converter normal -> bion -->
  <div>
    <div class="py-5" />

    <div>Normal -> Bion</div>
    <input type="text" bind:value={normal_date_1} />

    <div class="py-3" />

    <div class="bg-gray-300 p-2">=> {bion_date_1}</div>

    <!-- feedback -->
    {#if feedback}
      <div>
        <div>mauvais format</div>
      </div>
    {/if}
  </div>

  <!-- converter bion -> normal -->
  <div class="pl-40">
    <div class="py-5" />

    <div>Bion -> Normal</div>
    <input type="text" bind:value={bion_date_2} />

    <div class="py-3" />

    <div class="bg-gray-300 p-2">=> {normal_date_2}</div>

    <!-- feedback -->
    {#if feedback}
      <div>
        <div>mauvais format</div>
      </div>
    {/if}
  </div>
</div>
