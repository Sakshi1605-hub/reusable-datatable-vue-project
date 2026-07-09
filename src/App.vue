
<template>
  <div>
    <h1>Reusable Data Table</h1>

  <BaseDataTable
  :columns="columns"
  :data="students"
  @row-click="handleRowClick"
>
  <template #title>
    <h2>Student Information</h2>
  </template>

  <template #cell="{ row, column }">
    <span v-if="column.field === 'marks'">
      <strong>{{ row.marks }}</strong>
    </span>

    <span v-else>
      {{ row[column.field] }}
    </span>
  </template>
</BaseDataTable>
<BaseDataTable
  :columns="employeeColumns"
  :data="employees"
>
  <template #title>
    <h2> Employee Information</h2>
  </template>
</BaseDataTable>
<BaseDataTable
  :columns="productColumns"
  :data="products"
>
  <template #title>
    <h2>Product Information</h2>
  </template>
</BaseDataTable>

  

  </div>
</template>

<script setup>

import BaseDataTable from './components/BaseDataTable.vue'
import students from './data/students.json'
const employees = [
  { id: 101, name: 'Amit', department: 'HR', salary: 50000 },
  { id: 102, name: 'Riya', department: 'IT', salary: 70000 },
  { id: 103, name: 'Karan', department: 'Finance', salary: 65000 }
]

const products = [
  { id: 1, product: 'Laptop', price: 60000, stock: 15 },
  { id: 2, product: 'Mouse', price: 800, stock: 50 },
  { id: 3, product: 'Keyboard', price: 1500, stock: 30 }
]
const columns = [
  { label: 'ID', field: 'id' },
  { label: 'Name', field: 'name' },
  { label: 'Course', field: 'course' },
  { label: 'Marks', field: 'marks' }
]
const employeeColumns = [
  { label: 'ID', field: 'id' },
  { label: 'Name', field: 'name' },
  { label: 'Department', field: 'department' },
  { label: 'Salary', field: 'salary' }
]

const productColumns = [
  { label: 'ID', field: 'id' },
  { label: 'Product', field: 'product' },
  { label: 'Price', field: 'price' },
  { label: 'Stock', field: 'stock' }
]
const handleRowClick = (row) => {
  alert(`You clicked on ${row.name}`)
}
</script>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 20px;
}
</style>