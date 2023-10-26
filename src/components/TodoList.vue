<script setup>
import { ref, computed } from 'vue'

const newItem = ref("")

const newItemCategory = ref("private")

const showForm = ref(false)

const items = ref([
    {id: 1, label: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Quam eligendi perferendis libero cum assumenda perspiciatis totam numquam veritatis aperiam provident. Soluta sunt esse repellat, similique voluptate dolores quia enim excepturi repellendus libero quisquam ea nesciunt aperiam debitis quasi fuga velit commodi perferendis vitae quibusdam exercitationem, neque eligendi. Laudantium, odit beatae adipisci sed voluptates architecto enim accusamus exercitationem quis! Quae, error.', category: 'służbowe', completed: false},
    {id: 2, label: 'Lorem ipsum dolor sit amet consectetur, adipisicing elit. Cumque nemo iure voluptates nisi maiores autem impedit porro commodi dignissimos, ad pariatur! Eos quo veniam modi consequatur incidunt tempora laudantium impedit ea architecto, perferendis pariatur odio, aperiam molestiae iure nihil nesciunt! Totam autem beatae vero ea, rem voluptatibus possimus hic facilis.', category: 'prywatne', completed: false},
    {id: 3, label: 'Lorem, ipsum dolor sit amet consectetur adipisicing elit. Quia ad recusandae impedit minus quibusdam? Perspiciatis harum, labore aliquid impedit blanditiis sed. Assumenda quas, eos, quaerat ab corporis exercitationem iusto repellat cupiditate delectus, quidem dignissimos at autem sint commodi nostrum ut!', category: 'prywatne', completed: false},
    {id: 4, label: 'Lorem ipsum dolor sit amet, consectetur adipisicing elit. Expedita assumenda delectus et necessitatibus modi, natus rem at consequuntur qui fugit rerum consectetur nobis excepturi provident commodi, hic suscipit aspernatur dolorum.', category: 'służbowe', completed: false},
    {id: 5, label: 'Lorem ipsum, dolor sit amet consectetur adipisicing elit. Molestias perferendis sapiente minus, veritatis temporibus explicabo eius ratione hic at nemo!', category: 'prywatne', completed: false},
    {id: 6, label: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Tenetur, quisquam?', category: 'służbowe', completed: false},
])

const reversedItems = computed(()=>{
    return [...items.value].reverse()
})

const currentFilter = ref("wszystkie")

const activeItems = computed(()=>{

    console.log(currentFilter.value);

    if(currentFilter.value === 'prywatne')
    {
        return reversedItems.value.filter(item => item.category === "prywatne")
    }
    if(currentFilter.value === 'służbowe')
    {
        return reversedItems.value.filter(item => item.category === "służbowe")
    }
    
    return reversedItems.value.filter(item => !item.completed)
})


const saveItem = ()=>{
    items.value.push({id:items.value.length +1, label: newItem.value, priority: newItemCategory})
    newItem.value = ""
}

const showFormToogle = ()=>{
    showForm.value = !showForm.value
    newItem.value = ""
}

const completedToogle = (item)=>{
    item.completed = !item.completed
}

</script>

<template>
    <div class="container w-96 shadow-lg p-5 rounded bg-slate-50" >
        <!-- Header -->
        <header>
            <h1 class="text-end pe-6 pt-5 pb-7 font-bold text-gray-500 text-sm"><span class="text-blue-400">MM</span>Todo</h1>
        </header>
        
        <main class="h-full w-full mx-auto">
        <!-- NewItem FORM -->
            <form 
                v-if="showForm"
                @submit.prevent="saveItem()">
                <label>
                <input 
                    class=" p-3 w-3/5 rounded shadow-sm outline-none focus:bg-slate-50 text-gray-500 text-sm font-bold" type="text" placeholder="Nowe zadanie..."
                    v-model.trim="newItem">
                <button
                    v-bind:disabled="newItem.length === 0"
                    class="disabled:bg-gray-500 disabled:cursor-not-allowed bg-blue-300 hover:bg-blue-400 text-white font-semibold p-3 w-1/5 rounded shadow-sm text-sm ease-out duration-300">
                    Dodaj
                </button>
                <button
                    @click="showFormToogle()"
                    class="bg-gray-300 hover:bg-gray-400 text-white font-semibold p-3 w-1/5 rounded shadow-sm text-sm ease-out duration-300">
                    Powrót
                </button>
                <p class="mt-1 ms-2 text-sm text-gray-500 font-semibold">{{ newItem.length }}/50</p>
            </label>
            <label>
                <div class="flex items-center mt-2">
                    <p class="text-sm text-gray-600 font-bold">Kategoria</p>
                <input
                v-model="newItemCategory"
                class="ml-3" type="radio"
                value="private"> 
                <span class="ml-1 text-sm text-gray-500">Prywatne</span>

                <input
                v-model="newItemCategory"
                class="ml-3" type="radio"
                value="business"> 
                <span class="ml-1 text-sm text-gray-500">Służbowe</span>
                </div>
            </label>
            </form>


            <!-- filtry -->
            <div v-if="!showForm">
                <div>
                    <p class="text-sm text-gray-600 font-bold mb-1">Filtruj ({{ currentFilter }})</p>
                </div>
                <div class="flex">
                    <p @click="currentFilter = 'wszystkie'" class="text-sm text-gray-500 hover:text-gray-600 font-semibold cursor-pointer hover:underline">Wszystkie</p>
                    <p @click="currentFilter = 'prywatne'" class="text-sm text-gray-500 hover:text-gray-600 font-semibold mx-2 cursor-pointer hover:underline">Prywatne</p>
                    <p @click="currentFilter = 'służbowe'" class="text-sm text-gray-500 hover:text-gray-600 font-semibold cursor-pointer hover:underline">Służbowe</p>
                </div>
            </div>
            
            <!-- Border-bottom -->
            <div class="border-b-2 border-black-500 my-4"></div>

            <!-- ListItem Aktualne -->
            <div class="my-6" v-if="!showForm">
                <p class="text-sm font-semibold text-red-500" v-if="!activeItems.length">Aktualne nie ma żadnych zadań</p>
                <p class="text-sm font-semibold text-green-500" v-if="activeItems.length">Aktualne zadania</p>
                <ul class="overflow-auto max-h-56">
                    <li 
                        class="border-2 rounded-xl px-2 py-2 mb-1 text-gray-500 text-sm flex items-center justify-between"
                        v-for="item in activeItems" :key="item.id">
                        <div class="flex items-center">
                            <svg v-if="item.category === 'prywatne'" class="fill-green-500 me-3 text-md w-5" xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 576 512"><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path d="M575.8 255.5c0 18-15 32.1-32 32.1h-32l.7 160.2c0 2.7-.2 5.4-.5 8.1V472c0 22.1-17.9 40-40 40H456c-1.1 0-2.2 0-3.3-.1c-1.4 .1-2.8 .1-4.2 .1H416 392c-22.1 0-40-17.9-40-40V448 384c0-17.7-14.3-32-32-32H256c-17.7 0-32 14.3-32 32v64 24c0 22.1-17.9 40-40 40H160 128.1c-1.5 0-3-.1-4.5-.2c-1.2 .1-2.4 .2-3.6 .2H104c-22.1 0-40-17.9-40-40V360c0-.9 0-1.9 .1-2.8V287.6H32c-18 0-32-14-32-32.1c0-9 3-17 10-24L266.4 8c7-7 15-8 22-8s15 2 21 7L564.8 231.5c8 7 12 15 11 24z"/></svg>
                            <svg v-if="item.category === 'służbowe'" class="fill-blue-400 me-3 w-5" xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 512 512"><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path d="M184 48H328c4.4 0 8 3.6 8 8V96H176V56c0-4.4 3.6-8 8-8zm-56 8V96H64C28.7 96 0 124.7 0 160v96H192 320 512V160c0-35.3-28.7-64-64-64H384V56c0-30.9-25.1-56-56-56H184c-30.9 0-56 25.1-56 56zM512 288H320v32c0 17.7-14.3 32-32 32H224c-17.7 0-32-14.3-32-32V288H0V416c0 35.3 28.7 64 64 64H448c35.3 0 64-28.7 64-64V288z"/></svg>
                            <p class="w-64 overflow-x-hidden">{{ item.label }}</p>
                        </div>
                        <div class="flex items-center">
                            <svg 
                                @click="completedToogle(item)"
                                class="cursor-pointer fill-red-400 hover:fill-red-600 hover:text-lg ease-out duration-300" xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 448 512"><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path d="M135.2 17.7L128 32H32C14.3 32 0 46.3 0 64S14.3 96 32 96H416c17.7 0 32-14.3 32-32s-14.3-32-32-32H320l-7.2-14.3C307.4 6.8 296.3 0 284.2 0H163.8c-12.1 0-23.2 6.8-28.6 17.7zM416 128H32L53.2 467c1.6 25.3 22.6 45 47.9 45H346.9c25.3 0 46.3-19.7 47.9-45L416 128z"/></svg>
                        </div>
                    </li>
                </ul>
            </div>


            <!-- <pre>
                {{ activeItems }}
            </pre> -->

            <div class="flex justify-center items-center w-full">
                <svg 
                    @click="showFormToogle()"
                    v-if="!showForm"
                    class="cursor-pointer fill-blue-400 hover:fill-blue-500 text-6xl ease-out duration-300" xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 512 512"><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path d="M256 512A256 256 0 1 0 256 0a256 256 0 1 0 0 512zM232 344V280H168c-13.3 0-24-10.7-24-24s10.7-24 24-24h64V168c0-13.3 10.7-24 24-24s24 10.7 24 24v64h64c13.3 0 24 10.7 24 24s-10.7 24-24 24H280v64c0 13.3-10.7 24-24 24s-24-10.7-24-24z"/></svg>
            </div>



        </main>
    </div>
</template>
  