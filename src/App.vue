<script setup>
  import {ref, computed, onMounted} from 'vue'
  import Header from './components/Header.vue'
  import Balance from './components/Balance.vue'
  import IncomeExpenses from './components/IncomeExpenses.vue';
  import AddTransaction from './components/AddTransaction.vue';
  import TransactionList from './components/TransactionList.vue';
  import { useToast } from 'vue-toastification'
  const transactions = ref([])
  const toast = useToast()
  // const transactions = ref([
  //   {id: 1, text: 'Flowers', amount: -19.99},
  //   {id: 2, text: 'Salary', amount: 299.99},
  //   {id: 3, text: 'Book', amount: -25.99},
  //   {id: 4, text: 'Camera', amount: 250},
  // ])

  //get total 
  const total = computed(() => {
    return transactions.value.reduce( (acc, transaction) => {
      return acc + transaction.amount
    }, 0)
  })

  //get income
  const income = computed(() => {
    return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce( (acc, transaction) => {
      return acc + transaction.amount
    }, 0)
    .toFixed(2)
  })


  //get expense
  const expense = computed(() => {
    return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce( (acc, transaction) => {
      return acc + transaction.amount
    }, 0)
    .toFixed(2)
  })

  const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount,
    })
    // console.log(transactions)
    // console.log(JSON.stringify(transactions.value))
   
    saveTransactionsToLocalStorage()
    toast.success('Transaction Added')
  }

  //generate unique ids
  const generateUniqueId = () => {
    return Math.floor(Math.random() * 10000000)
  }

  //delete transaction
  const handleTransactionsDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
    saveTransactionsToLocalStorage()
    toast.success('Transaction Deleted')
  }

  //save to local storage
  const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
  }

  //first loads
  onMounted( () => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'))

    if(savedTransactions)
    {
      transactions.value = savedTransactions
    }
  })


</script>

<template>
  <Header></Header>
  <div class="container">
    <Balance :total="+total"></Balance>
    <IncomeExpenses :income="+income" :expense="+expense"></IncomeExpenses>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionsDeleted"></TransactionList>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"></AddTransaction>
     <!-- {{ transactions }}  -->
  </div>
</template>