<template>
    <div class="details-view-wrap">
        <article-cell-loading v-if="!strContent || !objArticle"></article-cell-loading>
        <div class="article-header" v-show="strContent && objArticle">
            <h1>{{objArticle.title}}</h1>
            <p class="time">{{objArticle.date}}</p>
            <div class="tag">
                <span class="tag-item">{{objArticle.classify}}</span>
            </div>
        </div>
        <div class="article-content" v-show="strContent && objArticle" ref="article" id="article" v-html="strContent"></div>
    </div>
</template>
<script>
    import marked from 'marked'
    import hljs from 'src/utils/highlight.pack'
    import ArticleCellLoading from 'src/components/article-cell-loading'

    export default {
        data () {
            return {
                strContent: '',
                objArticle: '',
            };
        },
        watch: {
            //监听路由变化
            '$route.params' () {
                if (this.$route.path.startsWith('/details')) {
                    this.strContent = '';
                    this.objArticle = '';
                    document.documentElement.scrollTop = 0;
                    document.body.scrollTop = 0;
                    this.$store.commit('SET_CATALOG', { value: [] });
                    this.reqArticleContent();
                    this.reqArticleList();
                }
            },
        },
        created () {
            this.reqArticleContent();
            this.reqArticleList();
        },
        methods: {
            reqArticleContent () {
                let { classify, id } = this.$route.params;
                this.$curl.get(`articles/${classify}/${id}?v=${new Date().getTime()}`).then((res) => {
                    this.strContent = marked(res);
                    this.$nextTick(() => {
                        if (this.$const.BASE_URL) {
                            document.querySelectorAll('img').forEach((img) => {
                                let src = img.getAttribute('src');
                                if (!src.startsWith('http')) {
                                    img.setAttribute('src', `${this.$const.BASE_URL}${src}`);
                                }
                            });
                        }
                        this.$refs.article.querySelectorAll('a').forEach((a) => {
                            a.setAttribute('target', '_bank');
                            if (this.$const.BASE_URL) {
                                let href = a.getAttribute('href');
                                if (!href.startsWith('http')) {
                                    a.setAttribute('href', `${this.$const.BASE_URL}${href}`);
                                }
                            }
                        });
                        setTimeout(() => {
                            document.querySelectorAll('pre').forEach((block) => {
                                hljs.highlightBlock(block);
                            });
                        });
                        if (this.strContent && this.objArticle) {
                            setTimeout(() => {
                                this.$store.commit('SET_CATALOG', { el:  this.$refs.article });
                            }, 200)
                        }
                    });
                }).catch((err) => {
                    console.log(err);
                })
            },
            reqArticleList () {
                this.$curl.get(`mocks/articles.json?v=${new Date().getTime()}`).then((res) => {
                    let arr = res || [];
                    let { classify, id } = this.$route.params;
                    this.objArticle = arr.filter((item) => item.id === `${classify}/${id}`)[0];
                    if (this.strContent && this.objArticle) {
                        setTimeout(() => {
                            this.$store.commit('SET_CATALOG', { el:  this.$refs.article });
                        }, 200)
                    }
                });
            },
        },
        components: {
            ArticleCellLoading,
        },
    }

</script>

<style lang="scss" scoped>
    @import "~src/assets/scss/define";
    .details-view-wrap{
        line-height: 1.42857;
    }
    .article-header{
        padding: j(50) j(20);
        h1{
            @extend %c3;
            font-size: j(30);
            margin-bottom: j(16);
        }
        .time{
            color: #969696;
            font-size: j(13);
            margin: j(10) 0;
        }
    }
    .tag{
        @extend %df;
        @extend %aic;
    }
    .tag-item{
        @extend %cfff;
        font-size: j(12);
        margin-right: j(5);
        border-radius: j(2);
        padding: j(2) j(5);
        background-color: #bbb;
    }

    @media screen and (max-width: 768px){
        .article-header{
            h1{
                font-size: j(26);
            }
        }
    }
</style>

<style lang="scss">
    @import "~src/assets/scss/define";
    @import "~src/assets/scss/highlight";
    .article-content{
        color: #404040;
        line-height: 1.8;
        font-size: j(16);
        padding: j(20) j(20) j(100);
        p{
            margin-bottom: j(20);
        }
        blockquote{
            padding: j(20);
            background-color: #fafafa;
            border-left: j(6) solid #e6e6e6;
            word-break: break-word;
            font-size: j(16);
            font-weight: normal;
            line-height: j(30);
            margin:0 0 j(20);
            p:last-child{
                margin: 0;
            }
        }
        pre{
            border-radius: j(5);
            padding: j(10);
            font-size: j(13);
            line-height: 1.4;
            font-weight: 400;
            margin: 0 0 j(20);
            background-color: #2d2d2d;
        }
        a{
            color: #0681d0;
            &:hover{
                text-decoration: underline;
            }
        }

        h1{
            @extend %dn;
            margin: j(16) 0;
        }
        h2{
            font-size: j(26);
        }
        h3{
            font-size: j(22);
        }
        h4{
            font-size: j(20);
        }
        h5{
            font-size: j(18);
        }
        h6{
            font-size: j(16);
        }

        h1,
        h2,
        h3,
        h4,
        h5,
        h6{
            margin-bottom: j(16);
        }

        h1,
        h2,
        h3,
        h4,
        h5,
        h6,
        p,
        li {
            > code{
                padding: j(2) j(5);
                font-size: j(13);
                color: #c7254e;
                background-color: #f2f2f2;

            }
        }
        table{
            @extend %bsb;
            width: 100%;
            margin-bottom: j(20);
            border-collapse: collapse;
            border: 1px solid #eee;
            border-left: none;
            word-break: break-word;
            td,
            th{
                padding: 8px;
                border: 1px solid #eee;
                line-height: 20px;
                vertical-align: middle;
            }
            tr:nth-child(2n){
                background-color: hsla(0,0%,70.2%,.15);
            }
        }
        img{
            max-width: 100%;
            height: auto;
        }
        ol,ul{
            padding-left: 0;
            line-height: j(30);
            margin: 0 0 j(20) j(20);
            p:last-child{
                margin: 0;
            }
            ul, ol{
                margin-bottom: 0;
            }
        }
    }
</style>
