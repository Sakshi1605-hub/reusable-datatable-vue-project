<template>
  <div>
    <slot name="title">
  <h2>Student List</h2>
</slot>
    <input
  v-model="search"
  type="text"
  placeholder="Search..."
/>
<div class="controls">
  <label>Rows per page:</label>

  <select v-model="itemsPerPage">
    <option :value="2">2</option>
    <option :value="5">5</option>
    <option :value="10">10</option>
  </select>
</div>

    <table border="1" cellpadding="8">
      <thead>
        <tr>
          <th
  v-for="column in columns"
  :key="column.field"
  @click="sortBy(column.field)"
  style="cursor: pointer;"
>
  {{ column.label }}

  <span v-if="sortField === column.field">
    {{ sortOrder === 'asc' ? '↑' : '↓' }}
  </span>
</th>
        </tr>
      </thead>

      <tbody>
      <tr
  v-for="row in paginatedData"
  :key="row.id"
  @click="handleRowClick(row)"
>
       
          <td
  v-for="column in columns"
  :key="column.field"
>
  <slot
    name="cell"
    :row="row"
    :column="column"
  >
    {{ row[column.field] }}
  </slot>
</td>
        </tr>
        <tr v-if="sortedData.length === 0">
        <td :colspan="columns.length">
        No Data Found
        </td>
        </tr>
      </tbody>
    </table>
    <div class="pagination">
  <button
    @click="currentPage--"
    :disabled="currentPage === 1"
  >
    Previous
  </button>

  <span>
    Page {{ currentPage }} of {{ totalPages }}
  </span>

  <button
    @click="currentPage++"
    :disabled="currentPage === totalPages"
  >
    Next
  </button>
</div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  columns: Array,
  data: Array
})
const emit = defineEmits(['row-click'])

const search = ref('')

const sortField = ref('')
const sortOrder = ref('asc')
const currentPage = ref(1)
const itemsPerPage = ref(2)

const filteredData = computed(() => {
  return props.data.filter(row =>
    Object.values(row).some(value =>
      String(value).toLowerCase().includes(search.value.toLowerCase())
    )
  )
})

const sortedData = computed(() => {
  const data = [...filteredData.value]

  if (!sortField.value) return data

  return data.sort((a, b) => {
    const valA = a[sortField.value]
    const valB = b[sortField.value]

    if (sortOrder.value === 'asc') {
      return valA > valB ? 1 : -1
    } else {
      return valA < valB ? 1 : -1
    }
  })
})

const sortBy = (field) => {
  if (sortField.value === field) {
    sortOrder.value =
      sortOrder.value === 'asc' ? 'desc' : 'asc'
  } else {
    sortField.value = field
    sortOrder.value = 'asc'
  }
}
const handleRowClick = (row) => {
  emit('row-click', row)
}
const totalPages = computed(() => {
  return Math.ceil(sortedData.value.length / itemsPerPage.value)
})

const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value
  const end = start + itemsPerPage.value

  return sortedData.value.slice(start, end)
})
</script>
<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  background: white;
}

th,
td {
  border: 1px solid #ddd;
  padding: 12px;
  text-align: left;
}

th {
  background-color: #4CAF50;
  color: white;
  cursor: pointer;
}

tr:nth-child(even) {
  background-color: #f9f9f9;
}

tr:hover {
  background-color: #f1f1f1;
}

input {
  width: 300px;
  padding: 10px;
  margin: 15px 0;
  border: 1px solid #ccc;
  border-radius: 5px;
}

h2 {
  margin-bottom: 10px;
}
.pagination {
  margin-top: 20px;
  display: flex;
  align-items: center;
  gap: 15px;
}

button {
  padding: 8px 15px;
  cursor: pointer;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
.controls {
  margin: 10px 0 20px;
}

select {
  padding: 8px;
  margin-left: 10px;
}
tbody tr {
  cursor: pointer;
}
</style>