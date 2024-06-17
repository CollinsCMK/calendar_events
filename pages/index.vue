<template>
    <div class="w-full max-w-[600px] m-auto border border-gray-700 rounded-lg shadow-[0px_0px_10px_#000]">
        <div class="flex justify-between items-center p-2 bg-[#f9f9f9] border border-b border-[#ccc]">
          <button @click="prevMonth">&lt;</button>
          <span>{{ monthYear }}</span>
          <button @click="nextMonth">&gt;</button>
        </div>
        
        <div class="grid grid-cols-7">
            <div class="p-2 text-center" v-for="day in daysOfWeek" :key="day">{{ day }}</div>
            
            <div
                class="p-2 text-center min-h-20 border border-[#f1f1f1]"
                v-for="day in calendarDays"
                :key="day.date"
                :class="day.today ? 'bg-yellow-500' : 'bg-white'"
            >
                <span>{{ day.date.getDate() }}</span>
                
                <div v-if="day.events.length">
                    <div v-for="event in day.events" :key="event.title" class="bg-[#007bff] text-white px-1 py-2 mt-1 rounded-md text-[12px]">
                      {{ event.title }}
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
  
<script setup>
    import { ref, computed } from 'vue';
    import { startOfMonth, endOfMonth, startOfWeek, endOfWeek, addDays, addMonths, isSameMonth, isSameDay, format } from 'date-fns';

    const events = ref([
    { title: 'Event 1', date: new Date(2024, 5, 1) },
    { title: 'Event 2', date: new Date(2024, 5, 15) },
    { title: 'Event 3', date: new Date(2024, 5, 25) }
    ]);
  
    const currentDate = ref(new Date());
    
    const daysOfWeek = ref(['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']);
    
    const monthYear = computed(() => format(currentDate.value, 'MMMM yyyy'));
    
    const startOfCurrentMonth = computed(() => startOfMonth(currentDate.value));

    const endOfCurrentMonth = computed(() => endOfMonth(currentDate.value));

    const startOfCalendar = computed(() => startOfWeek(startOfCurrentMonth.value));
    
    const endOfCalendar = computed(() => endOfWeek(endOfCurrentMonth.value));
  
    const calendarDays = computed(() => {
        let date = startOfCalendar.value;

        const days = [];

        while (date <= endOfCalendar.value) {
            days.push({
                date,
                currentMonth: isSameMonth(date, currentDate.value),
                today: isSameDay(date, new Date()),
                events: events.value.filter(event => isSameDay(event.date, date))
            });

            date = addDays(date, 1);
        }
        
        return days;
    });
  
    const prevMonth = () => {
        currentDate.value = addMonths(currentDate.value, -1);
    };
    
    const nextMonth = () => {
        currentDate.value = addMonths(currentDate.value, 1);
    };
</script>  