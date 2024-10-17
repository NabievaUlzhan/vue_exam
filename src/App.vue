<template>
  <img class="left" src="/src/pictures/decor_left.svg" alt="">
  <img class="right" src="/src/pictures/decor_right.svg" alt="">

  <div class="content">
    <h1>Welcome back!</h1>
    <p class="content_date">{{ currentDate.getDate() }}d / {{ currentDate.getMonth() + 1}}m / {{ currentDate.getFullYear() }}y</p>
    
    <div class="content_info">
      <div class="content_card balance">
        <p class="balance_extra">Total balance</p>
        <p class="balance_main" v-if="Math.round(incomesSummResult) - Math.round(expensesSummResult) >=0">${{ Math.round(incomesSummResult) - Math.round(expensesSummResult) }}.00</p>
        <p class="balance_main" v-else="">Insufficient funds</p>
      </div>

      <div class="content_card add">
        <div class="content_info income_input">
          <input v-model="newIncome.comment" type="text" placeholder="comment">
          <input v-model="newIncome.price" type="number" placeholder="price">
        </div>
          <button @click="addNew(newIncome, incomes), incomesSumm()" class="income_btn">Add income</button>
      </div>
      <div class="content_card add">
        <div class="content_info expense_input">
          <select v-model="newExpense.category">
            <option disabled selected hidden value="">category</option>
            <option v-for="expense in expenses" :value="expense.category">{{ expense.category }}</option>
          </select>
          <input v-model="newExpense.price" type="number" placeholder="price">
        </div>
        <button @click="addNew(newExpense, expenses), expensesSumm()" class="expense_btn">Add expence</button>
      </div>
    </div>

    <div class="content_info">
      <div class="content_card max_income">
        <div class="content_info max_income_top">
          <img class="max_picture" src="/src/pictures/up.png" alt="" srcset="">
          <h5>Max Profit:</h5>
          <p class="income_card-price">$ {{ findMaxArray(incomes, maxIncomeArray) }}</p>
        </div>
      </div>
      <div class="content_card max_income">
        <div class="content_info max_income_top">
          <img class="max_picture" src="/src/pictures/down.png" alt="">
          <h5>Max Expense:</h5>
          <p class="expense_card-price">${{ findMaxArray(expenses, maxExpenseArray) }}</p>
        </div>
      </div>
    </div>

    <div class="content_info max_categories">
      <div class="content_card max_category" v-for="max in maxCategoryArrays">
          <h4 class="cards_style-title">{{ max.category }} </h4>
        <p class="income_card-price"  v-if="max.price!=-Infinity">${{ max.price }}</p>
        <p class="income_card-price" v-else="">$0</p>  
      </div>
    </div>

    <div class="content_info">
      <div class="content_card income">
        <div class="content_card-title">
          <img src="/src/pictures/expenses.png" alt="">
          <h5>Profits</h5>
          <h5>${{ incomesSummResult }}.00</h5>
        </div>
        <div class="cards_style" v-for="income in incomes">
          <div>
            <p class="income_card-price">${{ income.price }}</p>
            <h4 class="cards_style-title">{{ income.comment }}</h4>
            <p class="cards_style-date">{{ income.date }}</p>
          </div>
          <button @click="removeOne(incomes,income.id), incomesSumm()">Delete</button>
        </div>
      </div>

      <div class="content_card expense">
        <div class="content_card-title">
          <img src="/src/pictures/expenses.png" alt="">
          <h5>Expenses</h5>
          <h5>${{ Math.round(expensesSummResult) }}.00</h5>
        </div>
        <div class="cards_style" v-for="expense in expenses">
          <div>
            <p class="expense_card-price">${{ expense.price }}</p>
            <h4 class="cards_style-title">{{ expense.category }}</h4>
            <p class="cards_style-date">{{ expense.date }}</p>
          </div>
          <button @click="removeOne(expenses,expense.id), expensesSumm()">Delete</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="js">
  import {ref, onMounted, computed, watch} from 'vue'
  import { v4 as uuidv4 } from 'uuid';

  const incomes = ref([
    {
      id: uuidv4(),
      price: 100,
      date: '2024-5-3',
      comment: 'Deposit'
    },
    {
      id: uuidv4(),
      price: 200,
      date: '2024-6-10',
      comment: 'Salary'
    },
    {
      id: uuidv4(),
      price: 300,
      date: '2024-6-17',
      comment: 'Scolarship'
    },
    {
      id: uuidv4(),
      price: 100,
      date: '2024-5-3',
      comment: 'Deposit'
    },
    {
      id: uuidv4(),
      price: 200,
      date: '2024-5-10',
      comment: 'Salary'
    },
    {
      id: uuidv4(),
      price: 300,
      date: '2024-5-30',
      comment: 'Scolarship'
    },
  ])
  const expenses = ref([
    {
      id: uuidv4(),
      price: 90.99,
      date: '2024-6-15',
      category: 'Travel'
    },
    {
      id: uuidv4(),
      price: 26.11,
      date: '2024-7-10',
      category: 'Food'
    },
    {
      id: uuidv4(),
      price: 53.90,
      date: '2024-5-14',
      category: 'Car service'
    },
    {
      id: uuidv4(),
      price: 94.90,
      date: '2024-9-11',
      category: 'Food'
    },
    {
      id: uuidv4(),
      price: 67.90,
      date: '2024-5-10',
      category: 'Shopping'
    },
    {
      id: uuidv4(),
      price: 98.90,
      date: '2024-1-27',
      category: 'Travel'
    },
    {
      id: uuidv4(),
      price: 20.90,
      date: '2024-1-27',
      category: 'Pet care'
    },
  ])
  const currentDate = ref(new Date())
  
  const incomesSummResult = ref(0)
  const expensesSummResult = ref(0)

  const incomesSumm = ()=>{
    incomesSummResult.value = 0
    return incomes.value.forEach(element => {
      return incomesSummResult.value+=element.price
    });
  }
  const expensesSumm = ()=>{
    expensesSummResult.value = 0
    return expenses.value.forEach(element => {
      return expensesSummResult.value+=element.price
    });
  }

  const maxIncomeArray = ref([])
  const maxExpenseArray = ref([])

  const findMaxArray = (array, maxArray)=>{
    array.filter(element => maxArray.push(element.price))
    return Math.max(...maxArray)
  }
  const newIncome = ref({  
    id: uuidv4(),
    price: '',
    comment: '',
    date: `${new Date().getFullYear()}-${new Date().getMonth()+1}-${new Date().getDate()}`
  }) 
  const newExpense = ref({  
    id: uuidv4(),
    price: '',
    category: '',
    date: `${new Date().getFullYear()}-${new Date().getMonth()+1}-${new Date().getDate()}`
  }) 

  const compareMaxArray = ref([])
  const maxCategoryArrays = ref([])

  const findMaxCategory =(category)=>{
    compareMaxArray.value.length = 0
    const sameElements = expenses.value.filter(element=> element.category == category)
    sameElements.forEach(element=> compareMaxArray.value.push(element.price))

    const newMaxCategory = ref({
      category: category,
      price: Math.max(...compareMaxArray.value)
    })
    maxCategoryArrays.value.push(newMaxCategory.value) 
  }

  const expensesCategory = ref(['Travel', 'Food', 'Car service', 'Home apliance', 'Medicine', 'Shopping', 'Pet care'])
  expensesCategory.value.forEach(element=>findMaxCategory(element))

  const addNew = (newElement, array)=>{
    array.push(newElement)  
    localStorage('income', JSON.stringify(newElement))
    watch(console.log(array))
  }
  const removeOne = (array, index)=>{
    const expenseIndex = array.findIndex((expense)=>expense.id == index)
      if(expenseIndex !== -1){
        array.splice(expenseIndex, 1)
    } 
  }

  onMounted(incomesSumm(), expensesSumm())
  watch(console.log(incomes.value))
