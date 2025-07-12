<script setup>
import { ref, computed, onMounted} from 'vue';
import { useToast } from 'vue-toastification';
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';



const toast = useToast()

const transactions=ref([]); 

    onMounted (() => {
      const savedTransaction = JSON.parse(localStorage.getItem
        ('transactions'))
        if(savedTransaction) {
          transactions.value = savedTransaction
        }
    })

    const total = computed (() => {
      return transactions.value.reduce((acc, transaction) => {
        return acc + transaction.amount
      }, 0)
    })



    const income = computed (() =>{
      return transactions.value.filter((transaction) => transaction.amount > 0)
      .reduce((acc, transaction) =>{
        return acc + transaction.amount
      }, 0)

  })
  
  const expenses = computed (() => {
      return transactions.value.filter((transaction) => transaction.amount < 0)
      .reduce((acc, transaction) => {
    return acc + transaction.amount
  },0)
    })

const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id:generateUniqueId(),
    text: transactionData.text,
    amount:transactionData.amount
  })

    saveTransactionsToLocalStorage();
    console.log(transactions)
    toast.success('Transaction added')
  }

const handleTransactionDelete = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)

  saveTransactionsToLocalStorage()
  toast.success('Transaction Deleted')
  
}

const generateUniqueId = () => {
  return  Math.floor(Math.random() * 1000000)
  }

  // Save to localstorage

  const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
  }
</script>



<template>
  <Header/>
  <div class="container">
    <Balance :total="+total"/>
    <IncomeExpense :expenses="+expenses" :income="+income"/>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDelete"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>