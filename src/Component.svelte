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

  // 🔥 Nuovo mapping dinamico dei colori
  export let typeColorMapping = [];

  let eventsList = [];

  function generateColorMap() {
    return typeColorMapping.reduce((acc, item) => {
      acc[item.type] = item.color;
      return acc;
    }, {});
  }

  function buildEvents() {
    const colorMap = generateColorMap();
    eventsList = [];

    if (dataProvider?.rows) {
      dataProvider.rows.forEach(event => {
        const eventType = event.type;
        const eventColor = colorMap[eventType] || mappingColor || '#313131';

        eventsList.push({
          title: event[mappingTitle],
          date: event[mappingDate],
          start: event[mappingStart],
          end: event[mappingEnd],
          color: eventColor,
          event,
          allDay: allday,
        });
      });
    }
  }

  onMount(buildEvents);

  // 🔄 Quando cambiano i dati, aggiorna la lista eventi
  $: dataProvider, typeColorMapping, mappingColor, buildEvents();

  let options = {
    headerToolbar: {
      start: headerOptionsStart,
      center: headerOptionsCenter,
      end: headerOptionsEnd,
    },
    plugins: [daygridPlugin, listPlugin, timeGridPlugin],
    initialDate: Date.now(),
    locale: language,
    dayMaxEvents: true,
    eventClick: (event) => {
      calendarEvent({ value: event.event });
    },
    events: eventsList,
    eventColor: '#378006',
    theme: true,
    ...langs[codeLang(language)],
  };

  export let headerOptionsStart;
  export let headerOptionsCenter;
  export let headerOptionsEnd;

  const { styleable } = getContext("sdk");
  const component = getContext("component");
</script>

<div use:styleable={$component.styles}>
  <FullCalendar {options} />
</div>