</script>

<style scoped lang="css">
  .left{
    position: fixed;
    z-index: -1;
    left: 0;
    bottom: 0;
    width: 700px;
    height: 700px;
  }
  .right{
    position: fixed;
    z-index: -1;
    right: 0;
    top: 0;
    width: 700px;
    height: 700px;
  }
  .content{
    width: 50%;
    height: auto;
    margin-bottom: 150px;
    /* border: 1px solid red; */
    margin: 100px auto;
    h1{
      text-align: center;
      font-size: 45px;
      font-weight: 700;
    }
    
  }
  .content_date{
    text-align: center;
    font-size: 30px;
    color: rgb(197, 197, 197);
    margin: 20px 0 50px 0;
  }
  .content_card{
    background-color: rgba(24, 24, 24, 0.85);
    border-radius: 20px;
    border: none;
    padding: 40px 30px 20px 30px;
    margin: 20px;
    h5{
      font-size: 35px;
      font-weight: 600;
    }
  }
  .add{
    width: 40%;
    select, input{
      appearance: none;
      width: 42%;
      height: 40px;
      background-color: rgb(55, 55, 55);
      border-top-left-radius: 10px;
      border-top-right-radius: 10px;
      font-size: 30px;
      font-weight: 500;
      color: white;
      padding-left: 10px;
    }
    button{
      width: 100%;
      height: 30px;
      border-radius: 5px;
      background-color: rgb(55, 55, 55);
      color: white;
      font-size: 30px;
      font-weight: 500;
      margin: 20px auto;
      border: none;
    }
  }
  .income_input{
    input{
      border-bottom-color: rgba(32, 201, 151, 1);
    }
  }
  .expense_input{
    input, select{
      border-bottom-color: rgba(235, 87, 87, 1);
    }
  }
  .income_btn:hover{
    border:1px solid rgba(32, 201, 151, 1);
  }
  .expense_btn:hover{
    border:1px solid rgba(235, 87, 87, 1);
  }
  .balance{
    width: 20%;
    height: 115px;
  }
  .balance_main{
    font-size: 40px;
    font-weight: 600;
    margin-top: 10px;
  }
  .balance_extra{
    font-size: 25px;
    color: rgb(197, 197, 197);
  }
  .content_info{
    display: flex;
    justify-content: space-between;
  }
  .income, .expense{
    width: 50%;
    padding: 60px;
  }
  .content_card-title{
    display: flex;
    align-items: center;
    margin-bottom: 40px;
    justify-content: space-between;
    img{
      margin-right: 20px;
      width: 70px;
      height: 70px;
    }
  }
  .cards_style{
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 40px;
    button{
      width: 150px;
      height: 50px;
      border-radius: 5px;
      background-color: rgb(55, 55, 55);
      color: white;
      font-size: 30px;
      font-weight: 500;
      border: none;
    }
    button:hover{
      border: 1px solid rgba(235, 87, 87, 1);
      color: rgba(235, 87, 87, 1);
    }
  }
  .income_card-price{
    font-size: 30px;
    font-weight: 600;
    color: rgba(32, 201, 151, 1);
  }
  .expense_card-price{
    font-size: 30px;
    font-weight: 600;
    color: rgba(235, 87, 87, 1);
  }
  .cards_style-title{
    font-size: 25px;
    font-weight: 500;
    margin: 10px 0 10px 0;
  }
  .cards_style-date{
    font-size: 25px;
    color: rgb(197, 197, 197);
  }
  .max_income{
    width: 45%;
    height: 40px;
    padding: 50px;
  }
  .max_income_top{
    display: flex;
    align-items: center;
  }
  .max_picture{
    width: 20px;
  }
  /* .max_category{
    width: 500px;
  } */
  .max_category{
    display: flex;
    justify-content: space-between;
    width: 25%;
    align-items: center;
  }
  .max_categories{
    width: 100%;
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
  }
</style>
