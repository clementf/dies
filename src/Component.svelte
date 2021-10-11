<script>
  import startOfMonth from 'date-fns/startOfMonth';
  import endOfMonth from 'date-fns/endOfMonth';
  import addMonths from 'date-fns/addMonths'
  import Month from './Month.svelte';
  import ChevronLeft from './ChevronLeft.svelte';
  import ChevronRight from './ChevronRight.svelte';
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  export let startDate;
  export let monthCount;
  export let events;
  export let config;

  $: startDayCalendar = startOfMonth(startDate)
  $: months = Array(monthCount)

  function dispatchMonthChanged(start){
    dispatch(
      'monthChanged',
      {
        startDate: start,
        endDate: endOfMonth(addMonths(start, 1)),
      }
    )
  }

  function increaseMonth(){
    startDayCalendar = addMonths(startDayCalendar, + monthCount)
    dispatchMonthChanged(startDayCalendar)
  }

  function decreaseMonth(){
    startDayCalendar = addMonths(startDayCalendar, - monthCount)
    dispatchMonthChanged(startDayCalendar)
  }
</script>

<div class="relative my-1 sm:mt-4">
  <div class="flex justify-between">
    <span class="w-5 h-5 cursor-pointer" on:click={decreaseMonth}>
      <ChevronLeft/>
    </span>

    <div class="grid grid-cols-2 gap-8 w-full">
      {#each months as month, index}
        <Month events={events} start={addMonths(startDayCalendar, index)} {...config} />
      {/each}
    </div>

    <span class="w-5 h-5 cursor-pointer" on:click={increaseMonth}>
      <ChevronRight/>
    </span>
  </div>
</div>

