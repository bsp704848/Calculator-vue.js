<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { evaluate } from 'mathjs'

const current = ref('')
const history = ref([])
const showHistory = ref(false) 

function append(value) {
    current.value += value
}

function clear() {
    current.value = ''
}

function del() {
    current.value = current.value.slice(0, -1)
}

function calculate() {
    try {
        const result = evaluate(current.value).toString();  
        const entry = `${current.value} = ${result}`
        history.value.push(entry)
        localStorage.setItem('calc-history', JSON.stringify(history.value))
        current.value = result
    } catch {
        current.value = "Error"
    }
}

function handleKey(event) {
    const key = event.key
    if (!isNaN(key) || "+-*/.%".includes(key)) {
        append(key)
    } else if (key === "Enter") {
        event.preventDefault()
        calculate()
    } else if (key === "Backspace") {
        del()
    } else if (key === "Escape") {
        clear()
    }
}

function toggleHistory() {
    showHistory.value = !showHistory.value
}

onMounted(() => {
    window.addEventListener("keydown", handleKey)
})

const savedHistory = localStorage.getItem('calc-history')
    if (savedHistory) {
        history.value = JSON.parse(savedHistory)
    }


onBeforeUnmount(() => {
    window.removeEventListener("keydown", handleKey)
})
</script>

<template>
    <div class="main p-6 rounded-xl shadow-xl w-80">
     
        <div class="mb-4 text-right text-2xl font-mono bg-gray-100 p-3 rounded relative">
            {{ current || '0' }}
            <button @click="toggleHistory" class="absolute left-1 top-1/2 transform -translate-y-1/2 text-lg">
                <i class="pi pi-history"></i></button>
        </div>

  
        <div class="grid grid-cols-4 gap-2">
            <button @click="clear" class="btn">C</button>
            <button @click="del" class="btn">Del</button>
            <button @click="append('%')" class="btn">%</button>
            <button @click="append('/')" class="btn">/</button>

            <button @click="append('7')">7</button>
            <button @click="append('8')">8</button>
            <button @click="append('9')">9</button>
            <button @click="append('*')">x</button>

            <button @click="append('4')">4</button>
            <button @click="append('5')">5</button>
            <button @click="append('6')">6</button>
            <button @click="append('-')">-</button>

            <button @click="append('1')">1</button>
            <button @click="append('2')">2</button>
            <button @click="append('3')">3</button>
            <button @click="append('+')">+</button>

            <button @click="append('0')" class="col-span-2 btn">0</button>
            <button @click="append('.')" class="btn">.</button>
            <button @click="calculate" class="btn bg-green-500 text-dark">=</button>
        </div>

 
        <div v-if="showHistory" class="mt-4 bg-white rounded p-3 text-sm h-32 overflow-y-auto">
            <h2 class="font-bold mb-2">History</h2>
            <ul>
                <li v-for="(item, index) in history" :key="index">{{ item }}</li>
            </ul>
        </div>
    </div>
</template>

<style scoped>
.btn {
    background-color: rgb(238, 228, 228);
    padding: 4px;
    border-radius: 10px;
    font-size: large;
    font-weight: bolder;
}

.btn:hover {
    background-color: rgb(143, 136, 136);
    color: white;
}

.main {
    background-color: rgb(49, 47, 47);
}
</style>
