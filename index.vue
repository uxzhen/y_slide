<template>
    <div class="height" @touchstart="start" @touchmove="move" @touchend="end">
        <div class="touchmove" :style="boxStyle">
            <div class="html flex f0" v-for="(item,index) in num" :key="index">
                <div class="form">
                    <div class="flex f0 f3">
                        <div class="fw fz30">{{index+1}}/{{num}}题</div>
                        <div class="tc">
                            <img class="start" src="@/assets/31.png" alt="png" />
                            <div class="fz24 start_text">收藏</div>
                        </div>
                    </div>
                    <div class="problem">
                        <div class="fw fz30 c28">问题：</div>
                        <div class="fz30 c28">PHP是世界上最好的语言PHP是世界上最好的语言PHP是世界上最好的语言?PHP是世界上最好的语言PHP是世界上最好的语言PHP是世界上最好的语言</div>
                    </div>
                    <div class="answer">
                        <div
                            v-for="(item,index) in data "
                            :key="index"
                            class="m0 flex bt item flex f0 f3"
                            @click="selcet(index)"
                            :class="[index===answer[0]?'error':'',index===answer[1]?'success':'']"
                        >
                            <div>
                                <span>{{if_key(index)}}：</span>
                                <span>{{item.name}}</span>
                            </div>

                            <img
                                class="if_img"
                                :src="error"
                                v-show="answer[0]===index&&answer[1]!==index"
                                alt
                            />
                            <img class="if_img" :src="success" v-show="answer[1]===index" alt />
                        </div>
                    </div>
                    <transition name="add_url">
                        <div v-if="if_answer">
                            <div
                                class="tc tips"
                                v-if="answer[1]!==answer[0]"
                            >很遗憾没有答对，正确答案：{{if_key(answer[1])}}</div>
                            <div class="flex tc f2 analysis">
                                <div class="bt">
                                    <img class="start" src="@/assets/19.png" alt="png" />
                                    <div class="fz26">视屏讲解</div>
                                </div>
                                <div class="bt">
                                    <img class="start" src="@/assets/26.png" alt="png" />
                                    <div class="fz26">知识框架</div>
                                </div>
                            </div>
                            <!-- 是否查看正确答案 -->
                            <div class="tc flex f0 fd next">
                                <img class="next_img" src="@/assets/35.png" alt />
                                <div class="fz26">向下滑切换下一道题</div>
                            </div>
                        </div>
                    </transition>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            // 开放可划方向
            left: false,
            bottom: false,
            top: false,
            right: false,
            data: [
                {
                    name: "我是错的",
                    answer: false,
                },
                {
                    name: "C是对的",
                    answer: false,
                },
                {
                    name: "答案c是对的选我,选我,选我,选我,选我,",
                    answer: true,
                },
                {
                    name: "我........是错的",
                    answer: false,
                },
            ],
            // touchmove 坐标
            boxStyle: {
                transitionDuration: "0s",
                transform: "translate(0,0)",
            },
            // 总页数
            num: 15,
            // 页面选中下标
            page_index: 0,
            // 窗口高度
            height: "",
            // y轴按下坐标
            y_start: "",
            y_end: "",
            // 是否已答题
            if_answer: false,
            // 正确答案和错误 下标1为用户选取的，2为默认答案
            answer: ["", ""],
            // 选择的正确和错误
            success: require("@/assets/34.png"),
            error: require("@/assets/33.png"),
        };
    },
    created() {
        this.set_title();
        this.get_data();
    },
    mounted() {
        this.get_height();
    },
    methods: {
        start(e) {
            this.boxStyle.transitionDuration = "0s";
            // console.log(e.changedTouches[0].pageY);
            this.y_start = e.changedTouches[0].pageY;
        },
        move(e) {
            this.boxStyle.transitionDuration = "0s";
            this.y_end = e.changedTouches[0].pageY;
            this.boxStyle.transform = `translate(0,${
                this.y_end -
                this.y_start +
                this.page_index * -this.height * 100 +
                "px"
            })`;
            // console.log("y坐标" + this.boxStyle.transform);
		},
		
        end(e) {
            if (this.y_start == this.y_end) return false;
            // console.log(e.changedTouches[0].pageY);
            this.y_end = e.changedTouches[0].pageY;
            this.page(this.y_start, this.y_end);
            console.log("y坐标" + this.boxStyle.transform);
        },
        go() {
            this.boxStyle.transitionDuration = "0.5s";
            this.boxStyle.transform = `translate(0,${
                this.page_index * -this.height * 100 + "px"
            })`;
            // 12.59 2 1
            // 25.18 4 2
            //  37.769 6 3
            // console.log("y坐标" + this.boxStyle.transform);
        },
        page(v1, v2) {
            if (this.y_start == this.y_end) return false;
            // console.log(v1, v2);
            if (v1 < v2) {
                console.log("上");
                // let img = document.querySelector(".imgs_c");
                if (this.page_index != 0) {
                    this.page_index--;
                }
            } else {
                console.log("下");
                if (this.page_index != this.num - 1) {
                    this.page_index++;
                    console.log("页码" + this.page_index);
                }
            }
            this.go();
        },
        set_title() {
            this.$store.commit("upPath", {
                url: "/answer",
                name: "PHP是世界上最好的语言",
                title: true,
            });
        },
        get_height() {
            // 获取窗口高度
            this.height =
                document.querySelectorAll(".html")[0].getBoundingClientRect()
                    .height / 100;
            console.log("窗口高度" + this.height + "rem");

            document.querySelector(".touchmove").style.height =
                this.num * this.height + "rem";
        },
        get_data() {},
        if_key(key) {
            switch (key) {
                case 0:
                    return "A";
                    break;
                case 1:
                    return "B";
                    break;
                case 2:
                    return "C";
                    break;
                case 3:
                    return "D";
                    break;
                default:
                    break;
            }
        },
        selcet(val) {
            if (this.if_answer) {
                return false;
            } else {
                this.if_answer = true;
            }

            this.answer[0] = val;
            for (let i = 0; i < this.data.length; i++) {
                if (this.data[i].answer) {
                    // this.answer[1] = i;
                    this.$set(this.answer, 1, i);
                    return i;
                }
            }
        },
    },
};
</script>

