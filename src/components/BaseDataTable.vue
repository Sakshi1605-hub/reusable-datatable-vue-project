<template>
  <div>

    <slot name="title">
      <h2>Table</h2>
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
<div class="add-form">
<slot name="form"></slot>
</div>



    <table border="1" cellpadding="8">

      <thead>

        <tr>

          <th
            v-for="column in columns"
            :key="column.field"
            @click="sortBy(column.field)"
          >

            {{ column.label }}

            <span v-if="sortField === column.field">

              {{ sortOrder === 'asc' ? '↑' : '↓' }}

            </span>
          </th>
          <th>
            Actions
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

          <td>
            <button
              @click.stop="deleteRow(row.id)"
            >

              Delete

            </button>

          </td>

        </tr>
        <tr v-if="sortedData.length === 0">
          <td :colspan="columns.length + 1">
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

        Page {{currentPage}} of {{totalPages}}

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
import { ref, computed, watch } from "vue";

const props = defineProps({
  columns: Array,
  data: Array
});

const emit = defineEmits([
  "row-click",
  "delete-row"
]);

const search = ref("");

const sortField = ref("");
const sortOrder = ref("asc");

const currentPage = ref(1);
const itemsPerPage = ref(5);

// Filter
const filteredData = computed(() => {
  return props.data.filter(row =>
    Object.values(row).some(value =>
      String(value)
        .toLowerCase()
        .includes(search.value.toLowerCase())
    )
  );
});

// Sort
const sortedData = computed(() => {
  const data = [...filteredData.value];

  if (!sortField.value) return data;

  return data.sort((a, b) => {
    const valA = a[sortField.value];
    const valB = b[sortField.value];

    if (sortOrder.value === "asc") {
      return valA > valB ? 1 : -1;
    } else {
      return valA < valB ? 1 : -1;
    }
  });
});

// Sorting
const sortBy = (field) => {
  if (sortField.value === field) {
    sortOrder.value =
      sortOrder.value === "asc" ? "desc" : "asc";
  } else {
    sortField.value = field;
    sortOrder.value = "asc";
  }
};

// Total Pages
const totalPages = computed(() => {
  return Math.max(
    1,
    Math.ceil(sortedData.value.length / itemsPerPage.value)
  );
});

// Pagination
const paginatedData = computed(() => {
  const start =
    (currentPage.value - 1) * itemsPerPage.value;

  return sortedData.value.slice(
    start,
    start + itemsPerPage.value
  );
});

// Row Click
const handleRowClick = (row) => {
  emit("row-click", row);
};

// Delete
const deleteRow = (id) => {
  emit("delete-row", id);
};

// Reset page when search changes
watch(search, () => {
  currentPage.value = 1;
});

// Keep page in range
watch([itemsPerPage, sortedData], () => {
  if (currentPage.value > totalPages.value) {
    currentPage.value = totalPages.value;
  }
});
</script>

<style scoped>
table{
width:100%;
border-collapse:collapse;
margin-top:20px;
}
th,td{
border:1px solid #ddd;
padding:12px;
}


th{
background:#4CAF50;
color:white;
cursor:pointer;
}
tr:nth-child(even){
background:#f5f5f5;
}
input{
width:300px;
padding:10px;
margin:15px 0;
}
button{
padding:8px 15px;
cursor:pointer;
}
.pagination{
margin-top:20px;
display:flex;
gap:15px;
align-items:center;
}
</style>
