<template>
    <f7-page name="hacker">
        <f7-navbar title="Best Stories" back-link="Back"></f7-navbar>
        <f7-list style="display: flex; flex-direction: column">
            <f7-list-item :link="'/item/'+ item.id" v-for="(item, index) in this.list">{{item.title}}</f7-list-item>
        </f7-list>
    </f7-page>
</template>

<script>
    export default {
        data() {
            return {
                list: []
            }
        },
        mounted() {
            console.log(this.list)
            this.$f7ready((f7) => {
                f7.request.get('https://hacker-news.firebaseio.com/v0/beststories.json', data => {
                    let test = JSON.parse(data)
                    test = test.slice(0, 10)

                    test.forEach(item => {
                        f7.request.get('https://hacker-news.firebaseio.com/v0/item/' + item + '.json', index => {
                            let item = JSON.parse(index)
                            this.list.push(item)
                        })
                    })
                })
            })
        }
    }
</script>