<style lang="scss" scoped>
.height {
    // height: 1;
    height: calc(100vh - 0.75rem);
    overflow: hidden;
    // overflow: auto;
    position: relative;
    .touchmove {
        position: absolute;
        // top: 0;
    }
}
.html {
    padding: 0 0.5rem;
    min-height: calc(100vh - 0.75rem);
    position: relative;
}
.form {
    display: inline-block;
    // min-height: calc(100vh - 0.75rem - 0.8rem);
    height: calc(100vh - 0.75rem - 0.8rem);
    overflow: hidden;
    overflow: auto;
    width: 100%;
    background: #fff;
    border-radius: 0.4rem;
    padding: 0.3rem;
}
.start {
    width: 0.56rem;
    height: 0.52rem;
    margin-bottom: 0.05rem;
}
.start_text {
    color: #505050;
}
.problem {
    margin-bottom: 0.57rem;
    div:first-child {
        margin-bottom: 0.2rem;
    }
}
.answer {
    > .item {
        width: 5rem;
        min-height: 0.68rem;
        border: 0.02rem solid #c4c4c4;
        border-radius: 0.34rem;
        // line-height: 0.68rem;
        padding: 0.12rem 0.3rem;
        margin-bottom: 0.3rem;
    }
    .item {
        position: relative;
    }
}
.error {
    background: #ffb2b2;
    border: 0.02rem solid #ffb2b2 !important;
}
.success {
    border: 0.02rem solid #b2ffd6 !important;
    background: #b2ffd6;
}
.if_img {
    width: 0.32rem;
    height: 0.32rem;
}
.tips {
    font-size: 0.3rem;
    font-weight: bold;
    color: #fb5656;
    margin: 0.39rem 0;
}
.analysis {
    img {
        width: 0.64rem;
        height: 0.64rem;
    }
}
.next {
    // position: absolute;
    // bottom: 0.7rem;
    // left: 0rem;
    width: 100%;
    animation: buzz 1.5s linear infinite;
    color: #c3c3c3;
    .next_img {
        margin-top: 0.52rem;
        width: 0.48rem;
        height: 0.48rem;
        margin-bottom: 0.05rem;
    }
}
</style>
