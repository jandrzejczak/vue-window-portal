<template>
  <table :style="getColumnSizes" ref="listingTable">
    <thead>
      <tr ref="listingHeader">
        <th v-for="header in columnSizes" :key="header.id">
          {{ header.name }}
          <input v-model="header.filter" placeholder="Filter" />
          <span
            @mousedown="initResize($event, header.name)"
            class="resize-handle"
          />
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, index) in filteredTableData" :key="index">
        <td v-for="(cell, cellIndex) in row" :key="cellIndex">{{ cell }}</td>
      </tr>
    </tbody>
  </table>
</template>

<script lang="ts">
type TableColumn = {
  width: string | number;
  name: string;
  id: number;
  filter?: string;
};

import { ref, onMounted, defineComponent, computed } from "vue";
export default defineComponent({
  name: "ListingTable",
  components: {},
  emits: [],
  setup() {
    const listingTable = ref<HTMLElement | null>(null);
    const listingHeader = ref<HTMLElement | null>(null);

    const tableHeaders = [
      "ID",
      "First name",
      "Last name",
      "Email",
      "Street",
      "Country",
      "University",
      "IBAN",
    ];

    const tableData = [
      {
        ID: "000001",
        "First name": "Lani",
        "Last name": "Ovendale",
        Email: "lovendale0@w3.org",
        Street: "7850 Old Shore Drive",
        Country: "United Kingdom",
        University: "University of Plymouth",
        IBAN: "BG34 MPVP 8782 88EX H1CJ SC",
      },
      {
        ID: "000002",
        "First name": "Israel",
        "Last name": "Tassell",
        Email: "itassell1@ow.ly",
        Street: "245 Merchant Circle",
        Country: "Macedonia",
        University: "South East European University",
        IBAN: "FR11 4824 2942 41H9 XBHC 46P2 O86",
      },
      {
        ID: "000003",
        "First name": "Eveleen",
        "Last name": "Mercer",
        Email: "emercer2@ow.ly",
        Street: "70700 Kipling Pass",
        Country: "Portugal",
        University: "Instituto Superior Novas Profissões - INP",
        IBAN: "GR96 7559 456P GUAN WTAJ 3VPB S0P",
      },
      {
        ID: "000004",
        "First name": "Conn",
        "Last name": "Whitley",
        Email: "cwhitley3@wsj.com",
        Street: "03 Service Terrace",
        Country: "Albania",
        University: "Epoka University",
        IBAN: "LI59 1813 2T7T VKTO 6RKE X",
      },
      {
        ID: "000005",
        "First name": "Cherye",
        "Last name": "Smitheram",
        Email: "csmitheram4@rambler.ru",
        Street: "9 Eliot Parkway",
        Country: "Indonesia",
        University: "Universitas Mahasaraswati Denpasar",
        IBAN: "BR27 4570 4226 4255 5239 0197 316T J",
      },
      {
        ID: "000006",
        "First name": "Bunnie",
        "Last name": "Sked",
        Email: "bsked5@51.la",
        Street: "03418 Ludington Plaza",
        Country: "Nigeria",
        University: "Federal University of Technology, Minna",
        IBAN: "ES45 6721 1332 3288 6455 1242",
      },
      {
        ID: "000007",
        "First name": "Helaine",
        "Last name": "Crother",
        Email: "hcrother6@opera.com",
        Street: "7932 Sloan Park",
        Country: "Philippines",
        University: "Saint Ferdinand College",
        IBAN: "GB50 HFAD 8121 3729 9841 31",
      },
      {
        ID: "000008",
        "First name": "Tana",
        "Last name": "Ajean",
        Email: "tajean7@sfgate.com",
        Street: "2 Schurz Junction",
        Country: "China",
        University: "Xi'an University of Electronic Science and Technology",
        IBAN: "KZ85 7422 XDPB P2VQ H8SR",
      },
      {
        ID: "000009",
        "First name": "Sollie",
        "Last name": "Greenrde",
        Email: "sgreenrde8@wikispaces.com",
        Street: "055 Mockingbird Way",
        Country: "Russia",
        University:
          "St. Petersburg State Mining Institute (Technical University)",
        IBAN: "CH61 6423 9T4W WP0I 8MUK C",
      },
      {
        ID: "000010",
        "First name": "Vernon",
        "Last name": "Millington",
        Email: "vmillington9@marketwatch.com",
        Street: "74 David Pass",
        Country: "Portugal",
        University: "Instituto Politécnico de Setúbal",
        IBAN: "ES71 2390 0665 9889 6631 1187",
      },
    ];

    const filteredTableData = computed(() => {
      // Filter tableData based on filter values in columnSizes
      return tableData.filter((row) => {
        return columnSizes.value.every((header) => {
          if (!header.filter) {
            // If no filter value, include the row
            return true;
          }
          // Check if the cell value contains the filter value
          const cellValue: string = row[header.name as keyof typeof row] || "";
          return cellValue.toLowerCase().includes(header.filter.toLowerCase());
        });
      });
    });

    const targetHeaderColumn = ref<TableColumn>();
    const columnSizes = ref<TableColumn[]>([]);

    const getColumnSizes = computed(() => {
      // Generate the grid-template-columns style based on columnSizes
      return `grid-template-columns: ${columnSizes.value
        .map((col) => `minmax(${col.width}, 1fr)`)
        .join(" ")};`;
    });

    const getLastParentElement = (element: HTMLElement) => {
      let parent = element.parentElement;
      while (parent?.parentElement) {
        parent = parent.parentElement;
      }
      return parent;
    };

    // Our real web app uses Vue.js but I'll stick to plain JavaScript here

    const min = 150;
    const columns = ref<{ size: string; header: HTMLTableCellElement }[]>([]);
    const headerBeingResized = ref<HTMLElement | null>(null);

    // The next three functions are mouse event callbacks
    // Get ready, they're about to resize
    const initResize = (event: MouseEvent, colName: string) => {
      targetHeaderColumn.value = columnSizes.value.find(
        (el) => el.name === colName
      );
      headerBeingResized.value = (event.target as HTMLElement)
        .parentNode as HTMLElement;
      getLastParentElement(headerBeingResized.value!)?.addEventListener(
        "mousemove",
        onMouseMove
      );
      getLastParentElement(headerBeingResized.value!)?.addEventListener(
        "mouseup",
        onMouseUp
      );

      getLastParentElement(headerBeingResized.value!)?.addEventListener(
        "mouseleave",
        onMouseOut
      );

      headerBeingResized.value!.classList.add("header--being-resized");
    };

    // Where the magic happens. I.e. when they're actually resizing
    const onMouseMove = (event: MouseEvent) =>
      requestAnimationFrame(() => {
        const horizontalScrollOffset = document.documentElement.scrollLeft;
        if (headerBeingResized.value) {
          const width =
            horizontalScrollOffset +
            event.clientX -
            headerBeingResized.value.offsetLeft;

          targetHeaderColumn.value!.width = Math.max(min, width) + "px";
        }
      });

    // Clean up event listeners, classes, etc.
    const onMouseUp = () => {
      getLastParentElement(headerBeingResized.value!)?.removeEventListener(
        "mousemove",
        onMouseMove
      );
      getLastParentElement(headerBeingResized.value!)?.removeEventListener(
        "mouseup",
        onMouseUp
      );
      getLastParentElement(headerBeingResized.value!)?.removeEventListener(
        "mouseleave",
        onMouseOut
      );
      headerBeingResized.value!.classList.remove("header--being-resized");
      headerBeingResized.value = null;
      targetHeaderColumn.value = undefined;
    };

    // @TODO Increase setInterval(increaseNumber, 10); by one when cursor is out of the screen
    const onMouseOut = (event: MouseEvent) =>
      requestAnimationFrame(() => {
        const horizontalScrollOffset = document.documentElement.scrollLeft;
        if (headerBeingResized.value) {
          const width =
            horizontalScrollOffset +
            event.clientX -
            headerBeingResized.value.offsetLeft;

          targetHeaderColumn.value!.width = Math.max(min, width) + "px";
        }
      });

    // Let's populate that columns array and add listeners to the resize handles

    onMounted(() => {
      columnSizes.value = tableHeaders.map((el, index) => ({
        id: index,
        name: el,
        width: `${min}px`, // Assuming that `min` is defined elsewhere in your code
      }));
    });

    return {
      columns,
      initResize,
      tableHeaders,
      tableData,
      listingTable,
      listingHeader,
      getColumnSizes,
      columnSizes,
      filteredTableData,
    };
  },
});
</script>

<style lang="scss" scoped>
table {
  display: grid;
  border-collapse: collapse;
  /* These are just initial values which are overriden using JavaScript when a column is resized */
  grid-template-columns: repeat(
    v-bind("tableHeaders.length"),
    minmax(150px, 1fr)
  );
}

thead,
tbody,
tr {
  display: contents;
}

th,
td {
  padding: 15px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

th {
  position: sticky;
  top: 0;
  background: #6c7ae0;
  text-align: left;
  font-weight: normal;
  font-size: 1.1rem;
  color: white;
}

th:last-child {
  border: 0;
}

.resize-handle {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  background: black;
  opacity: 0;
  width: 3px;
  cursor: col-resize;
}

.resize-handle:hover,
/* The following selector is needed so the handle is visible during resize even if the mouse isn't over the handle anymore */
.header--being-resized .resize-handle {
  opacity: 0.5;
}

th:hover .resize-handle {
  opacity: 0.3;
}

td {
  padding-top: 10px;
  padding-bottom: 10px;
  color: #808080;
}

tr:nth-child(even) td {
  background: #f8f6ff;
}
</style>
