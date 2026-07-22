<template>
  <div class="container">
    <div class="hero">
  <h1>Reusable Data Table</h1>
  
</div>
    <div class="selector">

  <label> Select Table</label>

  <select v-model="selectedTable">

    <option value="student">
       Students
    </option>

    <option value="employee">
       Employees
    </option>

    <option value="product">
       Products
    </option>

  </select>

</div>
   <div
v-if="selectedTable==='student'"
class="summary"
>

<div class="card">
<h3>Total Students</h3>
<h2>{{ totalStudents }}</h2>
</div>

<div class="card">
<h3>Average Marks</h3>
<h2>{{ averageMarks }}</h2>
</div>

<div class="card">
<h3>Highest Marks</h3>
<h2>{{ highestMarks }}</h2>
</div>

</div>
<div
v-if="selectedTable==='employee'"
class="summary"
>

<div class="card">
<h3>Total Employees</h3>
<h2>{{ totalEmployees }}</h2>
</div>

<div class="card">
<h3>Total Salary</h3>
<h2>{{ totalSalary }}</h2>
</div>

<div class="card">
<h3>Average Salary</h3>
<h2>{{ averageSalary }}</h2>
</div>

<div class="card">
<h3>Highest Salary</h3>
<h2>{{ highestSalary }}</h2>
</div>

</div>

<div
v-if="selectedTable==='product'"
class="summary"
>

<div class="card">
<h3>Total Products</h3>
<h2>{{ totalProducts }}</h2>
</div>

<div class="card">
<h3>Total Stock</h3>
<h2>{{ totalStock }}</h2>
</div>

<div class="card">
<h3>Total Inventory Value</h3>
<h2>{{ inventoryValue }}</h2>
</div>

</div>



    <!-- ================= STUDENT TABLE ================= -->
<div v-if="selectedTable==='student'">
    <BaseDataTable
      :columns="columns"
      :data="students"
      @delete-row="deleteStudent"
      @edit-row="openEdit"
    >

      <template #title>
        <h2>Student Information</h2>
      </template>


      <template #form>

        

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
          @input="allowOnlyNumbers"
          placeholder="Marks"
        />

        <button
        class="add-btn"
        @click="addStudent"
        >
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



    <br><br>
</div>



    <!-- ================= EMPLOYEE TABLE ================= -->

<div v-if="selectedTable==='employee'">
    <BaseDataTable
      :columns="employeeColumns"
      :data="employees"
      @delete-row="deleteEmployee"
      @edit-row="openEdit"
    >

      <template #title>
        <h2>Employee Information</h2>
      </template>


      <template #form>

        


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
          @input="allowOnlyNumbers"
          placeholder="Salary"
        />


      <button
      class="add-btn"
      @click="addEmployee"
      >
      Add Employee
      </button>


      </template>


    </BaseDataTable>




    <br><br>
</div>


    <!-- ================= PRODUCT TABLE ================= -->
<div v-if="selectedTable==='product'">

    <BaseDataTable
      :columns="productColumns"
      :data="products"
      @delete-row="deleteProduct"
      @edit-row="openEdit"
    >

      <template #title>
        <h2>Product Information</h2>
      </template>


      <template #form>


        


        <input
          v-model="newProduct.product"
          placeholder="Product"
        />


        <input
          v-model="newProduct.price"
          @input="allowOnlyNumbers"
          placeholder="Price"
        />


        <input
          v-model="newProduct.stock"
          @input="allowOnlyNumbers"
          placeholder="Stock"
        />


        <button
        class="add-btn"
        @click="addProduct"
        >
        Add Product
        </button>


      </template>

    </BaseDataTable>
  </div>

    <!-- DELETE POPUP -->

    <div
v-if="showDeleteDialog"
class="dialog delete-dialog"
>

<div class="warning-icon">
⚠️
</div>

<h2>
Delete Record?
</h2>

<p>
Are you sure you want to delete it permanently?
</p>

<div class="dialog-buttons">

<button
class="yes-btn"
@click="confirmDelete"
>
✔ Yes
</button>

<button
class="no-btn"
@click="cancelDelete"
>
✖ No
</button>

</div>

</div>

    <div
v-if="showEditDialog"
class="dialog"
>

<h3>Edit Data</h3>


<div
v-for="key in editFields"
:key="key"
class="edit-row"
>

<label>
{{ key }}
</label>

<input
v-model="editData[key]"
@input="['marks','salary','price','stock'].includes(key) ? allowOnlyNumbers($event) : null"
/>

