<script>
import FullCalendar from "@fullcalendar/vue";
import dayGridPlugin from "@fullcalendar/daygrid";
import timeGridPlugin from "@fullcalendar/timegrid";
import interactionPlugin from "@fullcalendar/interaction";
import { INITIAL_EVENTS, createEventId } from "./event-utils";

export default {
	components: {
		FullCalendar, // make the <FullCalendar> tag available
	},

	data: function() {
		return {
			calendarOptions: {
				plugins: [
					dayGridPlugin,
					timeGridPlugin,
					interactionPlugin, // needed for dateClick
				],
				headerToolbar: {
					left: "prev,next today",
					center: "title",
					right: "dayGridMonth,timeGridWeek,timeGridDay",
				},
				initialView: "dayGridMonth",
				initialEvents: INITIAL_EVENTS, // alternatively, use the `events` setting to fetch from a feed
				editable: true,
				selectable: true,
				selectMirror: true,
				dayMaxEvents: true,
				weekends: true,
				select: this.handleDateSelect,
				eventClick: this.handleEventClick,
				eventsSet: this.handleEvents,
				/* you can update a remote database when these fire:
        eventAdd:
        eventChange:
        eventRemove:
        */
			},
			currentEvents: [],
		};
	},

	methods: {
		handleWeekendsToggle() {
			this.calendarOptions.weekends = !this.calendarOptions.weekends; // update a property
		},

		handleDateSelect(selectInfo) {
			console.log(selectInfo);
			let title = prompt("Please enter a new title for your event");
			let calendarApi = selectInfo.view.calendar;

			calendarApi.unselect(); // clear date selection

			if (title) {
				calendarApi.addEvent({
					id: createEventId(),
					title,
					start: selectInfo.startStr,
					end: selectInfo.endStr,
					allDay: selectInfo.allDay,
				});
			}
		},

		handleEventClick(clickInfo) {
			// if (confirm(`Are you sure you want to delete the event '${clickInfo.event.title}'`)) {
			//   clickInfo.event.remove()
			// }

			console.log(clickInfo);
			let title = prompt(
				"On Cancel it will delete the event!!\nPlease enter a new title for your event.. ",
				clickInfo.event.title
			);
			let calendarApi = clickInfo.view.calendar;

			calendarApi.unselect(); // clear date selection

			clickInfo.event.remove();

			if (title) {
				calendarApi.addEvent({
					id: clickInfo.event.id,
					title,
					start: clickInfo.event.startStr,
					end: clickInfo.event.endStr,
					allDay: clickInfo.event.allDay,
				});
			}
			// alert(`You Clicked '${clickInfo.event.title}'`)
		},

		handleEvents(events) {
			this.currentEvents = events;
		},
	},
};
</script>

<template>
	<div class="demo-app">
		<div class="demo-app-main">
			<FullCalendar class="demo-app-calendar" :options="calendarOptions">
				<template v-slot:eventContent="arg">
					<b>{{ arg.timeText }}</b>
					<i>{{ arg.event.title }}</i>
				</template>
			</FullCalendar>
		</div>
	</div>
</template>

<style lang="css">
h2 {
	margin: 0;
	font-size: 16px;
}

ul {
	margin: 0;
	padding: 0 0 0 1.5em;
}

li {
	margin: 1.5em 0;
	padding: 0;
}

b {
	/* used for event dates/times */
	margin-right: 3px;
}

.demo-app {
	display: flex;
	min-height: 100%;
	font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
	font-size: 14px;
}

.demo-app-sidebar {
	width: 300px;
	line-height: 1.5;
	background: #eaf9ff;
	border-right: 1px solid #d3e2e8;
}

.demo-app-sidebar-section {
	padding: 2em;
}

.demo-app-main {
	flex-grow: 1;
	padding: 3em;
}

.fc {
	/* the calendar root */
	max-width: 1100px;
	margin: 0 auto;
}
</style>
