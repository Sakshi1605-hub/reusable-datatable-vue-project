<template>
  <div>

    <slot name="title">
      <h2>Table</h2>
    </slot>


    <div class="search-box">

<span>🔍</span>

<input
v-model="search"
type="text"
placeholder="Search..."
/>

</div>


    <div class="controls">

      <label>Rows per page:</label>

      <select v-model="itemsPerPage">

        <option :value="2">2</option>
        <option :value="5">5</option>
        <option :value="10">10</option>

      </select>
    </div>

    <div class="add-form">
    <div class="form-header">
    <h3>Add New Record</h3>
    </div>
    <div class="form-body">
    <slot name="form"></slot>
    </div>
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
           class="edit-btn"
           @click.stop="editRow(row)"
           >
           Edit
           </button>

          <button
          class="delete-btn"
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
  "delete-row",
  "edit-row"
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


// Edit
const editRow = (row) => {
  emit("edit-row", row);
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
background:white;
border-radius:18px;
overflow:hidden;
box-shadow:0 12px 30px rgba(0,0,0,.08);
}

th,
td{
padding:18px;
border-bottom:1px solid #f0f0f0;
}


th{
background:linear-gradient(90deg,#43a047,#2e7d32);
color:white;
cursor:pointer;
padding:16px;
font-size:16px;
}

tr:nth-child(even){
background:#fafafa;
}

tr:hover{
background:#e8f5e9;
transition:.2s;
}

input{
width:320px;
padding:12px 15px;
border-radius:10px;
border:1px solid #ddd;
margin-bottom:20px;
transition:.3s;
}

input:focus{
border-color:#4CAF50;
box-shadow:0 0 5px rgba(76,175,80,.4);
}

button{
padding:10px 18px;
border:none;
border-radius:8px;
cursor:pointer;
background:#4CAF50;
color:white;
font-weight:600;
transition:.3s;
margin-right:8px;
}

button:hover{
background:#2e7d32;
transform:scale(1.04);
}
/* Edit Button */

.edit-btn{
background:#2196F3;
}

.edit-btn:hover{
background:#1976D2;
}

/* Delete Button */

.delete-btn{
background:#f44336;
}

.delete-btn:hover{
background:#d32f2f;
}

/* Add Button */

.add-btn{
background:#4CAF50;
}


.add-btn:hover{
background:#2e7d32;
}

.pagination{
display:flex;
justify-content:center;
align-items:center;
gap:25px;
margin-top:30px;
font-weight:600;
}

.controls{
display:flex;
align-items:center;
gap:15px;
margin-bottom:20px;
font-weight:600;
}

.search-box{
display:flex;
align-items:center;
gap:10px;
margin-bottom:20px;
}

.search-box span{
font-size:20px;
color:#4CAF50;
}

.search-box input{
margin:0;
}
thead{
position:sticky;
top:0;
z-index:10;
}

.add-form{
background:white;
padding:25px;
border-radius:15px;
margin-top:20px;
margin-bottom:25px;
box-shadow:0 6px 18px rgba(0,0,0,.08);
}

.form-header{
margin-bottom:18px;
padding-bottom:12px;
border-bottom:1px solid #eee;
}

.form-header h3{
margin:0;
color:#2e7d32;
font-size:22px;
}

.form-body{
display:flex;
flex-wrap:wrap;
gap:15px;
align-items:center;
}

</style>
