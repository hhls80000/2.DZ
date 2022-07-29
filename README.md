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
        <div class="msgLi" v-for="item in msgList" :key="item.message">
          <dd :class="'img' + (item.isSelf ? 'Right' : 'Left')">
            <i class="i">
              <img
                :src="
                  item.isSelf
                    ? require('@/images/user.png')
                    : require('@/images/r.jpg')
                "
                :class="'img' + (item.isSelf ? 'Right' : 'Left')"
              />
            </i>
            <div class="msgBox">
              <div :class="'send' + (item.isSelf ? 'Right' : 'Left')">
                {{ item.isSelf ? " " : "东芝客服-小芝" }}
                {{ nowTime }}
              </div>
              <div
                id="msgSpan"
                :class="'span' + (item.isSelf ? 'Right' : 'Left')"
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
          </dd>
        </div>
      </div>
      <div class="footer">
        <div class="bar">
          <el-button class="sendButten" @click="dialogVisible = true"
            >留言</el-button
          >
          <el-dialog
            title="留言"
            :visible.sync="dialogVisible"
            width="30%"
            :append-to-body="true"
          >
            <span>这是一段信息</span>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="dialogVisible = false"
                >确 定</el-button
              >
            </span>
          </el-dialog>
          <div class="evaluation">
            <p>您对机器人的评价?</p>
            <el-rate v-model="rateValue" :texts="texts" show-text> </el-rate>
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
import robotMsgJson from "@/api/robotMsg.json";

export default {
  name: "App",
  data() {
    return {
      msgList: [],
      newData: "",
      value: null,
      highlight: true,
      defaultProps: {
        children: "subList",
        label: "comQuestion",
      },
      input: "",
      dialogVisible: false,
      formLabelWidth: "120px",
      nowTime: "",
      texts: ["非常不满意", "不满意", "一般满意", "满意", "非常满意"],
      rateValue: null,
    };
  },
  watch: {
    input: {
      function() {
        if (this.input.length) {
          this.disabled = false;
        } else {
          this.disabled = true;
        }
      },
    },
  },
  created() {
    let msg = [
      {
        comQuestion: "您好，这里是东芝客服部，我是机器人小芝，很高兴为您服务。",
      },
    ];
    this.pushMsgList(msg);
    this.getrobotMsgJson();
  },
  mounted() {
    // console.log(document.querySelector('.content').innerHTML);
  },
  methods: {
    // 获取初始数据
    getrobotMsgJson() {
      try {
        if (robotMsgJson.code == "200") {
          let list = this._.cloneDeep(robotMsgJson.list);
          for (let i = 0; i < list.length; i++) {
            list[i].subList = this._.flatten(list[i].subList);
          }
          this.pushMsgList(list);
          console.log(list);
        }
      } catch (error) {
        console.log("Request Failed", error);
      }
    },

    //树形控件点击事件
    handleNodeClick(data) {
      console.log(data);
      if (data.parentId) {
        let msg = [
          {
            comQuestion: data.comAnswer,
          },
        ];
        let aa = true;
        this.pushMsgList(msg, aa);
      }
    },
    //发送按钮事件
    sendSelfMsg() {
      console.log(this.input);
      let msg = [
        {
          comQuestion: this.input,
        },
      ];
      this.pushMsgList(msg, true);
      this.getNowTime();
      this.input = "";
    },

    //添加信息
    pushMsgList(msg, bar) {
      let buer = bar | false;
      let msgs = msg;
      console.log(msg);
      this.getNowTime();
      this.msgList.push({
        message: msgs,
        isSelf: buer,
      });
    },
    //获取当前时间
    getNowTime() {
      let now = new Date();
      let hour = now.getHours(); //获取当前小时数(0-23)
      let minute = now.getMinutes(); //获取当前分钟数(0-59)
      let second = now.getSeconds(); //获取当前秒数(0-59)
      this.nowTime =
        this.fillZero(hour) +
        ":" +
        this.fillZero(minute) +
        ":" +
        this.fillZero(second);
      Object.freeze(this.nowTime);
    },

    fillZero(str) {
      var realNum;
      if (str < 10) {
        realNum = "0" + str;
      } else {
        realNum = str;
      }
      return realNum;
    },

    //跳转聊天位置
    // jump() {
    //   this.$nextTick(() => {
    //     let el = this.scrollContainer;
    //     el.scrollTop = el.scrollHeight - el.clientHeight;
    //     // console.log(el, el.scrollTop, el.scrollHeight, el.clientHeight)
    //   });
    // },
    //对话框事件
    // handleClose(done) {

    // },
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

/* @media screen and (min-width: 768px) {
  .content .msgLi {
    max-width: 60%;
  }
} */

.content .msgLi {
  margin-top: 10px;
  padding-left: 10px;
}

.content .msgLi::after,
.content .msgLi .i::after {
  /*添加一个内容*/
  content: "";
  /*转换为一个块元素*/
  display: block;
  /*清除两侧的浮动*/
  clear: both;
}

.content .msgLi dd {
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

.content .msgLi .sendLeft {
  left: 8px;
  text-align: left;
  margin-left: 25px;
  line-height: 1.8;
  font-size: 13px;
  color: rgba(36, 46, 51, 0.4);
}

.content .msgLi .sendRight {
  right: 8px;
  text-align: right;
  margin-right: 25px;
  line-height: 1.8;
  font-size: 13px;
  color: rgba(36, 46, 51, 0.4);
}

.content .msgLi .imgLeft {
  float: left;
}

.content .msgLi .imgRight {
  float: right;
  flex-direction: row-reverse;
  margin-top: 15px;
}

.content .msgLi .spanLeft {
  float: left;
  background: #fff;
  text-align: left;
  margin-left: 25px;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 15px;
}

.content .msgLi .spanRight {
  float: right;
  /* background: #fff; */
  text-align: right;
  margin-right: 25px;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 15px;
}

.el-tree-node__content {
  flex-direction: row-reverse;
  justify-content: space-between;
}

.content .msgLi .spanLeft::after {
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

.content .msgLi .spanRight {
  float: right;
  background: #7cfc00;
  /* text-align: right; */
  margin-right: 25px;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 15px;
}

.content .msgLi .spanRight::after {
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

#main .footer .bar .evaluation p {
  margin-right: 15px;
  font-size: 13px;
  color: rgba(36, 46, 51, 0.4);
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

.content .msgLi .spanRight .el-tree-node__label{
  white-space: pre-wrap;

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

.el-dialog__header {
  background-color: #4b7edc;
}

.el-dialog__header .el-dialog__title {
  color: #fff;
  font-weight: 450;
}

.el-dialog__header,
.el-icon-close:before {
  color: #fff;
}

.el-rate {
  position: relative;
}

.el-rate .el-rate__text {
  position: absolute;
  left: -130px;
  width: 130px;
  padding: 1px;
  color: rgba(36, 46, 51, 0.4) !important;
  background-color: #f5f5f5;
  z-index: 99;
}
</style>
