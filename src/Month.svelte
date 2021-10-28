<script>
  import eachDayOfInterval from 'date-fns/eachDayOfInterval';
  import startOfMonth from 'date-fns/startOfMonth';
  import endOfMonth from 'date-fns/endOfMonth';
  import isWithinInterval from 'date-fns/isWithinInterval'
  import startOfDay from 'date-fns/startOfDay'
  import isEqual from 'date-fns/isEqual'
  import isToday from 'date-fns/isToday'

  export let start;
  export let selected;
  export let events;
  export let accentColor;
  export let languages;
  export let disableDaysWithEvent = true;
  export let showMarkers = true;

  $: days = eachDayOfInterval({
    start: startOfMonth(start),
    end: endOfMonth(start),
  }).map((day)  => {
    return {
      date: day,
      events: events.filter((event) => {
        return isWithinInterval(day, { start: event.startDate, end: event.endDate })
      })
    }
  })

  function computeDayStyles(selected, day) {
    if(isEqual(startOfDay(selected), day)){
      return `font-weight: bold; color: ${accentColor}`;
    }
    else if(isToday(day)){
      return `border-style: 2px; border-style: solid; border-color: ${accentColor};`
    }
    else {
      return "border-color: transparent;";
    }
  }

  function createPaddingDays(firstDay){
    return Array((firstDay.date.getDay() + 6) % 7)
  }

  // Display month name accoding to language passed as option
  // Uppercase the first char for presentation
  function formattedMonthHeader(start){
    return Intl.DateTimeFormat(languages || navigator.languages, {"month": "long", "year": "numeric"}).format(new Date(start)).replace(/^\w/, c => c.toUpperCase())
  }

</script>

<div>
  <div class="mb-4 text-sm font-semibold text-center text-gray-600 select-none">
    {formattedMonthHeader(start)}
  </div>

  <div class="w-full text-sm text-center text-gray-700 grid grid-cols-7">
    { #each createPaddingDays(days[0]) as _ }
    <div></div>
  {/each}

    { #each days as day, index}
    <div>
      <span style="{computeDayStyles(selected, day.date)} " class="relative flex flex-col items-center justify-center p-1 mx-1 border-2 rounded-full xl:mx-2 w-9 h-9">

        <span class="{day.events.length > 0 && disableDaysWithEvent ? "line-through text-gray-400" : "font-semibold text-gray-500"}">
          {day.date.getDate()}
        </span>

        <div class="flex items-center justify-center absolute bottom-0">
          {#each day.events as event}
            {#if showMarkers}
              <span style="background-color: {event.markerColor}" class="w-1 h-1 mx-px mb-px rounded-full "></span>
            {/if}
          {/each}
          </div>
        </span>

      </div>
    {/each}
    </div>
  </div>

