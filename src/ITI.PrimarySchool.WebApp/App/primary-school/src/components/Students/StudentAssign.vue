<template>
    <div>
        <div class="page-header">
            <h1>Assigner une classe à un élève</h1>
        </div>

        <form @submit="onSubmit($event)" v-if="model">
            <div class="form-group">
                <label class="required">Classe assignée : </label>
                <select class="form-control" v-model="model.classId">
                    <option :value="0">Aucune classe</option>
                    
                    <option v-for="c of availableClasses" :value="c.classId" >
                        {{ c.name }}
                    </option>
                </select>
            </div>

            <button type="submit" class="btn btn-primary">Assigner</button>
        </form>
    </div>
</template>

<script>
    import { mapActions } from 'vuex'
    import StudentApiService from '../../services/StudentApiService'
    import ClassApiService from '../../services/ClassApiService'

    export default {
        data () {
            return {
                model: null,
                studentId: null,
                availableClasses: []
            }
        },

        async mounted() {
            this.studentId = this.$route.params.id;

            try {
                this.model = await this.executeAsyncRequest(() => StudentApiService.getStudentAssignedClassAsync(this.studentId));
                this.availableClasses = await this.executeAsyncRequest(() => ClassApiService.getClassListAsync());

                if(this.model.classId > 0) this.availableClasses.push(this.model);
            }
            catch(e) {
                console.log(e)
                return this.redirectToList();
            }
        },

        methods: {
            ...mapActions(['executeAsyncRequest']),

            redirectToList() {
                this.$router.replace('/students');
            },

            async onSubmit(e) {
                e.preventDefault();

                try {
                    await this.executeAsyncRequest(() => StudentApiService.assignStudentToclassAsync(this.studentId, this.model.classId));
                    this.redirectToList();
                }
                catch(e) {

                }
            }
        }
    }
</script>

<style lang="less">

</style>