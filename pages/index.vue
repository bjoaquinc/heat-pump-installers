<template>
    <div class="container limit-width mx-auto w-100">
        <v-main class="mx-4 pt-4 mt-sm-8">
            <v-row id="about" align="center" class="py-12">
                <v-col cols="12" md="6">
                    <v-container class="pr-md-8">
                        <v-row>
                            <v-col cols="12">
                                <h1 class="text-h3 text-sm-h2 font-weight-bold">Find Heat Pump Installers Near You</h1>
                            </v-col>
                            <v-col cols="12" class="mt-6">
                                <p class="text-h5">Welcome to our platform dedicated to connecting you with skilled heat pump installers. We help you find experienced professionals ready to enhance your home's energy efficiency with top-quality heat pump installations.</p>
                            </v-col>
                            <v-col cols="12" class="mt-6">
                                <v-btn
                                    @click="scrollToSection('contractors')"
                                    color="primary"
                                    size="x-large"
                                >
                                    Find Installers
                                </v-btn>
                            </v-col>
                        </v-row>
                    </v-container>
                </v-col>
                <v-col cols="12" md="6">
                    <v-img 
                        src="~/assets/img/heat-pump.jpg"
                        class="rounded-xl"
                        aspect-ratio="16/9"
                        cover
                    />
                </v-col>
            </v-row>
            <div id="contractors" class="pt-15 d-flex flex-column">
                <h2 class="text-h4 text-sm-h2">Our Contractors</h2>
                <!-- Filter Section -->
                <v-row class="mt-6">
                    
                    <v-col cols="12" md="4">
                        <v-text-field
                            v-model="searchText"
                            label="Search by City or Contractor Name"
                            outlined
                            dense
                        />
                    </v-col>
                </v-row>
                <v-data-table :headers="headers" :items="filteredContractors" item-value="id" class="mt-3" :hide-default-header="isMobile">
                    <template v-slot:item="{ item }">
                        <tr>
                            <td class="py-6 text-sm-h5">
                            {{ item.name }}
                            </td>
                            <td class="text-sm-subtitle-1">{{ item.provinces.join(', ') }}</td>
                            <td class="text-sm-subtitle-1">{{ item.cities.join(', ') }}</td>
                            <td class="text-sm-subtitle-1">
                                <template v-for="email in item.emails">
                                    <a :href="'mailto:' + email">{{ email }}</a><br>
                                </template>
                            </td>
                            <td class="text-sm-subtitle-1">
                                <template v-for="{ phone, details} in item.phoneNumbers">
                                    <a :href="'tel:' + phone">{{ phone }}</a><br v-if="details"> {{ details }}<br>
                                </template>
                            </td>
                            <td><v-btn v-if="item.web_url" class="text-none" size="large" color="primary" :href="item.web_url" target="_blank" >See Website</v-btn></td>
                        </tr>
                    </template>
                </v-data-table>
            </div>
            
        </v-main class="d-flex">
        
    </div>
</template>

<script setup lang="ts">
import { VDataTable } from 'vuetify/components'
import contractors_json from '@/assets/data/heat_pump_contractors.json'

const { isMobile } = useDevice();
// TODO: UPDATE THIS URL
const WEBSITE_URL = 'https://example.com'

// SEO metadata
useSeoMeta({
    title: 'Find Heat Pump Installers Near You',
    ogTitle: 'Find Heat Pump Installers Near You',
    description: 'Welcome to our platform dedicated to connecting you with skilled heat pump installers. We help you find experienced professionals ready to enhance your home\'s energy efficiency with top-quality heat pump installations.',
    ogDescription: 'Welcome to our platform dedicated to connecting you with skilled heat pump installers. We help you find experienced professionals ready to enhance your home\'s energy efficiency with top-quality heat pump installations.',
    ogImage: '/assets/img/heat-pump.jpg',
    ogUrl: WEBSITE_URL,
    twitterCard: 'summary_large_image',
})

useHead({
    link: [
        {
            rel: 'canonical',
            href: WEBSITE_URL
        }
    ]
})

// Process Data

