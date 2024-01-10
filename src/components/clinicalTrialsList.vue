<template>
    <div class="container-fluid d-flex justify-content-end pt-2 pb-2">
        <input type="text" v-model="searchQuery" placeholder="Search by All " />
    </div>
    <div class="container-fluid">
        <table class="table table-bordered">
            <thead class="table-secondary text-center">
                <tr>
                    <th>Rank</th>
                    <th>NCTID</th>
                    <th>Condition</th>
                    <th>BriefTitle</th>
                    <th>
                        <!-- filter for phase -->
                        <div class="dropdown">
                            <button class="btn btn-secondary dropdown-toggle" type="button" id="phaseFilterDropdown"
                                data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                                {{ selectedPhase || "All Phases" }}
                            </button>
                            <div class="dropdown-menu" aria-labelledby="phaseFilterDropdown">
                                <a class="dropdown-item" href="#" @click="selectPhase('')"> All Phases </a>
                                <a class="dropdown-item" href="#" @click="selectPhase('Phase 1')">Phase 1 </a>
                                <a class="dropdown-item" href="#" @click="selectPhase('Phase 2')">Phase 2 </a>
                                <a class="dropdown-item" href="#" @click="selectPhase('Phase 3')">Phase 3</a>
                                <hr>
                                <a class="dropdown-item" href="#" @click="selectPhase('Not Applicable')">Not Applicable </a>
                            </div>
                        </div>
                    </th>
                    <th>OverallStatus</th>
                    <th>LocationContactEMail</th>
                </tr>
            </thead>
            <tbody class="text-center">
                <tr v-for="(data, index) in paginatedData" :key="data.Rank">
                    <td>{{ index + 1 }}</td>
                    <td>{{ data.NCTId[0] }}</td>
                    <td>{{ data.Condition[0] }}</td>
                    <td>{{ data.BriefTitle[0] }}</td>
                    <td>{{ data.Phase[0] }}</td>
                    <td>{{ data.OverallStatus[0] }}</td>
                    <td>{{ data.LocationContactEMail[0] }}</td>
                </tr>
            </tbody>
        </table>
    </div>

    <!-- Paggination -->

    <div class="container-fluid">
        <div class="row">
            <div class="col-md-4">
                <div class="pagination">

                    <button class="btn btn-sm bg-secondary-subtle me-1" @click="pageBack"
                        :disabled="currentPage === 1">&lt;</button>

                    <div class="btn-group" role="group">
                        <button v-for="page in visiblePages" :key="page" @click="onPage(page)"
                            :class="['btn', 'btn-sm', { 'bg-secondary-subtle me-1': currentPage !== page, 'btn-danger': currentPage === page }]">
                            {{ page }}
                        </button>
                    </div>

                    <button class="btn btn-sm bg-secondary-subtle " @click="pageNext"
                        :disabled="currentPage === totalPages">&gt;</button>
                </div>
            </div>
        </div>
    </div><br>
</template>

<script>
// FetchedData from here to fetchData method

import axios from 'axios'
export default {
    data() {
        return {
            fetchedData: [],
            currentPage: 1,           // For Pagination
            pageSize: 10,             // For Pagination
            displayPages: 6,          // For Pagination
            searchQuery: '',           // For Search
            selectedPhase: '',           // For phase
        };
    },
    mounted() {
        this.fetchData();
    },
    methods: {
        fetchData() {
            axios.get("https://classic.clinicaltrials.gov/api/query/study_fields?expr=MSI&max_rnk=500&fields=NCTId,Condition,BriefTitle,Phase,OverallStatus,LocationContactEMail&fmt=json")
                .then((resp) => {
                    this.fetchedData = resp.data.StudyFieldsResponse.StudyFields
                    console.log(this.fetchedData)
                })
                .catch((error) => {
                    console.error("An error occurred when fetching data", error)
                })
        },
        pageBack() {                                // For Pagination
            if (this.currentPage > 1) {
                this.currentPage -= 1
            }
        },
        pageNext() {                                        // For Pagination
            if (this.currentPage < this.totalPages) {
                this.currentPage += 1
            }
        },
        onPage(page) {                                          // For Pagination
            this.currentPage = page
        },
        filterData() {                                          // For Search                       
           
            const query = this.searchQuery.toLowerCase();

            const filteredData = this.fetchedData.filter(data => {
                
                const phaseMatches = this.selectedPhase === '' || data.Phase[0] === this.selectedPhase;
                const ContactEmails = data.LocationContactEMail.filter(email => email !== undefined).map(email => email.toLowerCase())
                
                    const queryMatches = data.NCTId[0].toLowerCase().includes(query) ||
                    data.BriefTitle[0].toLowerCase().includes(query) ||
                    ContactEmails.includes(query) ||
                    data.Condition[0].toLowerCase().includes(query) ||
                    data.OverallStatus[0].toLowerCase().includes(query);

                return phaseMatches && queryMatches;
            });

            return filteredData;
        },

        selectPhase(phase) {                                     //For phase 
            this.selectedPhase = phase;
        }

    },
    computed: {
        totalPages() {                                          // For Pagination
            return Math.ceil(this.filteredData.length / this.pageSize);
        },
        visiblePages() {                                        // For Pagination

            const visiblePages = [];

            const half = (this.displayPages / 2);
            const start = Math.max(this.currentPage - half, 1);
            const end = Math.min(start + this.displayPages - 1, this.totalPages);

            for (let i = start; i <= end; i++) {
                visiblePages.push(i);

            }
            return visiblePages;
        },

        paginatedData() {                                   // For Pagination
            const startIndex = (this.currentPage - 1) * this.pageSize;
            const endIndex = startIndex + this.pageSize;
            return this.filteredData.slice(startIndex, endIndex);                                               //changed here filteredData in the place of fetchedData to both features
        },
        filteredData() {                                    // For Search  
            return this.filterData();

        },
    }
}

</script>

<style></style>