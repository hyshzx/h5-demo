<template>
    <div class="content">
        <div class="p-search">
            <div class="search-input">
                <form id="search_form">
                    <input type="text" placeholder="搜索你想要了解的二手优品" class=" col-90" id="p-search">
                </form>
            </div>
        </div>
        <div class="p_banner">
            <mt-swipe :auto="4000">
                <mt-swipe-item v-for="(item,index) in bannerData" :key="index">
                    <div class="demo-carousel"><img :src="item" alt=""></div>
                </mt-swipe-item>
            </mt-swipe>
        </div>
        <div class="p_navbar">
            <ul class="p_navbar_list flex">
                <li class="p_nav flex_column" v-for="(item,key) in navbarData" @click="gotoPageOffice(item.id)" :key="key"><img :src="item.src" height="84" width="84" alt="">
                    <span>{{item.title}}</span>
                </li>
            </ul>
        </div>
        <div class="p_hot_recommend">
            <p class="p_title">热门推荐
                <span class="red"> HOT</span>
            </p>
            <ul class="p_list">
                <li class="p_nav" v-for="(item,key) in categoryData" @click="gotoDetailPage(item.id)" :key="key"><img :src="item.img" alt=""></li>
            </ul>
        </div>
        <div class="p_hot_superior">
            <p class="p_title">热门优品</p>
            <ul class="p_list">
                <li class="p_nav flex_column" v-for="(item,key) in articleData" @click="gotoDetailPage(item.id)" :key="key"><img :src="item.img" alt="">
                    <p class="good_name">{{item.name}}</p>
                    <p class="good_content">{{item.content}}</p>
                    <p class="good_price">￥{{item.price}}</p>
                </li>
            </ul>
        </div>
        <bottom-nav :showData="BottomShowData"></bottom-nav>
    </div>
</template>
<script>
import { ajax } from "../lib/axios.js";
import bottomNav from "./bottomNavbar.vue";
export default {
    name: "index",
    data() {
        return {
            BottomShowData: {
                show_home: true,
                show_sale: false,
                show_recycle: false,
                show_me: false
            },
            bannerData: [],
            categoryData: [],
            articleData: [],
            navbarData: [
                {
                    src: "../static/img/index/icon_home_you.png",
                    title: "官方新品",
                    id: "1"
                },
                {
                    src: "../static/img/index/icon_home_erye.png",
                    title: "二爷优品",
                    id: "2"
                },
                {
                    src: "../static/img/index/icon_home_baozhang.png",
                    title: "官方保障",
                    id: "3"
                }
            ],
            search_value: ""
        };
    },

    created() {
        var self = this;
        ajax.index.getIndex().then(function(response) {
            console.log(response);
            if (response.status == 200) {
                if (response.data && response.data.api_code == 200) {
                    let bannerData = response.data.top_banners;
                    bannerData.forEach(function(item) {
                        item.img = "http://116.62.222.82:8082" + item.img;
                        self.bannerData.push(item.img);
                    });
                    let categoryData = response.data.top_categories.reverse();
                    categoryData.forEach(function(item) {
                        let oneItem = {};
                        oneItem.img = "http://116.62.222.82:8082" + item.img;
                        if (item.target) {
                            oneItem.id = item.target.id;
                        }
                        self.categoryData.push(oneItem);
                    });
                    let articleData = response.data.top_article;
                    articleData.forEach(item => {
                        let oneItem = {};
                        if (!item.img.trim() && item.target) {
                            oneItem.img =
                                "http://116.62.222.82:8082" +
                                item.target.models[0].imgs[0];
                            oneItem.content = item.target.content;
                            oneItem.price = item.target.price;
                            oneItem.name = item.target.name;
                            oneItem.id = item.target.id;
                        }
                        self.articleData.push(oneItem);
                    });
                }
            }
        });
    },
    components: {
        bottomNav: bottomNav
    },
    methods: {
        gotoDetailPage(goodId) {
            console.log(goodId);
            this.$router.push({
                path: "good",
                query: {
                    goodId: goodId
                }
            });
        },
        gotoPageOffice(id) {
            if (id == 1 || id == 2) {
                this.$router.push({
                    path: "good_office",
                    query: {
                        id: id
                    }
                });
            }
        }
    }
};
</script>
<style lang="less" scoped>
.content {
    background-color: #efefef;
    .p-search {
        width: 750px;
        height: 88px;
        position: fixed;
        margin: 0 auto;
        top: 0px; // left: 50%;
        // transform: translateX(-50%);
        z-index: 300;
        background: -webkit-linear-gradient(90deg, #00c7e9, #09b8f9);
        background: -o-linear-gradient(90deg, #00c7e9, #09b8f9);
        background: -moz-linear-gradient(90deg, #00c7e9, #09b8f9);
        background: linear-gradient(90deg, #00c7e9, #09b8f9);
        .search-input {
            height: 100%;
            padding: 14px 24px;
            input {
                height: 60px;
                width: 90%;
                border: 1px solid #fff;
                padding-left: 60px;
                border-radius: 30px;
                line-height: 60px;
                background: url("../../static/img/index/icon_search.png")
                    no-repeat 22px 13px;
                color: #fff;
            }
        }
    }
    .p_banner {
        margin-top: 88px;
        height: 420px;
        img {
            height: 100%;
        }
    }
    .p_navbar {
        height: 160px;
        background-color: #fff;
        .p_navbar_list {
            height: 160px;
            align-items: center;
            justify-content: space-between;
            padding: 0 60px;
            .p_nav {
                align-items: center;
                img {
                    display: flex;
                    flex-shrink: 0;
                    width: 84px;
                    height: 84px;
                }
                span {
                    margin-top: 10px;
                    font-size: 28px;
                    color: #333;
                }
            }
        }
    }
    .p_hot_recommend {
        margin-top: 20px;
        padding: 20px 24px;
        background-color: #fff;
        .p_title {
            font-size: 32px;
            color: #333;
            font-weight: bold;
            margin-bottom: 20px;
            .red {
                color: #d43629;
            }
        }
        .p_list {
            overflow: hidden;
            .p_nav {
                float: right;
            }
            .p_nav:nth-child(1) {
                margin-left: 10px;
            }
            .p_nav:nth-child(2) {
                margin-bottom: 10px;
            }
        }
    }
    .p_hot_superior {
        margin-top: 20px;
        padding: 20px 24px;
        background-color: #fff;
        margin-bottom: 98px;
        .p_title {
            font-size: 32px;
            color: #333;
            font-weight: bold;
            margin-bottom: 20px;
            .red {
                color: #d43629;
            }
        }
        .p_list {
            width: 100%;
            overflow-y: hidden;
            overflow-x: scroll;
            display: flex;
            flex-wrap: no-wrap;
            .p_nav {
                align-items: center;
                img {
                    width: 210px;
                    height: 210px;
                }
                .good_content {
                    width: 260px;
                    font-size: 24px;
                    color: #999;
                    text-align: center;
                    line-height: 1.2;
                }
                .good_name {
                    width: 260px;
                    font-size: 28px;
                    color: #333;
                    text-align: center;
                    line-height: 1.2;
                }
                .good_price {
                    width: 260px;
                    font-size: 28px;
                    color: #d43629;
                    text-align: center;
                    line-height: 1.2;
                }
            }
        }
    }
}
</style>
