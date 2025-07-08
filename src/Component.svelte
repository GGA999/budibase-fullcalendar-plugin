<script>
  import { getContext } from "svelte";
  import '@fullcalendar/core/locales-all';
  import FullCalendar from 'svelte-fullcalendar';
  import daygridPlugin from '@fullcalendar/daygrid';
  import timeGridPlugin from '@fullcalendar/timegrid';
  import listPlugin from '@fullcalendar/list';
  import { onMount } from "svelte";
  import { langs, codeLang } from "./lang";

  export let language;
  export let calendarEvent;
  export let mappingTitle;
  export let mappingDate;
  export let mappingStart;
  export let mappingEnd;
  export let dataProvider;
  export let mappingColor;
  export let allday;
  export let typeColorMapping = [];

  export let headerOptionsStart;
  export let headerOptionsCenter;
  export let headerOptionsEnd;

  let eventsList = [];

  function generateColorMap() {
    const map = {};
    (typeColorMapping || []).forEach(item => {
      if (item?.type && item?.color) {
        map[item.type] = item.color;
      }
    });
    return map;
  }

  function buildEvents() {
    const colorMap = generateColorMap();
    const newEvents = [];

    if (dataProvider?.rows) {
      dataProvider.rows.forEach(event => {
        const type = event.type;
        const eventColor = colorMap[type] || mappingColor || '#313131';
        newEvents.push({
          title: event[mappingTitle],
          start: event[mappingStart] || event[mappingDate],
          end: event[mappingEnd],
          color: eventColor,
          allDay: allday,
          event
        });
      });
    }

    eventsList = newEvents;
  }

  // Costruisce gli eventi all'inizio
  onMount(() => {
    buildEvents();
  });

  // Ricostruisce quando cambia il dataProvider o la mappatura dei colori
  $: if (dataProvider || typeColorMapping) {
    buildEvents();
  }

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