</div>



<div class="dialog-buttons">

<button
class="save-btn"
@click="saveEdit"
>
 Save Changes
</button>

<button
class="cancel-btn"
@click="cancelEdit"
>
✖ Cancel
</button>

</div>


</div>

  </div>
</template>

<script setup>
import { ref, computed,onMounted, watch } from "vue";
import BaseDataTable from "./components/BaseDataTable.vue";
import studentData from "./data/students.json";



/* ================= DATA ================= */


const students = ref(studentData);
const employees = ref([
{
id:101,
name:"Amit",
department:"HR",
salary:50000
},

{
id:102,
name:"Riya",
department:"IT",
salary:70000
},

{
id:103,
name:"Karan",
department:"Finance",
salary:65000
}

]);

const products = ref([
{
id:1,
product:"Laptop",
price:60000,
stock:15
},

{
id:2,
product:"Mouse",
price:800,
stock:50
},

{
id:3,
product:"Keyboard",
price:1500,
stock:30
}

]);
/* ================= COLUMNS ================= */
const columns = [
{
label:"ID",
field:"id"
},

{
label:"Name",
field:"name"
},

{
label:"Course",
field:"course"
},

{
label:"Marks",
field:"marks"
}

];

const employeeColumns=[
{
label:"ID",
field:"id"
},

{
label:"Name",
field:"name"
},

{
label:"Department",
field:"department"
},

{
label:"Salary",
field:"salary"
}

];
const productColumns=[

{
label:"ID",
field:"id"
},

{
label:"Product",
field:"product"
},

{
label:"Price",
field:"price"
},

{
label:"Stock",
field:"stock"
}

];
/* ================= FORM DATA ================= */


const newStudent = ref({

name:"",
course:"",
marks:""

});
const newEmployee = ref({

name:"",
department:"",
salary:""

});

const newProduct = ref({

product:"",
price:"",
stock:""

});
const selectedTable = ref("student");
/* ================= DELETE POPUP ================= */

const showDeleteDialog = ref(false);
const deleteId = ref(null);


const deleteType = ref("");


/* ================= EDIT POPUP ================= */
const showEditDialog = ref(false);

const editData = ref({});

const editId = ref(null);
const editFields = ref([]);

/* ================= LOCAL STORAGE ================= */

onMounted(() => {

  const savedStudents = localStorage.getItem("students");
  const savedEmployees = localStorage.getItem("employees");
  const savedProducts = localStorage.getItem("products");

  if (savedStudents) {
    students.value = JSON.parse(savedStudents);
  }

  if (savedEmployees) {
    employees.value = JSON.parse(savedEmployees);
  }

  if (savedProducts) {
    products.value = JSON.parse(savedProducts);
  }

});

watch(
  students,
  (newValue) => {
    localStorage.setItem(
      "students",
      JSON.stringify(newValue)
    );
  },
  { deep: true }
);

watch(
  employees,
  (newValue) => {
    localStorage.setItem(
      "employees",
      JSON.stringify(newValue)
    );
  },
  { deep: true }
);

watch(
  products,
  (newValue) => {
    localStorage.setItem(
      "products",
      JSON.stringify(newValue)
    );
  },
  { deep: true }
);

const totalStudents = computed(() => {
  return students.value.length;
});

const averageMarks = computed(() => {
  if (students.value.length === 0) return 0;

  const total = students.value.reduce(
    (sum, student) => sum + Number(student.marks),
    0
  );

  return (total / students.value.length).toFixed(1);
});

const highestMarks = computed(() => {
  if (students.value.length === 0) return 0;

  return Math.max(
    ...students.value.map(student => Number(student.marks))
  );
});
const totalEmployees = computed(() => employees.value.length);

const totalSalary = computed(() => {
  return employees.value.reduce(
    (sum, e) => sum + Number(e.salary),
    0
  );
});

const averageSalary = computed(() => {
  if (employees.value.length === 0) return 0;

  return (
    totalSalary.value / employees.value.length
  ).toFixed(0);
});

const highestSalary = computed(() => {

if(employees.value.length===0) return 0;

return Math.max(
...employees.value.map(
e=>Number(e.salary)
)
);

});
const totalProducts = computed(() => products.value.length);

const totalStock = computed(() => {
  return products.value.reduce(
    (sum, p) => sum + Number(p.stock),
    0
  );
});

