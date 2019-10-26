<template>
    <div id="visitor-table">
        <p v-if="visitors.length < 1" class="empty-table">
            No visitors
        </p>
        <div v-else>
            <table class="contain-table">
                <thead>
                <tr>
                    <th colspan="2">
                        Show rows: 
                        <select id="recordsToShow" v-model="recordsToShow">
                            <option :value="0" >All</option>
                            <option :value="opt" v-for="(opt, index) in recordLimit" :key="index">{{ opt }}</option>
                        </select>
                    </th>
                    <th colspan="2">
                        Sort by: 
                        <select id="sortByOts" v-model="SortByValue">
                            <option :value="opt" :selected="SortByValue === opt ? true : false" v-for="opt in SortBy" :key="opt">{{ opt }}</option>
                        </select>
                    </th>
                    <th>
                        Sort order: 
                        <select id="sortByOts" v-model="ActiveSortOrder">
                            <option :value="opt" :selected="ActiveSortOrder === opt ? true : false" v-for="opt in SortOrder" :key="opt">{{ (opt === 0) ? 'Descending' : 'Ascending' }}</option>
                        </select>
                    </th>
                </tr>
                <tr>
                    <th>Name</th>
                    <th>Address</th>
                    <th>Phone</th>
                    <th>Visit at</th>
                    <th>Actions</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="visitor in VisitorRecords" :key="visitor.id">
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
                <tfoot>
                    <tr>
                        <td colspan="8">
                            <p v-if="PaginationPages >= 1">
                                <button v-for="Page in PaginationPages" :key="Page" @click="currentPage = Page">
                                    {{ Page }}
                                </button>
                            </p>
                        </td>
                    </tr>

                </tfoot>
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
                recordsToShow: 0,   // Default no. of rows to show
                recordLimit: [2,5,10,20,50], // Limits for the pagination filter,
                currentPage: 1,
                
                // Sort comparison keys 
                // *** `title` key is used to search the item in object to sort. So please before changing anything. Please be sure 
                // 0 -> Name
                // 1 -> Address
                // 2 -> Phone
                // 3 -> Visit at
                SortBy: ['Name', 'Address', 'Phone', 'Visit at'],
                SortByValue: 'Visit at',

                // Sorting order
                SortOrder: [0,1],
                ActiveSortOrder: 0
            }
        },
        watch:{
            // Reset the current pagination number if number of records is changed
            recordsToShow(){
                this.currentPage = 1
            }
        },
        computed:{
            // Filter the visitor data based on pagination
            VisitorRecords(){
                let visitors = this.visitors

                // Apply sorting to array
                if(this.ActiveSortBy) visitors = this.sortArr(visitors)
                
                // If pagination page is set
                if (this.currentPage > 0 && this.recordsToShow) {
                    let endIndex = this.currentPage * this.recordsToShow
                    let startIndex = endIndex - this.recordsToShow

                    visitors = visitors.filter((obj, key) => key >= startIndex && key < endIndex)
                }else if (this.recordsToShow) {
                    // If only pagination is set
                    visitors = visitors.filter((obj, key) => key < this.recordsToShow)
                }

                return visitors
            },

            // Creates the page buttons for pagination
            PaginationPages(){
                let returnVal = []
                if(this.VisitorRecords.length > 0 && this.recordsToShow > 0){
                    if(parseInt(this.RecordPerPage) >= 1){
                        for(let i = 1; i <= this.RecordPerPage; i++) returnVal.push(i)
                    }
                }
                return returnVal.length
            },

            // Returns how many pages should be made
            RecordPerPage(){
                const numToFill = this.visitors.length / this.recordsToShow;
                return (numToFill >= 1) ? Math.ceil(numToFill) : 1
            },

            // Active sort value
            ActiveSortBy(){
                return (this.SortByValue && this.SortBy.includes(this.SortByValue) && this.SortOrder.includes(this.ActiveSortOrder)) ? true : false
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
            sortArr(visitors = false){
                if(!visitors || typeof visitors !== 'object' || !this.SortBy.includes(this.SortByValue)) return visitors
                
                const SortField = this.SortByValue.toLowerCase().replace(/ /g, '_')
                const newArr = visitors.sort((a, b) => {
                    let firstItem = a[SortField]
                    let secondItem = b[SortField]

                    // ---- Change `firstItem` / `SecondItem` logic in the respective column section below
                    // if (SortField == 'name') {
                        
                    // }else if (SortField == 'address') {
                        
                    // }else if (SortField == 'phone') {
                        
                    // } else
                    if (SortField == 'visit_at') {
                        const firstDate = this.formatDate(a[SortField])
                        const secondDate = this.formatDate(b[SortField])

                        firstItem = new Date(...firstDate)
                        secondItem = new Date(...secondDate)
                    }
                    if(firstItem > secondItem) return (this.ActiveSortOrder === 0) ? -1 : 1
                    if(firstItem < secondItem) return (this.ActiveSortOrder === 0) ? 1 : -1
                })

                return newArr
            },
            // Normalize the saved date/time format for sort comparison
            formatDate(obj = false){
                if(!obj) return false
                const [date, time] = obj.split(' ')
                const [day, month, year] = date.split('-')
                const [hours, minutes, seconds] = time.split(':')

                return [year, month, day, hours, minutes, seconds]
            },
            formatPhone(phn) {
                let areaCode = phn.slice(0, 3)
                let nxx = phn.slice(3, 6)
                let ext = phn.slice(6, 11)
                return `(${areaCode}) ${nxx}-${ext}`
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
