# 2.TEXT

<template>
  <div id="app">
    <div id="main">
      <div class="header">
        <div class="left">
          <img class="img" src="@/images/logo.png" alt="" />
          <div class="title">东芝客服-小芝</div>
        </div>
      </div>
      <div class="content">
        <div>
          <li class="msgLi" v-for="item in msgList" :key="item.message">
            <i>
              <img
                :src="
                  item.isSelf
                    ? require('@/images/user.png')
                    : require('@/images/r.jpg')
                "
                :class="'img' + (item.isSelf ? 'right' : 'left')"
              />
            </i>
            <div class="msgBox">
              <div class="send">
                {{ item.isSelf ? " " : "东芝客服-小芝" }}
                {{
                  (nowTime.getHours() >= 10
                    ? nowTime.getHours()
                    : "0" + nowTime.getHours()) +
                  ":" +
                  (nowTime.getMinutes() >= 10
                    ? nowTime.getMinutes()
                    : "0" + nowTime.getMinutes()) +
                  ":" +
                  (nowTime.getSeconds() >= 10
                    ? nowTime.getSeconds()
                    : "0" + nowTime.getSeconds())
                }}
              </div>
              <div
                id="msgSpan"
                :class="'span' + (item.isSelf ? 'right' : 'left')"
              >
                <div>
                  <el-tree
                    :data="item.message"
                    :props="defaultProps"
                    accordion
                    auto-expand-parent
                    node-key="item.message.id"
                    icon-class="el-icon-arrow-right"
                    label="item.message.comQuestion"
                    children="item.message.subList"
                    @node-click="handleNodeClick"
                  ></el-tree>
                </div>
              </div>
            </div>
          </li>
        </div>
      </div>
      <div class="footer">
        <div class="bar">
          <el-button class="sendButten">留言</el-button>
          <div class="evaluation">
            <!-- <p></p> -->
            <el-rate v-model="value" show-text>请您对机器人评价? </el-rate>
          </div>
        </div>
        <div class="messageBoard">
          <el-input
            type="textarea"
            rows="2"
            v-model="input"
            placeholder="您好！这里是东芝客服部，请详细描述您的问题"
          ></el-input>
          <el-button class="sendButten" @click="sendSelfMsg">发送</el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import robotMsgJson from "@/api/robotMsg.json";

export default {
  name: "App",
  data() {
    return {
      msgList: [],
      nowTime: new Date(),
      newData: "",
      value: null,
      highlight: true,
      defaultProps: {
        children: "subList",
        label: "comQuestion",
      },
      input: "",
    };
  },
  watch: {},
  created() {
    let msg = [
      {
        comQuestion: "您好，这里是东芝客服部，我是机器人小芝，很高兴为您服务。",
      },
    ];

    this.msgList.push({
      message: msg,
      isSelf: false,
    });

    this.getrobotMsgJson();
    this.sendSelfMsg();
  },
  mounted() {
    // console.log(document.querySelector('.content').innerHTML);
  },
  methods: {
    // 获取初始数据
    getrobotMsgJson() {
      try {
        // if (robotMsgJson.code == "200") {
        //   let list = this._.cloneDeep(robotMsgJson.list);
        //   for (let i = 0; i < list.length; i++) {
        //     list[i].subList = this._.flatten(list[i].subList);
        //   }
        //   this.msgList.push({
        //     message: list,
        //     isSelf: false,
        //   });
        // }

      } catch (error) {
        console.log("Request Failed", error);
      }
    },

    //树形控件点击事件
    handleNodeClick(data) {
      console.log(data);
    },
    //发送按钮事件
    sendSelfMsg() {
      console.log(this.input);
    },
  },

  // 销毁时
  beforeDestroy() {},
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

* {
  padding: 0;
  margin: 0;
}

.all-img {
  width: 40px;
  height: 40px;
}

#main {
  /* margin-top: -215px;
  margin-left: -469px; */
  position: fixed;
  top: 50%;
  left: 50%;
  background-color: #ddd;
  width: 986px;
  min-width: 986px;
  transform: translate(-50%, -50%);
  overflow: hidden;
}
#main .header {
  height: 62px;
  background: #4b7edc;
  display: flex;
  justify-self: start;
}
#main .header .left {
  font-size: 14px;
  padding: 12px 16px;
  overflow: hidden;
  display: flex;
  align-items: center;
}
#main .header .left .img {
  width: 40px;
  height: 40px;
  /* border-radius: 50%; */
  /* -webkit-border-radius: 50%; */
  vertical-align: middle;
  display: block;
}
#main .header .left .title {
  font-size: 20px;
  font-weight: 550;
  margin-left: 12px;
  overflow: hidden;
  color: #fff;
}

