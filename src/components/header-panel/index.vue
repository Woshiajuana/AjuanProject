<template>
    <div class="header-section">
        <div class="header-inner">
            <a class="logo" href="/#/">{{$const.USER_NAME}}</a>
            <div class="search" :class="[strKeyword && 'active']">
                <div class="search-input">
                    <input v-model="strKeyword" ref="input" @input="handleInput" @keyup.enter="handleSearch" type="text" placeholder="请输入关键字"/>
                </div>
                <div v-show="strKeyword" @click="handleClear" class="search-btn iconfont icon-close"></div>
                <div @click="handleSearch" @mouseenter="$refs.input.focus()" class="search-btn iconfont icon-search"></div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data () {
            return {
                strKeyword: '',
            };
        },
        watch: {
            '$route.query' (v, o) {
                this.strKeyword = this.$route.query.search || '';
            },
        },
        methods: {
            handleInput () {
                if (!this.strKeyword) {
                    this.handleSearch();
                }
            },
            handleClear () {
                this.strKeyword = '';
                this.handleSearch();
            },
            handleSearch () {
                let query = {};
                let path = this.$route.path;
                if (this.strKeyword) {
                    query.search = this.strKeyword;
                }
                this.$refs.input && this.$refs.input.focus();
                if (this.$route.path !== '/' && !this.$route.path.startsWith('/classify')) {
                    path = '/';
                    if (!this.strKeyword)
                        return null;
                }
                this.$router.push({ path, query });
            }
        }
    }
</script>

<style lang="scss" scoped>
    @import "~src/assets/scss/define";

    .header-section{
        @extend %pf;
        @extend %w100;
        @extend %bsb;
        @extend %t0;
        z-index: 9;
        background-color: $bgColor;
    }
    .header-inner{
        @extend %ma;
        @extend %df;
        @extend %aic;
        @extend %jcs;
        max-width: j(960);
        height: j(50);
    }
    .logo{
        @extend %cp;
        @extend %fwn;
        color: $mainColor;
        font-size: j(24);
    }
    .search{
        @extend %df;
        @extend %pr;
        @extend %aic;
        @extend %bsb;
        @include tst(all, 0.3s);
        padding-right: j(36);
        background-color: #999;
        border-radius: j(20);
        width: j(36);
        height: j(36);
        &.active,
        &:hover{
            width: j(240);
            .search-input{
                @extend %db;
            }
            .search-btn{
                &.icon-close{
                    @extend %df;
                }
                &.icon-search{
                    background-color: $mainColor;
                }
            }
        }
    }
    .search-input{
        @extend %dn;
        @extend %df1;
        @extend %h100;
        input{
            @extend %w100;
            @extend %h100;
            @extend %df;
            @extend %aic;
            @extend %bsb;
            @extend %cfff;
            padding-left: j(16);
            background-color: transparent;
            border: none;
            font-size: j(13);
        }
        input::-webkit-input-placeholder {
            color: #fff;
        }
        input:-moz-placeholder {
            color: #fff;
        }
        input:-ms-input-placeholder {
            color: #fff;
        }
    }
    .search-btn{
        @extend %df;
        @extend %aic;
        @extend %jcc;
        @extend %cp;
        @extend %h100;
        @extend %cfff;
        width: j(36);
        font-size: j(20);
        &.icon-close{
            @extend %dn;
            color: #ddd;
            font-size: j(16);
        }
        &.icon-search{
            @extend %pa;
            @extend %t0;
            @extend %r0;
            @include tst(all, 0.3s);
            border-radius: 50%;
        }
    }

    @media screen and (max-width: 768px){
        .header-section{
            padding: 0 j(16);
        }
    }
</style>
