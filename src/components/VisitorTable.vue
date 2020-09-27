<template>
    <div id="visitor-table">
        <p v-if="visitors.length < 1" class="empty-table">
            No visitors
        </p>
        <div v-else>
            <div class="pages-numbers">
                <span v-for="page in getPages()" v-bind:class="{'page-span': true, 'active': page.id===pageCurrent}"
                      :key="page.id" @click="jumpToPage(page.id)">{{page.label}}</span>
            </div>
            <table class="contain-table">
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
                <tr v-for="visitor in getVisitors()" :key="visitor.id">
                    <td v-if="editing === visitor.id">
                        <input type="text" v-model="visitor.name"/>
                    </td>
                    <td v-else>{{ visitor.name }}</td>

                    <td v-if="editing === visitor.id">
                        <input type="text" v-model="visitor.address"/>
                    </td>
                    <td v-else>{{visitor.address}}</td>

                    <td v-if="editing === visitor.id">
                        <input type="text" v-model="visitor.phone"/>
                    </td>
                    <td v-else>{{formatPhone(visitor.phone)}}</td>

                    <td v-if="editing === visitor.id">
                        <input type="text" v-model="visitor.visit_at"/>
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
    </div>
</template>

<script>
    export default {
        name: 'visitor-table',
        props: {
            visitors: Array,
            noOfVisitorsInPage: Number
        },
        data() {
            return {
                editing: null,
                pageCurrent: 1,
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
            },
            jumpToPage(pageNo) {
                this.pageCurrent = pageNo
            },
            getVisitors() {
                let startIndex = (this.pageCurrent - 1) * this.$props.noOfVisitorsInPage
                let endIndex = this.pageCurrent * this.$props.noOfVisitorsInPage
                return this.$props.visitors.slice(startIndex, endIndex)
            },
            getPages() {
                let visitorsLength = this.$props.visitors.length
                let pageSpanLength = visitorsLength / this.$props.noOfVisitorsInPage
                if (visitorsLength % this.$props.noOfVisitorsInPage !== 0) pageSpanLength++
                return this.$props.visitors.slice(0, pageSpanLength).map((item, index) => {
                    return {
                        id: index + 1,
                        label: index + 1
                    }
                })
            },
            formatPhone(phn) {
                return phn
            },
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

    .pages-numbers {
        margin-bottom: 10px;
    }

    span.page-span {
        background: #0246a2;
        color: #fff;
        border-collapse: collapse;
        padding: 6px 12px;
        cursor: pointer;
        border: 1px solid #eee;
    }

    span.page-span.active {
        background: #318457;
    }
</style>