/* 内容区 */
#main .content {
  top: 62px;
  bottom: 121px;
  padding-left: 16px;
  padding-right: 16px;
  overflow-x: hidden;
  -webkit-overflow-scrolling: touch;
  background: #f5f5f5;
  padding-bottom: 20px;
  width: auto;
  max-height: 320px;
  box-sizing: border-box;
}

@media screen and (min-width: 768px) {
  .content .msgLi {
    max-width: 60%;
  }
}

.content .msgLi {
  margin-top: 10px;
  padding-left: 10px;
  display: block;
  clear: both;
  display: flex;
}

.content .msgLi .msgBox {
  position: relative;
}

.content .msgLi img {
  width: 40px;
  height: 40px;
  margin-top: 5px;
}

.content .msgLi #msgSpan {
  /* background: #7cfc00; */
  border-radius: 10px;
  float: left;
  width: 420px;
  box-shadow: 0 0 3px #ccc;
}

.content .msgLi .send {
  left: 8px;
  text-align: left;
  margin-left: 25px;
  line-height: 1.8;
  font-size: 13px;
  color: rgba(36, 46, 51, 0.4);
}

.content .msgLi .imgleft {
  float: left;
}

.content .msgLi .imgright {
  float: right;
}

.content .msgLi .spanleft {
  float: left;
  background: #fff;
  text-align: left;
  margin-left: 25px;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 15px;
}

.el-tree-node__content {
  flex-direction: row-reverse;
  justify-content: space-between;
}

.content .msgLi .spanleft::after {
  content: "";
  position: absolute;
  width: 0;
  height: 0;
  top: 34px;
  left: 15px;
  border-top: 10px solid transparent;
  border-bottom: 10px solid transparent;
  border-right: 10px solid #fff;
}

.content .msgLi .spanright {
  float: right;
  background: #7cfc00;
  text-align: right;
  margin-right: 25px;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 15px;
}

.content .msgLi .spanright {
  content: "";
  position: absolute;
  width: 0;
  height: 0;
  top: 34px;
  right: 15px;
  border-top: 10px solid transparent;
  border-bottom: 10px solid transparent;
  border-left: 10px solid #7cfc00;
}

#main .footer {
  background-color: #f5f5f5;
}

#main .footer .bar {
  display: flex;
  justify-content: space-between;
  margin-left: 10px;
  margin-right: 10px;
  padding: 8px 10px;
}

#main .footer .bar .evaluation {
  display: flex;
  align-items: center;
}
#main .footer .messageBoard {
  height: 110px;
  background-color: #fff;
  border-bottom: solid 2px #ddd;
  border-left: solid 1px #ddd;
  border-right: solid 1px #ddd;
  border-top: solid 2px #ddd;
  text-align: right;
}

::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-thumb {
  background-color: #d1d1d1;
  border-radius: 3px;
  -webkit-border-radius: 3px;
  border-left: 2px solid transparent;
  border-top: 2px solid transparent;
}

.el-tree-node__label {
  font-size: 14px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.el-tree-node > .el-tree-node__children {
  color: #666;
}
.el-tree-node.is-expanded > .el-tree-node__children {
  color: #666;
}

.el-tree-node__content {
  padding: 5px;
  border-bottom: 1px solid #ddd;
}

.el-tree-node__content:hover {
  background-color: #fff;
  color: rgb(35, 124, 240);
}

.el-textarea__inner {
  resize: none;
  border: none;
  /* min-height: 80px !important; */
}

.el-textarea__inner:focus {
  border: none;
  /* border-top: solid 2px #ddd; */
}

.el-button {
  margin-right: 20px;
}
</style>
