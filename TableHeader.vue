<template>
    <div class="d-flex flex-row">
        <h1 class="display-1 green--text text--darken-4 mt-5 font-weight-bold">{{title}}</h1>

        <v-spacer></v-spacer>

        <my-text-field
            v-if="hasSearch"
            label="Search"
            v-model="search"
            hide-details
            class="mr-2 mt-5"
            capitalize
            @input="v => { $emit('search', v) }"
        ></my-text-field>

        <my-date-picker
            v-if="hasDate"
            v-model="dt"
            hide-details
            shrink
            readonly
            class="mx-2 mt-5"
            @input="reload"
        ></my-date-picker>

        <v-btn class="ml-2 mt-5" color="success" @click="reload">
            <v-icon>
                {{mdiReload}}
            </v-icon>
        </v-btn>
    </div>
</template>

<script>
    import { mdiReload } from '@mdi/js'

    export default {
        name: "TableHeader",

        props: {
            title: {type: String, default: null},
            hasSearch: {type: Boolean, default: true},
            hasDate: {type: Boolean, default: true}
        },

        data: () => ({
            search: "",
            dt: null,

            mdiReload
        }),

        methods: {
            reload() {
                if(this.hasDate && this.dt) {
                    this.addParamsToLocation({date: this.dt, ...this.$route.query})
                    this.$emit('reload', this.dt)
                } else {
                    this.$emit('reload')
                }
            },

            addParamsToLocation(params) {
                history.pushState(
                    {},
                    null,
                    this.$route.path +
                    '?' +
                    Object.keys(params)
                        .map(key => {
                        return (
                            encodeURIComponent(key) + '=' + encodeURIComponent(params[key])
                        )
                        })
                        .join('&')
                )
            }
        },

        mounted() {
            if(this.hasDate) {
                if(this.$route.query.date) {
                    this.dt = this.$route.query.date
                } else {
                    this.dt = this.currentDateToIsoString()
                    this.addParamsToLocation({date: this.dt, ...this.$route.query})
                }
            }
        }
    }
</script>
