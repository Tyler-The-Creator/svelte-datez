<script lang='ts'>
    import {slide} from 'svelte/transition';
    import {onMount} from 'svelte';

    export let position: 'top' | 'bottom' | 'left' | 'right' = 'bottom';

    let currentDate: Date = new Date();
    let selectedDate: Date = new Date();
    let showDatePicker = false;
    let days: number[] = [];

    const months: string[] = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];

    let container: HTMLDivElement;

    onMount(() => {
        const handleClickOutside = (event) => {
            if (container && !container.contains(event.target) && showDatePicker) {
                showDatePicker = false;
            }
        };

        document.addEventListener('click', handleClickOutside);

        return () => {
            document.removeEventListener('click', handleClickOutside);
        };
    });

    function daysInMonth(month: number, year: number): number {
        return new Date(year, month + 1, 0).getDate();
    }

    function generateDays(): void {
        const numDays = daysInMonth(currentDate.getMonth(), currentDate.getFullYear());
        days = Array.from({length: numDays}, (_, i) => i + 1);
    }

    function nextMonth(): void {
        currentDate = new Date(currentDate.getFullYear(), currentDate.getMonth() + 1, 1);
        generateDays();
    }

    function prevMonth(): void {
        currentDate = new Date(currentDate.getFullYear(), currentDate.getMonth() - 1, 1);
        generateDays();
    }


    function selectDate(day: number): void {
        selectedDate = new Date(currentDate.getFullYear(), currentDate.getMonth(), day);
        showDatePicker = false;
    }

    // Generate the initial set of days for the current month
    generateDays();

    const toggleDatePicker = () => {
        showDatePicker = !showDatePicker;
    };

    function goToToday(): void {
        currentDate = new Date();
        selectedDate = new Date();
        generateDays();
    }
</script>

<div class='relative inline-block text-left'
     bind:this={container}>
    <button on:click|stopPropagation={toggleDatePicker}
            class='px-3 py-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500'>
        {`${selectedDate.getDate()} ${months[selectedDate.getMonth()]} ${selectedDate.getFullYear()}`}
    </button>

    {#if showDatePicker}
        <!--		<div transition:slide={{duration: 100}} class="absolute z-10 mt-2 w-64 rounded-md shadow-lg bg-white">-->
        <div
                transition:slide={{duration: 300}}
                class="absolute z-10 w-64 rounded-md shadow-lg bg-white dark:bg-gray-600 p-2"
                style={position === 'top' ? 'bottom: calc(100% + 0.5rem); left: 50%; transform: translateX(-50%);' :
          position === 'bottom' ? 'top: calc(100% + 0.5rem); left: 50%; transform: translateX(-50%);' :
          position === 'left' ? 'top: 50%; right: calc(100% + 0.5rem); transform: translateY(-50%);' :
          'top: 50%; left: calc(100% + 0.5rem); transform: translateY(-50%);'}
        >
            <div class='flex justify-between items-center px-5 py-2'>
                <button on:click={prevMonth} class='py-1 px-2 rounded-md hover:bg-blue-100'>&lt;</button>
                <span class='cursor-default'>{months[currentDate.getMonth()]} {currentDate.getFullYear()}</span>
                <button on:click={nextMonth} class='py-1 px-2 rounded-md hover:bg-blue-100'>&gt;</button>
            </div>
            <div class='grid grid-cols-7'>
                {#each days as day}
                    <button on:click={() => selectDate(day)}
                            class='w-8 h-8 rounded-full m-1 flex items-center justify-center hover:bg-blue-100 hover:dark:bg-gray-400 focus:outline-none focus:ring-2 focus:ring-blue-500'>
                        {day}
                    </button>
                {/each}
            </div>
            <button on:click={goToToday}
                    class="w-max text-left p-2.5 rounded-md hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-blue-500">
                Today
            </button>
        </div>
    {/if}
</div>
