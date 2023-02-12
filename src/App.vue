<template>
  <div id="app" style="display: flex; flex-flow: column; margin: 20">
    <div
    id="data"
    style="font-family:'Courier New', Courier, monospace;font-size: 18px; width: 100%; margin-bottom: 55px"
      scroll-with-animation
      scroll-y="true"
    >
      <!-- 用来获取消息体高度 -->
      <div id="okk" scroll-with-animation>
        <!-- 消息 -->
        <div
          v-for="(x, i) in msgList"
          :key="i"
        >
          <!-- 用户消息 头像可选加入-->
          <div
            v-if="x.my"
            style="
              display: flex;
              justify-content: end;;
              width: 100%;
            "
          >
            <div style="width: 400rpx">
              <div style="border-radius: 35rpx">
                <pre
                  style="
                    word-break: break-all;
                    width: 100%;
                    white-space: pre-wrap;
                    word-wrap: break-word;
                  "
                  >{{ x.msg }}</pre
                >
              </div>
            </div>
            <div style="width: 50px;height:50px;border-radius: 50%;overflow: hidden;">
              <img style="width: 50px;" src="https://www.jishuya.cn/wp-content/uploads/2022/05/技术鸭LOGO.jpg" switch-src="https://www.jishuya.cn/wp-content/uploads/2022/05/技术鸭LOGO.jpg" alt="技术鸭（JISHUYA）-网络文章交流社区">
            </div>
          </div>
          <!-- 机器人消息 -->
          <div
            v-if="!x.my"
            style="
              display: flex;
              flex-direction: row;
              align-items: flex-start;
              width: 100%;
            "
          >
            <div style="width: 500rpx">
              <div style="border-radius: 35rpx; background-color: #f9f9f9">
                <pre
                  style="
                    word-break: break-all;
                    white-space: pre-wrap;
                    word-wrap: break-word;
                  "
                >
 {{ x.msg }}</pre
                >
              </div>
            </div>
          </div>
          <a-divider />
        </div>

        <div style="height: 130rpx"></div>
      </div>
    </div>

    <!-- 底部导航栏 -->
    <div
      style="
        position: fixed;
        bottom: 0px;
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
      "
    >
      <div
        style="
          font-size: 55rpx;
          display: flex;
          flex-direction: row;
          justify-content: space-around;
          align-items: center;
          width: 75%;
          margin: 20;
          height: 51px;
        "
      >
        <a-input
          v-model="msg"
          type="text"
          style="
            width: 75%;
            height: 45px;
            border-radius: 50px;
            padding-left: 20px;
            margin-left: 10px;
          "
          @press-enter="sendMsg"
          default-value="content"
          placeholder-class="my-neirong-sm"
          placeholder="用一句简短的话描述您的问题"
          allow-clear
        />
        <!-- <input
          v-model="msg"
          type="text"
          style="
            width: 75%;
            height: 45px;
            border-radius: 50px;
            padding-left: 20px;
            margin-left: 10px;
          "
          @confirm="sendMsg"
          confirm-type="search"
          placeholder-class="my-neirong-sm"
          placeholder="用一句简短的话描述您的问题"
        /> -->
        <a-button
          @click="sendMsg"
          :disabled="msgLoad"
          type="primary"
          shape="round"
          style="height: 45px; width: 20%; border-radius: 2500px"
        >
          {{ sentext }}
          <a-spin v-show="msgLoad"/>
        </a-button>
      </div>
    </div>
    <!-- </view> -->
  </div>
</template>

<script setup>
import { reactive, ref } from "vue";
import axios from "axios";
/* 你的秘钥 你可以在这个网站去申请 : https://platform.openai.com/*/
const api = "sk-";
const msgLoad = ref(false);
const anData = reactive({});
const sentext = ref("发送");
const animationData = reactive({});
const showTow = ref(false);
const msgList = reactive([
  {
    my: false,
    msg: "你好我是openAI机器人,请问有什么问题可以帮助您?",
  },
]);
let msgContent = "";
let msg = ref("");
function sendMsg() {
  // 消息为空不做任何操作
  if (msg.value == "") {
    return 0;
  }
  sentext.value = "请求中";
  msgList.push({
    msg: msg.value,
    my: true,
  });
  msgContent += "YOU:" + msg.value + "\n";
  msgLoad.value = true;
  // 清除消息
  msg.value = "";
  axios
    .post(
      "https://api.openai.com/v1/completions",
      {
        prompt: msgContent,
        max_tokens: 2048,
        model: "text-davinci-003",
      },
      {
        headers: {
          "content-type": "application/json",
          Authorization: "Bearer " + api,
        },
      }
    )
    .then((res) => {
      let text = res.data.choices[0].text
        .replace("openai:", "")
        .replace("openai：", "")
        .replace(/^\n|\n$/g, "");
      msgList.push({
        msg: text,
        my: false,
      });
      msgContent += text + "\n";
      msgLoad.value = false;
      sentext.value = "发送";
      setTimeout(() => {
        window.scrollTo(0, document.body.scrollHeight);
      }, 500);
    }).catch((e) => {
      $message.info('未响应,请重试')
      msgLoad.value = false;
      sentext.value = "发送";
     })
}
</script>

<style>
</style>