<template>
    <div id="visitor-table">
        <p v-if="visitors.length < 1" class="empty-table">
            No visitors
        </p>
        <table v-else class="contain-table">
            <thead>
            <tr>
                <th>Name</th>
                <th>Address</th>
                <th>Phone</th>
                <th>Visit at</th>
                <th>Actions</th>
                <th v-if="VisitorRecords.length >= 5">
                    Show rows: 
                    <select id="recordsToShow" v-model="recordsToShow">
                        <option :value="0" >All</option>
                        <option :value="opt" v-for="(opt, index) in recordLimit" :key="index">{{ opt }}</option>
                    </select>
                </th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="visitor in VisitorRecords" :key="visitor.id">
                <td v-if="editing === visitor.id">
                    <input type="text" v-model="visitor.name"/>
                </td>
                <td v-else>{{ visitor.name }}</td>

                <td v-if="editing === visitor.id">
                    <input type="text" v-model="visitor.address" />
                </td>
                <td v-else>{{visitor.address}}</td>

                <td v-if="editing === visitor.id">
                    <input type="text" v-model="visitor.phone" />
                </td>
                <td v-else>{{visitor.phone}}</td>

                <td v-if="editing === visitor.id">
                    <input type="text" v-model="visitor.visit_at" />
                </td>
                <td v-else>{{visitor.visit_at}}</td>

                <td v-if="editing === visitor.id" class="button-container">
                    <button @click="editVisitor(visitor)">Save</button>
                    <button class="muted-button" @click="editing = null">Cancel</button>
                </td>
                <td v-else class="button-container">
                    <button @click="editMode(visitor.id)" class="warning">Edit</button>
                    <button @click="deleteVisitor(visitor)" class="danger">Delete</button>
                </td>
            </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        name: 'visitor-table',
        props: {
            visitors: Array
        },
        data() {
            return {
                editing: null,
                recordsToShow: 5,   // Default no. of rows to show
                recordLimit: [5,10,20,50], // Limits for the pagination filter
            }
        },
        computed:{
            // Filter the visitor data based on pagination
            VisitorRecords(){
                let visitors = this.visitors
                // If pagination is set
                if (this.recordsToShow) visitors = visitors.filter((obj, key) => key < this.recordsToShow)

                return visitors
            }
        },
        methods: {
            editMode(id) {
                this.editing = id
            },
            editVisitor(visitor) {
                if (visitor.name === '' || visitor.address === '') return
                this.$emit('edit:visitor', visitor.id, visitor)
                this.editing = null
            },
            deleteVisitor(visitor) {
                if (confirm("Are you sure you want to delete?")) {
                    this.$emit('delete:visitor', visitor.id);
                }
            }
        }
    }
</script>

<style scoped>
    button {
        margin: 0.5rem;
        max-height: 40px;
    }
    input {
        margin: 0;
    }
    .empty-table {
        text-align: center;
    }

    .button-container {
        display: flex;
        align-items: center
    }
    
    td {
        min-height: 90px;
    }

    thead th, td {
        border-bottom: none;
    }
    tr {
        border-top: 1px solid #dedede;
        border-bottom: 1px solid #dedede;
    }
</style>
