<template>
    <div class="hot-trend">
        <div class="first-group"
             @click="getDocView(top1.id)"
        >
            <div class="doc-thumb">
                <DocThumb :flag="true" :title="top1.name" :docId="top1.thumbId"></DocThumb>
                <div class="top-1">
                    <span>Top 1</span>
                </div>
            </div>
            <div class="first-doc-detail">
                <div class="doc-title">{{ top1.name }}</div>
                <div class="doc-info">
                    <Icon type="ios-chatbubbles-outline"/>
                    {{ top1.commentNum }}
                </div>
                <div class="doc-info">
                    <Icon type="ios-thumbs-up-outline"/>
                    {{ top1.likeNum }}
                </div>
                <div class="doc-info">
                    <Icon type="md-heart-outline"/>
                    {{ top1.collectNum }}
                </div>
            </div>
        </div>
        <div class="second-group">
            <div class="trend-item" v-for="(item, index) in hotTrend" :key="index" @click="getDocView(item.id)">
                <div class="trend-num" :style="index | xxx">
                    {{ index + 2 }}
                </div>
                <div class="trend-title">
                    <span>{{ item.name }}</span>
                </div>

            </div>
        </div>
    </div>
</template>

<script>
import DocThumb from '@/home/DocThumb'
import StatsRequest from "@/api/stats";

export default {
    name: "HotTrend.vue",
    components: {DocThumb},
    data() {
        return {
            top1: {
                name: "document.docx",
                id: null,
                commentNum: 0,
                likeNum: 0,
                collectNum: 0
            },
            hotTrend: []
        }
    },
    filters: {
        xxx(value) {
            if (value < 2) {
                return {
                    backgroundColor: "#FACF36"
                }
            } else if (value < 4) {
                return {
                    backgroundColor: "#FFFAE4"
                }
            } else {
                return {
                    backgroundColor: "#fff",
                    borderColor: "#AAAAAA",
                    color: "#AAAAAA"
                }
            }

        }
    },
    created() {
        this.init()
    },
    methods: {
        init() {
            let data = {
                top1: {},
                others: []
            }

            StatsRequest.getHotTrend().then(response => {
                if (response.code === 200) {
                    data = response.data;
                    let topValue = data.top1 | null;
                    if (topValue != null) {
                        this.top1 = data.top1
                    }
                    let tempData;
                    if (data.others.length > 8) {
                        tempData = data.others.slice(0, 7)
                    } else {
                        tempData = data.others
                    }
                    if (tempData != null) {
                        this.hotTrend = tempData.sort(this.compare('hit'))
                    }
                }
            }).catch(err => {
                this.$Message.error("出错：" + err);
            })
        },
        compare(property) {
            return function (a, b) {
                let value1 = a[property];
                let value2 = b[property];
                return value1 - value2;
            }
        },
        getDocView(id) {
            this.$router.push({
                path: '/preview',
                query: {
                    docId: id
                }
            })

        }
    }

}
</script>

<style lang="scss" scoped>
.hot-trend {
    width: 100%;
    height: 140px;
    text-align: left;
    font-size: 12px;
    font-family: PingFangSC-Regular, PingFang SC, sans-serif;
    font-weight: 400;
    color: #000000;

    .first-group {
        width: 100%;
        display: flex;
        justify-content: flex-start;
        padding: 4px;

        &:hover {
            background-color: #f1f2f3;
            cursor: pointer;
        }

        .doc-thumb {
            width: 100px;
            height: 140px;
            position: relative;

            .top-1 {
                position: absolute;
                top: 0;
                left: 0;
                width: 58px;
                height: 24px;
                background-color: #FACF36;
                border: 1px solid #000000;
                border-radius: 2px 0 100px 0;
                text-align: center;

                span {
                    font-size: 14px;
                    font-family: PingFangSC-Semibold, PingFang SC, sans-serif;
                    font-weight: 600;
                    color: #000000;
                    line-height: 22px;
                }
            }
        }

        .first-doc-detail {
            padding-left: 20px;

            .doc-title {
                height: 70px;
                line-height: 17px;
                overflow: hidden;
                -webkit-line-clamp: 4;
                text-overflow: ellipsis;
                display: -webkit-box;
                -webkit-box-orient: vertical;
            }

            .doc-info {
                height: 24px;
                line-height: 24px;
                color: #464646;
            }
        }
    }

    .second-group {
        margin-top: 20px;
        //padding: 0 4px;
        .trend-item {
            display: flex;
            justify-content: flex-start;
            padding: 10px 4px;

            &:hover {
                cursor: pointer;
                background-color: #f1f2f3;
                border-radius: 4px;
            }

            .trend-num {
                width: 24px;
                height: 24px;
                display: block;
                border-radius: 2px;
                border: 1px solid #000000;
                line-height: 24px;
                text-align: center;
                font-weight: 600;
                font-size: 14px;
            }

            .trend-title {
                width: 200px;
                overflow: hidden;
                white-space: nowrap;
                text-overflow: ellipsis;

                span {
                    padding: 0 10px;
                    line-height: 24px;
                    font-weight: 400;
                    font-size: 12px;
                }
            }

        }
    }
}
</style>