const inventoryValue = computed(() => {
  return products.value.reduce(
    (sum, p) =>
      sum + Number(p.price) * Number(p.stock),
    0
  );
});



/* ================= ADD STUDENT ================= */
const allowOnlyNumbers = (event) => {
  event.target.value = event.target.value.replace(/[^0-9]/g, "");
};

const addStudent = ()=>{
const newId = students.value.length > 0
? Math.max(...students.value.map(s=>s.id))+1
:1;
students.value.push({
id:newId,
...newStudent.value
});

newStudent.value={
name:"",
course:"",
marks:""
};
};

// ================= ADD EMPLOYEE =================

const addEmployee = ()=>{
const newId = employees.value.length > 0
? Math.max(...employees.value.map(e=>e.id))+1
:101;
employees.value.push({
id:newId,
...newEmployee.value
});

newEmployee.value={
name:"",
department:"",
salary:""
};
};

// ================= ADD PRODUCT =================

const addProduct = ()=>{
const newId = products.value.length > 0
? Math.max(...products.value.map(p=>p.id))+1
:1;
products.value.push({
id:newId,
...newProduct.value
});

newProduct.value={
product:"",
price:"",
stock:""

};
};





/* ================= DELETE FUNCTIONS ================= */



const deleteStudent=(id)=>{


deleteId.value=id;

deleteType.value="student";

showDeleteDialog.value=true;


};




const deleteEmployee=(id)=>{


deleteId.value=id;

deleteType.value="employee";

showDeleteDialog.value=true;


};




const deleteProduct=(id)=>{


deleteId.value=id;

deleteType.value="product";

showDeleteDialog.value=true;


};


/* ================= EDIT FUNCTION ================= */

const openEdit = (row) => {

  editData.value = { ...row };

  editId.value = row.id;

  if ("course" in row) {

    editFields.value = [
      "id",
      "name",
      "course",
      "marks"
    ];

  }

  else if ("department" in row) {

    editFields.value = [
      "id",
      "name",
      "department",
      "salary"
    ];

  }

  else {

    editFields.value = [
      "id",
      "product",
      "price",
      "stock"
    ];

  }

  showEditDialog.value = true;

};
const saveEdit = ()=>{

  let student = students.value.find(
    s=>s.id===editId.value
  );

  if(student){
    Object.assign(student, editData.value);
  }


  let employee = employees.value.find(
    e=>e.id===editId.value
  );

  if(employee){
    Object.assign(employee, editData.value);
  }


  let product = products.value.find(
    p=>p.id===editId.value
  );

  if(product){
    Object.assign(product, editData.value);
  }


  showEditDialog.value=false;

};



const cancelEdit = ()=>{

  showEditDialog.value=false;

  editData.value={};

  editId.value=null;

};


/* ================= CONFIRM DELETE ================= */


const confirmDelete=()=>{


if(deleteType.value==="student"){


students.value = students.value.filter(

student=>student.id!==deleteId.value

);


}



else if(deleteType.value==="employee"){


employees.value = employees.value.filter(

employee=>employee.id!==deleteId.value

);


}



else if(deleteType.value==="product"){


products.value = products.value.filter(

product=>product.id!==deleteId.value

);


}




showDeleteDialog.value=false;

deleteId.value=null;

deleteType.value="";


};





const cancelDelete=()=>{


showDeleteDialog.value=false;


deleteId.value=null;


deleteType.value="";


};



</script>



<style>

body{
font-family:'Segoe UI',Tahoma,Geneva,Verdana,sans-serif;
margin:0;
background:#f4f6f9;
color:#333;
margin:0;
padding:0;
box-sizing:border-box;

}


.dialog{
position:fixed;
top:50%;
left:50%;
transform:translate(-50%,-50%);
background:white;
padding:30px;
width:400px;
border-radius:18px;
box-shadow:0 20px 50px rgba(0,0,0,.25);
z-index:1000;
animation:popup .3s;
}

@keyframes popup{
from{
opacity:0;
transform:translate(-50%,-60%);
}
to{
opacity:1;
transform:translate(-50%,-50%);
}
}

.dialog input{
width:100%;
margin-top:5px;
margin-bottom:15px;
padding:10px;
border:1px solid #cccccc;
border-radius:8px;
backdrop-filter:blur(10px);
}


.dialog button{
width:120px;
margin-top:15px;
margin-right:10px;

}

.edit-field input{
width:100%;
padding:10px;
border-radius:8px;
border:1px solid #4CAF50;

}


