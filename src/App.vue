<template>
    <Header />
    <div class="container">
        <Balance :total ="total"/>
        <IncomeExpences :income = "+income" :expense = "+expense"/>
        <TransectionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
        <AddTransection @transectionSubmitted="handleTransactionSubmitted"/>
    </div>
   
        
  
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpences from './components/IncomeExpences.vue';
import TransectionList from './components/TransectionList.vue';
import AddTransection from './components/AddTransection.vue';
import { useToast } from 'vue-toastification';

import {ref, computed, onMounted} from 'vue';

const toast = useToast();


const transactions = ref([]);

onMounted(() => {
    const savedTransaction = JSON.parse(localStorage.getItem('transactions'));
    if(savedTransaction){
        transactions.value = savedTransaction;
    }
});


//Get total
const total = computed(()=>{
    return transactions.value.reduce((acc, transaction)=>{
        return acc + transaction.amount;

    }, 0);
});

//Get income
const income = computed(()=>{
    return transactions.value
    .filter((transaction)=> transaction.amount > 0)
    .reduce((acc, transaction)=>{
        return acc + transaction.amount;

    }, 0)
    .toFixed(2);
});



//Get expense
const expense = computed(()=>{
    return transactions.value
    .filter((transaction)=> transaction.amount < 0)
    .reduce((acc, transaction)=>{
        return acc + transaction.amount;

    }, 0)
    .toFixed(2);
});

//Add transaction
const handleTransactionSubmitted = (transactionData) =>{
   transactions.value.push({
    id: gemerateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
   });
   saveTransactionToLocalStorage();
   toast.success('Transaction added');
}

//Generate unique id

const gemerateUniqueId = () =>{
    return Math.floor(Math.random()*1000000);
};

//Delete Transation 

const handleTransactionDeleted = (id) => {
   transactions.value = transactions.value.filter((transaction) => 
   transaction.id !== id
   );
   saveTransactionToLocalStorage();
   toast.success('Transaction Deleted');
};

//save to local-storage
const saveTransactionToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>