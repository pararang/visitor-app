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
            </tr>
            </thead>
            <tbody>
            <tr v-for="visitor in visitors" :key="visitor.id">
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

                <td v-if="editing === visitor.id">
                    <button @click="editVisitor(visitor)">Save</button>
                    <button class="muted-button" @click="editing = null">Cancel</button>
                </td>
                <td v-else >
                    <button @click="editMode(visitor.id)">Edit</button>
                    <button @click="$emit('delete:visitor', visitor.id)">Delete</button>
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
                editing: null
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
            }
        }
    }
</script>

<style scoped>
    button {
        margin: 0.5rem 0.5rem 0 0;
    }
    input {
        margin: 0;
    }
    .empty-table {
        text-align: center;
    }
    .container {
        display: flex;
    }
</style>
