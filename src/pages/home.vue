<template>
  <f7-page name="home">
      <f7-navbar>
          <div style="display: flex; width: 100%; justify-content: space-around;">
              <f7-list-item link="/" title="New"></f7-list-item>
              <f7-list-item link="/topstories/" title="Top"></f7-list-item>
              <f7-list-item style="margin-left: 20px" link="/bestseries/" title="Best"></f7-list-item>
          </div>
      </f7-navbar>
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
                f7.request.get('https://hacker-news.firebaseio.com/v0/newstories.json', data => {
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
<style lang="scss">
    * {
        list-style: none;
    }
    a {
        text-decoration: none!important;
    }
    .back > span {
        color: white!important;
    }
    .page-content {
        background-color: #f2f3f5!important;
    }
    .navbar-bg {
        background-color: #FF6500!important;
    }
    .navbar .title {
        color: white!important;
    }
    .item-title {
        color: white!important;
    }
    .icon {
        color: white!important;
    }
</style>