const contractors = contractors_json
const processedContractors = contractors.map((contractor) => {
    // Process provinces
    let processedProvinces: string[] = []
    if (contractor.provinces) {
        processedProvinces = contractor.provinces.split(';').map((province) => {
            return province.trim()
        })
    }
    // Process cities
    let processedCities: string[] = []
    if (contractor.cities) {
        processedCities = contractor.cities.split(';').map((city) => {
            return city.trim()
        })
    }
    // Process emails
    let processedEmails: string[] = []
    if (contractor.emails) {
        processedEmails = contractor.emails.split(';').map((email) => {
            return email.trim()
        })
    }
    // Process phoneNumbers
    let processedPhoneNumbers: {
        phone: string,
        details: string | null
    }[] = []
    if (contractor.phoneNumbers) {

        processedPhoneNumbers = contractor.phoneNumbers.split(';').map((phoneNumber) => {
            const phoneData = phoneNumber.trim().split(' (')
            const phone = phoneData[0]
            const details = phoneData[1] ? phoneData[1].replace(')', '') : null

            return {
                phone,
                details,
            }
        })
    }
    
    return {
        name: contractor.name,
        provinces: processedProvinces,
        cities: processedCities,
        emails: processedEmails,
        phoneNumbers: processedPhoneNumbers,
        web_url: contractor.website
    }
})

const uniqueProvinces = Array.from(new Set(processedContractors.map(contractor => contractor.provinces).flat())).sort()
const selectedProvince = ref('')

const uniqueCities = Array.from(new Set(processedContractors.map(contractor => contractor.cities).flat())).sort()
const selectedCity = ref('')


const searchText = ref('')

const filteredContractors = computed(() => {
    let filteredContractors = processedContractors
    // Filter by province
    if (selectedProvince.value) {
        filteredContractors = processedContractors.filter(contractor => contractor.provinces.includes(selectedProvince.value))
    } 
    // Filter by city
    if (selectedCity.value) {
        filteredContractors = processedContractors.filter(contractor => contractor.cities.includes(selectedCity.value))
    }
    // Filter by search text
    if (searchText.value) {
        filteredContractors = processedContractors.filter(contractor => contractor.name.toLowerCase().includes(searchText.value.toLowerCase()) ||  contractor.cities.join(', ').toLowerCase().includes(searchText.value.toLowerCase()) )
    }
    return filteredContractors
})

// Functions

const scrollToSection = (section: string) => {
    const element = document.getElementById(section);
    element?.scrollIntoView({
        behavior: 'smooth',
        block: 'start',
        inline: 'nearest'
    });
}

defineExpose({
scrollToSection,
})

// Data

type DataTableHeader = VDataTable["$props"]["headers"]
const headers: DataTableHeader= [
    {
        title: 'Contractor Name',
        align: 'start',
        sortable: false,
        key: 'name',
    },
    { title: 'Province', key: 'province', sortable: false },
    { title: 'Cities Serviced', key: 'cities', sortable: false },
    { title: 'Emails', key: 'emails', sortable: false },
    { title: 'Phone Numbers', key: 'phoneNumbers', sortable: false },
    { title: 'Website', key: 'web_url', sortable: false }
]

// const desserts = [
//     {
//         id: 1,
//         name: 'Sample, Inc.',
//         province: 'Ontario',
//         city: 'Toronto',
//         email: 'sample@gmail.com',
//         phone: '123-456-7890',
//         web_url: 'https://sample.com'
//     },
//     {
//         id: 2,
//         name: 'Sample, Inc.',
//         province: 'Ontario',
//         city: 'Toronto',
//         email: 'sample@gmail.com',
//         phone: '123-456-7890',
//         web_url: 'https://sample.com'
//     },
//     {
//         id: 3,
//         name: 'Sample, Inc.',
//         province: 'Ontario',
//         city: 'Toronto',
//         email: 'sample@gmail.com',
//         phone: '123-456-7890',
//         web_url: 'https://sample.com'
//     },
//     {
//         id: 4,
//         name: 'Sample, Inc.',
//         province: 'Ontario',
//         city: 'Toronto',
//         email: 'sample@gmail.com',
//         phone: '123-456-7890',
//         web_url: 'https://sample.com'
//     },
//     {
//         id: 5,
//         name: 'Sample, Inc.',
//         province: 'Ontario',
//         city: 'Toronto',
//         email: 'sample@gmail.com',
//         phone: '123-456-7890',
//         web_url: 'https://sample.com'
//     },
//     {
//         id: 6,
//         name: 'Sample, Inc.',
//         province: 'Ontario',
//         city: 'Toronto',
//         email: 'sample@gmail.com',
//         phone: '123-456-7890',
//         web_url: 'https://sample.com'
//     },
//     {
//         id: 7,
//         name: 'Sample, Inc.',
//         province: 'Ontario',
//         city: 'Toronto',
//         email: 'sample@gmail.com',
//         phone: '123-456-7890',
//         web_url: 'https://sample.com'
//     },
    
// ]
</script>

<style lang="sass" scoped>
.container
    margin-top: 60px
    margin-bottom: 200px
</style>