.edit-field label{
font-weight:bold;
margin-bottom:5px;
text-transform:capitalize;
}


.edit-field input{
padding:8px;
width:200px;

}

.summary{
display:grid;
grid-template-columns:repeat(auto-fit,minmax(260px,1fr));
gap:25px;
margin-bottom:35px;
}

.card{
background:white;
padding:28px;
border-radius:18px;
box-shadow:0 10px 30px rgba(0,0,0,.08);
border-left:6px solid #4CAF50;
transition:.3s;
}

.card:hover{
transform:translateY(-8px);
box-shadow:0 15px 40px rgba(0,0,0,.15);
}

.card h3{
color:#666;
font-size:17px;
font-weight:500;
}

.card h2{
margin-top:15px;
font-size:38px;
color:#2e7d32;
}

.card:hover{
transform:translateY(-5px);
}

.card h3{
margin:0;
font-size:18px;
color:#666;
}

.card h2{
margin-top:15px;
font-size:34px;
color:#4CAF50;
}

.card h3{
margin:0;
font-size:18px;
}

.card h2{
margin-top:10px;
}

.container{
max-width:1400px;
margin:auto;
padding:35px;
}

.hero{
background:linear-gradient(135deg,#2e7d32,#4CAF50);
padding:35px;
border-radius:18px;
text-align:center;
margin-bottom:30px;
color:white;
box-shadow:0 10px 25px rgba(0,0,0,.15);
}

.hero h1{
margin:0;
font-size:42px;
font-weight:700;
}

.hero p{
margin-top:12px;
font-size:18px;
opacity:.9;
}

.selector{
display:flex;
align-items:center;
justify-content:flex-start;
gap:18px;
margin-bottom:30px;
padding:18px 22px;
background:white;
border-radius:15px;
box-shadow:0 4px 15px rgba(0,0,0,.08);
}

.selector label{
font-size:17px;
font-weight:600;
color:#444;
}

.selector select{
padding:10px 18px;
border-radius:10px;
border:1px solid #ddd;
font-size:15px;
cursor:pointer;
outline:none;
transition:.3s;
background:white;
}

.selector select:hover{
border-color:#4CAF50;
}

.selector select:focus{
border-color:#4CAF50;
box-shadow:0 0 8px rgba(76,175,80,.35);
}

.add-form{

background:#fff;

padding:25px;

border-radius:15px;

box-shadow:0 10px 25px rgba(0,0,0,.08);

margin-top:20px;

margin-bottom:25px;

}

.add-form h3{
margin-bottom:15px;
color:#2e7d32;
}
.add-form input{
margin-right:10px;
margin-bottom:10px;
}
.add-btn{
background:linear-gradient(135deg,#43a047,#2e7d32);
color:white;
border:none;
padding:12px 22px;
border-radius:10px;
font-size:15px;
font-weight:600;
cursor:pointer;
transition:.3s;
}

.add-btn:hover{
background:linear-gradient(135deg,#2e7d32,#1b5e20);
transform:translateY(-2px);
box-shadow:0 8px 18px rgba(46,125,50,.35);
}

.dialog-buttons{
display:flex;
justify-content:center;
gap:20px;
margin-top:25px;
}

.save-btn,
.cancel-btn{

width:180px;
height:52px;

border:none;
border-radius:10px;

font-size:16px;
font-weight:600;

cursor:pointer;

transition:.3s;
}

.save-btn{

background:#2e7d32;
color:white;

}

.save-btn:hover{

background:#1b5e20;
transform:translateY(-2px);

}

.cancel-btn{

background:#e53935;
color:white;

}

.cancel-btn:hover{

background:#c62828;
transform:translateY(-2px);

}

.delete-dialog{

text-align:center;
width:430px;

}

.warning-icon{

font-size:55px;

margin-bottom:15px;

}

.delete-dialog h2{

margin:0;
font-size:28px;

color:#333;

}

.delete-dialog p{

margin-top:15px;
margin-bottom:30px;

font-size:17px;

color:#666;

}

.yes-btn,
.no-btn{

width:170px;
height:50px;

border:none;
border-radius:10px;

font-size:16px;
font-weight:600;

cursor:pointer;

transition:.3s;

}

.yes-btn{

background:#43a047;
color:white;

}

.yes-btn:hover{

background:#2e7d32;

}

.no-btn{

background:#e53935;
color:white;

}

.no-btn:hover{

background:#c62828;

}
</style>