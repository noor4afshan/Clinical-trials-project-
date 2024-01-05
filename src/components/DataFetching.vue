<!-- This component is only created for sample and its code is not including anywhere -->
<template>

</template>

<script>
import axios from 'axios'

export default {
    data() {
        return {
            fetchedData: [],
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
        }
    }
}

</script>

<!-- Extra code -->

<!-- // const visiblePages = [];
// if (this.filteredData.length < 10) {
//     visiblePages.push(this.currentPage);
// } else {
//     const half = (this.displayPages / 2);
//     const start = Math.max(this.currentPage - half, 1);
//     const end = Math.min(start + this.displayPages - 1, this.totalPages);
//     // const count = this.displayPages - (end - start + 1);
//     for (let i = start; i <= end; i++) {
//         visiblePages.push(i);
//     }
//     return visiblePages; -->