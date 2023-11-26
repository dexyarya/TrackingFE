<script setup >
import { ref } from "vue"


const open = ref(false)

const toggle = () => {
    open.value = !open.value

}

</script>
<template>
    <div>
        <div class="font-sans antialiased" id="app">
            <nav class="flex items-center justify-between flex-wrap bg-teal p-6 bg-slate-100  shadow-sm">
                <div class="flex items-center flex-no-shrink text-gray-800 mr-6">
                    <h1 class="text-3xl font-bold "><span
                            class="text-yellow-500 font-extrabold text-3xl">Tracking</span><span
                            class="font-bold text-sm">Resi</span>
                    </h1>
                </div>
                <div class="block sm:hidden">
                    <button @click="toggle"
                        class="flex items-center px-3 py-2 border rounded text-teal-lighter border-teal-light hover:text-gray-100 hover:border-gray-100">
                        <svg class="fill-current h-3 w-3" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
                            <title>Menu</title>
                            <path d="M0 3h20v2H0V3zm0 6h20v2H0V9zm0 6h20v2H0v-2z" />
                        </svg>
                    </button>
                </div>
                <div :class="open ? 'block' : 'hidden'" class="w-full flex-end sm:flex sm:items-center sm:w-auto">
                    <div class="text-sm sm:flex-grow">
                        <a href="#responsive-header"
                            class="no-underline block mt-4 sm:inline-block sm:mt-0 text-teal-lighter hover:text-white mr-4">
                            Docs
                        </a>
                        <a href="#responsive-header"
                            class="no-underline block mt-4 sm:inline-block sm:mt-0 text-teal-lighter hover:text-white mr-4">
                            Examples
                        </a>
                        <a href="#responsive-header"
                            class="no-underline block mt-4 sm:inline-block sm:mt-0 text-teal-lighter hover:text-white">
                            Blog
                        </a>
                    </div>

                </div>
            </nav>
        </div>
    </div>
</template>