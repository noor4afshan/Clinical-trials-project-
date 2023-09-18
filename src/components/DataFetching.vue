<!-- This component is only created for sample and it's code is not including anywhere -->
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