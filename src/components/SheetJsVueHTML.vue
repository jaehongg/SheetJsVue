<script setup>
import { ref, onMounted } from 'vue';
import { read, utils, writeFileXLSX } from 'xlsx';

const rows = ref([]);

onMounted(async () => {
  /* Download from https://sheetjs.com/pres.numbers */
  const f = await fetch('https://sheetjs.com/pres.numbers');
  const ab = await f.arrayBuffer();

  /* parse workbook */
  const wb = read(ab);

  /* example data */
  // const employees = [
  //   {
  //     id: '1',
  //     name: 'Saitama',
  //     age: '32',
  //   },
  //   {
  //     id: '2',
  //     name: 'Genos',
  //     age: '24',
  //   },
  // ];

  /* update data */
  rows.value = utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]]);
  // rows.value = utils.sheet_to_json(employees);
});

/* get state data and export to XLSX */
function exportFile() {
  const ws = utils.json_to_sheet(rows.value);
  const wb = utils.book_new();
  utils.book_append_sheet(wb, ws, 'Data');
  writeFileXLSX(wb, 'SheetJSVueAoO.xlsx');
}
</script>

<template>
  {{ rows }}
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>Index</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, idx) in rows" :key="idx">
        <td>{{ row.Name }}</td>
        <td>{{ row.Index }}</td>
      </tr>
    </tbody>
    <tfoot>
      <td colSpan="{2}">
        <button @click="exportFile">Export XLSX</button>
      </td>
    </tfoot>
  </table>
</template>
