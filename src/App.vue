<template>
    <div class="t-bg-gray-200 t-min-h-full">
        <div class="t-max-w-screen-md t-mx-auto">
            <div class="container">
                <h4 class="t-text-2xl t-font-bold t-py-5 t-text-center">
                    Himit Pens Cari Teks 
                </h4>
                <span class="t-text-gray-600 t-text-center t-block">
                    Source Video URL [ {{ videoUrl }} ]
                </span>

                <div class="t-py-5">
                    <input type="text" class="form-control t-mb-3" v-model="keyword" placeholder="Search text ..." >
                    <div class="t-text-center">
                        <button @click="search" class="t-rounded-lg t-bg-indigo-700 t-text-white t-py-2 t-px-4 t-outline-none t-border-0 t-shadow-none hover:t-bg-indigo-600"> Search </button>
                    </div>
                </div>
                <div class="t-pb-5" v-if="result.data.length">
                    <div v-for="(item, index) in result.data" :key="index" class="t-mb-4 t-rounded-lg t-shadow-md t-p-4 t-bg-white">
                        <div class="t-mb-2">
                            <div v-html="item.text"></div>
                        </div>
                        <div class="t-text-gray-600 t-text-md t-mb-5 t-text-sm">
                            Start: {{ item.start }} - End : {{ item.end }}
                        </div>
                        <div>
                            <a :href="`${videoUrl}&t=${item.start}s`" target="_blank" class="t-bg-blue-700 t-text-white t-py-2 t-px-4 hover:t-text-white t-rounded-lg t-text-sm"> Go to Video </a>
                        </div>
                    </div>
                    <div v-if="result.data.length" class="t-flex t-flex-wrap t-justify-center">
                        <button class="t-bg-blue-500 t-text-white t-p-2 t-m-2 t-text-xs t-rounded-md" @click="() => result.page = 1" v-if="result.page != 1" > First </button>
                        <button class="t-bg-blue-500 t-text-white t-p-2 t-m-2 t-text-xs t-rounded-md" @click="() => result.page = result.page -1 " v-if="result.page > 1"> Prev </button>
                        <button class="t-bg-blue-500 t-text-white t-p-2 t-m-2 t-text-xs t-rounded-md" @click="() => result.page = result.page + 1 " v-if="result.page < result.lastPage">  Next Page </button>
                        <button class="t-bg-blue-500 t-text-white t-p-2 t-m-2 t-text-xs t-rounded-md" @click="() => result.page = result.lastPage " v-if="result.page < result.lastPage" >Last</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
// import moment from 'moment';

export default {
    data() {
        return {
            apiUrl: 'https://cari-teks-video-api.vercel.app/api/search',
            videoUrl: 'https://www.youtube.com/watch?v=klnvttPfOUM',
            keyword: '',
            isLoading: false,
            result: {
                data: [],
                total: 0,
                page: 0,
                lastPage: 0,
            }
        }
    },
    computed: {
    },
    watch: {
        'result.page': function(newVal) {
            console.log('page changed', newVal);
            this.fetchData();
        }
    },
    methods: {
        cleanUpData() {

        },
        search() {
            this.result = {
                ...this.result,
                page: 1,
                data: [],
            };
            this.fetchData();
        },
        async fetchData() {
            try {
                const response = await axios.get(`${this.apiUrl}`, {
                    params: {
                        url: this.videoUrl,
                        q: this.keyword,
                        page: this.result.page,
                    }
                });
                const _result = {
                        ...this.result, 
                        data: response.data.data,
                        total: response.data.total,
                        lastPage: Math.floor(response.data.total / response.data.data.length) 
                    }
                this.result = _result;
            } catch (error) {
                console.log(error);
            }
        },
    },
    mounted() {
        this.fetchData();
    }
}
</script>

<style lang="scss">
    html, body {
        height: 100%;
    }
</style>