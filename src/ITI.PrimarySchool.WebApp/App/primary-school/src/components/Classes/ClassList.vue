<template>
    <div>
        <div class="page-header">
            <h1>Gestion des classes</h1>
        </div>

        <div class="panel panel-default">
            <div class="panel-body text-right">
                <router-link class="btn btn-primary" :to="`classes/create`"><i class="glyphicon glyphicon-plus"></i> Ajouter une classe</router-link>
            </div>
        </div>

        <table class="table table-striped table-hover table-bordered">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Nom</th>
                    <th>Niveau</th>
                    <th>Options</th>
                </tr>
            </thead>

            <tbody>
                <tr v-if="classList.length == 0">
                    <td colspan="4" class="text-center">Il n'y a actuellement aucune classe.</td>
                </tr>

                <tr v-for="i of classList">
                    <td>{{ i.classId }}</td>
                    <td>{{ i.name }}</td>
                    <td>{{ i.level }}</td>
                    <td>
                        <router-link :to="`classes/edit/${i.classId}`"><i class="glyphicon glyphicon-pencil"></i></router-link>
                        <a href="#" @click="deleteClass(i.classId)"><i class="glyphicon glyphicon-remove"></i></a>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    import { mapActions } from 'vuex'
    import ClassApiService from '../../services/ClassApiService'

    export default {
        data() {
            return {
                classList: []
            }
        },

        async mounted() {
            await this.refreshList();
        },

        methods: {
            ...mapActions(['notifyLoading', 'notifyError']),

            async refreshList() {
                try {
                    //this.store.dispatch('notifyLoading', true);
                    this.notifyLoading(true);
                    this.classList = await ClassApiService.getClassListAsync();
                }
                catch(error) {
                    this.notifyError(error);
                }
                finally {
                    this.notifyLoading(false);
                }
            },

            async deleteClass(classId) {
                try {
                    this.notifyLoading(true);
                    await ClassApiService.deleteClassAsync(classId);
                    this.notifyLoading(false);
                    
                    await this.refreshList();
                }
                catch(error) {
                    this.notifyError(error);
                }
                finally {
                    this.notifyLoading(false);
                }
            }
        }
    }
</script>

<style lang="less">

</style>