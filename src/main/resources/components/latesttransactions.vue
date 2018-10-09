<template>
    <div class="container-fluid">
        <div id="demogrid">
            <div class="form-group">
                <label class="col-form-label" for="searchField">SEARCH</label>
                <input name="query" v-model="searchQuery" type="text" class="form-control"
                       placeholder="Please, enter your request here..." id="searchField">
            </div>

            <demo-grid
                    :data="gridData"
                    :columns="gridColumns"
                    :filter-key="searchQuery">
            </demo-grid>
        </div>
    </div>

    <table class="table table-hover table-sm" id="table-template">
        <thead class="thead-dark">
        <tr>
            <th v-for="key in columns"
                @click="sortBy(key)"
                :class="{ active: sortKey == key }">
                {{ key | capitalize }}
                <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'">
          </span>
            </th>
        </tr>
        </thead>
        <tbody class="table-striped">
        <tr v-for="entry in filteredData" class="table-light">
            <td v-for="key in columns">
                {{entry[key]}}
            </td>
        </tr>
        </tbody>
    </table>
</template>

<script>
    export default {
        name: "latesttransactions"
    }

    Vue.component('demo-grid', {
        template: '#table-template',
        props: {
            data: Array,
            columns: Array,
            filterKey: String
        },
        data: function () {
            var sortOrders = {}
            this.columns.forEach(function (key) {
                sortOrders[key] = 1
            })
            return {
                sortKey: '',
                sortOrders: sortOrders
            }
        },
        computed: {
            filteredData: function () {
                var sortKey = this.sortKey
                var filterKey = this.filterKey && this.filterKey.toLowerCase()
                var order = this.sortOrders[sortKey] || 1
                var data = this.data
                if (filterKey) {
                    data = data.filter(function (row) {
                        return Object.keys(row).some(function (key) {
                            return String(row[key]).toLowerCase().indexOf(filterKey) > -1
                        })
                    })
                }
                if (sortKey) {
                    data = data.slice().sort(function (a, b) {
                        a = a[sortKey]
                        b = b[sortKey]
                        return (a === b ? 0 : a > b ? 1 : -1) * order
                    })
                }
                return data
            }
        },
        filters: {
            capitalize: function (str) {
                return str.charAt(0).toUpperCase() + str.slice(1)
            }
        },
        methods: {
            sortBy: function (key) {
                this.sortKey = key
                this.sortOrders[key] = this.sortOrders[key] * -1
            }
        }
    })

    var dataURL = 'https://my-json-server.typicode.com/andrewkharyshyn/explorer/serverResponse';
    var latest = new Vue({
        el: '#latesttransactions',
        data: {
            searchQuery: '',
            gridColumns: ['Hash', 'Origin', 'Destination', 'Timestamp', 'Key', 'Value'],
            gridData: []
        },
        mounted() {
            var self = this
            $.getJSON(dataURL, function (data) {
                self.gridData = data;
            });
        }
    })
</script>

<style scoped>

</style>