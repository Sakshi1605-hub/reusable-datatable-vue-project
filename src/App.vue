<template>
  <div>
    <h1>Reusable Data Table</h1>

    <!-- ================= STUDENT TABLE ================= -->

    <BaseDataTable
      :columns="columns"
      :data="students"
      @delete-row="deleteStudent"
    >
      <template #title>
        <h2>Student Information</h2>
      </template>

      <template #form>
        <h3>Add Student</h3>

        <input
          v-model="newStudent.name"
          placeholder="Name"
        />

        <input
          v-model="newStudent.course"
          placeholder="Course"
        />

        <input
          v-model="newStudent.marks"
          placeholder="Marks"
        />

        <button @click="addStudent">
          Add Student
        </button>
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

    <br /><br />

    <!-- ================= EMPLOYEE TABLE ================= -->

    <BaseDataTable
      :columns="employeeColumns"
      :data="employees"
      @delete-row="deleteEmployee"
    >
      <template #title>
        <h2>Employee Information</h2>
      </template>

      <template #form>
        <h3>Add Employee</h3>

        <input
          v-model="newEmployee.name"
          placeholder="Name"
        />

        <input
          v-model="newEmployee.department"
          placeholder="Department"
        />

        <input
          v-model="newEmployee.salary"
          placeholder="Salary"
        />

        <button @click="addEmployee">
          Add Employee
        </button>
      </template>
    </BaseDataTable>

    <br /><br />

    <!-- ================= PRODUCT TABLE ================= -->

    <BaseDataTable
      :columns="productColumns"
      :data="products"
      @delete-row="deleteProduct"
    >
      <template #title>
        <h2>Product Information</h2>
      </template>

      <template #form>
        <h3>Add Product</h3>

        <input
          v-model="newProduct.product"
          placeholder="Product"
        />

        <input
          v-model="newProduct.price"
          placeholder="Price"
        />

        <input
          v-model="newProduct.stock"
          placeholder="Stock"
        />

        <button @click="addProduct">
          Add Product
        </button>
      </template>
    </BaseDataTable>
  </div>
</template>

<script setup>
import { ref } from "vue";
import BaseDataTable from "./components/BaseDataTable.vue";
import studentData from "./data/students.json";

/* -------------------- DATA -------------------- */

const students = ref(studentData);

const employees = ref([
  { id: 101, name: "Amit", department: "HR", salary: 50000 },
  { id: 102, name: "Riya", department: "IT", salary: 70000 },
  { id: 103, name: "Karan", department: "Finance", salary: 65000 }
]);

const products = ref([
  { id: 1, product: "Laptop", price: 60000, stock: 15 },
  { id: 2, product: "Mouse", price: 800, stock: 50 },
  { id: 3, product: "Keyboard", price: 1500, stock: 30 }
]);

/* -------------------- COLUMNS -------------------- */

const columns = [
  { label: "ID", field: "id" },
  { label: "Name", field: "name" },
  { label: "Course", field: "course" },
  { label: "Marks", field: "marks" }
];

const employeeColumns = [
  { label: "ID", field: "id" },
  { label: "Name", field: "name" },
  { label: "Department", field: "department" },
  { label: "Salary", field: "salary" }
];

const productColumns = [
  { label: "ID", field: "id" },
  { label: "Product", field: "product" },
  { label: "Price", field: "price" },
  { label: "Stock", field: "stock" }
];

/* -------------------- FORM DATA -------------------- */

const newStudent = ref({
  name: "",
  course: "",
  marks: ""
});

const newEmployee = ref({
  name: "",
  department: "",
  salary: ""
});

const newProduct = ref({
  product: "",
  price: "",
  stock: ""
});

/* -------------------- ADD -------------------- */

const addStudent = () => {
  const newId =
    students.value.length > 0
      ? Math.max(...students.value.map(s => s.id)) + 1
      : 1;

  students.value.push({
    id: newId,
    ...newStudent.value
  });

  newStudent.value = {
    name: "",
    course: "",
    marks: ""
  };
};

/* -------------------- DELETE -------------------- */

const deleteStudent = (id) => {
  students.value = students.value.filter(student => student.id !== id);
};

const deleteEmployee = (id) => {
  employees.value = employees.value.filter(employee => employee.id !== id);
};

const deleteProduct = (id) => {
  products.value = products.value.filter(product => product.id !== id);
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 20px;
}
</style>