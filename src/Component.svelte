<script>
  import { getContext } from "svelte";
  import '@fullcalendar/core/locales-all';
  import FullCalendar from 'svelte-fullcalendar';
  import daygridPlugin from '@fullcalendar/daygrid';
  import timeGridPlugin from '@fullcalendar/timegrid';
  import listPlugin from '@fullcalendar/list';
  import { langs, codeLang } from "./lang";

  export let language;
  export let calendarEvent;
  export let mappingTitle;
  export let mappingDate;
  export let mappingStart;
  export let mappingEnd;
  export let dataProvider;
  export let colorMapping;
  export let allday;
  export let typeColorMapping = [];

  export let headerOptionsStart;
  export let headerOptionsCenter;
  export let headerOptionsEnd;

  let eventsList = [];

  function buildEvents() {
    if (!dataProvider?.rows) {
      eventsList = [];
      return;
    }

    const newEvents = dataProvider.rows.map(event => ({
      title: event[mappingTitle],
      start: event[mappingStart] || event[mappingDate],
      end: event[mappingEnd],
      color: event[colorMapping] || '#313131',
      allDay: allday,
      event
    }));

    eventsList = newEvents;
  }

  $: dataProvider?.rows, buildEvents();
  $: colorMapping, buildEvents();
  $: typeColorMapping, buildEvents();

  // Opzioni FullCalendar reattive
  let options;
  $: options = {
    headerToolbar: {
      start: headerOptionsStart,
      center: headerOptionsCenter,
      end: headerOptionsEnd
    },
    plugins: [daygridPlugin, timeGridPlugin, listPlugin],
    initialDate: new Date().toISOString().substring(0, 10),
    locale: language,
    dayMaxEvents: true,
    eventClick: info => calendarEvent({ value: info.event.extendedProps.event }),
    events: eventsList,
    theme: true,
    ...langs[codeLang(language)]
  };

  const { styleable } = getContext("sdk");
  const component = getContext("component");
</script>

<div use:styleable={$component.styles}>
  <FullCalendar {options} />
</div>
