<template>
    <div class="home-view-wrap">
        <div class="cell" v-for="(group, gInd) in computedArticles" :key="gInd">
            <p class="cell-date">{{group.name}}</p>
            <article-cell v-for="(item, index) in group.children" :key="index" :data="item"></article-cell>
        </div>
        <article-cell-loading
            :is-complete="numTotal !== -1 && numIndex * numSize >= numTotal"
        ></article-cell-loading>
    </div>
</template>
<script>

    import ArticleCell from 'src/components/article-cell'
    import ArticleCellLoading from 'src/components/article-cell-loading'
    import ScrollMixin from 'src/mixins/scroll.mixin'

    export default {
        mixins: [
            ScrollMixin,
        ],
        computed: {
            computedArticles () {
                let arr = this.arrData.slice(0, this.numIndex * this.numSize);
                let fmt = [];
                arr.forEach((item, index) => {
                    let { group } = item;
                    let [ objGroup ] = fmt.filter((item) => {
                        return item.name === group
                    });
                    if (!objGroup) {
                        objGroup = { name: group, children: [] };
                        fmt.push(objGroup);
                    }
                    objGroup.children.push(item);
                });
                return fmt;
            }
        },
        watch: {
            '$route' (v, o) {
                if (this.$route.path === '/' || this.$route.path.startsWith('/classify')) {
                    this.reqArticleList();
                }
            },
        },
        data () {
            return {
                arrData: [],
                numTotal: -1,
                numSize: 20,
                numIndex: 1,
                isLoading: false,
                arrSourceData: [],
            }
        },
        created () {
            this.reqArticleList();
        },
        methods: {
            reqArticleList () {
                if (this.arrSourceData.length) {
                    let arr = this.arrSourceData;
                    let { classify } = this.$route.params;
                    let { search } = this.$route.query;
                    if (classify) {
                        arr = arr.filter((item) => item.classify.toLocaleLowerCase() === classify);
                    }
                    if (search) {
                        arr = arr.filter((item) => {
                            const reg = new RegExp(search, 'ig');
                            return reg.test(item.title) || reg.test(item.abstract);
                        });
                    }
                    // 划分日期
                    this.arrData = arr;
                    this.numTotal = this.arrData.length;
                    return null;
                }
                this.$curl.get(`mocks/articles.json?v=${new Date().getTime()}`).then((res) => {
                    this.arrSourceData = res || [];
                    this.reqArticleList();
                });
            },
            scrollCallback () {
                let { scrollTop, scrollHeight, clientHeight } = this.scroll$;
                if (scrollTop + clientHeight > scrollHeight - 300) {
                    if (this.$route.path === '/') this.loadingMore();
                }
            },
            loadingMore () {
                if (this.isLoading)
                    return;
                if (this.numIndex * this.numSize >= this.numTotal)
                    return;
                this.isLoading = true;
                setTimeout(() => {
                    this.numIndex++;
                    this.isLoading = false;
                }, 500);
            },
        },
        components: {
            ArticleCell,
            ArticleCellLoading,
        },
    }

</script>
<style lang="scss" scoped>
    @import "~src/assets/scss/define";
    .home-view-wrap{
    }
    .cell{

    }
    .cell-date{
        @extend %df;
        @extend %aic;
        @extend %c6;
        @extend %fwb;
        @extend %ps;
        top: j(50);
        padding-left: j(20);
        font-size: j(18);
        background-color: #f2f2f2;
        height: j(50);
    }
</style>
