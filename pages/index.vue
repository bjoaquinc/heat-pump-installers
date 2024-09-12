<template>
    <div class="container limit-width mx-auto w-100">
        <v-main class="mx-4 pt-4 mt-sm-8">
            <v-row id="home" align="center" class="py-12">
                <v-col cols="12" md="6">
                    <v-container class="pr-md-8">
                        <v-row>
                            <v-col cols="12">
                                <h1 class="text-h3 text-sm-h2 font-weight-bold">Top Heat Pump Installers Across Canada</h1>
                            </v-col>
                            <v-col cols="12" class="mt-6">
                                <p class="text-h5">Based on Google Reviews, Reddit suggestions and advice on the internet</p>
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
                <h2 class="text-h4 text-sm-h2">Contractors</h2>
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
            <!-- About Section -->
            <v-row id="about" align="center" class="py-12">
                <v-col cols="12" md="7">
                    <v-container class="pr-md-8">
                        <v-row>
                            <v-col cols="12">
                                <h1 class="text-h4 text-sm-h2">About Me</h1>
                            </v-col>
                            <v-col cols="12" class="mt-sm-6">
                                <p class="text-subtitle-1 text-sm-h5">Hi! My name is Joaquin, I’m a software engineer and I started Heat Pump Installers. ca to make it easier for Canadian homeowners and reliable heat pump contractors to find each other. After several HVAC technicians tried to talk me out of getting a heat pump and some contractors clearly overpricing me (I suspect because of the government benefits), I wanted to help make the process easier for other people like me. This website is a work-in-progress and I update it frequently. If you have any suggestions for improvements, please email me here:
</p>
                            </v-col>
                            <!-- <v-col cols="12" class="mt-6">
                                <v-btn
                                    @click="scrollToSection('contractors')"
                                    color="primary"
                                    size="x-large"
                                >
                                    Find Installers
                                </v-btn>
                            </v-col> -->
                        </v-row>
                    </v-container>
                </v-col>
                <v-col cols="12" md="5">
                    <v-img 
                        src="~/assets/img/about.jpeg"
                        class="rounded-xl"
                        aspect-ratio="16/9"
                        cover
                    />
                </v-col>
            </v-row>
            <!-- Footer -->
            <v-footer class="mt-12">
                <v-row align="center" justify="center">
                    <v-col cols="12" class="text-center">
                        <p class="text-subtitle-1">© 2024 Heat Pump Installers. All rights reserved.</p>
                    </v-col>
                </v-row>
            </v-footer>
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
    title: 'Top Heat Pump Installers Across Canada',
    ogTitle: 'Top Heat Pump Installers Across Canada',
    description: 'Welcome to our platform dedicated to connecting you with skilled heat pump installers in Canada. We help you find experienced professionals ready to enhance your home\'s energy efficiency with top-quality heat pump installations.',
    ogDescription: 'Welcome to our platform dedicated to connecting you with skilled heat pump installers in Canada. We help you find experienced professionals ready to enhance your home\'s energy efficiency with top-quality heat pump installations.',
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


const selectedProvince = ref('')

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
</script>

<style lang="sass" scoped>
.container
    padding-top: 60px
    padding-bottom: 60px
</style>