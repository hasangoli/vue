<template>
  <Header />
  <div class="container">
    <Balance :total='+total' />
    <IncomeExpenses :income='+income' :expenses='+expenses' />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import AddTransaction from './components/AddTransaction.vue';
import Balance from './components/Balance.vue';
import Header from './components/Header.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';

import { useToast } from 'vue-toastification';

import { computed, onMounted, ref } from 'vue';

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions')) || [];
  transactions.value = savedTransactions;
});

// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0).toFixed(2);
});

// Get income
const income = computed(() => {
  return transactions.value.filter(transaction => transaction.amount > 0).reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0).toFixed(2);
});

// Get expenses
const expenses = computed(() => {
  return transactions.value.filter(transaction => transaction.amount < 0).reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0).toFixed(2);
});

// Handle transaction
const handleTransactionSubmitted = transactionData => {
  transactions.value.push({
    id: new Date(),
    text: transactionData.text,
    amount: transactionData.amount,
  });

  saveTransactionsToLocalStorage()

  toast.success('Transaction added');
};

const handleTransactionDeleted = id => {
  transactions.value = transactions.value.filter(transaction => transaction.id !== id);

  saveTransactionsToLocalStorage()

  toast.success('Transaction deleted');
};

// Save to local storage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>
