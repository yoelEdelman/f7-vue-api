<template>
    <f7-page name="item">
        <f7-navbar :title="title" back-link="Back"></f7-navbar>
        <f7-block-title>{{title}}</f7-block-title>
        <p v-if="content !== null" style="margin-left: 1em; margin-right: 1em">{{content}}</p>
        <a class="card-content-padding link external" target="_blank" :href="url">Lien vers l'article.</a>
            <h2 class="card-content-padding" v-if="kids">Commentaires:</h2>

            <f7-card class="card-content-padding" v-if="comment.text" v-for="comment in comments">
                <h3>{{comment.by}}<span style="font-size: 10px; margin-left: 10px">{{moment(comment.time, "YYYYMMDD").locale('fr').fromNow()}}</span></h3>
                <p style="overflow: hidden"><span v-html="comment.text"></span></p>
            </f7-card>
        <p class="card-content-padding" v-if="comments.length === 0">Aucun commentaire.</p>
    </f7-page>
</template>

<script>
    export default {
        data() {
            return {
                id: this.$f7route.params.id,
                title: null,
                content: null,
                url: null,
                kids: null,
                comments: []
            }
        },
        filters: {
            moment: function (date) {
                return this.moment(date)
            }
        },
        mounted() {
            this.$f7ready(f7 => {
                f7.request.get('https://hacker-news.firebaseio.com/v0/item/' + this.id + '.json', index => {
                    let data = JSON.parse(index)
                    this.title = data.title
                    this.content = data.text
                    this.url = data.url

                    if (data.kids) {
                        this.kids = data.kids
                        data.kids.forEach(comment => {
                            f7.request.get('https://hacker-news.firebaseio.com/v0/item/' + comment + '.json', data => {
                                let comment = JSON.parse(data)

                                let date = new Date(comment.time * 1000);
                                comment.time = date

                                this.comments.push(comment)
                            })
                        })
                    }
                })
            })
        }
    }
</script>
<style scoped lang="scss">
    a {
        padding-block-start: 0!important;
    }
    p {
        margin-top: 0;
    }
    h3, h2 {
        margin-bottom: 0!important;
    }
    h2 {
        padding-top: 0!important;
        padding-bottom: 0!important;
        margin-block-start: 0!important;
        margin-block-end: 0!important;
    }
</style>
